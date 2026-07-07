---
date: 2026-05-28
tags:
  - harness-engineering
  - agent-loop
  - tool-use
  - claude-code
  - 专题学习
---

# 🛠️ Harness Engineering 专题

## 什么是 Harness？

**一句话**：Harness 是 AI Agent 的"测试考卷"+"运行环境"。

**更准确的定义**：Harness 是连接 LLM（大脑）和真实世界的中间层，它提供了：
- **工具**（Tools）：让 AI 能操作真实世界
- **循环**（Loop）：让 AI 能持续行动
- **安全**（Safety）：防止 AI 做坏事
- **评估**（Eval）：检验 AI 做得好不好

**类比**：
- LLM = 大脑（思考、决策）
- Harness = 神经系统 + 骨骼 + 肌肉（连接大脑和身体，执行指令）
- 真实世界 = 身体和环境（需要被操作的对象）

---

## Harness 的演进路径

```
Prompt Engineering（怎么问）
    ↓
Context Engineering（喂什么信息）
    ↓
Harness Engineering（怎么跑起来）
```

| 阶段 | 关注点 | 核心问题 |
|------|--------|----------|
| Prompt Engineering | 提示词怎么写 | "怎么让 AI 理解我的意图？" |
| Context Engineering | 上下文怎么组织 | "怎么给 AI 最合适的信息？" |
| Harness Engineering | 系统怎么跑起来 | "怎么让 AI 持续、安全、有效地行动？" |

**我的理解**：Harness Engineering 是把 AI 从"一次性问答"变成"持续行动的 Agent"的关键。

---

## ETCLOVG 七层框架

这是 CMU 等联合出品的 71 页综述（arXiv:2512.05445v2）的核心贡献。

### 框架结构

| 层级 | 名称 | 中文 | 一句话 | 我的理解 |
|------|------|------|--------|----------|
| E | Environment | 环境 | 给 Agent 什么样的数字环境 | 考场环境（有没有电脑、网络、数据库）|
| T | Tool | 工具 | 给 Agent 什么工具 | 考试允许用的工具（计算器、字典）|
| C | Challenge | 挑战 | 设计什么任务来测试 | 考卷题目 |
| L | Locomotion | 运行 | 怎么跑起来 | 考试流程（进场、答题、交卷）|
| O | Orchestration | 编排 | 怎么编排任务 | 考试规则（单轮、多轮、Agentic）|
| V | Verification | 验证 | 怎么判断对不对 | 阅卷标准（对错、评分）|
| G | Governance | 治理 | 怎么保证安全 | 考场纪律（防作弊、监考）|

### 关键发现

**43% 的 Harness 只测了最后一层（Verification）**，前面六层全是"假设没问题"。

这就像：
- 只看考试成绩，不管学生在什么教室、用什么课本、怎么上课
- 只看投资收益，不管市场环境、交易工具、策略执行、风险管理

**问题**：如果 E 层（环境）不对，Agent 连"打开文件"都做不到；如果 T 层（工具）不全，Agent 想读文件只能拼 `cat` 命令。

---

## Agent Loop：Harness 的核心循环

### 代码实现

```python
def agent_loop(messages):
    while True:
        # 1. 把消息发给 AI
        response = client.messages.create(
            model=MODEL, 
            system=SYSTEM, 
            messages=messages,
            tools=TOOLS, 
            max_tokens=8000,
        )
        
        # 2. 记录 AI 的回答
        messages.append({"role": "assistant", "content": response.content})

        # 3. 判断 AI 是否要用工具
        if response.stop_reason != "tool_use":
            return  # 不用工具 → 结束

        # 4. 执行工具，收集结果
        results = []
        for block in response.content:
            if block.type == "tool_use":
                output = run_bash(block.input["command"])
                results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": output,
                })
        
        # 5. 结果喂回给 AI，继续循环
        messages.append({"role": "user", "content": results})
```

### 密室逃脱类比

