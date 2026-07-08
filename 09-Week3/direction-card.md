# 方向卡 — Direction Card

> Week 3 任务：方向卡
> 项目：EIV (Execution-Integrity Validator)

## 参赛赛道

Z.AI 赛道（Web3 × Long-Horizon Task）。
模型后端：GLM-5.1（赛道要求的 demo backend，但我们系统设计是 model-agnostic 的，不绑死一个模型）。

## 项目名

**EIV（Execution-Integrity Validator）** — 代号确认沿用。
（之前的代号 AEGIS 因为撞名已经不用了。）

## 目标用户

- **把资产交给 AI agent 跑链上操作的人或团队**：他们需要事后能查证 agent 有没有照授权做事。就像你请了个代购，你希望事后能核对小票看他有没有乱买。
- **生态里会根据 agent 信誉决定接不接受某个 agent 的一方**：比如对手协议、用户、别人的 gate。
- **想低成本接入独立验证的 agent host**：只需要通过一个薄 client 请求验证就行。

## 要解决的问题

授权（签了字的 intent）和执行（链上交易）是两个分开的事件，没有东西天生保证它们一致。

打个比方：你给代购签了一份购物清单（"最多花 100 块、只去 A 超市、买完东西全拿回来、用完优惠券要归零"），但代购实际执行的时候可能去了 B 超市、多留了优惠券、或者东西没全拿回来。

现在没有一个独立、公开、不可篡改的记录能证明 agent 有没有照办——这是规模化把钱交给 agent 的信任天花板。

## 最小功能（MVP）

1. **事后验证**（post-hoc）：不挡当下交易，事后独立检查执行有没有对上授权。
2. **确定性检查引擎**：违规分类 A–G，MVP 聚焦 A（Target）/ C（Auth-Expansion）/ D（Amount）/ F（Residual），B/E 顺手带；三级严重度：FAIL / WARN-SAFETY / WARN-SPEC。
3. **GLM-5.1 调查回圈 + grounding guard**：任何 FAIL 都必须能还原成可重现的 PoC（Proof of Concept），否则不能定 FAIL。简单说就是——你说它犯规了，得拿出录像证据。
4. **把判定写到 ERC-8004**：attest 进 Validation Registry，累积 reputation。
5. **2–3 个 demo 场景**：dashboard 展示 intent-vs-execution diff + mock consumer 根据 reputation 拒绝低信誉 agent。

## 目标链

**ETH Sepolia**（已拍板）。

理由：
- Sepolia 上有比较真实的 DeFi/swap 活动 → 有真实交易可以验证（Monad testnet 的 DeFi 太薄了）
- ERC-8004 合约已经部署在 Sepolia
- 工具链成熟（Etherscan-Sepolia / Foundry / RPC）

PoC 模拟走 Foundry mainnet-fork（复用 AIP 的 fork 测试），attest 打在 Sepolia。

## 技术路径

- **复用**：AIP 的 EIP-712 intent、invariant 检查库、Foundry/fork 模拟
- **新做**：GLM-5.1 调查回圈 + grounding guard、ERC-8004 整合 + attest、薄 client（MCP）、dashboard、mock consumer
- **现状**：已有可跑的 `predicates.py` 确定性核心 + walking-skeleton validator 服务（HTTP API + 三个 stub 边界）
- **runtime**：TBD（不绑特定 framework）

## 主要风险

1. **整合最后一天接不起来** → Day-1 已冻结接口 + 假数据 walking skeleton 先行
2. **"凭什么信 validator" 没答好** → 用 grounding guard 洞见回应：FAIL 可重跑、PASS 有界、staking/TEE 是 future
3. **做成薄 LLM 包装而非真长程 agent** → 刻意建多步调查回圈，log/demo 显式秀长程
4. **ERC-8004 Validation Registry 仍在讨论中** → 自行部署最小兼容版（DESIGN.md 既有备案）

## 已拍板

- codename：沿用 **EIV（Execution-Integrity Validator）**
- 目标链：**ETH Sepolia**
- ERC-8004：pin master commit hash（无 tags），license CC0
