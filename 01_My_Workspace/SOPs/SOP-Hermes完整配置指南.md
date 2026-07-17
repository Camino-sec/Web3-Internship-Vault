---
created: 2026-07-08
tags: [sop, hermes, 配置, ai-agent, telegram, 自动化]
---

# ⚕️ SOP：Hermes 完整配置指南

> **目标**：把 Hermes 配置成 Luvia 的全能 Web3 学习助手
> **已配置**：Xiaomi MiMo + Telegram + 本地终端 ✅
> **待配置**：SOUL 人格 + 定时任务 + Skills
> **适用环境**：macOS + Xiaomi MiMo 提供商 + Telegram Gateway

---

## 📋 配置清单

| 步骤 | 内容 | 状态 |
|------|------|------|
| 1 | API Key + Base URL 配置 | ✅ 已完成 |
| 2 | Telegram Gateway 连接 | ✅ 已完成 |
| 3 | SOUL.md 人格设定 | ⬜ 待执行 |
| 4 | 定时任务（Cron） | ⬜ 待执行 |
| 5 | Skills 安装 | ⬜ 待执行 |
| 6 | 测试验证 | ⬜ 待执行 |

---

## 🔧 Step 1：设置 SOUL.md

将 SOUL 配置内容复制到 `~/.hermes/SOUL.md`：

```bash
# 复制 SOUL 配置
cat "/Users/human.exe/Desktop/Web3-Internship-Vault/00_Reference_Library/AI_Web3_School/Hermes/Hermes-SOUL配置.md" > ~/.hermes/SOUL.md
```

或者手动编辑：
```bash
nano ~/.hermes/SOUL.md
```

**SOUL.md 核心内容**：
- 角色设定：Luvia 的 Web3 学习助手
- 六大核心能力：SOP 生成、学习资料推荐、每日提醒、概念解释、课程内容、WCB 提交
- 知识库路径：Obsidian Vault 路径配置
- 对话规则：先查笔记再回答、双链意识、进度感知

---

## 🔧 Step 2：添加定时任务

在终端依次运行以下命令：

### 任务 1：每日学习提醒（每天 21:00）
```bash
hermes cron add "daily-reminder" "0 21 * * *" "检查今天的 Daily Note 是否已写。如果没有，提醒 Luvia：'今天学了什么？花 2 分钟记录一下，周末写 SOP 时会感谢自己的 📝'。如果已写，鼓励一句。同时检查本周目标完成进度。"
```

### 任务 2：每周 SOP 生成（每周六 10:00）
```bash
hermes cron add "weekly-sop" "0 10 * * 6" "读取本周的所有 Daily Note 和新增概念笔记，生成 SOP 周报初稿。包含：本周目标回顾、学习产出、新增概念列表、踩坑与难点、关键收获、下周待办建议。将初稿发给 Luvia 审核。"
```

### 任务 3：每周 WCB 提醒（每周日 20:00）
```bash
hermes cron add "wcb-reminder" "0 20 * * 0" "提醒 Luvia：本周 WCB 提交截止了。检查本周是否有需要提交的内容：学习笔记、代码链接、社交媒体分享。如果有 SOP 周报，帮她整理成可提交的格式。"
```

### 任务 4：每周一目标设定（每周一 09:00）
```bash
hermes cron add "weekly-goal" "0 9 * * 1" "新的一周开始了！根据训练营课程大纲，提醒 Luvia 本周的学习主题和目标。列出需要预习的概念，并检查 vault 中是否已有相关笔记。"
```

### 验证定时任务
```bash
hermes cron list
```

---

## 🔧 Step 3：安装 Skills

将 skills 文件复制到 Hermes 的 skills 目录：

```bash
# 创建 skills 目录（如果不存在）
mkdir -p ~/.hermes/skills

# 复制 skills
cp "/Users/human.exe/Desktop/Web3-Internship-Vault/00_Reference_Library/AI_Web3_School/Hermes/skills/"*.md ~/.hermes/skills/
```

