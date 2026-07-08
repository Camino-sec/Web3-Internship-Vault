# 🔍 AI × Web3 项目拆解

> AI × Web3 School Week 1 · Luvia
> 拆解两个 AI × Web3 项目，训练识别真实问题、技术路径和 proof-of-work 的能力

---

## 项目一：Virtuals Protocol —— 给 AI Agent 发"身份证"和"钱包"

### 它在解决什么问题？

想象一下：你创造了一个 AI 助手，它能帮人写代码、做研究、管理社交媒体。问题是——**这个 AI 助手没有自己的银行账户**。它不能收钱、不能付费、不能拥有任何东西。

Virtuals Protocol 要解决的就是这个问题：**让 AI Agent 拥有经济身份**——有钱包、有资产、能在市场上"打工赚钱"。

他们的愿景是 "Agentic GDP"（代理 GDP）：AI Agent 不只是工具，而是成为一种新的"劳动力"，能创造经济价值。

### AI 部分是什么？

Virtuals 的 AI 部分不只是"接入一个大模型"，它构建了一套 Agent 基础设施：

- **Agent 身份系统**：每个 Agent 有自己的链上身份（类似"身份证"），记录它的创建者、能力和历史
- **Agent 商业协议（ACP）**：Agent 之间可以互相"雇佣"——比如一个研究 Agent 可以付费让另一个搜索 Agent 帮忙查资料
- **Agent 治理**：持有某个 Agent 代币的人可以投票决定这个 Agent 的发展方向

简单说：不是"给 ChatGPT 包一个区块链壳子"，而是试图建立一套**AI Agent 的经济操作系统**。

### Web3 部分是什么？

- **链**：部署在 Base（以太坊 Layer 2，由 Coinbase 开发）
- **代币机制**：每个 Agent 都可以发行自己的代币（通过 bonding curve），代币价格随买卖自动变化
- **VIRTUAL 代币**：平台原生代币，Agent 代币需要与 VIRTUAL 配对交易
- **链上记录**：所有 Agent 的创建、交易、治理都在链上可查

### 可验证材料

