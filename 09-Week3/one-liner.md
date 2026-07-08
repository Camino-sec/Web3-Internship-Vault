# 一句话定位 — One-Liner

> Week 3 任务：一句话定位

## 解决什么问题

授权（签了字的 intent）和执行（链上交易）是两个分开的事件，没有东西天生保证一致。现在缺一个独立、公开、不可篡改的记录，能证明一个 AI agent 有没有照它被授权的 intent 执行。

## 给谁用

把链上操作交给 AI agent 的人/团队（需要事后能查证），以及生态里会根据 agent 信誉决定接不接受某 agent 的一方。

## Week 4 最小 demo 要跑通哪条链路

一份签章授权 × 一筆 testnet 交易 → validator 独立捞 tx 解码 → 确定性检查比对偏离（grounding guard，FAIL 须有可重现 PoC）→ 判定 attest 进 ERC-8004 Validation Registry → mock consumer 读到该 agent 的 FAIL reputation 并拒绝它。

---

### 一句话版（备用）

一隻独立、GLM-5.1 驱动的验证 agent，事后查证某 agent 的链上交易有没有照它签章的 intent 执行，把判定 attest 进 ERC-8004 并累积 reputation —— 不碰钱、不挡交易、不是钱包。
