# Deep Research Pack — 标准阅读摘要

> Week 3 任务：2–3 个标准的阅读摘要（解决什么 / 边界 / 还缺什么）
> 项目：EIV (Execution-Integrity Validator)

## 1. ERC-8004（Trustless Agents / Validation + Reputation Registry）

### 解决什么

给 agent 提供持久身份 + validation + reputation 三件套，让"某 agent 的某个判定/行为"能以不可篡改、可追溯的方式上链。

- Validation 接口：`validationRequest(validator, agentId, requestURI, requestHash)` → `validationResponse(requestHash, response, responseURI, responseHash, tag)`
- 验证对象是 **agentId + 资料 hash commitment**（非单一 tx）—— 语意由使用方自定

打个比方：ERC-8004 就像一个"信誉局"，agent 的每一次验证结果都会被记录在案，任何人可以来查。

### 边界

- Validation 原生就是 **post-hoc（request→respond）**，规格明文"payments orthogonal"—— 标准本身不含 gating / 闸门化。要闸门全得自建（off-standard）
- 现有 validator 类型（Oasis ROFL、Phala TEE、Reclaim ZK）验的是"谁在操作 / 代码可信"，不是"执行有没有对上 intent"
- 仍是 **Draft**；Validation 那块仍在跟 TEE 社群讨论更新中 → 接口会变

### 还缺什么（= EIV 的切入点）

- "验执行有没有对上 intent" 这类 validator 在 ERC-8004 上目前是**真空**
- 对 EIV 的影响：用官方 `erc-8004/erc-8004-contracts`、不重写 registry；把 intent + txRef 编进 requestURI/requestHash；**pin 版本**以防 Draft 变动

## 2. x402

### 解决什么

链上支付相关协议（HTTP 402 Payment Required 语意的链上实现方向）。属于 agent 经济里"支付"这一面。

### 边界 / 与 EIV 的关系

- EIV **不碰钱、不挡交易、不是钱包、不自建 escrow**
- 支付协议与 EIV 的"执行完整性验证"是正交的（各管各的）
- 在 AP2 三支柱框架下，支付/授权的执行是 Authorization 面；EIV 做的是 Accountability 面

### 还缺什么

DESIGN.md 对 x402 的细节记录较简（仅列为已核标准）。更深入的分析待补。

## 3. Google AP2（Agents-to-Payments Protocol）

### 解决什么

提出 agent 支付的三支柱框架：**Authorization / Authenticity / Accountability**。给"agent 代用户花钱"这件事一个结构化的信任模型。

打个比方：
- **Authorization**（有没有被授权）= 你给代购的购物清单
- **Accountability**（出事能不能追责）= 审计师核对小票
- **Authenticity**（agent 是否忠实反映用户真实意图）= 代购是否真的理解你想要什么

### 边界（三支柱各自谁来解）

- **Authorization**（有没有被授权）→ enforcement 类（如 AIP）挡；EIV **验**
- **Accountability**（出事能不能追责）→ **正是 EIV 的 attestation + reputation**
- **Authenticity**（agent 是否忠实反映用户真实意图）→ 第三层，最难，目前没人解，EIV 明确不宣称（= L3，future）

### 还缺什么

- Authenticity 是公认的开放难题
- 对 EIV 的影响：EIV 用 AP2 三支柱替自己精确定位在 **Accountability**，并诚实声明不碰 Authenticity

---

## 交叉定位（一句话）

- **AIP** = Authorization 的 enforcement（在执行路径里挡）
- **EIV** = Accountability（事后验 + 上链 attestation + reputation），骑在 ERC-8004 之上，填"执行对不对得上 intent"这个 validator 真空
- **Authenticity** = 没人解的第三层，EIV 不碰

## Sources

- ERC-8004 EIP：https://eips.ethereum.org/EIPS/eip-8004
- 官方 erc-8004-contracts：https://github.com/erc-8004/erc-8004-contracts
- AP2 官方：https://cloud.google.com/blog/products/ai-machine-learning/announcing-agents-to-payments-ap2-protocol