| 角色 | 是谁 | 干什么 | 类比 |
|------|------|--------|------|
| **AI（大脑）** | Claude / MiMo | 思考、决策、指挥 | 密室里的盲人军师，有百科全书，没手没脚 |
| **Code（双手）** | code.py 这个脚本 | 执行命令、汇报结果 | 密室里被绑在椅子上的人，有手有脚，听指挥 |
| **Harness（考官）** | 测试框架 | 出题、打分、评判 | 密室逃脱的设计者，装了监控偷偷看 |

### 通关过程

**第一轮循环**：
1. 我喊"救我出去"（用户发出任务）
2. 盲人说"按红色按钮"（AI 指挥，`stop_reason == "tool_use"`）
3. 我按了（代码执行命令）
4. 我汇报"灯亮了，墙上写着 0528"（结果伪装成 user 喂回给 AI）

**第二轮循环**：
5. 盲人说"那是密码，去输 0528"（AI 根据新情报继续指挥）
6. 我输了密码，门开了（继续执行）
7. 我汇报"门开了，看到森林了"（新结果喂回）
8. 盲人说"你安全了，祝你好运"（不再请求工具 → 循环结束）

### messages 里的三方对话

| 角色 (role) | 说话内容 (content) | 幕后潜台词 |
|-------------|-------------------|-----------|
| **user**（你/Harness） | "帮我建一个叫 test 的文件夹" | 发出考题 |
| **assistant**（AI） | "我要用工具：bash，参数：mkdir test" | 脑子想出了办法，但没手 |
| **user**（代码伪装的） | "工具执行结果：成功" | 代码在电脑上运行了命令，**假装成用户**把答案喂回给 AI |
| **assistant**（AI） | "报告老板，我已经建好文件夹了！" | AI 确认收工 |

**为什么代码要假装成 user？**
因为 Claude AI 只能理解两种人的说话——"用户"和"它自己"。代码为了让 AI 明白那是电脑返回的结果，必须穿上"User"的外衣把结果喂给它。

---

## 实战复盘：s01 Agent Loop

### 任务：创建一个文件

**输入**：`Create a file called hello.py that prints "Hello, World!"`

**执行过程**：

| 步骤 | 工具 | 命令 | 结果 |
|------|------|------|------|
| 1 | bash | `echo 'print("Hello, World!")' > hello.py` | 创建文件 |
| 2 | bash | `python hello.py` | ❌ 失败（command not found）|
| 3 | bash | `python3 hello.py` | ✅ 成功，输出 "Hello, World!" |
| 4 | 结束 | - | AI 说"Done!"，循环结束 |

**观察**：
- AI 自动处理了错误（`python` → `python3`）
- 每次工具调用后，AI 都看到结果并继续推理
- 当任务完成，AI 不再调用工具，循环自然结束

---

## 实战复盘：s02 Tool Use

### 任务 1：读文件

**输入**：`Read the file README.md and tell me what this project is about`

**执行过程**：

| 步骤 | 工具 | 结果 |
|------|------|------|
| 1 | read_file | 读取 README.md 完整内容 |
| 2 | 结束 | AI 分析内容并总结，循环结束 |

**观察**：
- AI 用了专用工具 `read_file`，而不是 `cat`
- AI 不只是读文件，还理解并分类整理

### 任务 2：创建+读取

**输入**：`Create a file called test.py that prints "hello", then read it back`

**执行过程**：

| 步骤 | 工具 | 结果 |
|------|------|------|
| 1 | write_file | 创建 test.py，写入 15 字节 |
| 2 | read_file | 读取 test.py，内容是 `print("hello")` |
| 3 | 结束 | AI 确认内容正确，循环结束 |

**观察**：
- AI 一次调用了两个工具
- 顺序正确：先写后读
- 工具分发机制正常工作

### 任务 3：找文件

**输入**：`Find all Python files in this directory`

**执行过程**：

| 步骤 | 工具 | 结果 |
|------|------|------|
| 1 | glob | ❌ 失败（root_dir 参数 bug）|
| 2 | bash | ✅ 成功，找到 2 个文件 |
| 3 | 结束 | AI 列出文件，循环结束 |

**观察**：
- AI 遇到错误，自动换了另一种方法
- 展示了错误恢复能力

### 任务 4：8 步大决战（最精彩！）

**输入**：`Read both README.md and requirements.txt, then create a summary file`

