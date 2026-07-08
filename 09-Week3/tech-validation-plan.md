# 技术验证计划 — Tech Validation Plan（Week 4）

> Week 3 任务：技术验证计划
> 项目：EIV (Execution-Integrity Validator)

## 背景

walking skeleton 已把所有外部依赖放在干净接口 + stub 后。Week 4 = 把 stub 换成真实实现，且接口已冻结、换实现不用重构上层。

## A. 三个 stub 边界（walking skeleton 的核心待验点）

### 1. ChainAdapter.get_execution_trace()
- **现状（stub）**：MockChainAdapter 读 JSON fixture
- **Week 4 要验证的"真"**：RpcChainAdapter 真用 Sepolia RPC 解 tx logs/trace，decode 成 ExecutionTrace
- **验收信号**：对一笔真 Sepolia tx，解出的 trace 字段与链上实际一致

### 2. AttestationSink.attest()
- **现状（stub）**：StubAttestationSink 印 ERC-8004 response、回假 tx ref
- **Week 4 要验证的"真"**：OnChainAttestationSink 真写 ERC-8004 Validation Registry
- **验收信号**：Sepolia 上能查到该 attestation；回传真 tx hash

### 3. EIP712Verifier.verify()
- **现状（stub）**：StubEIP712Verifier 一律接受
- **Week 4 要验证的"真"**：真 EIP-712 typed-data 验章 + ecrecover
- **验收信号**：正确签章通过、篡改/错签 → 401

## B. 逐项关键技术点

### 1. Chain adapter 真解 tx
- **目标**：给一个真 Sepolia tx hash，RPC 捞 receipt/trace，decode 出 approvals / transfers / residual allowance
- **工具**：Foundry / Etherscan-Sepolia / 公开 Sepolia RPC
- **fallback**：先用结构正确的 fixture 跑通管线（已有），再切真 Sepolia RPC

### 2. ERC-8004 真写 attestation
- **目标**：真送 `validationResponse` 到 Validation Registry（部署于 Sepolia）
- **整合事实**：
  - ERC-8004 repo 无 releases/tags → pin master commit hash
  - Identity Registry / Reputation Registry 已部署在 Sepolia 可直接用
  - ⚠️ Validation Registry（EIV 核心依赖）是 in-flux 的部分 → 自部署最小兼容版
  - repo 为 TS + Solidity（附 ABI），由 ABI 写 Solidity interface 接进 Foundry
- **fallback**：Validation Registry 即走自部署最小兼容版，不阻塞

### 3. EIP-712 真验章
- **目标**：接 AIP 已证的 `hashIntent`，真做 typed-data digest + ecrecover
- **验收**：正确签章 PASS；错签 / 篡改 intent → 验章失败（401）

### 4. Agent trace（GLM-5.1 调查回圈可观测）
- **目标**：多步调查过程要可追溯、log 显式秀长程（对抗"薄包装"质疑）
- **验收**：一次 FAIL 判定的 trace 能完整还原"比对 → 起 PoC → 校验 → attest"各步

### 5. Testnet 交易
- **目标**：真在 Sepolia 发出 2–3 个场景对应的交易（干净 / 残留 allowance / 未授权 target）
- **由**：玩具 agent / fixture 产生（仅 1 名开发者，走最小脚本）
- **验收**：每个场景对应一笔真 Sepolia tx hash

### 6. 权限 / 边界检查（确定性核心对真 trace）
- **目标**：`predicates.py`（已测）对"真 tx 解出的 trace"仍判得对（A/C/D/F）
- **验收**：真 trace 上，干净→PASS、残留→FAIL、未授权→FAIL，违规消息正确

### 7. Demo 截图 / 录影材料
- **目标**：intent-vs-execution diff（money shot）+ mock consumer 拒绝低 reputation agent 的画面
- **范围**：1-dev 现实下 dashboard 砍到最小——简单表格 / 静态页 / CLI viewer 即可
- **验收**：绿灯/红灯两条链路 + PoC + consumer 拒绝，可截图可录

## C. 现有可重跑的验收基线（别重做）

- `python -m eiv.demo` → PASS / FAIL / FAIL 三笔符合预期
- `python -m eiv.selftest` → 23/23 通过（in-process verdicts + 真 HTTP server 200/400/401/404）
- 诚实标：23/23 是骨架内部一致性与接口正确性，**非真链正确性**——上面 A/B 各项才是把它推到真链

## D. 依赖状态

- 目标链：**ETH Sepolia（已定）**
- ERC-8004：**pin master commit（无 tags）、Identity/Reputation 已部署可用、Validation Registry 自部署（已定）**
- 开发人力：**仅 John 一人做全部技术** → 玩具 agent 走最小脚本、dashboard 砍到最小
- 仍待追：ERC-8004 Validation Registry 后续会变（in-flux），持续追上游
