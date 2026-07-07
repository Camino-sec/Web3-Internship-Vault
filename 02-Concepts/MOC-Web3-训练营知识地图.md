# MOC - Web3 训练营知识地图

> MOC = Map of Content，所有知识的入口。
> 从这里出发，点击链接跳转到具体笔记。
> 打开 [[04-Canvas/Week-01-Knowledge-Map.canvas|Canvas 知识地图]] 查看可视化关系图。

---

## 📋 学习计划

- [[05-Knowledge/Web3-Summer-Internship-学习计划|Web3 暑期实习学习计划（科学强化版）]]
- [[05-Knowledge/学习策略速查卡|学习策略速查卡]]
- [[05-Knowledge/AI-Web3学习计划|AI-Web3 共学营学习计划]]
- [[05-Knowledge/AI 学习地图|AI 学习地图]]
- [[05-Knowledge/Web3 学习地图|Web3 学习地图]]

---

## ⛓️ Web3 基础（8 个概念）

> 学习路径：区块链 → 以太坊 → 智能合约 → DeFi / 隐私协议
> 同时：区块链 → 钱包与交易 → 账户类型 / Gas 费

| 概念 | 红绿灯 | 关联 |
|------|--------|------|
| [[02-Concepts/Web3基础/区块链是什么\|区块链是什么]] | 🟢 | → 以太坊、钱包、DePIN |
| [[02-Concepts/Web3基础/以太坊入门\|以太坊入门]] | 🟢 | 区块链 → **以太坊** → 智能合约 |
| [[02-Concepts/Web3基础/智能合约\|智能合约]] | 🟢 | 以太坊 → **智能合约** → DeFi、隐私、Agentic Commerce |
| [[02-Concepts/Web3基础/DeFi概览\|DeFi 概览]] | 🟡 | 智能合约 → **DeFi** → Tokenomics |
| [[02-Concepts/Web3基础/钱包与交易\|钱包与交易]] | 🟢 | 区块链 → **钱包** → 账户类型、Gas |
| [[02-Concepts/Web3基础/EOA-智能账户-多签对比\|EOA / 智能账户 / 多签]] | 🟢 | 钱包 → **账户类型** |
| [[02-Concepts/Web3基础/链上隐私与隐私协议\|链上隐私与隐私协议]] | 🟡 | 智能合约 → **隐私协议** |
| [[02-Concepts/Gas费\|Gas 费]] | 🟢 | 钱包 → **Gas 费** |

### 知识链路

```
区块链（信任算法）
    ↓ 构建于
以太坊（可编程区块链）
    ↓ 运行
智能合约（链上自动执行程序）
    ├─→ DeFi（去中心化金融）→ Tokenomics
    └─→ 链上隐私与隐私协议

区块链
    ↓ 管理
钱包（管理私钥的工具）
    ├─→ 账户类型（EOA / 智能账户 / 多签）
    └─→ Gas 费（交易手续费）
```

---

## 🤖 AI 基础（6 个核心概念）

> 学习路径：LLM → 神经网络 / Prompt 工程 / AI Agent → Agent 记忆系统

| 概念 | 红绿灯 | 关联 |
|------|--------|------|
| [[02-Concepts/AI基础/LLM是什么\|LLM 是什么]] | 🟢 | → 神经网络、Prompt 工程、AI Agent |
| [[02-Concepts/AI基础/神经网络基础\|神经网络基础]] | 🟢 | LLM → **神经网络** |
| [[02-Concepts/AI基础/Prompt工程\|Prompt 工程]] | 🟢 | LLM → **Prompt 工程** |
| [[02-Concepts/AI基础/AI-Agent\|AI Agent]] | 🟡 | LLM → **AI Agent** → 记忆系统、Agentic Commerce |
| [[02-Concepts/AI基础/Agent记忆系统\|Agent 记忆系统]] | 🟡 | AI Agent → **记忆系统** |
| [[02-Concepts/AI基础/Vibe Coding\|Vibe Coding]] | 🟡 | AI 辅助编程方式 |

### 知识链路