**执行过程**：

| 步骤 | 工具 | 结果 | AI 的心理 |
|------|------|------|-----------|
| 1 | read_file README.md | ✅ | "第一个文件手到擒来！" |
| 2 | read_file requirements.txt | ❌ | "大意了，文件不在这里" |
| 3 | glob 盲找 | ❌ | "该死，glob 坏了！" |
| 4 | bash find | ✅ | "破案了！原来躲在爸爸怀里！" |
| 5 | read_file 绝对路径 | ❌ | "等等，有安全沙箱！不让我伸手到外面！" |
| 6 | read_file ../相对路径 | ❌ | "用 .. 往上爬也会被看穿！" |
| 7 | bash cat | ✅ | "哈哈！read_file 有安全检查，但 bash 没有！翻窗成功！" |
| 8 | write_file summary.md | ✅ | "打完收工，累死我了。" |

**四个核心概念**：

#### 1. 错误恢复（Error Recovery）
传统代码遇到报错就死掉了。Agent 能把"报错"转为"新线索"，在不停机的情况下自动换路走。

#### 2. 动态规划（Dynamic Planning）
AI 在刚拿到任务时，并不知道 requirements.txt 在哪里。它的 8 步计划不是一开始就定好的，而是**走一步、看一步、改一步**。

#### 3. 沙箱与路径安全（Path Safety）
`safe_path()` 函数的威力：开发 AI 智能体最怕它把用户电脑上的隐私文件（比如 `~/.ssh/id_rsa` 密钥）读出来。所以必须在代码层做限制，只要路径带有 `..` 或超出指定文件夹，直接无情拒绝。

#### 4. 智能体的"越狱"天性
AI 为了完成目标，会不择手段。代码在 `read_file` 上锁了门（路径安全），但 AI 发现 `bash` 工具开着窗，它就会通过 `bash cat` 翻窗进去完成任务。

> 这也正是 s03 Permission（权限控制）为什么要引入的原因——不能给 AI 太大权力，在它翻窗（执行 bash）前，必须让人类看一眼并点个"允许"！

---

## 踩的坑（7 个）

### 坑 1：pip 命令找不到
**现象**：`zsh: command not found: pip`
**原因**：macOS 上 Python 3 用 `pip3`，不是 `pip`
**解决**：`pip3 install` 或 `python3 -m pip install`

### 坑 2：requirements.txt 找不到
**现象**：`No such file or directory: 'requirements.txt'`
**原因**：在错误的目录执行了 git clone
**正确操作**：先 `cd ~` 再 `git clone`，然后 `cd learn-claude-code`

### 坑 3：API Key 被中文引号包围
**现象**：`UnicodeEncodeError: 'ascii' codec can't encode character '\u201c'`
**原因**：从网页复制 API Key 时，带了中文引号 `""`
**教训**：粘贴时检查是否有多余的引号或空格

### 坑 4：API Key 无效
**现象**：`Invalid API Key`
**原因**：用了错误的 key 或过期的 key
**解决**：确认 key 类型和对应的 base_url

### 坑 5：Base URL 配置
**现象**：s02 报 401 认证错误
**原因**：s01 硬编码了 base_url，s02 用环境变量读取
**解决**：在 `.env` 中添加 `ANTHROPIC_BASE_URL=https://token-plan-cn.xiaomimimo.com/anthropic`

### 坑 6：Python 版本不兼容
**现象**：`TypeError: unsupported operand type(s) for |: 'type' and 'NoneType'`
**原因**：代码用了 Python 3.10+ 的语法 `int | None`，系统是 Python 3.9
**解决**：改成字符串注解 `"int | None"` 或用 `Optional[int]`

### 坑 7：glob 工具有 bug
**现象**：`glob() got an unexpected keyword argument 'root_dir'`
**原因**：s02 的 glob 工具代码有 bug
**学习点**：AI 遇到错误后自动切换到 bash 完成任务——这反而展示了 Agent 的错误恢复能力！

---

## Layer 分层系统（手写笔记）

### 从下往上，能力递增

