---
created: 2026-07-08
tags: [fluxa, agent, payment, infra, week1]
related: "[[05-Knowledge/AI×Web3/FluxA-AI原生支付基础设施]]"
---

# FluxA 手写笔记梳理 — Agent 支付概念图谱

> 来源：分享会手写笔记
> 整理：Luvia · 2026-07-08

---

## 一、概念全景图

```
┌─────────────────────────────────────────────────────────┐
│                    Agent 通信层                          │
│                                                         │
│  Agent ←→ Agent    A2A                                  │
│  Agent ←→ Business A2B                                  │
│  Agent ←→ Tool     A2T (API/Skill)                      │
│  Model ←→ Context  MCP (Model Context Protocol)         │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                    支付层                                │
│                                                         │
│  x402 Payment      互联网原生支付（HTTP 402）             │
│  ODP / AEP2        嵌入式授权协议（无需人工确认）         │
│  Intent-Pay        签一次意图，Agent 自动执行            │
│  AgentCard         一次性虚拟卡（用完自毁）              │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                    风控层                                │
│                                                         │
│  Harness Engineering  约束工程，让 Agent 在控制范围内    │
│  AI Wallet 风控引擎   AI 自动审核每笔交易               │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                    合规层                                │
│                                                         │
│  KYC               身份验证                             │
│  EOA 钱包          外部拥有账户                         │
│  ERC-4337          账户抽象                             │
│  ERC-8004          声誉系统（可能的 ERC-864 笔误）       │
│  DSL               领域特定语言                         │
│  法币支付          传统金融对接                         │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 二、关键概念解释

### 1. Agent 通信协议

| 协议 | 全称 | 含义 | 类比 |
|------|------|------|------|
| A2A | Agent to Agent | Agent 之间互相调用 | 两个员工之间互相协作 |
| A2B | Agent to Business | Agent 调用企业服务 | 员工联系供应商 |
| A2T | Agent to Tool | Agent 调用工具/API | 员工使用工具干活 |
| MCP | Model Context Protocol | 模型上下文协议 | 统一的工具接口标准 |

**为什么需要这些协议？**
就像人类社会需要「语言」来沟通，Agent 之间也需要标准协议来协作。没有统一协议，每个 Agent 都说「方言」，没法互操作。

### 2. x402 Payment — 互联网原生支付

**HTTP 状态码 402 = Payment Required**

想象一下：你访问一个网页，服务器返回「402 Payment Required」——意思是「请付钱再访问」。

传统互联网没有这个机制，所以需要 FluxA 这样的基础设施来实现。

**类比**：
- 传统互联网：访问网站 → 免费（靠广告赚钱）
- x402 时代：访问网站 → 直接付钱（微支付，$0.001）

### 3. ODP / AEP2 — 嵌入式授权协议

**ODP = On-Demand Payment（按需支付）**
**AEP2 = Agent Embedded Payment Protocol（Agent 嵌入式支付协议）**

核心思想：**把签名授权直接附在 HTTP 请求头里**，Agent 不需要人工确认就能支付。

```http
POST /v1/search HTTP/1.1
X-AEP2-Mandate: {
    "amount": "0.80",
    "asset": "USDC",
    "payee": "agent_cmo",
    "sig": "0xa5_3f"
}
```

**类比**：
- 传统方式：每次花钱都要打电话问妈妈要钱
- AEP2：妈妈给了你一张「额度卡」，你直接刷卡就行，但有上限

### 4. Intent-Pay — 签意图模式

**传统方式**：每一笔交易都要人工确认（像每次花钱都要问妈妈）
**Intent-Pay**：签一次「意图」，Agent 自动执行（像妈妈说「这个月生活费 2000，你看着花」）

**三步流程**：
1. **Draft** — AI Wallet 自动起草意图（金额、商户、用途）
2. **Sign** — 用户签一次（可以用自然语言：「每个月最多 500 刀，自己判断」）
3. **Harness** — 每笔交易都在意图范围内执行，AI 风控引擎自动审核

**为什么叫 Harness（约束）？**
就像给马套上缰绳——马可以自由奔跑，但方向由骑手控制。Agent 可以自由花钱，但范围由用户定义。

### 5. AgentCard — 一次性虚拟卡

**核心功能**：为 AI Agent 生成一次性 Visa/Mastercard 卡号

**工作流程**：
1. Agent 决定需要买 $450 的机票
2. 向 AI Wallet 请求 AgentCard，锁定 $450
3. FluxA 立即生成一次性虚拟卡号
4. Agent 在商家结账页填写卡号
5. 支付成功 → 卡自毁，未用余额退回钱包

**三个特性**：
- **金额锁定**：不能超支
- **一次性使用**：泄露了也没用
- **无凭证泄露**：真实卡号不进入 Agent 上下文

### 6. Co-wallet — 共管钱包

**传统钱包**：你一个人控制
**Co-wallet**：你和 Agent 共同控制

就像「联合账户」——你设定规则（每月上限、商户白名单），Agent 在规则内自由执行。

### 7. Harness Engineering — 约束工程

**核心思想**：让 Agent 在「安全边界」内自由行动

**类比**：
- 传统方式：每次出门都要问妈妈「我能去哪」
- Harness：妈妈说「小区内随便玩，别出大门」

**三个关键概念**：
- **Why（先决条件）**：什么时候才需要操作
- **Harness（约束）**：操作的边界和限制
- **Approvals（审批）**：什么操作需要人工确认

---

## 三、核心洞察

### 1. T+1, T+3 是 Agent 的「天敌」

传统金融结算需要 1-3 天。但 Agent 需要毫秒级响应——你让它订机票，它等 3 天钱才到账，机票早就没了。

**解决方案**：先授权后结算（AEP2），先服务后付钱。

### 2. 风控 ≠ 限制，风控 = 自由

听起来矛盾？其实不矛盾。

**没有风控**：Agent 每花一分钱都要问你 → 你烦死了
**有风控**：Agent 在你设定的范围内自由行动 → 你放心，Agent 高效

**类比**：公司给员工一张「额度卡」——员工出差不用每次报销，但有上限和白名单。

### 3. 开发者能力开放化

FluxA 的 Monetize 模块让开发者可以：
- 把 API 变成收费服务
- 把 MCP 工具变成变现渠道
- 零代码接入

**类比**：以前开发者写代码是「免费劳动」，现在 FluxA 让开发者的能力可以「自动变现」。

---

## 四、与之前学习的连接

| FluxA 概念 | 之前学过的 | 连接点 |
|-----------|-----------|--------|
| x402 Payment | AI×Web3 School Week 2 模块 B | 同一个协议，现在看到真实产品了 |
| Agent 支付 | AI×Web3 问题地图「支付/商业/结算」 | 问题地图里的场景被 FluxA 实现了 |
| Harness | Agent 安全笔记 | Harness = 约束工程，和安全是同一套逻辑 |
| ERC-4337 | 账户抽象笔记 | Agent 钱包的基础技术 |
| ERC-8004 | AI×Web3 School Week 3 | 声誉系统，Agent 的「信用分」 |
| AgentCard | 一次性虚拟卡 | 让 Agent 能用传统支付方式 |

---

## 五、开放问题

- [ ] x402 和 AEP2 的关系是什么？是同一个协议的不同版本，还是互补的？
- [ ] Co-wallet 的「共同控制」在技术上怎么实现？是多签还是策略引擎？
- [ ] AgentCard 的「用完自毁」是怎么做到的？是合约层面的还是中心化系统的？
- [ ] 法币支付对接怎么处理合规问题？KYC 是必须的吗？
- [ ] Harness Engineering 和 AI×Web3 School 学的「Agent 安全」是同一个东西吗？

---

## 六、术语表

| 术语 | 全称 | 含义 |
|------|------|------|
| A2A | Agent to Agent | Agent 间通信协议 |
| A2B | Agent to Business | Agent 调用企业服务 |
| A2T | Agent to Tool | Agent 调用工具/API |
| MCP | Model Context Protocol | 模型上下文协议 |
| x402 | HTTP 402 | 互联网原生支付协议 |
| ODP | On-Demand Payment | 按需支付 |
| AEP2 | Agent Embedded Payment Protocol | Agent 嵌入式支付协议 |
| Harness | Harness Engineering | 约束工程 |
| Co-wallet | Co-managed Wallet | 共管钱包 |
| KYC | Know Your Customer | 身份验证 |
| EOA | Externally Owned Account | 外部拥有账户 |
| ERC-4337 | EIP-4337 | 账户抽象标准 |
| ERC-8004 | EIP-8004 | 声誉系统标准 |
| DSL | Domain Specific Language | 领域特定语言 |

---

*整理时间：2026-07-08*
*来源：FluxA 分享会手写笔记*
