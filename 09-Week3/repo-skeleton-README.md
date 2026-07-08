# EIV — Execution-Integrity Validator（Repo README 草稿）

> Week 3 任务：Repo Skeleton README
> 项目：EIV (Execution-Integrity Validator)

独立、**事后（post-hoc）**验证一个 AI agent 的链上交易，有没有符合它**签章的授权（IntentSpec）**，并把判定 attest 到 ERC-8004。
定位 = **L2 authorization-conformance**（验"有没有照签章授权做"），不是 L3"懂不懂用户真意"。
**不碰钱、不挡交易、不是钱包。**

## Problem

授权（intent）和执行（tx）是两个分开的事件，没有东西天生保证一致。

打个比方：你给代购签了购物清单（"最多花 100 块、只去 A 超市、用完优惠券要归零"），但代购实际执行时可能去了 B 超市、多留了优惠券、或者东西没全拿回来。现在没有一个独立、公开、不可篡改的记录能证明代购有没有照办。

这就是 EIV 要解决的问题——只不过"代购"换成了 AI agent，"购物清单"换成了签章 intent，"小票"换成了链上交易。

## Track

Z.AI 赛道（Web3 × Long-Horizon Task）。GLM-5.1 为赛道要求的 demo 模型后端；系统 model-agnostic。

## Target Chain

**ETH Sepolia**：有较真实的 DeFi/swap 活动可验、ERC-8004 合约已部署、工具成熟。PoC 模拟走 Foundry mainnet-fork，attest 打 Sepolia。

## MVP Flow

1. 用户给：签章授权（IntentSpec）+ 交易参照（txRef）
2. validator 经薄 client 收到 `validationRequest`
3. 解析签章 intent（EIP-712）→ 自己上链捞 tx（decode calldata + state change）
4. GLM-5.1 比对偏离（target / spender / outflow / residual allowance / value）
5. **grounding guard**：疑似偏离 → 生成 Foundry PoC 重现；跑不出来则回退重查，跑得出来才定 FAIL
6. 判定（PASS / FAIL + 违反项 + PoC）attest 进 ERC-8004 Validation Registry → 累积 reputation
7. mock consumer 读 reputation → 拒绝低信誉 agent（demo 出"约束力"）

**违规分类**：A:Target / B:Recipient / C:AuthExpansion / D:Amount / E:Deadline / F:Residual / G:SpecQuality
**MVP 范围**：A / C / D / F（B/E 顺手带，G 延后）
**三级 severity**：FAIL（违签章 spec 字段）/ WARN-SAFETY（有风险但 spec 没禁）/ WARN-SPEC（spec 本身信息不足）

## Tech Stack

- **确定性核心**：`predicates.py`（纯 Python 标准库，已测；唯一判定真相来源）
- **服务层**：stdlib HTTP API（零第三方依赖）
- **外部边界**（接口 + stub，Week 4 填真）：EIP712Verifier / ChainAdapter / AttestationSink
- **复用**：AIP 的 EIP-712 intent、invariant 检查库、Foundry/fork 模拟
- **ERC-8004**：官方 erc-8004-contracts（pin master commit；Identity/Reputation 已部署 Sepolia；Validation Registry 自部署最小兼容版）
- **GLM-5.1**：调查/编排外圈（model-agnostic，GLM-5.1 为赛道指定 demo backend）

## Repo Layout（现状）

```
eiv/
  predicates.py     # 确定性核心（已测，不动）
  schema.py         # JSON <-> dataclass、amount 解析、intent hash
  intent_source.py  # IntentSource + EIP712Verifier 边界
  chain_adapter.py  # ChainAdapter 边界 + MockChainAdapter
  attestation.py    # AttestationSink 边界 + StubAttestationSink
  store.py          # ValidationStore：记忆体 + 可选 JSON 落地
  service.py        # ValidatorService.run()
  api.py            # stdlib HTTP API
  demo.py           # 三 fixture 端到端 demo
  selftest.py       # 自动化验收（in-process + HTTP）
  mcp_tool.py       # validate_execution() MCP tool
  fixtures/         # intents/ + traces/
  runs/             # 验证纪录落地
```

## How to Run

```bash
# 从 hackathon/ 目录（纯 python，无需安装第三方）
python -m eiv.demo        # 端到端 demo：PASS / FAIL / FAIL
python -m eiv.selftest    # 自动化验收：23/23
python -m eiv.api --port 8000   # 起 API
```

## Risks（诚实声明）

- **非即时防护**：究责/信任层，不阻止单笔；约束力来自 reputation
- **validator 信任有界**：FAIL 可重跑（半 trustless）；PASS 无法被证明，靠覆盖率/诚实，production 才上 staking/zkML/TEE（future）
- **ERC-8004 Validation Registry in-flux**：无 canonical 已部署地址 → 自部署最小兼容版
- **非首创**：边 = "grounded 执行完整性"切法 + ERC-8004 上这类 validator 的真空

## Validation Plan（Week 4）

把三个 stub 换真：① RpcChainAdapter 真解 testnet tx ② OnChainAttestationSink 真写 ValidationRegistry ③ EIP712Verifier 真验章 + ecrecover。加 GLM-5.1 调查外圈 + grounding guard，2–3 场景端到端 + dashboard diff + mock consumer。
