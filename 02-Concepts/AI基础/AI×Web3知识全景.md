---
tags:
  - concept
  - AI-Agent
  - MCP
  - Web3-Tool-Use
  - Chain-aware-Context
  - 共学营
  - week2
date: 2026-05-26
related:
  - "[[02-AI基础/AI-Agent|AI Agent]]"
  - "[[02-AI基础/Agentic-Wallet与Pact协议|Agentic Wallet 与 Pact 协议]]"
  - "[[01-Web3基础/钱包与交易|钱包与交易]]"
---

# AI × Web3 知识全景：从 Agent 到链上执行

> Agent 通过 Chain-aware Context 看见世界，通过 MCP + Web3 Tool Use 操作世界，通过 Pact 约束自己不乱来。

## 全景图

```
┌─────────────┐    ┌──────────────┐    ┌─────────────────┐
│  MCP 协议    │◄──│  AI Agent    │──►│  Web3 Tool Use  │
│  工具接口标准 │    │  执行循环     │    │  链上工具调用     │
└──────┬──────┘    └──────┬───────┘    └────────┬────────┘
       │                  │                      │
       │         ┌────────▼────────┐             │
       │         │Chain-aware      │             │
       │         │Context 链感知    │◄────────────┘
       │         │Agent 的"眼睛"    │
       │         └────────┬────────┘
       │                  │
       │    ┌─────────────▼──────────────┐
       └───►│   Cobo Agentic Wallet      │
            │   Pact = 权限边界           │
            └────────────────────────────┘
```

---

## 一、AI Agent — 大脑

> Agent 是能围绕目标持续调用工具、读取状态、调整步骤的 AI 系统。

### 核心公式

**模型提候选行动 → 系统限制行动空间 → 用户批准高风险边界**

### 五大组件

```
┌─────────────────────────────────────────────┐
│                AI Agent                      │
│                                              │
│  Tool Use ── Planning ── State               │
│      │           │          │                │
│      └─────── Reflection ◄─┘                │
│                  │                           │
│            Multi-Agent                       │
└─────────────────────────────────────────────┘
```

- **Tool Use**：调用外部能力（搜索、API、代码执行）
- **Planning**：把目标拆成步骤
- **State**：记录任务进度、工具结果、错误
- **Reflection**：检查中间结果，修正下一步
- **Multi-Agent**：多个 Agent 分工协作

### 最危险的设计

> 模糊目标 + 广泛工具 + 长期记忆 + 大额资产权限

**生活类比**：你请了个管家，但没说清楚要做什么（模糊目标），给了他所有钥匙（广泛工具），让他永远住你家（长期记忆），还给了他无限额的信用卡（大额资产权限）。

---

## 二、Chain-aware Context — 眼睛

> 让 AI 在行动前，能看见正确的链上状态，而不是靠猜测。

### 第一性原理

> 模型不能凭语言记忆判断链上事实，链上事实必须从工具和索引层读取。

**生活类比**：你问朋友"我银行卡里有多少钱"，他不能凭印象猜，必须打开银行 App 看一眼。

### 六个知识节点

| 节点 | 难度 | 做什么 |
|------|------|--------|
| On-chain Data | 初级 | 读余额、交易、日志（必须带 chain id + block number） |
| Contract Docs | 初级 | 理解合约设计意图（ABI 只告诉你签名，文档告诉你为什么） |
| ABI / Event | 中级 | 编码函数调用、解析事件（能调用 ≠ 应该调用） |
| Transaction History | 中级 | 判断过去做过什么（证据必须能回到链上） |
| Explorer Context | 初级 | 可视化链上证据（给 link 比说"成功"更可靠） |
| Indexing Context | 中级 | 整理事件为可查询数据（落后 500 区块 ≠ 当前事实） |
| Citation | 初级 | 每句话附上链上证据（没 citation 只算观点） |

### 一个好的链感知上下文包

```
├── 用户目标
├── 当前 chain id 和网络名称
├── 用户地址和余额
├── 相关合约地址、ABI、文档和风险提示
├── 最近交易和授权
├── 索引数据更新时间
└── 每条关键结论的 citation
```

---

## 三、Web3 Tool Use — 手脚

> 把 RPC、合约读取、交易生成、钱包确认变成 Agent 可调用的工具。

### 核心原则

> **读写分离** — 查询和交易必须是不同工具、不同权限。

### 工具分级

```
🟢 初级（只读，风险低）
├── RPC Tool      — 读链状态、查余额、估算 gas
├── Contract Read  — 调用合约 view/pure 函数
└── Explorer Tool  — 查交易、合约源码、event

🔴 高级（写入，风险高）
├── Contract Write — 改变链上状态，需模拟+权限+确认
├── Wallet Tool    — 连接账户、请求签名（最敏感！）
└── DeFi Tool      — swap、借贷、授权（直接影响资产）
```

### 权限分层

```
查询余额        → 自动允许
生成交易草稿    → 自动允许
小额白名单支付  → session key 允许
大额转账/授权   → 必须人工确认
任意合约调用    → 默认禁止
```

### 关键：参数结构化

> chain id、contract address、method、args、value、slippage 不能埋在自然语言里。

