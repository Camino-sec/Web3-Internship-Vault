---
created: 2026-07-06
tags: [Hermes, 配置, 指南, 完整版]
---

# ⚕️ Hermes 完整配置指南

> **目标**：把 Hermes 配置成 Luvia 的全能 Web3 学习助手
> **已配置**：Xiaomi MiMo + Telegram + 本地终端 ✅
> **待配置**：SOUL 人格 + 定时任务 + Skills

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

## Step 1：设置 SOUL.md

将 `Hermes-SOUL配置.md` 中的内容复制到 `~/.hermes/SOUL.md`：

```bash
# 复制 SOUL 配置
cat "/Users/human.exe/Desktop/Web3-Internship-Vault/05-Knowledge/Hermes/Hermes-SOUL配置.md" > ~/.hermes/SOUL.md
```

或者手动编辑：
```bash
nano ~/.hermes/SOUL.md
```

---

## Step 2：添加定时任务

在终端依次运行以下命令：

```bash
# 每天 21:00 提醒写 Daily Note
hermes cron add "daily-reminder" "0 21 * * *" "检查今天的 Daily Note 是否已写。如果没有，提醒 Luvia：'今天学了什么？花 2 分钟记录一下，周末写 SOP 时会感谢自己的 📝'。如果已写，鼓励一句。同时检查本周目标完成进度。"

# 每周六 10:00 生成 SOP 周报草稿
hermes cron add "weekly-sop" "0 10 * * 6" "读取本周的所有 Daily Note 和新增概念笔记，生成 SOP 周报初稿。包含：本周目标回顾、学习产出、新增概念列表、踩坑与难点、关键收获、下周待办建议。将初稿发给 Luvia 审核。"

# 每周日 20:00 提醒 WCB 提交
hermes cron add "wcb-reminder" "0 20 * * 0" "提醒 Luvia：本周 WCB 提交截止了。检查本周是否有需要提交的内容：学习笔记、代码链接、社交媒体分享。如果有 SOP 周报，帮她整理成可提交的格式。"

# 每周一 09:00 发送本周目标
hermes cron add "weekly-goal" "0 9 * * 1" "新的一周开始了！根据训练营课程大纲，提醒 Luvia 本周的学习主题和目标。列出需要预习的概念，并检查 vault 中是否已有相关笔记。"
```

验证定时任务：
```bash
hermes cron list
```

---

## Step 3：安装 Skills

将 skills 文件复制到 Hermes 的 skills 目录：

```bash
# 创建 skills 目录（如果不存在）
mkdir -p ~/.hermes/skills

# 复制 skills
cp "/Users/human.exe/Desktop/Web3-Internship-Vault/05-Knowledge/Hermes/skills/"*.md ~/.hermes/skills/
```

验证 skills：
```bash
hermes skills list
```

---

## Step 4：重启 Gateway

```bash
hermes gateway stop
hermes gateway start
```

---

## Step 5：测试验证

在 Telegram 中依次测试：

```
# 测试概念解释
解释 什么是 MEV？

# 测试 SOP 生成
生成周报

# 测试 WCB 提交
wcb

# 测试每日提醒（等待 21:00 或手动触发）
hermes cron run "daily-reminder"
```

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

### Hermes 没有按预期回应
```bash
# 检查 SOUL.md 是否正确加载
cat ~/.hermes/SOUL.md | head -20

# 检查定时任务
hermes cron list

# 检查 skills
hermes skills list
```

### 定时任务没有触发
```bash
# 检查 gateway 是否在运行
hermes gateway status

# 重启 gateway
hermes gateway stop
hermes gateway start
```

### Telegram 收不到消息
```bash
# 检查 Telegram 配置
hermes config | grep telegram

# 重启 gateway
hermes gateway restart
```

---

## 📁 相关文件

- [[Hermes-SOUL配置|SOUL 人格设定]]
- [[Hermes-Cron定时任务|定时任务配置]]
- [[skills/SOP生成器|SOP 生成器 Skill]]
- [[skills/WCB提交助手|WCB 提交助手 Skill]]
- [[skills/概念解释助手|概念解释助手 Skill]]
- [[Hermes-Agent-配置经验|API Key 配置经验]]

---

#Hermes #配置 #指南 #完整版
