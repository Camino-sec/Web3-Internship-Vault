# 🛠️ Week 1 工具准备记录

> AI × Web3 School Cohort 0 · Luvia· 学员 #3312

---

## 一、协作工具

| 工具 | 状态 | 用途 |
|------|------|------|
| **Telegram** | ✅ 已安装 · @Luvia0616 | 主要沟通渠道：接收课程通知、参与社群讨论、与 Learning Agent 交互 |
| **Zoom** | ✅ 已安装 · zoom.us.app | 参加线上直播课程、周五例会、共学分享 |
| **GitHub** | ✅ CLI 已认证 · `Monica06161127` | 学习仓库管理、提交 PoW、版本控制笔记和代码 |
| **Google Calendar** | ⚠️ 已安装 · Zoom 插件连接未成功 | 课程日程管理（Zoom 端已连接 Calendar，但 Calendar 端连接 Zoom 未成功，不影响使用） |
| **NotebookLM** | ✅ Web 端使用 | AI 辅助阅读：上传课程材料生成摘要、问答、知识图谱 |
| **Obsidian** | ✅ 本地知识库 · `~/AI-Web3-Notes/` | 结构化笔记管理：6 大分类目录，双向链接，每日学习记录 |

---

## 二、AI 工具

| 工具 | 版本 | 用途 |
|------|------|------|
| **Hermes Agent** | v0.14.0 | **主力 Learning Agent**：每日学习提醒（9:00 + 21:00 cron）、WCB 任务查询与提交、学习笔记生成、知识梳理 |
| **Claude Code** | v2.1.146 | 代码辅助：编写脚本、调试代码、理解技术文档，适合需要深度代码交互的任务 |
| **NotebookLM** | Web 端 | 文档理解：上传 HandBook / 讲义，生成结构化摘要和问答 |

**选择理由**：Hermes Agent 作为日常学习的核心助手，覆盖了从任务管理到笔记生成的全流程；Claude Code 作为代码层面的补充，在需要写代码或理解代码时使用。两者搭配，一个管「学」，一个管「写」。

**未安装（可选）**：
- Codex / Cursor：如果后续需要更沉浸式的 IDE 编码体验，可以再配置
- Z.ai / GLM API：如果需要国产模型做中文内容生成，可以按需接入

---

## 三、Web3 工具

| 工具 | 状态 | 用途 |
|------|------|------|
| **MetaMask** | ✅ Chrome 插件 v13.30.0 | 钱包管理：连接 DApp、签署交易、管理测试网账户 |
| **测试钱包** | ✅ 已创建 | 地址：`0x7F1a...524DB`（Sepolia 测试网） |
| **Sepolia Faucet** | ✅ 已获取测试 ETH | 来源：`sepolia-faucet.pk910.de` |
| **Etherscan (Sepolia)** | ✅ 已使用 | 区块浏览器：查看交易详情、验证交易状态 |
| **Node.js** | v26.1.0 + npm 11.13.0 | 运行时环境：后续部署智能合约、运行 Hardhat/Foundry 的基础 |
| **GitHub CLI (gh)** | ✅ 已认证 | 仓库管理、代码提交、WCB 任务证明链接生成 |

**已完成的 Web3 实践**：
- ✅ Sepolia 测试网 ETH 转账（从账户 1 → 账户 2，交易已确认）
- ✅ 交易哈希：`0xf9294ae5...1de8c5`
- ✅ 区块浏览器验证：[Etherscan 链接](https://sepolia.etherscan.io/tx/0xf9294ae505992162fa29c10a8d92c8b3485bc7be290e03e1e3f4a3f80c1de8c5)

**尚未安装（Week 2+ 按需配置）**：
- **Hardhat**：智能合约开发框架，适合 JavaScript/TypeScript 开发者，Week 2 如需部署合约时安装
- **Foundry**：高性能合约开发工具链，适合 Solidity 进阶，后续按课程要求选择
- **Remix IDE**：Web 端在线合约编辑器，零安装即可使用，适合初学者快速上手

---

## 四、工具搭配策略

我的学习路径是「产品研究 + 内容运营 + Hackathon 项目」，所以工具选择遵循 **够用、能扩展** 的原则：

```
日常学习流：
  Telegram（通知） → Hermes Agent（任务管理 + 笔记） → Obsidian（知识沉淀） → GitHub（版本控制 + 提交）

代码实践流：
  Handbook（学习） → Claude Code（写代码） → Node.js + Hardhat/Foundry（部署） → Etherscan（验证）

内容产出流：
  Obsidian（笔记） → Hermes Agent（小红书草稿） → X / 小红书（发布）
```

---

## 五、下一步计划

| 优先级 | 事项 | 预计时间 |
|--------|------|----------|
| 🔴 高 | Google Calendar ↔ Zoom 插件重新配置 | 10 分钟 |
| 🟡 中 | 按 Week 2 课程要求安装 Hardhat 或使用 Remix | 视课程需要 |
| 🟢 低 | 探索 Foundry / Codex 等进阶工具 | Week 3+ |

---

## 六、安全声明

⚠️ 本文档不包含任何 API Key、私钥、助记词、.env 文件或敏感凭证。钱包地址和交易哈希均为测试网公开信息。

---

*记录时间：2026-05-23 · AI × Web3 School Week 1*
