# Workshop 笔记 — Workshop Notes

> Week 3 任务：选择相关 Workshop 笔记
> 项目：EIV (Execution-Integrity Validator)

## 1. VC Workshop（投资人视角看 AI × Web3 项目）

### 核心要点

投资人看 AI × Web3 项目时，最关注的几个维度：

1. **真实问题 vs 伪需求**：不是"AI + Web3"就能讲故事，得有真实的、只有 AI × Web3 才能解决的痛点
2. **护城河**：你的方案有没有"边"？别人抄你难在哪？
3. **可验证性**：链上数据是天然的可验证证据——这是 Web3 相比纯 AI 项目的独特优势
4. **团队执行力**：尤其是技术 lead 能不能把架构落地

### 对 EIV 的启发

- EIV 的"边"在于：**grounded 执行完整性**这个切法 + ERC-8004 上这类 validator 的真空
- 可验证性是我们的天然优势——attestation 上链、PoC 可重现、reputation 可查
- 需要在 pitch 里讲清楚"为什么不是薄 LLM 包装"

## 2. Agent / FluxA Workshop（Agent 架构与工作流）

### 核心要点

1. **Agent 的本质**：不只是调 API，而是有感知→推理→行动→反馈的闭环
2. **长程任务的挑战**：多步推理容易丢失上下文、每步的错误会累积
3. **Grounding 的重要性**：LLM 的输出必须有"锚"——不能纯靠模型判断，得有可验证的证据

### 对 EIV 的启发

- EIV 的 grounding guard 就是这个思路：LLM 可以提出"疑似偏离"，但 FAIL 必须有可重现 PoC
- 多步调查回圈的设计（解析 → 捞链 → 比对 → 起 PoC → 校验 → attest）本身就是"长程任务"
- 这正好对齐 Z.AI 赛道的"Long-Horizon Task"要求

## 3. 两场 Workshop 的交叉洞察

- **VC 看的是"为什么值钱"**，Agent Workshop 讲的是"怎么做到"
- EIV 的故事线：**Agent 经济的信任天花板**（VC 语言）→ **执行完整性验证**（技术语言）→ **ERC-8004 attestation + reputation**（实现语言）
- 三个层次串起来就是完整的 pitch 叙事

## John 的参与情况

- VC workshop：出席 ✓
- Agent/FluxA workshop：出席 ✓
- 两场皆有记录，具体 Q&A 待 John 补充