**Skills 包含**：
- SOP 生成器：自动生成周报初稿
- WCB 提交助手：整理提交内容
- 概念解释助手：用生活化方式解释概念
- 学习资料推荐：搜索相关学习资源

### 验证 skills
```bash
hermes skills list
```

---

## 🔧 Step 4：重启 Gateway

```bash
hermes gateway stop
hermes gateway start
```

**理解**：
- Gateway 是 Hermes 的 Telegram 连接服务
- 修改配置后必须重启才能生效
- 重启会重新加载 SOUL.md 和 skills

---

## 🔧 Step 5：测试验证

在 Telegram 中依次测试：

### 测试概念解释
```
解释 什么是 MEV？
```

**预期效果**：
- 用生活化方式解释 MEV
- 关联已有笔记（如果有）
- 询问是否需要创建概念笔记

### 测试 SOP 生成
```
生成周报
```

**预期效果**：
- 读取本周 Daily Note
- 生成 SOP 周报初稿
- 发送给 Luvia 审核

### 测试 WCB 提交
```
wcb
```

**预期效果**：
- 整理本周产出
- 生成提交内容草稿
- 提醒提交截止时间

### 测试每日提醒（手动触发）
```bash
hermes cron run "daily-reminder"
```

**预期效果**：
- 检查今日 Daily Note 状态
- 发送提醒或鼓励

---

## 🎯 使用场景速查

| 场景 | 在 Telegram 中说 | Hermes 会做什么 |
|------|------------------|----------------|
| 不懂概念 | `解释 [概念名]` | 用生活化方式解释，关联已有笔记 |
| 周末总结 | `生成周报` | 自动读取本周笔记，生成 SOP 初稿 |
| 提交 WCB | `wcb` | 整理本周产出为提交格式 |
| 找资料 | `推荐资料 [主题]` | 搜索相关文章/视频/教程 |
| 每日打卡 | 自动提醒（21:00） | 提醒写 Daily Note |
| 周一目标 | 自动提醒（周一 9:00） | 发送本周学习目标 |

---

## 🔧 故障排除

### Q1：Hermes 没有按预期回应

**排查步骤**：
```bash
# 检查 SOUL.md 是否正确加载
cat ~/.hermes/SOUL.md | head -20

# 检查定时任务
hermes cron list

# 检查 skills
hermes skills list
```

**常见原因**：
- SOUL.md 文件路径错误
- Skills 文件未复制
- Gateway 未重启

### Q2：定时任务没有触发

**排查步骤**：
```bash
# 检查 gateway 是否在运行
hermes gateway status

# 重启 gateway
hermes gateway stop
hermes gateway start
```

**常见原因**：
- Gateway 未启动
- Cron 表达式错误
- 系统时间不正确

### Q3：Telegram 收不到消息

**排查步骤**：
```bash
# 检查 Telegram 配置
hermes config | grep telegram

# 重启 gateway
hermes gateway restart
```

**常见原因**：
- Telegram Bot Token 无效
- 网络连接问题
- Gateway 配置错误

---

## 💡 最佳实践

1. **定期检查**：每周检查一次定时任务状态
2. **备份配置**：定期备份 `~/.hermes/` 目录
3. **更新 Skills**：根据学习进度更新 skills 内容
4. **优化 SOUL**：根据使用体验调整 SOUL.md 配置
5. **监控日志**：遇到问题时查看 `~/.hermes/logs/` 日志

---

## 📁 相关文件

- [[SOP-Hermes-Agent配置经验]] - API Key 配置和故障排除
- [[SOP-Hermes-Cron定时任务配置]] - 定时任务详细配置
- [[SOP-Hermes-SOUL配置]] - SOUL 人格设定详解

---

> 📝 **最后更新**：2026-07-08
> 🏷️ **适用环境**：macOS + Xiaomi MiMo 提供商 + Telegram Gateway
> 👨‍💻 **创建者**：Luvia's AI Assistant