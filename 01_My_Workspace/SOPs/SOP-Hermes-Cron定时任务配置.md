---
created: 2026-07-08
tags: [sop, hermes, cron, 定时任务, 自动化, telegram]
---

# ⏰ SOP：Hermes Cron 定时任务配置

> **目标**：配置 Hermes 的定时任务，实现自动化学习提醒和周报生成
> **适用环境**：macOS + Hermes Agent + Telegram Gateway
> **预计时间**：15-20 分钟

---

## 📋 定时任务概览

| 任务名称 | 触发时间 | 功能 |
|---------|---------|------|
| daily-reminder | 每天 21:00 | 提醒写 Daily Note |
| weekly-sop | 每周六 10:00 | 自动生成 SOP 周报草稿 |
| wcb-reminder | 每周日 20:00 | 提醒 WCB 提交 |
| weekly-goal | 每周一 09:00 | 发送本周学习目标 |

---

## 🔧 任务 1：每日学习提醒（每天 21:00）

### 配置命令
```bash
hermes cron add "daily-reminder" "0 21 * * *" "检查今天的 Daily Note 是否已写。如果没有，提醒 Luvia：'今天学了什么？花 2 分钟记录一下，周末写 SOP 时会感谢自己的 📝'。如果已写，鼓励一句。同时检查本周目标完成进度。"
```

### Cron 表达式解析
- `0 21 * * *` = 每天 21:00
- 格式：`分钟 小气 日 月 星期`
- `0` = 整点
- `21` = 21 点（晚上 9 点）
- `* * *` = 每天、每月、每周

### 预期效果
- 每天晚上 9 点自动提醒
- 如果已写笔记 → 鼓励 + 进度检查
- 如果没写 → 温和提醒

### 手动测试
```bash
hermes cron run "daily-reminder"
```

---

## 🔧 任务 2：每周 SOP 生成（每周六 10:00）

### 配置命令
```bash
hermes cron add "weekly-sop" "0 10 * * 6" "读取本周的所有 Daily Note 和新增概念笔记，生成 SOP 周报初稿。包含：本周目标回顾、学习产出、新增概念列表、踩坑与难点、关键收获、下周待办建议。将初稿发给 Luvia 审核。"
```

### Cron 表达式解析
- `0 10 * * 6` = 每周六 10:00
- `6` = 星期六（0=周日，1=周一，...，6=周六）

### 预期效果
- 每周六上午 10 点自动生成周报草稿
- 基于本周实际笔记内容
- Luvia 审核修改后即可使用

### 手动测试
```bash
hermes cron run "weekly-sop"
```

---

## 🔧 任务 3：每周 WCB 提醒（每周日 20:00）

### 配置命令
```bash
hermes cron add "wcb-reminder" "0 20 * * 0" "提醒 Luvia：本周 WCB 提交截止了。检查本周是否有需要提交的内容：学习笔记、代码链接、社交媒体分享。如果有 SOP 周报，帮她整理成可提交的格式。"
```

### Cron 表达式解析
- `0 20 * * 0` = 每周日 20:00
- `0` = 星期日

### 预期效果
- 每周日晚 8 点提醒提交 WCB
- 自动整理本周产出为提交格式

### 手动测试
```bash
hermes cron run "wcb-reminder"
```

---

## 🔧 任务 4：每周一目标设定（每周一 09:00）

### 配置命令
```bash
hermes cron add "weekly-goal" "0 9 * * 1" "新的一周开始了！根据训练营课程大纲，提醒 Luvia 本周的学习主题和目标。列出需要预习的概念，并检查 vault 中是否已有相关笔记。"
```

### Cron 表达式解析
- `0 9 * * 1` = 每周一 09:00
- `1` = 星期一

### 预期效果
- 每周一早上 9 点发送本周学习目标
- 关联已有笔记，避免重复学习

### 手动测试
```bash
hermes cron run "weekly-goal"
```

