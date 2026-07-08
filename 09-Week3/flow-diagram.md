# 最小闭环流程图 — Flow Diagram

> Week 3 任务：最小闭环流程图
> 项目：EIV (Execution-Integrity Validator)

## 1. 最小闭环（六段）

用户输入（签章授权 IntentSpec + txRef）
→ AI agent 处理（GLM-5.1 调查回圈：解析 intent → 编排查证）
→ Web3 机制（独立捞链上 tx，不信执行方自报）
→ 链/SDK/工具调用（EIP-712 验章 · decode calldata/state · Foundry PoC · ERC-8004 attest）
→ 输出（PASS / FAIL + 违反项 + PoC · 上链 attestation + reputation）
→ 验证材料（可重现 PoC · validation record · dashboard diff · mock consumer 拒绝）

用生活类比：你请代购买东西 → 代购执行 → 审计师独立去超市查小票 → 核对购物清单和小票差异 → 出审计报告 → 商场根据信誉决定要不要接待这个代购。

## 2. 验证回圈（grounding guard 的核心）

```
validationRequest(intent, txRef)
→ 解析签章 intent（EIP-712）
→ 链上捞 tx：decode calldata + state change
→ GLM-5.1 比对：target/spender? outflow? residual allowance? value?
→ 疑似偏离？
  ├─ 否 → PASS
  └─ 是 → 生成 Foundry PoC 重现
       → PoC 跑得出来？
         ├─ 否（不可重现）→ 回退重查
         └─ 是 → FAIL + 违反项 + PoC
PASS/FAIL → validationResponse → ERC-8004
```

关键设计：**没有 PoC 就不能定 FAIL**。这就像法官判案——你说有罪，得拿出证据（PoC），不能光靠感觉。

## 3. Demo 流程（秀出约束力）

```
场景一：授权内 swap（干净）
  玩具 Agent → testnet 发交易
  → EIV Validator 捞 tx → 无偏离
  → attest PASS → dashboard 绿灯

场景二：偷留 residual allowance
  玩具 Agent → testnet 发交易
  → EIV Validator 捞 tx → 抓偏离 → 跑 PoC
  → attest FAIL + 证据 → dashboard 红灯 + intent-vs-execution diff + PoC
  → Mock Consumer 读该 agent reputation（已含 FAIL）
  → Mock Consumer 拒绝该 agent ← 这就是"约束力"
```

## 4. 系统架构图

```
┌─────────────────────────────────┐
│  任何 Agent（host）              │
│  ├─ Agent 执行链上 tx            │
│  └─ Thin client / MCP request   │
└──────────┬──────────────────────┘
           │ validationRequest
           ▼
┌─────────────────────────────────┐
│  EIV Validator（独立 / moat）    │
│  ├─ GLM-5.1 调查回圈            │
│  ├─ 确定性检查                   │
│  ├─ Grounding guard              │
│  └─ Validator API                │
└──────────┬──────────────────────┘
           │ 独立捞执行真相
           ▼
┌─────────────────────────────────┐
│  链上 tx / state                 │
└─────────────────────────────────┘
           │ validationResponse (attest)
           ▼
┌─────────────────────────────────┐
│  ERC-8004                        │
│  Validation + Reputation Registry│
└──────────┬──────────────────────┘
           │ 读 FAIL/reputation
           ▼
┌─────────────────────────────────┐
│  Mock Consumer / Gate            │
│  读 reputation → 接受/拒绝       │
└─────────────────────────────────┘
```
