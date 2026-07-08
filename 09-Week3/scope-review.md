# 范围检视 — Scope Review

> Week 3 任务：范围检视
> 项目：EIV (Execution-Integrity Validator)

## 砍掉的（MVP 不做）

### 1. Production-grade trust（staking / zkML / TEE）
- 砍因：这些是让 validator 本身"可信"的 production 解法，9 天做不完且非本期主张
- 处置：诚实声明边界——FAIL 可重跑（半 trustless），PASS 有界靠覆盖率/诚实，production 才上这层

### 2. 闸门化 / pre-exec enforcement（即时拦截单笔）
- 砍因：ERC-8004 Validation 原生 post-hoc 且"payments orthogonal"，闸门全得自建（off-standard）；对此 niche 闸门化会塌回 AIP 的 commitment/binding + in-path enforcement，更难更不差异化
- 处置：明确走 post-hoc；约束力交给 reputation/究责层

### 3. 链下行为验证（off-chain behavior）
- 砍因：独立者只能验它能独立观察的；链下无法独立捞真相
- 处置：MVP 只验 on-chain 执行完整性，链下列 future

### 4. L3 Authenticity / 反 poisoning（agent 是否忠于用户真意）
- 砍因：AP2 第三支柱，公认最难、目前无人解
- 处置：EIV 明确定位 L2（authorization-conformance），不宣称 L3

## 延后（taxonomy / 范围收敛）

### 5. G: SpecQuality（spec linter）整类
- 延因：daily 2026-06-06 已决"MVP = A+C+D，F 加分，G 延后"
- 处置：predicate 引擎目前对"spec 未定义 maxSlippageBps"只发一条 WARN-SPEC；完整 spec-linter 延后

### 6. 完整 violation taxonomy（B/E 之外的延伸）
- 收敛：MVP 聚焦 A（Target）/ C（Auth-Expansion）/ D（Amount）/ F（Residual）；B（Recipient）/ E（Deadline）顺手带（引擎已实现）；其余偏离型态靠 GLM-5.1 调查 + grounding guard 兜

## 改 mock / stub（本期用替身，接口已冻）

7. **签章验证** → `StubEIP712Verifier`（一律接受）；Week 4 才换真 EIP-712 + ecrecover
8. **链上真相** → `MockChainAdapter`（读 fixture）；Week 4 才换 `RpcChainAdapter` 真解 tx
9. **上链 attest** → `StubAttestationSink`（印 response、回假 tx ref）；Week 4 才换真写 ValidationRegistry
10. **reputation consumer** → mock consumer（读 reputation → 拒绝）；本来就是 demo 演法，非降级
11. **runtime / agent framework** → TBD，不绑特定 framework

## 恢复（2-dev 带回的范围）

### 12. Dashboard 回到 ON（scoped but proper 的 demo UI）
- 原砍因：技术 #2 退出，1-dev 无前端人力 → 砍到最小
- 恢复因：**#2 已归队** → dashboard 回到 ON，由 #2 负责
- 新范围：proper 的 demo UI（暗色主题、intent-vs-execution diff 视图、violations 列表、attestation 信息、mock consumer 决策），不再是最小 CLI viewer
- repo：独立仓库 `eiv-dashboard`，通过 HTTP API 对接 `eiv-core`

## 一句话

砍 production-grade trust（zkML/TEE）、砍 enforcement（闸门）、砍链下、砍 L3；延 G（spec linter）；dashboard 恢复为 scoped proper UI（#2 负责）；其余外部依赖用已冻接口 + stub 替身，Week 4 换真。
