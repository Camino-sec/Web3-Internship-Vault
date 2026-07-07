---
tags: [ai, agent, day4]
aliases: [AI Agent, 智能体]
---

# AI Agent

## 一句话解释
能理解目标、自己拆步骤、按需调用工具并执行任务的 AI，不只是回答问题，而是能把事情往前推进。

## 和普通 Chatbot 的区别
| | Chatbot | AI Agent |
|---|---------|----------|
| 交互方式 | 你问一句，它答一句 | 你给一个目标，它会自己规划并持续推进 |
| 能力 | 主要负责生成文本和回答问题 | 会规划、调用工具、记忆上下文、执行操作 |
| 自主性 | 基本没有，需要人不断指挥 | 有一定自主性，会决定下一步做什么 |

## Agent 的核心能力
- 规划（Planning）：把大任务拆成小步骤，决定先做什么后做什么
- 工具使用（Tool Use）：调用外部工具（搜索、计算器、API、数据库）完成自己做不到的事
- 记忆（Memory）：记住之前的对话和操作，保持上下文连贯
- 行动（Action）：真正去执行操作，不只是"说"而是"做"

## Workflow（工作流） vs Agent
| | Workflow | Agent |
|---|---------|-------|
| 灵活性 | 固定流程，一步一步走 | 自己决定下一步做什么 |
| 类比 | 工厂流水线 | 自由职业者 |
| 适合 | 流程明确的任务 | 需要判断和调整的任务 |

## 生活类比
Chatbot 像一个坐在柜台后的答疑员，你问什么它答什么。Agent 更像一个助理，你说“帮我订明天下午的高铁并整理出行信息”，它会自己查时间、对比选项、整理结果，再回来找你确认关键步骤。

## 相关概念
- [[02-AI基础/LLM是什么|LLM是什么]]
- [[02-AI基础/Prompt工程|Prompt工程]]
- [[02-AI基础/Vibe Coding|Vibe Coding]]
- 共学营Week 1和Week 2都讲 Agent

## 参考资料
- DeepLearning.AI "AI Agentic Design Patterns with AutoGen"
- Matt Wolfe "What are AI Agents?" (YouTube)

## 我的理解程度
- [ ] 能说清 Agent 和 Chatbot 的区别
- [ ] 知道 Agent 的4个核心能力
- [ ] 能举例说明 Agent 的应用场景
