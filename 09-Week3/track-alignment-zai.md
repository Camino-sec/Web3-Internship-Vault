# Z.AI 赛道对齐任务 — Track Alignment

> Week 3 任务：Z.AI 赛道对齐任务
> 项目：EIV (Execution-Integrity Validator)

## Z.AI 赛道要求

**赛道**：Web3 × Long-Horizon Task
**模型**：GLM-5.1（赛道指定 demo backend）
**核心要求**：Agent 必须自主分解 → 计划 → 执行 → 迭代 → 交付（不是一次性 API 调用）

## EIV 如何对齐

### 1. 任务复杂度 ✓

EIV 的验证流程不是简单的"调一次 API 看结果"，而是一个多步调查链：
- 解析签章 intent（EIP-712）
- 独立从链上捞交易（RPC 解码）
- 多维度比对偏离（target / spender / outflow / residual / value）
- 疑似偏离 → 生成 Foundry PoC 重现
- PoC 校验 → 判定 PASS/FAIL
- attest 到 ERC-8004 → 累积 reputation

### 2. 闭环完整性 ✓

从输入（intent + txRef）到输出（attestation + reputation）是完整闭环：
- **输入**：签章授权 + 交易参照
- **调查**：GLM-5.1 驱动多步调查回圈
- **验证**：确定性引擎判定 + grounding guard
- **输出**：上链 attestation + reputation 累积
- **约束**：mock consumer 根据 reputation 拒绝低信誉 agent

### 3. 长程稳定性 ✓

- 多步调查回圈有明确的步骤边界和日志
- grounding guard 防止 LLM 幻觉导致误判
- 接口冻结 + walking skeleton 先行，降低整合风险

### 4. Web3 价值 ✓

- **ERC-8004**：链上 attestation + reputation，不可篡改
- **ETH Sepolia**：真实 DeFi 交易可验证
- **信任基础设施**：解决"规模化把钱交给 agent"的信任天花板

## 对齐度评估

| 维度 | EIV 表现 | 说明 |
|------|----------|------|
| 任务复杂度 | ★★★★☆ | 多步调查链，非一次性调用 |
| 闭环完整性 | ★★★★★ | 从输入到约束力全闭环 |
| 长程稳定性 | ★★★★☆ | grounding guard + 接口冻结 |
| Web3 价值 | ★★★★★ | ERC-8004 attestation + reputation |

## Demo Day 要秀的核心

1. **长程任务执行 log**：完整展示"解析 → 捞链 → 比对 → PoC → 校验 → attest"各步
2. **三个场景**：干净 PASS / 残留 FAIL / 未授权 FAIL
3. **约束力**：mock consumer 读 reputation 拒绝低信誉 agent
4. **诚实边界**：FAIL 可重跑 / PASS 有界 / 非即时防护
