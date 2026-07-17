---
tags: [github, 探索日志, week2, Moss, Solidity, Hardhat]
date: 2026-07-17
---

# GitHub 探索日志

> 学习如何阅读一个真实的 GitHub 开源项目，理解 Maintainer 如何组织和管理项目。

---

## 一、三个项目概览

| 项目 | 语言 | Stars | License | 定位 |
|------|------|-------|---------|------|
| **Moss** | TypeScript | 58 | MIT | Monad 链上 Agent-协议交互框架 |
| **Solidity** | C++ | 25,683 | GPL-3.0 | 智能合约编程语言编译器 |
| **Hardhat** | TypeScript | 8,494 | MIT | 以太坊开发环境（编译/部署/测试/调试） |

三个项目代表了 Web3 开发栈的不同层次：
- **Solidity** = 语言层（你写代码用的"语法"）
- **Hardhat** = 工具层（你写完代码后用来编译、测试、部署的"工具箱"）
- **Moss** = 应用层（AI Agent 安全操作链上协议的"翻译官"）

---

## 二、项目目录结构

### Moss（nishuzumi/moss）

```
moss/
├── packages/                    # 核心代码（monorepo）
│   ├── core/                    # 装饰器、Registry、参数契约、Capability 验证
│   ├── simulator/               # debug_traceCall、状态串联、Change 提取
│   ├── erc/                     # ERC-20/721 协议适配器
│   ├── system/                  # Monad 运行时、系统常量
│   ├── protocol-kuru/           # Kuru DEX 适配器
│   ├── mcp-server/              # MCP 传输层（AI Agent 接入点）
│   └── protocols/_template/     # 新协议开发模板
├── docs/                        # 文档
│   ├── getting-started.md       # 新手教程
│   ├── mcp-tools.md             # MCP 工具契约
│   ├── protocol-onboarding.md   # 如何接入新协议
│   ├── agent-skill.md           # Agent 安全规则
│   └── adr/                     # 架构决策记录
├── examples/                    # 示例代码
├── CONTEXT.md                   # 领域词汇表
├── AGENTS.md / CLAUDE.md        # AI Agent 开发指引
└── SECURITY.md                  # 安全声明
```

**特点**：TypeScript monorepo（pnpm workspace），每个协议一个独立 package，模块化清晰。特别的是，它同时提供了 `AGENTS.md` 和 `CLAUDE.md`——专门为 AI Agent 参与开发而设计。

### Solidity（argotorg/solidity）

```
solidity/
├── libsolidity/                 # Solidity 编译器核心（AST、类型检查、代码生成）
├── libyul/                      # Yul 中间语言（优化器、代码生成）
├── libevmasm/                   # EVM 汇编层（字节码生成）
├── liblangutil/                 # 语言工具（源码位置、错误报告）
├── libsmtutil/                  # SMT 求解器（形式化验证）
├── libsolc/                     # C API 接口
├── libsolutil/                  # 通用工具库
├── solc/                        # 命令行编译器入口
├── test/                        # 测试套件
├── docs/                        # 官方文档（.rst 格式，ReadTheDocs）
├── scripts/                     # CI/构建脚本
└── CMakeLists.txt               # C++ 构建系统
```

**特点**：C++ 项目，用 CMake 构建。目录按编译器的分层架构组织——从源码解析（liblangutil）→ AST（libsolidity）→ 中间表示（libyul）→ 字节码（libevmasm），每一层职责分明。文档用 reStructuredText（.rst），托管在 ReadTheDocs。

### Hardhat（NomicFoundation/hardhat）

```
hardhat/
├── packages/                    # monorepo 核心包
│   ├── hardhat/                 # 主包（编译、测试、任务系统）
│   ├── hardhat-verify/          # 合约验证插件
│   ├── hardhat-ethers/          # ethers.js 集成
│   └── ...                      # 其他插件
├── e2e/                         # 端到端测试
├── end-to-end/                  # 集成测试
├── scripts/                     # 构建/发布脚本
├── docs/                        # 文档
└── CLAUDE.md / AGENTS.md        # AI Agent 开发指引
```

**特点**：也是 TypeScript monorepo，插件化架构。和 Moss 一样有 `AGENTS.md`——说明"AI Agent 参与开源开发"正在成为趋势。

---

## 三、README 对比

| 维度 | Moss | Solidity | Hardhat |
|------|------|----------|---------|
| **开头** | 一句话定义 + 警告声明 | 项目名 + 徽章 | Banner 图 + 一句话 |
| **快速上手** | 3 条命令就能跑 | 需要编译 C++，门槛高 | npm install 即可 |
| **架构说明** | 详细的验证流程 | 简短，指向文档 | 简短，指向文档 |
| **贡献指引** | 有 CONTRIBUTING.md + 模板 | 详细的 CODING_STYLE.md | 有 CONTRIBUTING.md |
| **多语言** | 有中文 README ✅ | 无 | 无 |
| **AI Agent 支持** | AGENTS.md + CLAUDE.md | 无 | AGENTS.md |

**我的观察**：Moss 作为新项目（58 stars），README 写得比很多大项目都好——有中文版、有领域词汇表、有安全声明、有架构决策记录。这说明作者非常重视开发者体验，尤其是对中文社区和 AI Agent 开发者的友好度。