```
LLM（大语言模型）
    ├─→ 神经基础：神经网络基础、反向传播、梯度下降
    ├─→ 使用方式：Prompt 工程
    └─→ 核心应用：AI Agent
              └─→ Agent 记忆系统
```

### 其他 AI 概念笔记

- [[02-Concepts/AI基础/反向传播算法|反向传播算法]]
- [[02-Concepts/AI基础/梯度下降法|梯度下降法]]
- [[02-Concepts/AI基础/AI基础概念卡片|AI 基础概念卡片]]
- [[02-Concepts/AI基础/AI×Web3知识全景|AI × Web3 知识全景]]
- [[02-Concepts/AI基础/Agentic-Wallet与Pact协议|Agentic Wallet 与 Pact 协议]]
- [[02-Concepts/AI基础/DevRel与Ops的区别|DevRel 与 Ops 的区别]]

---

## 🔗 AI × Web3 交叉（6 个方向）

> 这是两个领域的交叉地带：AI Agent 在 Web3 生态中能做什么？

| 方向 | 核心问题 | 来源概念 |
|------|----------|----------|
| [[02-Concepts/AI×Web3/Agentic Commerce\|Agentic Commerce]] | Agent 如何自主交易？ | AI Agent + 智能合约 |
| [[02-Concepts/AI×Web3/DePIN\|DePIN]] | 如何用代币激励硬件资源？ | 区块链 + 物理基础设施 |
| [[02-Concepts/AI×Web3/Tokenomics\|Tokenomics]] | 代币经济如何设计？ | DeFi + 经济模型 |
| [[02-Concepts/AI×Web3/AI Security Privacy\|AI Security & Privacy]] | Agent 的安全边界在哪？ | AI Agent + 链上隐私 |
| [[02-Concepts/AI×Web3/Layer2\|Layer 2]] | 如何扩展区块链性能？ | 以太坊扩容方案 |
| [[02-Concepts/AI×Web3/Chainlink与预言机\|Chainlink 与预言机]] | 链上如何获取链下数据？ | 智能合约 + 外部数据 |

### 知识交叉关系

```
AI Agent（执行者）
    └─→ Agentic Commerce（自主交易）
            ↑ 需要
        智能合约（基础设施）

区块链（底层）
    └─→ DePIN（去中心化物理基础设施）

DeFi（去中心化金融）
    └─→ Tokenomics（代币经济学）

AI Agent（执行者）
    └─→ AI Security & Privacy（安全边界）
```

### 其他交叉笔记

- [[02-Concepts/AI×Web3/AI×Web3交叉流程图|AI × Web3 交叉流程图]]
- [[02-Concepts/AI×Web3/受限Web3助手Workflow|受限 Web3 助手 Workflow]]

---

## 🟣 Monad 生态（待学习）

> 这是训练营的核心内容，目前还是空白，等待填充。

| 概念 | 状态 | 说明 |
|------|------|------|
| 并行执行 | 🔴 未学 | Monad 核心技术 |
| MonadBFT | 🔴 未学 | 共识机制 |
| EVM 兼容 | 🔴 未学 | 与以太坊的关系 |

---

## 📚 每周 SOP 总结

- [[03-SOP-Weekly/Week-01-SOP|Week 1 SOP]] — 学习系统搭建 + Web3 基础
- [[03-SOP-Weekly/Week-02-SOP|Week 2 SOP]]
- [[03-SOP-Weekly/Week-03-SOP|Week 3 SOP]]
- [[03-SOP-Weekly/Week-04-SOP|Week 4 SOP]]

---

## 🛠️ 工具与配置

- [[05-Knowledge/Hermes/Hermes-Agent-配置经验|Hermes Agent 配置经验]]
- [[05-Knowledge/Hermes/Hermes-完整配置指南|Hermes 完整配置指南]]

---

## 📅 每日笔记

> 按日期索引，学习时随手记一句，周末整合进 SOP。

- [[01-Daily/2026-07-06|2026-07-06]] — Day 1: 学习系统搭建

---

## ❓ 问题清单

- [[05-Knowledge/05-问题清单|待解决问题清单]]

---

#MOC #web3 #ai #训练营