**反例**：用户说"帮我转点钱给那个合约"→ Agent 猜？
**正例**：工具要求 `{chain: 1, to: "0x...", method: "transfer", args: [...], value: "100"}`

---

## 四、MCP 协议 — 神经系统

> AI 应用的"工具接口标准"：Client 负责和模型交互，Server 负责暴露能力。

### 第一性原理

> 模型不应该直接拥有世界；它应该通过明确协议访问被授权的上下文和工具。

### 架构

```
┌──────────────┐         ┌──────────────┐
│  MCP Client  │◄───────►│  MCP Server  │
│  (IDE/Agent) │  JSON-RPC│  (工具/数据)  │
│              │         │              │
│  ┌────────┐  │         │  ┌────────┐  │
│  │ Model  │  │         │  │ Tools  │  │
│  └────────┘  │         │  │Resources│  │
│              │         │  │Prompts │  │
└──────────────┘         │  └────────┘  │
                         └──────────────┘
```

### 三要素

- **Server**：暴露 resources、tools、prompts（重点是边界：哪些只读、哪些有副作用）
- **Client**：连接模型和 server（重点是让用户知道：连了哪些 server、能调什么工具）
- **Tool Schema**：描述工具名字、参数、返回值（模糊 schema = 模型用错误参数填空）

### 权限模型

| 动作 | 风险 | 授权方式 |
|------|------|---------|
| 读文档 | 低 | 自动允许 |
| 查 issue | 低 | 自动允许 |
| 创建 PR | 中 | 会话授权 |
| 发支付 | 高 | 人工确认 |
| 删文件 | 高 | 默认禁止 |

---

## 五、Cobo Pact — 安全带

> 把上面四者框在安全边界里的实战方案。

### 为什么需要 Pact？

四个模块各管一件事：
- Agent 管"怎么做"
- Chain-aware Context 管"看见什么"
- Web3 Tool Use 管"能调什么"
- MCP 管"怎么接工具"

但没人管"**权限边界**"——这就是 Pact 的位置。

### Pact 四组件

```
┌─────────────────────────────────────┐
│           Pact 协议                  │
├─────────────┬───────────────────────┤
│ 1. Intent   │ Agent 要完成什么目标    │
│ 2. Plan     │ 具体怎么做（步骤）      │
│ 3. Policies │ 红线在哪（风控约束）     │
│ 4. Complete │ 怎样算做完（完成条件）   │
└─────────────┴───────────────────────┘
```

### 可信执行链路

```
submit（提交请求）→ approve（人类审批）→ 强制执行
```

---

## 六、五者如何协作（实战场景）

**场景：用户说"帮我把 100 USDC 从以太坊转到 Arbitrum"**

```
1. Chain-aware Context
   → 读取：以太坊 USDC 余额、Arbitrum gas 价格、桥的合约地址
   → 带 citation：每个数据附上 block number 和 explorer link

2. Agent (Planning)
   → 拆步骤：检查余额 → 选择桥 → 估算费用 → 生成交易 → 等确认

3. MCP
   → 发现可用工具：read_balance, estimate_gas, build_bridge_tx
   → 用 Tool Schema 确保参数正确

4. Web3 Tool Use
   → 只读工具自动执行：查余额、估算 gas
   → 写入工具进入审批：生成交易草稿，等用户确认

5. Pact
   → Intent: 转 100 USDC 到 Arbitrum
   → Plan: 检查余额 → 调用桥合约 → 等待确认
   → Policies: 最大滑点 1%，单次不超过 500 USDC
   → Complete: Arbitrum 上收到 USDC
   → submit → approve → 执行
```

---

## 🤔 开放问题

1. MCP 协议和 Web3 Tool Use 的边界在哪？MCP 管"怎么接"，Web3 Tool Use 管"接什么"？
2. Chain-aware Context 的实时性要求有多高？DeFi 场景可能需要秒级更新
3. 如果 Agent 的 Reflection 判断"这笔交易风险太高"，但用户坚持要执行，怎么办？
4. Multi-Agent 场景下，Chain-aware Context 如何在 Agent 之间共享？
5. Pact 协议目前有哪些项目在实际使用？

---

## 📚 手册原文

- [AI Agent](https://aiweb3.school/zh/handbook/ai/agent/)
- [Chain-aware Context](https://aiweb3.school/zh/handbook/bridge/chain-aware-context/)
- [Web3 Tool Use](https://aiweb3.school/zh/handbook/bridge/web3-tool-use/)
- [MCP](https://aiweb3.school/zh/handbook/ai/mcp/)

---

## ✍️ 学习笔记

这是 Day 9（2026-05-26）Cobo Agentic Wallet 讲座的延伸阅读整理。

**核心感悟**：AI × Web3 不是"AI + 区块链"的简单拼接，而是一套完整的系统设计：
- **看见**（Chain-aware Context）→ **思考**（Agent）→ **行动**（Web3 Tool Use）→ **约束**（Pact）
- 每一层都有自己的安全边界，缺了任何一层都会出问题

这让我想到昨天学的"Agent 记忆系统"——记忆让 Agent 更聪明，但记忆泄露 = 资产泄露。安全和能力永远是跷跷板的两端，而 Pact 这样的协议就是找到平衡点的工具。