---

## 四、Issues & Pull Requests

### Moss 的 Issues 和 PRs

**有趣的 Issue**：
- **#77**：建议添加中文 FAQ 文档，帮助中文新手入门。提出者是 @bky0211，列了 5 个常见问题（什么是 Moss？需要什么前置知识？能不花钱试吗？支持哪些链？怎么加新协议？）
- **#73**：请求添加 Windows 中文部署教程
- **#75**：添加 Monadscan ABI 自动获取功能
- **#74**：添加 Bound Protocol 工厂模式

**有趣的 PR**：
- **#20**：添加繁体中文文档（@antony819）
- **#22**：添加 PancakeSwap V2 适配器（@cointem）
- **#24**：添加 PancakeSwap V3 swap 适配器（@lora-sys）
- **#29**：添加 ABI 自动获取脚本（@lora-sys）

**观察**：Moss 的社区正在快速成长。Issue 和 PR 的参与者不只是核心团队，还有外部开发者在主动贡献协议适配器（PancakeSwap）、文档翻译（繁体中文）、工具改进（ABI 获取）。这是一个健康的开源社区的标志。

### Solidity 的 Issues

- **#16869**：澄清 `--optimize-runs` 参数的含义。很多人以为它控制"优化次数"，实际上它控制的是"部署成本 vs 执行成本"的权衡。这个 Issue 提出改善文档，加上具体使用场景的建议。
- **#16872**：引入 SWAPN/DUPN 操作码支持（EIP-8024）
- **#16849**：性能优化——用 boost 替换 std::unordered_map

### Hardhat 的 Issues

- **#8442**：支持测试名称过滤的取反功能（类似 `--grep` 的反向操作）
- **#8445**：升级 EDR 依赖到 v0.14.2
- **#8435**：为 mocha 和 node 添加 `--grep` 测试

---

## 五、我最感兴趣的一个 Issue

### Moss #77 — 为中文新手添加 FAQ 文档

**为什么感兴趣**：

这个 Issue 直接和我自己的体验相关。我第一次读 Moss 的 README 时，虽然有中文版，但还是有很多地方需要反复看才能理解（比如 Capability 是什么、Receipt 和 Event 的区别、为什么不能签名）。

如果有一个 FAQ，把"什么是 Moss"、"需要什么前置知识"、"能不花钱试吗"这些问题提前回答了，新手的入门体验会好很多。

**更深层的意义**：这个 Issue 说明 Moss 的社区已经开始关注**本地化和新手体验**了。很多开源项目只关注技术功能，忽略了文档的可及性。Moss 作为一个只有 58 stars 的项目，能在早期就重视这个问题，说明 Maintainer 有长远的社区建设意识。

**如果我要参与**：我可以帮着写这个 FAQ——因为我自己就是那个"中文新手"，我踩过的坑就是 FAQ 的最佳素材。

---

## 六、学习收获

### 1. 开源项目的"三件套"结构

不管项目大小，成熟的开源项目都有三个核心文件：
- **README.md**：告诉别人"这是什么、怎么用"
- **CONTRIBUTING.md**：告诉别人"怎么参与贡献"
- **SECURITY.md**：告诉别人"发现安全问题怎么办"

这就像一家店铺——README 是招牌和菜单，CONTRIBUTING 是招聘启事，SECURITY 是报警按钮。

### 2. monorepo 是主流组织方式

Moss 和 Hardhat 都用 pnpm workspace 管理 monorepo。好处是：
- 每个模块（包）可以独立开发、独立版本
- 共享配置和依赖
- 方便新人理解"这个功能在哪个包里"

### 3. AI Agent 正在改变开源开发方式

Moss 和 Hardhat 都提供了 `AGENTS.md`——这是专门为 AI Agent（比如 Claude Code）写的开发指引。这意味着：
- AI 不只是用来"写代码"，还可以参与开源项目的 Issue 讨论、PR Review
- 项目需要为 AI Agent 提供结构化的上下文（领域词汇、架构决策、安全规则）
- 这可能是未来开源协作的新范式

### 4. 文档质量决定社区成长速度

Moss 的文档质量远超其项目规模（58 stars 但有中文 README、领域词汇表、架构决策记录）。这直接反映在社区活跃度上——外部开发者主动贡献协议适配器和文档翻译。好的文档 = 低门槛 = 更多贡献者。

### 5. 项目层次对应不同参与方式

- **Solidity**（语言层）：适合深入研究编译器原理、参与语言设计讨论
- **Hardhat**（工具层）：适合写插件、报 Bug、改进测试
- **Moss**（应用层）：适合贡献协议适配器、写文档、做教程

对于我这种非开发者来说，**Moss 是最容易参与的**——写 FAQ、翻译文档、做新手教程，这些都是我能做的事情。

---

## 参考链接

- Moss：https://github.com/nishuzumi/moss
- Solidity：https://github.com/argotorg/solidity
- Hardhat：https://github.com/NomicFoundation/hardhat
- Moss Issue #77：https://github.com/nishuzumi/moss/issues/77
- Solidity Issue #16869：https://github.com/argotorg/solidity/issues/16869
