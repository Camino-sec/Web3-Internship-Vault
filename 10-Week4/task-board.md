# Week 4 Task Board — EIV Demo Day

> 原则：**只做一个最小可验证主流程**，其余全部 Cut 或 Mock。
>
> 主流程定义：**用户签名授权 → GLM-5.1 Agent 提议交易 → EIV 确定性裁决 → Console 展示结果 → Sepolia 链上 Attestation**

---

## Must-have（Demo 缺一不可）

| # | 任务 | Owner | Deadline | 验证方式 | 状态 |
|---|------|-------|----------|----------|------|
| M1 | 核心验证引擎（predicates A–G + EIP-712 + eth.py） | John | ✅ 已完成 | `python -m eiv.selftest` 174/174 pass | ✅ |
| M2 | Agent Loop（GLM-5.1 propose → EIV gate → verdict） | John | ✅ 已完成 | `python glm_sandbox.py` 三幕全绿 | ✅ |
| M3 | Web Console（零依赖 UI + 场景演示） | John | ✅ 已完成 | `python -m eiv.api` → 浏览器打开 `/` 可用 | ✅ |
| M4 | ERC-8004 Attestation 上链（Sepolia） | John | ✅ 已完成 | [tx 0xbc50…6f0](https://sepolia.etherscan.io/tx/0xbc50b963d8f9c9f5f34ba7764f510e0f6cddf4d67a4b927584170bdac40ec6f0) 已确认 | ✅ |
| M5 | README 更新（Z.AI Track Context + 完整文档） | Luvia | ✅ 已完成 | README 包含 Z.AI 分享会承接段落 | ✅ |
| M6 | Demo 视频录制（3–5 分钟） | John + Luvia | 6/14 15:00 | 视频文件上传到 repo 或网盘，链接可访问 | ⬜ |
| M7 | 两个 repo push 到 GitHub 最新状态 | John | 6/14 16:00 | `git log` 最新 commit 与本地一致，README 渲染正常 | ⬜ |

---

## Should-have（有了更好，但 Demo 不依赖）

| # | 任务 | Owner | 说明 |
|---|------|-------|------|
| S1 | Dashboard 连接真实 API | #2 | eiv-dashboard 当前用 mock-data；如果时间允许，对接 `python -m eiv.api` 的 `/validations` 端点 |
| S2 | Demo 脚本 / talking points | Luvia | 写一份 3 分钟口述稿，覆盖：问题是什么 → EIV 怎么解 → live demo → 链上证明 |
| S3 | Long-horizon task 执行日志 | John | Agent loop 的 JSONL 审计日志，展示多步分解 + 纠错过程 |

---

## Nice-to-have（锦上添花）

| # | 任务 | Owner | 说明 |
|---|------|-------|------|
| N1 | 多场景 demo（clean swap / drain / replay） | John | Console 已有 bundled scenarios，Demo 时可多展示一个 |
| N2 | Reputation API 演示 | John | `/reputation/{addr}` 端点已实现，可展示 agent 信任分 |

---

## Cut / Mock（明确不做，用 Mock 或省略）

| # | 决策 | 原因 |
|---|------|------|
| C1 | Dashboard 实时数据 → **用 mock-data 演示** | 时间不够，Dashboard 骨架已能展示 UI 设计思路 |
| C2 | 多链支持 → **仅 Sepolia** | Demo 聚焦单链，多链是后续迭代 |
| C3 | 生产部署 → **本地运行** | Demo 用 localhost，不部署到公网 |
| C4 | MCP tool 集成 → **不展示** | 已实现但不是 Demo 核心，省略 |
| C5 | SQLite 持久化 → **用 JSON store** | 功能已实现但 Demo 不需要数据库 |

---

## 主流程一句话

> **Agent 提议，EIV 验证，链上证明。**
>
> 用户签名一个 DeFi swap 授权 → GLM-5.1 Agent 自主分解任务并提议交易 → EIV 确定性裁决（PASS/FAIL） → 结果展示在 Console → Attestation 写入 Sepolia 的 ERC-8004 合约。

---

## 时间线

```
6/13 下午  M5 README 更新 ✅
6/13 晚上  M6 Demo 视频录制
6/14 上午  M7 push 最终代码 + 视频
6/14 下午  提交到 WCB
6/14 晚上  Demo Day 🎤
```

---

## 相关仓库

- **eiv-core**（验证引擎）：[Monica06161127/eiv-core](https://github.com/Monica06161127/eiv-core)
- **eiv-dashboard**（Dashboard）：[Monica06161127/eiv-dashboard](https://github.com/Monica06161127/eiv-dashboard)

---

*AI × Web3 Agentic Builders Hackathon · Z.AI track · EIV Team*