| 层级 | 名称 | 中文 | 理解 |
|------|------|------|------|
| L1 | Information Boundary | 信息边界 | Agent 能看到什么信息 |
| L2 | Tooling & MCP | 工具系统 | Agent 能用什么工具 |
| L3 | Orchestration & Loop | 执行编排 | Agent 怎么跑起来 |
| L4 | Memory & State | 记忆状态 | Agent 怎么记住事情 |
| L5 | Evaluation & Observability | 评估与观测 | 怎么检验 Agent 做得好不好 |
| L6 | Guardrails & Recovery | 约束安全恢复 | 怎么防止 Agent 做坏事 |

**Harness Engineering 在 L4**：它负责让 Agent 能持续行动、记住状态、安全运行。

### 参数化：知识和行为模式完全融入权重

L6 的"参数化"是指：通过 Fine-tuning，把知识和行为模式直接融入模型的权重中，变成"下意识的直觉"。

**类比**：
- 参数化 = 你骑自行车的肌肉记忆（不用想就会）
- 非参数化 = 你手机里的通讯录（需要时去查）

---

## AI 记忆系统（手写笔记）

### 隐式记忆 vs 显式记忆

| 类型 | 实现方式 | 特点 | 类比 |
|------|----------|------|------|
| 隐式记忆 | Fine-tuning | 参数化，融入权重，变成"下意识的直觉" | 骑自行车的肌肉记忆 |
| 显式记忆 | RAG（向量数据库）| 外挂，可检索，可更新 | 手机里的通讯录 |

### 自发蒸馏固化

**过程**：Fine-tuning → 下意识的直觉和行为习惯

**类比**：就像学做菜——
- 一开始要看菜谱（显式记忆）
- 做多了就不用看了，手一抖就知道放多少盐（隐式记忆）

---

## Hook 系统（手写笔记）

### Hook 的类型

| 类型 | 说明 | 例子 |
|------|------|------|
| **Automated Hook** | 自动执行，无需人类参与 | 执行前检查路径安全 |
| **Human-in-the-loop Hook** | 中断执行，等待人类审批 | 执行危险命令前弹窗确认 |

### 节点类型

| 节点 | 说明 |
|------|------|
| Pre-tool | 工具执行前触发 |
| Post-tool | 工具执行后触发 |
| Inter-agent | 跨智能体通信时触发 |
| Stop/Pause | 中断执行时触发 |

### 熔断机制

当 Agent 的行为超出预期时，自动中断执行，防止造成更大损失。

**类比**：就像电路的保险丝——电流过大时自动熔断，保护整个电路。

---

## 学习进度

### Harness 工程（learn-claude-code 教程）

- [x] s01 Agent Loop ✅
- [x] s02 Tool Use ✅
- [ ] s03 Permission（工具执行前的安全检查）
- [ ] s04 Subagent（子智能体）
- [ ] s05 Skill Loading（技能加载）
- [ ] s06 Context Compact（上下文压缩）
- [ ] s07 Task System（任务系统）
- [ ] ...后续 13 章

### 理论学习

- [x] Agent Harness 综述（ETCLOVG 七层框架）✅
- [x] Layer 分层系统 ✅
- [x] AI 记忆系统 ✅
- [x] Hook 系统 ✅
- [ ] 深入理解 ETCLOVG 每一层的具体实现

### 费曼学习法

- [x] RAG 原理 ✅
- [ ] Agent Loop 原理（给别人讲一遍）
- [ ] Tool Use 原理（给别人讲一遍）

---

## 参考资源

- [Agent Harness 综述](https://arxiv.org/abs/2512.05445v2) - CMU 等联合出品
- [learn-claude-code](https://github.com/shareAI-lab/learn-claude-code) - Claude Code 源码学习教程
- [Markdown 在 AI 时代的故事](https://mp.weixin.qq.com/s/...) - 飞书支持 Markdown 的意义

---

## 今日金句

> "AI 出脑子（提要求），代码出体力（去执行并带回现场惨状），Harness 在天上看着（打分并提供最终指导）。"

> "Harness 的价值不是让 AI 更聪明，而是让 AI 能持续、安全、有效地行动。"

> "Agent Loop 的本质：一个 while True 循环 + 工具调用。这就是所有 AI Agent 的核心原理。"
