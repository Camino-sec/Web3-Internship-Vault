---
created: 2026-07-08
tags: [sop, hermes, 配置, ai-agent, 故障排除, api-key]
---

# ⚕️ SOP：Hermes Agent 配置经验

> **一句话总结**：Hermes 是一个本地部署的 AI Agent 框架，支持 Telegram 聊天、定时任务、多工具调用。配置的核心坑在于 **API Key 类型必须和 Base URL 严格匹配**。
> **适用环境**：macOS + Xiaomi MiMo 提供商 + Telegram Gateway

---

## 🎯 什么是 Hermes？

Hermes 是一个开源的 AI Agent 平台（类似 Claude Code 的对话式助手），可以：
- 📱 通过 **Telegram** 随时对话
- 🛠️ 自动执行代码、文件操作、定时任务
- 🧠 有自己的记忆系统（Memory）
- 🔌 支持多种大模型提供商（OpenRouter、Xiaomi MiMo 等）

> 💡 **生活类比**：把 Hermes 想象成一个"住在你电脑里的 AI 管家"，你可以通过 Telegram 随时找它帮忙干活。

---

## 📋 配置流程总览

```
hermes setup          → 首次安装向导
hermes model          → 修改模型/提供商
hermes config set     → 快速写入配置项
hermes doctor         → 诊断工具（排错神器！）
hermes gateway start/stop → 启停 Telegram 网关
hermes chat -q "xxx"  → 终端直接测试对话
```

---

## ⚠️ 核心踩坑：API Key 与 Base URL 的匹配规则

### 问题现象

在 Telegram 中对话时，Hermes 返回：
```
❌ Non-retryable error (HTTP 401): HTTP 401: Invalid API Key
```

### 根本原因

**Xiaomi MiMo 将不同套餐的接口地址和密钥进行了严格隔离**，混用必报 401。

### 匹配规则表

| 密钥开头 | 套餐类型 | 正确的 Base URL |
|---------|---------|----------------|
| `sk-` | 按量付费 | `https://api.xiaomimimo.com/v1` |
| `tp-` | 订阅包月（中国大陆） | `https://token-plan-cn.xiaomimimo.com/v1` |
| `tp-` | 订阅包月（新加坡） | `https://token-plan-sgp.xiaomimimo.com/v1` |

> 💡 **生活类比**：就像你办了"包月健身卡"（tp-），但刷卡时去了另一家分店（错误的 Base URL），系统当然说"卡无效"。

---

## 🔧 配置步骤（从零开始）

### Step 1：运行安装向导

```bash
hermes setup
```

按提示选择：
- Inference Provider → Xiaomi MiMo
- 输入 API Key
- 选择 Base URL（根据你的套餐类型！）
- 选择模型（如 `mimo-v2.5-pro`）

### Step 2：验证配置

```bash
hermes doctor
```

检查 `API Connectivity` 部分，确保 `xiaomi` 前面是 ✅。

### Step 3：终端测试

```bash
hermes chat -q "测试，请回复ok"
```

**先在终端确认能通，再去 Telegram 测试！** 这样能排除 Telegram Gateway 的干扰。

### Step 4：启动 Telegram 网关

```bash
hermes gateway start
```

在 Telegram 中发送 `/new` 开始新对话。

---

## 🚨 常见问题排查

### Q1：终端测试成功，但 Telegram 还是报 401

**原因**：macOS 的 launchd 后台服务缓存了旧的环境变量。

**解决**：
```bash
# 强杀所有残留进程
killall -9 hermes-gateway 2>/dev/null; killall -9 hermes 2>/dev/null

# 前台启动（强制读取最新环境变量）
hermes gateway run
```

然后在 Telegram 发送 `/new` 重置会话。

### Q2：`hermes gateway start` 报 "Could not find service"

```
Could not find service "ai.hermes.gateway" in domain for user gui: 501
↻ launchd job was unloaded; reloading service definition
✓ Service started
```

**这是正常现象**！因为之前 `stop` 卸载了服务，系统自动重载。只要最后显示 `✓ Service started` 就没问题。

### Q3：curl 直连 API 成功，但 Hermes 报 401

**原因**：Hermes 读取的 `.env` 文件中的 Key 可能写错或带了多余字符。

**排查**：
```bash
# 查看 Hermes 实际使用的 Key
grep XIAOMI ~/.hermes/.env

# 强制重新写入
hermes config set XIAOMI_API_KEY "你的正确密钥"
hermes config set XIAOMI_BASE_URL "https://token-plan-cn.xiaomimimo.com/v1"
```

### Q4：修改了 .env 但没生效

**原因**：Hermes 的配置在 Gateway 启动时加载到内存，修改文件不会热更新。

**解决**：必须重启 Gateway。
```bash
hermes gateway stop
hermes gateway start
```

---

## 🧠 关键经验总结

1. **先终端，后 Telegram** — 任何配置改动后，先用 `hermes chat -q` 测试，确认本地通了再启动 Gateway。

2. **Key 和 URL 必须匹配** — `sk-` 用 `api.xiaomimimo.com`，`tp-` 用 `token-plan-cn.xiaomimimo.com`，混用必挂。

3. **改完配置必须重启** — `.env` 修改后，Gateway 不会自动加载，必须 `stop` + `start`。

4. **Telegram 里发 `/new`** — 重启后旧会话可能缓存了错误状态，`/new` 强制刷新。

5. **`hermes doctor` 是排错利器** — 遇到问题先跑这个，80% 的问题它能直接告诉你答案。

---

## 📁 配置文件位置

| 文件 | 用途 |
|-----|------|
| `~/.hermes/.env` | API Key 存储（敏感信息） |
| `~/.hermes/config.yaml` | 主配置文件（模型、提供商、工具等） |
| `~/.hermes/SOUL.md` | Agent 人格设定 |
| `~/.hermes/memories/` | Agent 记忆存储 |
| `~/.hermes/logs/` | 运行日志（排错时查看） |

---

## 🔗 相关链接

- [Hermes 官方文档](https://hermes-agent.nousresearch.com/docs)
- [Xiaomi MiMo API 文档](https://mi.com)

---

## 📁 相关 SOP

- [[SOP-Hermes完整配置指南]] - 完整配置流程
- [[SOP-Hermes-Cron定时任务配置]] - 定时任务配置
- [[SOP-Hermes-SOUL配置]] - SOUL 人格设定

---

> 📝 **最后更新**：2026-07-08
> 🏷️ **适用环境**：macOS + Xiaomi MiMo 提供商 + Telegram Gateway
> 👨‍💻 **创建者**：Luvia's AI Assistant