# Risk / Assumption Memo

> Week 3 任务：Risk / Assumption Memo
> 项目：EIV (Execution-Integrity Validator)

## 前提假设

1. **ERC-8004 生态会持续发展**：我们押注 ERC-8004 作为 attestation + reputation 的链上标准。如果这个标准被弃用或大幅改动，需要重新适配。
2. **Sepolia 有足够真实的 DeFi 活动**：我们的验证需要真实交易可验。如果 Sepolia 上 DeFi 活动太少，可能需要切到其他 testnet 或 mainnet fork。
3. **GLM-5.1 能胜任多步调查任务**：作为赛道指定的 demo backend，我们需要它能诠释松散 intent、编排多步调查、抓未列举偏离。
4. **FAIL reputation 有约束力**：我们的核心假设是"agent 的 FAIL reputation 能被其他协议/用户读取并作为拒绝依据"。这在 production 中尚无大量先例。

## 最可能失败点（Pre-Mortem）

### 1. #2 技能 / 分工未完全确认
- **风险**：#2 刚归队，技能方向（前端 / Solidity / backend）尚未由 John 确认；若 #2 偏 Solidity 而非前端，dashboard 可能需退回最小形式
- **对策**：John 尽快确认 #2 技能并分配；默认 #2 owns dashboard；若实际偏 Solidity 则改分担合约/测试、dashboard 部分回退

### 2. ERC-8004 Validation Registry in-flux
- **风险**：Validation Registry 没有 canonical 已部署地址，仍在讨论中，接口可能变
- **对策**：自部署最小兼容版（DESIGN.md 既有备案）；pin master commit 控变动

### 3. 最后一天整合接不起来
- **风险**：各模块独立开发，最后串接时可能出问题
- **对策**：Day-1 已冻结接口 + 假数据 walking skeleton 先行；23/23 selftest 已覆盖接口正确性

### 4. "薄包装"质疑
- **风险**：评委可能质疑我们只是把 LLM 包了一层，不是真长程 agent
- **对策**：刻意建多步调查回圈，log/demo 显式秀长程（解析 → 捞链 → 比对 → 起 PoC → 校验 → attest）

## Week 4 Fallback

| 场景 | Fallback |
|------|----------|
| Validation Registry 无法按时部署 | 走自部署最小兼容版，不依赖上游可用性 |
| GLM-5.1 调查回圈不稳定 | 先半自动（人 + LLM 协作），保证核心流程跑通 |
| 真链 tx 解码出问题 | 先用结构正确的 fixture 跑通管线，再切真 Sepolia RPC |
|| #2 dashboard 做不出来 | 退回最小表格 / CLI viewer，money shot 仍保留 |
| 2–3 场景跑不齐 | 至少 1 个场景全真链端到端，其余诚实标明 mock |

## 核心信任主张一致性

- **FAIL 可重跑**（trustless 一半）：任何人拿到相同 intent + txRef，可以重跑验证看结果是否一致
- **PASS 有界**：没验出问题不等于没问题，依赖覆盖率与诚实
- **非即时防护**：这是究责/信任层，不阻止单笔交易
- **Production 补丁**：staking / zkML / TEE 是 future，不在 MVP 范围

## 诚实边界（必须一致地说）

- **FAIL 可重跑** → 半 trustless（自己跑 PoC 即可验证）
- **PASS 有界** → "没验出问题"无法被证明，依赖覆盖率与诚实；production 才上 staking / zkML / TEE
- **非即时防护** → 究责 / 信任层，不阻止单笔交易
