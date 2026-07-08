# Sprint Plan — Week 4（6/8–6/13）

> Week 3 任务：Week 4 Sprint Plan
> 项目：EIV (Execution-Integrity Validator)
> 截止：2026-06-13 12:00（UTC+8）提交 · 6/14 Demo Day

## 团队分工（2-dev 版）

- **John** = 技术 lead，validator 核心 + chain adapter + ERC-8004 整合 + LLM 调查回圈 + 自部署 Validation Registry + 部署
- **技术 #2** = Dashboard / Demo UI + 共担整合 / 测试
- **Luvia** = 运营，扛全部非程式（pitch + 3–5 分钟影片 + README 打磨 + 提交 + 发推文 + Demo Day + 协调）

**2-dev 范围**：dashboard 回到 ON，由 #2 负责（scoped but proper 的 demo UI）；John 专注核心链路。

## 前置（已完成，Week 4 不重做）

- 接口 schema 冻结 + 假数据 walking skeleton（`predicates.py` 已测；selftest 23/23）
- DESIGN.md v2 为权威设计

## 6/8（一）— 开核心工

- **John**：起 `RpcChainAdapter` 骨架（接 Sepolia RPC）；确认 AIP `hashIntent` 可接；由 ERC-8004 ABI 写 Solidity interface（为自部署 Validation Registry 铺路）
- **#2**：dashboard 起手——接 walking skeleton 冻结 schema（`GET /validations` / `/validations/{id}`），先用现有 fixture 渲染 intent-vs-execution diff 骨架
- **Luvia**：pitch 大纲 + 影片脚本骨架；README 打磨清单；确认提交流程与素材
- 真实现：chain adapter 骨架 + ERC-8004 interface + dashboard 骨架

## 6/9（二）— Chain adapter 真解 tx + 玩具 agent 最小脚本

- **John**：RpcChainAdapter 真解一笔 Sepolia tx → ExecutionTrace；写最小脚本让玩具 agent 在 Sepolia 发"干净 swap"交易
- **#2**：diff 视图填肉——PASS/FAIL 双态、violations 列表（category/severity/detail）、金额字符串安全显示；对接 John 解出的真 trace
- **Luvia**：依 demo 流程草拟 pitch 叙事（对齐"FAIL 可重跑 / 约束力来自 reputation"）；收集截图素材
- 真实现：1 条真链 trace 解出 + 1 笔真干净 tx
- Mock/fallback：其余场景仍 fixture

## 6/10（三）— 自部署 Validation Registry + ERC-8004 真写 attestation + EIP-712 真验章

- **John**：在 Sepolia 部署最小兼容 Validation Registry；OnChainAttestationSink 真送 validationResponse（Identity/Reputation 用官方已部署）；EIP712Verifier 换真验章 + ecrecover；补"残留 allowance""未授权 target"两场景最小发 tx 脚本
- **#2**：dashboard 显示 attestation 区块（attestation_ref / response / tag）；mock consumer 视图（读 reputation → 接受/拒绝）；共担 attest 路径测试
- **Luvia**：README 打磨（problem / track / MVP flow / risks）；整理提交字段清单
- 真实现：Validation Registry 部署 + attest 上链 + 真验章

## 6/11（四）— End-to-end 串接 + GLM-5.1 调查外圈 + 最小 dashboard

- **John**：把 source→adapter→validate→attest 串成真链端到端（至少干净场景全真）；接 GLM-5.1 调查回圈 + grounding guard，log 显式秀长程
- **#2**：dashboard 端到端对接真数据；打磨 money shot（diff + PoC + consumer 拒绝）；共担 end-to-end 测试
- **Luvia**：依端到端结果定稿 pitch；开始录影分镜；持续 README
- 真实现：≥1 场景真链端到端 + attestation + dashboard 真数据
- Mock/fallback：GLM 外圈可先半自动

## 6/12（五）— 2–3 场景 + 边界硬化 + 录影

- **John**：跑齐 2–3 场景（干净 PASS / 残留 FAIL / 未授权 FAIL）；边界硬化（错签→401、未知 tx→404，selftest 已覆盖）；把 demo 跑顺给运营录
- **#2**：dashboard 定版 + 截图；与 John 对"demo 跑顺"彩排；共担场景回归测试
- **Luvia**：录 3–5 分钟影片；README / proposal 定稿；备妥提交内容
- 真实现：demo 场景齐
- Mock/fallback：诚实标明哪些场景的链上部分仍 mock

## 6/13（六）— 提交（12:00 截止）

- 上午 **John**：最终端到端彩排、截图、诚实限制声明就位（FAIL 可重跑 / PASS 有界 / 非即时防护）
- 上午 **#2**：dashboard 最终检查、demo 录制支援
- 上午 **Luvia**：README 最终检查、**执行提交**、**手动发布赛道推文**（John 确认后）
- **12:00 前提交**
- 缓冲：任何未完成项一律走当天 fallback，不临时扩范围

## 6/14（日）— Demo Day

- Luvia 主讲 pitch + 影片；John 答技术质疑；#2 demo UI 现场 / 录影支援

## 「真实现 vs mock」一览

| 能力 | 目标状态 | Fallback |
|------|----------|----------|
| Chain adapter 解 tx | 真（≥1 场景，Sepolia） | 其余 fixture |
| ERC-8004 attest | 真上链（自部署 Validation Registry） | 本即自部署 |
| EIP-712 验章 | 真 ecrecover | —（优先做完） |
| GLM-5.1 调查外圈 | 真回圈 + grounding guard | 半自动 |
|| Dashboard / diff 视图 | **scoped proper UI**（#2 负责） | 退回最小表格 / CLI viewer |
| Reputation consumer | mock consumer | 本就是 demo 演法 |
| 多场景 | 2–3 场景齐 | 部分链上 mock，诚实标 |