---

## 🔧 管理命令

### 查看所有定时任务
```bash
hermes cron list
```

**输出示例**：
```
daily-reminder: 0 21 * * * - 检查今天的 Daily Note 是否已写...
weekly-sop: 0 10 * * 6 - 读取本周的所有 Daily Note...
wcb-reminder: 0 20 * * 0 - 提醒 Luvia：本周 WCB 提交截止了...
weekly-goal: 0 9 * * 1 - 新的一周开始了！...
```

### 删除某个任务
```bash
hermes cron remove "daily-reminder"
```

**注意**：删除后需要重新添加才能恢复

### 手动触发某个任务
```bash
hermes cron run "weekly-sop"
```

**用途**：
- 测试任务是否正常工作
- 手动触发周报生成
- 调试定时任务问题

---

## 🐛 常见问题排查

### Q1：定时任务没有触发

**排查步骤**：
```bash
# 检查 gateway 是否在运行
hermes gateway status

# 查看定时任务列表
hermes cron list

# 手动触发测试
hermes cron run "daily-reminder"
```

**常见原因**：
- Gateway 未启动
- Cron 表达式错误
- 系统时间不正确

### Q2：任务触发但没有执行预期动作

**排查步骤**：
```bash
# 查看 Hermes 日志
tail -f ~/.hermes/logs/hermes.log

# 检查 SOUL.md 是否正确加载
cat ~/.hermes/SOUL.md | head -20
```

**常见原因**：
- SOUL.md 配置不正确
- Skills 未安装
- 任务描述不清晰

### Q3：任务触发时间不正确

**排查步骤**：
```bash
# 检查系统时区
date

# 检查 Hermes 时区配置
hermes config | grep timezone
```

**常见原因**：
- 系统时区设置错误
- Cron 表达式时区不匹配

---

## 💡 最佳实践

1. **先手动测试**：添加任务后先用 `hermes cron run` 测试
2. **清晰描述**：任务描述要具体明确，避免歧义
3. **合理时间**：选择合适的时间触发，避免打扰
4. **定期检查**：每周检查一次定时任务状态
5. **备份配置**：记录所有定时任务命令，便于恢复

---

## 🔧 高级配置

### 修改任务触发时间
```bash
# 先删除旧任务
hermes cron remove "daily-reminder"

# 添加新任务（修改时间）
hermes cron add "daily-reminder" "0 22 * * *" "检查今天的 Daily Note 是否已写..."
```

### 临时禁用任务
```bash
# 删除任务（记录命令以便恢复）
hermes cron remove "daily-reminder"

# 需要时重新添加
hermes cron add "daily-reminder" "0 21 * * *" "检查今天的 Daily Note 是否已写..."
```

### 查看任务执行日志
```bash
# 查看 Hermes 日志
tail -f ~/.hermes/logs/hermes.log

# 搜索特定任务日志
grep "daily-reminder" ~/.hermes/logs/hermes.log
```

---

## 📊 Cron 表达式速查

| 表达式 | 含义 |
|--------|------|
| `0 21 * * *` | 每天 21:00 |
| `0 10 * * 6` | 每周六 10:00 |
| `0 20 * * 0` | 每周日 20:00 |
| `0 9 * * 1` | 每周一 09:00 |
| `*/5 * * * *` | 每 5 分钟 |
| `0 0 1 * *` | 每月 1 日 00:00 |
| `0 0 * * 0` | 每周日 00:00 |

---

## 📁 相关 SOP

- [[SOP-Hermes完整配置指南]] - 完整配置流程
- [[SOP-Hermes-Agent配置经验]] - API Key 配置和故障排除
- [[SOP-Hermes-SOUL配置]] - SOUL 人格设定

---

> 📝 **最后更新**：2026-07-08
> 🏷️ **适用环境**：macOS + Hermes Agent + Telegram Gateway
> 👨‍💻 **创建者**：Luvia's AI Assistant