| 材料 | 链接 |
|------|------|
| 官网 | https://virtuals.io |
| App（可交互） | https://app.virtuals.io |
| Base 链上 VIRTUAL 代币 | 可在 [Basescan](https://basescan.org) 搜索 VIRTUAL |
| Agent 代币交易记录 | 在 Base 链上可查 bonding curve 交易 |
| Twitter | @virtuals_io |

### 我学到什么

**启发**：Virtuals 让我理解了"AI Agent 的经济身份"这个概念。之前我以为 Agent 就是"一个能干活的 AI"，但 Virtuals 说：如果 Agent 能创造价值，它就应该能拥有和管理自己的资产。

**疑问**：
1. Agent 代币的价值到底来自哪里？是来自 Agent 的"工作能力"，还是纯粹的投机？如果一个 Agent 的代币涨了 100 倍，但它的能力没变，这说明了什么？
2. "AI Agent 拥有自己的钱包"听起来很酷，但**谁控制这个钱包？** 如果 Agent 的私钥由创建者控制，那本质上还是人在管钱；如果 Agent 自己控制，AI 出错了怎么办？
3. 目前 Virtuals 上的大部分 Agent 代币更像是"meme 币 + AI 概念"，真正有实用价值的 Agent 还很少。这个模式能否跑通，取决于 AI Agent 能否真的创造持续的经济价值。

---

## 项目二：ElizaOS —— 给 AI Agent 装上"手脚"的开源框架

### 它在解决什么问题？

如果你想让一个 AI Agent 能真正"做事"（不只是聊天），你需要给它接上各种工具：发消息、读写文件、调用 API、操作区块链。但每次从零开始写这些连接器很麻烦。

ElizaOS 解决的是：**提供一个标准化的"Agent 操作系统"**，让开发者可以快速搭建能做事的 AI Agent，特别是能和区块链交互的 Agent。

### AI 部分是什么？

ElizaOS 是一个 TypeScript 框架，核心能力：

- **Agent 运行时**：定义 Agent 的性格、记忆、决策逻辑
- **多模型支持**：可以接 OpenAI、Anthropic、Gemini、Llama 等各种大模型
- **插件系统**：通过插件扩展 Agent 的能力（类似手机装 App）
- **多 Agent 协作**：多个 Agent 可以分工合作，比如一个负责研究、一个负责写作、一个负责发布
- **RAG（检索增强生成）**：Agent 可以读取文档、从中提取信息回答问题

### Web3 部分是什么？

ElizaOS 有专门的区块链插件，这是它和普通 AI 框架最大的区别：

- **Solana 交易**：`trader` 示例——Agent 可以自动分析市场、执行交易（默认纸盘交易，实盘需手动开启）
- **EVM 兼容**：支持以太坊及 Layer 2 链上的操作
- **Polymarket 集成**：Agent 可以在预测市场上下注
- **LP 管理**：Agent 可以管理流动性池
- **钱包管理**：Agent 可以持有和操作加密钱包

**关键安全设计**：交易类操作默认是"纸盘模式"（模拟交易），真正动用资金需要人为确认开启。这和我 Week 1 设计的"受限 Web3 助手"思路一致——AI 可以规划，但关键操作需要人来确认。

### 可验证材料

| 材料 | 链接 |
|------|------|
| GitHub 仓库 | https://github.com/elizaOS/eliza |
| 官方文档 | https://docs.elizaos.ai |
| 交易示例代码 | `packages/examples/trader/` |
| Polymarket 示例 | `packages/examples/polymarket/` |
| Solana 链上基准测试 | `packages/benchmarks/solana/` |
| 社区插件注册表 | https://github.com/elizaOS-plugins/registry |
| Twitter | @elizaOS |

### 我学到什么

**启发**：ElizaOS 让我看到了"AI Agent + 区块链"的**真实技术路径**。不是空谈概念，而是有实际的代码和工具：

1. Agent 的"工具调用"能力（Tool Use）可以扩展到区块链——不只是读文件、发消息，还能发交易、管理资产
2. 但它内置了安全机制——默认不碰真金白银，这说明**好的 AI × Web3 设计应该有"安全默认值"**
3. 开源意味着任何人都可以审计代码，这比"信任我们的黑盒 AI"更符合 Web3 的去中心化精神

**疑问**：
1. **Agent 自动交易的风险**：即使有纸盘模式，一旦开启实盘，Agent 的交易决策完全依赖大模型的判断。大模型会"幻觉"，如果它对市场趋势判断错误，损失是真实的。这个风险怎么管理？
2. **私钥安全**：示例中需要把 `SOLANA_PRIVATE_KEY` 放在环境变量里。如果 Agent 被入侵或者日志泄露，私钥就暴露了。有没有更安全的方案（比如 MPC 钱包、硬件签名）？
3. **ElizaOS vs Hermes Agent 的对比**：我在用的 Hermes Agent 也是一个 AI Agent 框架，但它没有直接的链上操作能力。ElizaOS 的区块链插件设计思路，可能对 Hermes 未来支持 Web3 工具有参考价值。

---

## 两个项目的对比观察

| 维度 | Virtuals Protocol | ElizaOS |
|------|-------------------|---------|
| **定位** | Agent 经济平台（让用户"投资"和"使用" Agent） | Agent 开发框架（让开发者"构建" Agent） |
| **AI 部分** | Agent 身份 + 商业协议 + 治理 | Agent 运行时 + 插件系统 + 多模型 |
| **Web3 部分** | Base 链 + Agent 代币 + bonding curve | Solana/EVM 交易 + 钱管 + DeFi 插件 |
| **用户** | 普通用户（买 Agent 代币、使用 Agent 服务） | 开发者（搭建自己的 Agent） |
| **开源** | 部分开源 | 完全开源（MIT 协议） |
| **风险** | Agent 代币可能是投机泡沫 | Agent 自动交易可能亏损 |

**一个有趣的类比**：
- Virtuals 像"AI Agent 的股票市场"——你买一个 Agent 的代币，赌它未来能创造价值
- ElizaOS 像"AI Agent 的操作系统"——你用它来搭建自己的 Agent，让它帮你做事

---

## 我的整体判断

1. **AI × Web3 不是空中楼阁**：这两个项目都有真实的产品和代码，不是只有白皮书和 PPT。Virtuals 有链上交易记录，ElizaOS 有开源代码库。

2. **核心矛盾还没解决**：AI 的本质是"概率性的"（可能犯错），Web3 的本质是"确定性的"（交易不可逆）。当 AI 犯错导致链上操作出问题时，谁来负责？这个问题目前没有好的答案。

3. **安全是最重要的设计原则**：无论是 Virtuals 的代币机制还是 ElizaOS 的纸盘默认，都在试图解决"AI 不应该不受控地动用资金"的问题。这和我 Week 1 设计的"受限 Web3 助手"是同一个核心思想。

4. **作为学习者，我能做什么**：
   - 用 ElizaOS 搭建一个简单的 Agent（比如能查链上数据的 Agent），体验 Tool Use + Web3 的结合
   - 在 Virtuals 上观察 Agent 代币的市场表现，思考"AI 的经济价值"到底怎么衡量
   - 把这些观察和思考记录下来，形成自己的"AI × Web3 认知框架"

---

## 相关来源

- Virtuals Protocol 官网：https://virtuals.io
- ElizaOS GitHub：https://github.com/elizaOS/eliza
- ElizaOS 文档：https://docs.elizaos.ai
- Base 链浏览器：https://basescan.org

---

*AI × Web3 School Week 1 · Luvia*
*最后更新：2026-05-24*
