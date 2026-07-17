---
tags: [ai, 基础, day2, day4]
aliases: [prompt engineering, 提示工程]
---
[[2026-05-13]] 
# Prompt 工程

## 一句话解释
通过更清楚地描述目标、上下文、限制和输出格式，让 AI 更稳定地给出你真正想要的结果。

## 核心技巧
- 角色设定：先告诉 AI 它现在扮演谁，比如“你是给初学者讲课的助教”
- Few-shot（给例子）：先给一两个示例，让 AI 知道你想要什么风格和结构
- Chain of Thought（一步步想）：要求它分步骤分析复杂任务，避免一上来就乱答
- 明确输出格式：提前规定字数、表格、项目符号、JSON 等输出形式

## 好 Prompt vs 坏 Prompt

坏的：
> 帮我写个东西

好的：
> 你是一个共学营助教。请用初学者能听懂的语言，解释什么是 tool use，不超过200字，并给一个生活类比。

## 我的 Prompt 练习记录
> 把你写过的10个 Prompt 记在这里

## 在共学营中的用处
- Week 1：用来更高效地理解 LLM、agent、tool use、智能合约这些基础概念
- Week 1：做 Vibe Coding / HermesAgent 实践时，Prompt 直接决定 AI 写出来的代码和步骤质量
- Week 2-3：写 proposal、整理治理提案、总结风险控制思路时，也都离不开好的 Prompt

## 相关概念
- [[02-AI基础/LLM是什么|LLM是什么]]
- [[02-AI基础/AI-Agent|AI-Agent]]

## 参考资料
- DeepLearning.AI "ChatGPT Prompt Engineering for Developers"
