# Sponsor / Mentor 问题清单

> Week 3 任务：Sponsor / Mentor 问题清单
> 项目：EIV (Execution-Integrity Validator)

## Q1：ERC-8004 Validation Registry 定版 / 部署惯例

**问题**：ERC-8004 Validation Registry 目前没有 canonical 已部署地址（仍在跟 TEE 社群讨论更新中），我们的做法是自部署最小兼容版。请问这是推荐的做法吗？有没有最佳实践或参考实现？

**背景**：Identity Registry 和 Reputation Registry 已在 Sepolia 部署可直接用，但 Validation Registry 是我们核心依赖的 in-flux 部分。

## Q2：Sepolia tx 解码 / RPC 建议

**问题**：在 Sepolia 上解码交易（decode calldata + state change → ExecutionTrace），有没有推荐的 RPC 端点或工具？我们目前计划用公开 Sepolia RPC + Foundry，有没有更高效的做法？

**背景**：我们需要独立从链上捞交易真相，不信任执行方自报。

## Q3：Reputation 采纳惯例

**问题**：ERC-8004 的 reputation 机制，目前生态里有没有实际采纳的案例？即"某个 agent 的 reputation 记录被其他协议/用户读取并作为决策依据"的场景？

**背景**：这是我们 EIV "约束力"的核心假设——FAIL reputation 能被 mock consumer 读取并拒绝。想知道这在 production 中是否已有先例（开放问题）。
