---
created: 2026-07-11
tags: [AI, 求职, 面试, PhD, 职业发展, 参考资料]
category: 职业发展
source: Alisa Wuffles 博客
---

# 📝 PhD 工业界求职指南（Alisa Wuffles 经验分享）

> [!abstract] 一句话总结
> UW NLP 博士毕业求职 Research Scientist 岗位的全过程复盘：11 家公司、57 场面试，涵盖面试类型、准备策略、薪资谈判和心态管理。

---

## 一、作者背景

- 6 年 NLP PhD @ University of Washington
- 求职方向：Research Scientist / Member of Technical Staff
- 研究兴趣：tokenization（最后两年聚焦于此）
- 核心观点：**建立一个专业领域的深度**帮助她在求职中脱颖而出

---

## 二、求职时间线概览

- 面试了 **11 家公司**，共 **57 场面试**
- 另有 **46 场 recruiter 电话** + **16 场 offer 后沟通**
- 大量非正式社交/网络建设对话

> [!tip] 面试安排策略
> - 用几家不太在意的公司**练手**，但注意**精力有限**，别在真正想去的公司面前燃尽
> - 公司的 headcount 和团队招聘状态比你的准备程度更影响时间安排
> - offer deadline 通常有**弹性**，可以谈判延期，但要警惕 "exploding offer"

---

## 三、七种面试类型

### 1. 🖥️ ML Coding（最常见）
- 实现架构、解码策略、传统 ML 算法
- **PyTorch 必须熟练**，偶尔要求纯 numpy（如手写反向传播）

### 2. 💻 General Coding
- 基本就是 LeetCode，偶尔加点 ML 风味
- 基础概念也会出现在 ML coding 面试中

### 3. 🗣️ Technical Discussion（技术讨论）
- 不写代码，但非常技术性
- 两种形式：
  - **深度型**：围绕一个话题讨论实验设计，面试官会追问假设和后续实验
  - **广度型**：快速问答（如"位置编码有哪些方式？""PPO 和 GRPO 的区别？"）

### 4. 🔬 Research Discussion（研究讨论）
- 介绍自己的项目，面试官从这里展开
- 准备要点：为什么选这个方向、有什么 insight、未来看好什么方向
- **根据岗位调整 pitch**，用关键词让面试官快速定位你的相关性

### 5. 🎭 Behavioral（行为面试）
- 经典行为面试 + 偶尔问 AI 安全/社会影响
- **提前准备故事清单**，映射到常见行为面试问题
- ⚠️ 作者第一次行为面试翻车：以为自己"行为没问题"，结果被简单问题问懵

### 6. 📐 Math（数学）
- 从趣味逻辑题到严肃的数学推导
- 复习：**概率论、线性代数、微积分**

### 7. 🎤 Job Talk（求职报告）
- 比学术报告更短，聚焦一个方向
- 作者的 job talk 全部围绕 tokenizer

---

## 四、准备策略

### 核心原则
> "面试准备是你能做的最有价值的事。" —— 和本科考试一样，需要专门花时间学。

### 具体方法

1. **系统学习基础**
   - Stanford CS336: Language Modeling from Scratch（推荐入门）
   - 作者整理了 [LLM notes](https://alisawuffles.github.io/llm-notes/) 和 math notes

2. **深度学习每个概念**
   - 读博客 + 论文
   - 和 ChatGPT/Claude 对话理解
   - **从零实现练习**（特别是 Transformer，面试高频考点）

3. **模拟真实环境**
   - ⚠️ **练习时关闭 AI 辅助**，否则会低估自己的依赖程度

4. **针对每场面试定制准备**
   - 每场面试像一门不同的课，你只有 ~3 天准备期末考
   - 根据 JD、公司方向、recruiter 提示判断考察范围

5. **面试当天**
   - **睡够觉**比临时抱佛脚重要（作者曾因 2 小时睡眠搞砸第一场）
   - 面试后记笔记，方便复盘

### 🌟 学习的意外收获
- 知识面拓宽 → 研究自信心提升
- 不再害怕暴露知识盲区
- **正在进行的项目也变得更好**——能想到以前想不到的技术方案

---

## 五、薪资谈判

### 为什么必须谈判？
- 初始 offer **默认留有谈判空间**
- recruiter 甚至会主动说："我不指望你接第一个 offer"
- 几周的谈判努力 ≈ 可能等于**好几年的薪资差距**

### 谈判技巧
- **找朋友要数据点**——了解市场行情
- 每次打电话前**写好要分享和不分享的内容**
- 预判对方的问题和论点，**准备好逐字稿**
- 和潜在同事/manager 多聊，了解团队文化

---

## 六、心态管理

> [!warning] 真实的情绪代价
> 作者坦言：求职期间压力大、痛苦、生活其他方面几乎停摆。这不是你一个人的问题。

### 常见情绪挑战
- 和同龄人比较的焦虑
- 每个人都对你的去向有意见
- 信息不完整下的重大决策压力
- 小选择（如联系谁）被放大影响

### 最后的话
> PhD 是一段特别的时光——唯一的工作就是有好想法、执行它、作为研究者成长。虽然 industry 的力量很诱人，但也请珍惜 PhD 这段独一无二的时光。

---

## 七、推荐学习资源

- LeetCode 75 / Neetcode Blind 75
- Stanford CS336: Language Modeling from Scratch
- The Illustrated GPT-2
- Self-Attention & Transformers
- Backpropagation
- Introduction to Policy Gradient for LMs
- Lightweight Guide to understanding GRPO and RL principles
- How to Scale Your Model

---

## 八、对 Luvia 的启发 🌱

虽然你不是 PhD，但这篇文章里有些**底层逻辑是通用的：

- **建立专业深度**：不管是 Web3 还是 AI，找到一个你真正感兴趣的点深挖
- **社交网络的力量**：内推 > 海投，在社群里主动交流
- **行为面试要提前准备**：别觉得"我人没问题就不会翻车"
- **学习本身就有 side benefit**：你今天在 Monad Builder Camp 学的东西，可能会在未来某个意想不到的地方派上用场

---

## 相关笔记

- [[2026-07-11|Daily Note 2026-07-11]]

---

#AI #求职 #面试 #PhD #职业发展 #参考
