# 赛道选择说明 — Tweet Draft

> Week 3 任务：赛道选择说明

## 赛道选择

我们选择 **Z.AI 赛道（Web3 × Long-Horizon Task）**。

## 为什么选 Z.AI

1. **技术对齐**：EIV 的验证流程本身就是"长程任务"——多步调查回圈（解析 intent → 捞链 → 比对 → PoC → 校验 → attest），不是一次性 API 调用
2. **模型要求匹配**：Z.AI 赛道指定 GLM-5.1 作为 demo backend，我们系统设计为 model-agnostic，GLM-5.1 作为调查/编排脑正好合适
3. **Web3 价值天然**：ERC-8004 attestation + reputation 是链上原生的，不需要额外包装

## 为什么没选 Cobo

Cobo 赛道需要 CAW（Agentic Wallet）作为关键组件，但 CAW 不支持 ERC-7579 Hook 挂载。我们之前的 AIP Protocol 项目基于 ERC-7579，无法直接接入。虽然可以做中间层适配，但 Z.AI 赛道对我们的技术栈更自然。

## 项目一句话

一隻独立、GLM-5.1 驱动的验证 agent，事后查证某 agent 的链上交易有没有照它签章的 intent 执行，把判定 attest 进 ERC-8004 并累积 reputation —— 不碰钱、不挡交易、不是钱包。
