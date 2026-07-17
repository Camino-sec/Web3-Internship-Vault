---
tags: [开源贡献, Moss, week2, ops, 计划]
date: 2026-07-17
---

# 开源贡献计划 — Moss

## 一、Builder 身份

**方向**：Ops → Content Producer

我是一个零代码背景的金融学大一学生，正在参加 Monad Builder Camp。我的强项不是写代码，而是**把技术翻译成人话**——写笔记、做教程、整理文档，让新手少走弯路。

选择 Moss 作为贡献目标的原因：
1. Moss 的技术文档质量很高，但**面向中文新手的入门材料还缺**
2. 我自己读 Moss README 时踩过坑，这些坑就是 FAQ 的最佳素材
3. Moss 的社区正在成长期（58 stars），贡献者友好，PR 响应快
4. 项目有 Issue #77 明确提出了"需要中文 FAQ"的需求，可以直接对接

## 二、贡献方向

**补充 Documentation + 整理 FAQ + 编写 Tutorial**

具体来说，我要做三件事：

### 产出 1：中文 FAQ 文档（docs/faq-zh.md）

针对 Issue #77 的需求，编写一份面向中文新手的常见问题解答。

**内容规划**：
- Moss 是什么？一句话解释
- 需要什么前置知识？（Node 22+、pnpm、基础命令行）
- 能不花钱试吗？（可以，Moss 只模拟不签名，不需要私钥和资金）
- 支持哪些链？（目前 Monad 主网，chain ID 143）
- 怎么加新协议？（指向 protocol-onboarding.md）
- 和直接用 ethers.js 调合约有什么区别？（Moss 统一了发现、构建、模拟、验证的流程）
- Agent 安全规则是什么意思？（用"银行双签"类比解释）
- Capability、Receipt、Change 这些概念怎么理解？（生活类比）

**为什么选这个**：我自己就是那个"中文新手"，我踩过的坑就是 FAQ 的最佳素材。写 FAQ 不需要会写代码，但需要真正理解项目——这正是我这两周做的事。

### 产出 2：Getting Started 中文新手教程改进建议

阅读现有的 `docs/getting-started.zh-CN.md`，从新手视角提出改进建议：
- 哪些步骤缺少前置说明
- 哪些术语需要额外解释
- 哪些地方容易卡住

以 Issue 或 PR Comment 的形式提交反馈。

### 产出 3：一个生活化类比的 Moss 项目介绍（小红书帖）

用我自己的语言，写一篇面向中文社区的 Moss 项目介绍帖：
- 不用技术术语开头，而是从"AI 帮你操作区块链"的场景切入
- 用生活类比解释核心概念
- 附上自己跑通 Quickstart 的真实体验
- 发布到小红书，扩大 Moss 在中文社区的知名度

## 三、本周目标（7/17 - 7/19）

| 优先级 | 任务 | 产出 | 截止时间 |
|--------|------|------|----------|
| P0 | 完成 FAQ 文档初稿 | `docs/faq-zh.md` | 7/18 |
| P0 | 在 Moss 仓库提交 Issue 或 PR | GitHub Issue/PR | 7/18 |
| P1 | 阅读 getting-started.zh-CN.md 并提改进建议 | GitHub Comment | 7/19 |
| P1 | 写小红书 Moss 介绍帖 | 小红书帖子 | 7/19 |

## 四、预计产出

1. **`docs/faq-zh.md`** — 中文 FAQ 文档（约 1500 字，8-10 个问题）
2. **GitHub Issue/PR** — 提交到 nishuzumi/moss 仓库
3. **getting-started 改进建议** — 以 Issue Comment 形式提交
4. **小红书帖 ×1** — Moss 项目中文介绍

## 五、完成计划

### Day 1（7/17，今天）
- [x] 阅读 Moss README、中文 README、CONTEXT.md、agent-skill.md
- [x] 浏览 Issues 和 PRs，找到 Issue #77
- [x] 制定贡献计划（本文档）
- [ ] Fork Moss 仓库到自己的 GitHub

### Day 2（7/18）
- [ ] 撰写 FAQ 文档初稿（8-10 个问题）
- [ ] Push 到自己的 Fork
- [ ] 提交 PR 到 Moss 主仓库
- [ ] 同时在 Issue #77 下回复，说明自己正在做这件事

### Day 3（7/19）
- [ ] 阅读 getting-started.zh-CN.md，记录新手视角的改进建议
- [ ] 在 Moss 仓库提交改进建议 Issue
- [ ] 写小红书 Moss 介绍帖
- [ ] 整理所有产出链接，提交 WCB

## 六、为什么选这个方向而不是其他？

**不选 Dev Builder 方向**：我还在学 Solidity 实验 1.9，写代码修 Bug 还太早。贡献应该从自己擅长的事情开始，而不是硬挤进不熟悉的领域。

**不选 Research Builder 方向**：我已经做了 Moss 的项目分析（GitHub 探索日志），但纯研究笔记的受众有限。相比之下，FAQ 和教程能直接帮助到其他中文新手，影响力更大。

**选 Ops Builder 方向**：写文档、做教程、整理 FAQ，这些是我这两周一直在做的事情。Obsidian 笔记、小红书帖子、学习打卡——我的"内容产出"能力已经被验证过。现在是把它用在真实开源项目上的好机会。

---

## 参考链接

- Moss 仓库：https://github.com/nishuzumi/moss
- Issue #77（中文 FAQ 需求）：https://github.com/nishuzumi/moss/issues/77
- Moss 中文 README：https://github.com/nishuzumi/moss/blob/main/README.zh-CN.md
- Getting Started 中文版：https://github.com/nishuzumi/moss/blob/main/docs/getting-started.zh-CN.md
