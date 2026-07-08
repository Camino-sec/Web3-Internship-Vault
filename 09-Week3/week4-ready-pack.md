# Week 4 Ready Pack — EIV（Execution-Integrity Validator）

> Week 3 任务：完整 Week 4 Ready Pack
> 截止：2026-06-13 12:00（UTC+8）提交 · 6/14 Demo Day

## 一頁速览

**EIV（Execution-Integrity Validator）** = 一隻独立、GLM-5.1 驱动的**事后（post-hoc）**验证 agent：查证某个 AI agent 的链上交易有没有照它**签章的授权（intent）**执行，把判定 attest 进 **ERC-8004**，累积 reputation 形成约束力。**不碰钱、不挡交易、不是钱包。** 定位 **L2 authorization-conformance**。

**已拍板决策**：
- **赛道**：Z.AI（Web3 × Long-Horizon Task）；模型 = GLM-5.1（赛道指定 demo backend，系统 model-agnostic）
- **目标链**：**ETH Sepolia**（DeFi/swap 较真实 → 有真 tx 可验；ERC-8004 已部署；工具成熟）
- **ERC-8004**：pin master commit（无 tags）；Identity / Reputation 已部署 Sepolia 可直接用；Validation Registry 自部署最小兼容版
- **团队**：**3 人，2 dev** —— John（技术 lead）+ #2（归队，负责 Dashboard）+ Luvia（运营）。#2 归队 → dashboard 回到 ON
- **codename**：沿用 **EIV**

**现状**：walking skeleton 已完成（`predicates.py` 确定性核心已测；selftest 23/23；三个 stub 边界接口冻结）。Week 4 = 把 stub 换真 + 接 GLM-5.1 外圈 + 2–3 场景 demo。

## 捆绑的 6 份核心文件

### ① Direction Card → `direction-card.md`
方向卡：赛道 / 项目名 / 目标用户 / 问题 / MVP / 技术路径 / 目标链 / 主要风险。
重点：Z.AI 赛道、目标链 ETH Sepolia、violation taxonomy A–G（MVP=A/C/D/F）、三级 severity、grounding guard。

### ② Proposal Memo → `proposal-memo.md`
一頁提案：目标用户 / 真实场景 / 最小功能 / 验证方式 / 风险边界 / 赛道。
重点：swap 授权的具体偏离型态；诚实边界（非即时防护、validator 信任有界、L2 定位）。

### ③ Repo Skeleton README → `repo-skeleton-README.md`
对外 repo README：problem / track / target chain / MVP flow / tech stack / repo layout / how to run / risks / validation plan。
重点：`eiv/` 已是雏形（17 档/1706 行，零第三方依赖）；ERC-8004 整合事实。代码已拆分为独立仓库：`eiv-core`（John）+ `eiv-dashboard`（#2）。

### ④ Sprint Plan（Week 4）→ `sprint-plan-week4.md`
6/8–6/13 每日计划，已按 2-dev 重排。John 专注核心链路；#2 负责 dashboard + 共担整合/测试；Luvia 扛 pitch / 影片 / README / 提交 / 发推文。

### ⑤ Risk Memo → `risk-memo.md`
前提假设 / 最可能失败点（pre-mortem）/ Week 4 fallback。
重点：#2 技能/分工确认；ERC-8004 Validation Registry in-flux → 自部署；诚实边界声明。

### ⑥ Sponsor / Mentor Questions → `sponsor-mentor-questions.md`
1–3 个具体、可一句话回答的问题。
重点：Q1 ERC-8004 Validation Registry 定版/部署惯例；Q2 Sepolia tx 解码建议；Q3 reputation 采纳惯例。

## 附：Sponsor SDK 整合计划 → `sponsor-sdk-integration-plan.md`
GLM-5.1 接在 model-interface 边界后当调查/编排脑；verdict 仍由确定性核心 + grounding guard 收敛。

## 其他支援文件
- 一句话定位 → `one-liner.md`
- 赛道对齐 → `track-alignment-zai.md`
- 深度研究包 → `deep-research-pack.md`
- 流程图 → `flow-diagram.md`
- 范围检视 → `scope-review.md`
- 技术验证计划 → `tech-validation-plan.md`
- Gap 诊断 → `gap-diagnosis.md`
- 团队状态 → `team-status.md`
- Workshop 笔记 → `workshop-notes.md`

## 提交检查

- [ ] 端到端至少 1 场景真链（Sepolia）+ 真 ERC-8004 attestation 跑通
- [ ] 2–3 场景齐（干净 PASS / 残留 FAIL / 未授权 FAIL）；未完成项走当天 fallback，诚实标明
- [ ] 诚实限制声明就位：FAIL 可重跑 / PASS 有界 / 非即时防护
- [ ] README / pitch / 影片定稿；由 Luvia 执行提交
- [ ] 赛道推文由 John 确认后、Luvia 手动发布（不自动）
