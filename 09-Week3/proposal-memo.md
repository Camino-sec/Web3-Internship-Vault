# 一頁提案备忘 — Proposal Memo

> Week 3 任务：一頁提案备忘
> 项目：EIV (Execution-Integrity Validator)

## 赛道

Z.AI 赛道（Web3 × Long-Horizon Task）。GLM-5.1 为赛道要求的 demo 模型后端；系统设计为 model-agnostic。

## 目标链

**ETH Sepolia**（已拍板）：有较真实的 DeFi/swap 活动可验、ERC-8004 合约已部署、工具成熟（Etherscan-Sepolia / Foundry / RPC）；PoC 走 Foundry mainnet-fork，attest 打 Sepolia。

## 目标用户

- 把资产交给 AI agent 执行链上操作、需要事后能证明 agent 有照授权做事的人/团队
- 根据 agent reputation 决定是否接受某 agent 的生态方（对手协议、用户、别人的 gate）

## 真实场景

agent 拿到一份签章授权："换最多 100 USDC → WETH、只走 router R、产出回到用户、执行后 allowance 归零"。

agent 上链执行后，可能出现：
- 打到未授权合约（A:Target）
- approve 给未授权对象或超额（C:Auth-Expansion）
- amountIn 超过上限或 amountOut 低于下限（D:Amount）
- 执行后残留 allowance（F:Residual）
- 产出送到非授权 recipient（B）
- 过了 deadline（E）

EIV 事后独立捞这笔 tx、和签章 intent 比对，判定有没有偏离，并把判定上链。

打个比方：就像审计师事后核对"购物清单"和"实际小票"，看有没有多买、买错、多留零钱。

## 最小功能

1. post-hoc on-chain 执行完整性验证（不挡当下交易）
2. 确定性检查引擎：taxonomy A–G（MVP = A/C/D/F）+ 三级 severity（FAIL / WARN-SAFETY / WARN-SPEC）
3. GLM-5.1 调查回圈 + grounding guard（FAIL 须有可重现 PoC）
4. attest 判定到 ERC-8004 Validation Registry，累积 reputation
5. 2–3 场景 + dashboard（intent-vs-execution diff）+ mock consumer（根据 reputation 拒绝）

## 验证方式

- **确定性检查（判定的真）**：复用 AIP invariant（allowlist / outflow cap / residual-allowance / value）+ Foundry/fork 模拟
- **GLM-5.1（调查的脑）**：诠释松散 intent、决定查什么、编排多步调查、抓未列举偏离、起草 PoC，但被 grounding guard 绑（无可重现证据不得定 FAIL）
- **现状**：`predicates.py` 已测；walking skeleton 的 selftest 23/23 通过（骨架内部一致性，非真链正确性）

## 风险边界（诚实声明）

- **非即时防护**：这是究责/信任层，不阻止单笔交易；约束力来自 reputation
- **validator 信任有界**：FAIL 可重跑（trustless 一半）；PASS（没验出问题）无法被证明，依赖覆盖率与诚实，production 才靠 staking / zkML / TEE 补（此为 future）
- **定位 L2**：验"有没有照签章授权做"（authorization-conformance），不宣称 L3"懂不懂用户真意"（= AP2 Authenticity，尚无人解）
- **非首创**：已有相邻前作；我们的边 = "grounded 执行完整性"这个切法 + ERC-8004 上这类 validator 的真空

## 已拍板 / 待补

- 已拍板：目标链 = ETH Sepolia；codename = EIV（沿用）；ERC-8004 = pin master commit、Identity/Reputation 已部署直接用、Validation Registry 自部署；团队 2 人（John 技术 + Luvia 运营）
- 待补：ERC-8004 Validation Registry 后续会变（持续追上游）
