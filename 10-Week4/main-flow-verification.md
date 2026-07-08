# Week 4 — 最小可验证主流程

> 主流程：**输入 → AI / Agent 处理 → Web3 机制 → 可验证结果**
>
> 一句话：用户签名授权 → Agent 提议交易 → EIV 确定性裁决 → 链上 Attestation 证明

---

## Step 1 — 输入（Signed Intent）

用户通过 EIP-712 签名一份结构化授权，声明「允许 Agent 做什么」。

**代码位置：** `eiv/eip712.py`（typed-data digest + ecrecover 签名/验证）

**实际输入示例**（`eiv/fixtures/intents/intent_clean.json`）：

```json
{
  "spec": {
    "allowed_targets": ["0xRouter"],
    "allowed_spenders": ["0xRouter"],
    "token_in": "USDC",
    "token_out": "WETH",
    "max_amount_in": "100",
    "min_amount_out": "90",
    "recipient": "0xUser",
    "deadline": 1000,
    "require_zero_residual": true,
    "bounded_approval": true,
    "max_slippage_bps": 50
  },
  "signer": "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266",
  "signature": "0xd5f0541cfb58af6ed287708f61e6a2b1b26ec87b5de0c28f4f2d8a1a55d8a0933e18031aacc2acfca1b0ad809f906eff1f6c667eb83deffd67af17e2b69b8f431c",
  "domain": { "name": "EIV", "version": "1", "chainId": 11155111 }
}
```

**含义：** 用户授权 Agent 在 Uniswap Router 上用最多 100 USDC 换 WETH，至少拿回 0.02 WETH，只能发到自己的钱包，1 小时内有效，不留残留授权。

---

## Step 2 — AI / Agent 处理（GLM-5.1 Proposes）

GLM-5.1 Agent 接收任务，自主分解为步骤，提议一笔链上交易。

**代码位置：**
- `eiv/glm.py` — `GlmClient`（OpenAI-compatible API 调用）、`spec_from_prompt`（自然语言 → IntentSpec）、`propose_transaction`（Agent 提议交易）
- `eiv/agent_loop.py` — `AgentRun`（多步循环：plan → propose_tx → gate → 修正）

**Agent Loop 工作流：**

```
用户任务: "帮我在 Uniswap 用 USDC 换 WETH"
         ↓
   GLM-5.1 分解任务 → 生成 plan
         ↓
   提议交易 {to: 0xRouter, data: swap calldata}
         ↓
   EIV GATE 检查 → APPROVE / REJECT
         ↓
   如果 REJECT → 把违规原因喂回 GLM → 自我修正
         ↓
   全过程写入 JSONL 审计日志
```

**安全边界：** Agent 只能「提议」，不能「执行」。唯一执行路径是 EIV GATE 返回 APPROVE。核心验证模块 `eiv/predicates.py` 不依赖任何 AI 模型。

---

## Step 3 — Web3 机制（Deterministic Validation + On-chain Attestation）

EIV 对 Agent 提议的交易执行确定性裁决（A–G 规则），然后将结果写入 Sepolia 链上合约。

**代码位置：**
- `eiv/predicates.py` — 确定性验证引擎（7 类规则：A:Target, B:Recipient, C:AuthExpansion, D:Amount, E:Deadline, F:Residual, G:Slippage）
- `eiv/attestation.py` — `OnChainAttestationSink`（ERC-8004 链上写入）
- `contracts/` — `EIVValidationRegistry`（Solidity 0.8.19，Sepolia 已部署）

**运行结果**（`python -m eiv.demo`，3 个场景用同一份签名授权）：

| 场景 | 执行 | 裁决 | 违规 |
|------|------|------|------|
| Clean swap | 合规的 router swap | ✅ PASS | 0 |
| Residual allowance | 无限授权留给 router | ❌ FAIL | C:AuthExpansion + F:Residual |
| Unauthorized target | 转给攻击者地址 | ❌ FAIL | A:Target + B:Recipient + C:AuthExpansion |

**自测验证：** `python -m eiv.selftest` → **174/174 passed**（含密码学向量、HTTP 行为、RPC 解码、Attestation 编码、Console 端点）

---

## Step 4 — 可验证结果（On-chain Proof）

Attestation 已写入 Sepolia，任何人可独立复查。

**链上记录：**

| 项目 | 值 |
|------|-----|
| ERC-8004 合约 | [`0x6719c69829740232f652b4b6bad8e6850922a2fb`](https://sepolia.etherscan.io/address/0x6719c69829740232f652b4b6bad8e6850922a2fb) |
| Attestation 交易 | [`0xbc50b963d8f9c9f5f34ba7764f510e0f6cddf4d67a4b927584170bdac40ec6f0`](https://sepolia.etherscan.io/tx/0xbc50b963d8f9c9f5f34ba7764f510e0f6cddf4d67a4b927584170bdac40ec6f0) |
| 区块 | 11041392 |
| Gas | 145,626 |
| 事件 | `ValidationResponse` emitted |
| requestHash | `0xe218a34c8204b392b0455de31668d208aef8549a039af6076654fd033ac76748` |
| 链上查询 | `hasValidation(requestHash)` → **true** |
| Tag | `EIV.L2.PASS` |

**复查方式：** 在 Sepolia Etherscan 打开合约地址 → Read Contract → `hasValidation` 输入 requestHash → 返回 true。

---

## 主流程代码路径汇总

| 步骤 | 代码文件 | 命令 / 入口 |
|------|----------|-------------|
| 1. 输入 | `eiv/eip712.py` + `eiv/schema.py` | `python -m eiv.eip712 sign --intent ... --key 0x...` |
| 2. Agent 处理 | `eiv/glm.py` + `eiv/agent_loop.py` | `python glm_sandbox.py`（需 GLM_API_KEY） |
| 3. 验证 | `eiv/predicates.py` + `eiv/service.py` | `python -m eiv.demo` |
| 3. 上链 | `eiv/attestation.py` + `contracts/` | `python attest_live.py` |
| 4. 复查 | `eiv/api.py` + Console UI | `python -m eiv.api` → 浏览器打开 `/` |

---

## 相关仓库

- **eiv-core**（验证引擎 + Agent Loop）：[Monica06161127/eiv-core](https://github.com/Monica06161127/eiv-core)
- **eiv-dashboard**（Dashboard UI）：[Monica06161127/eiv-dashboard](https://github.com/Monica06161127/eiv-dashboard)
- **学习仓库**（本文件）：[Monica06161127/ai-web3-school-cohort-0](https://github.com/Monica06161127/ai-web3-school-cohort-0)

---

*AI × Web3 Agentic Builders Hackathon · Z.AI track · EIV Team*
