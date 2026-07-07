---
date: 2026-05-29
tags:
  - harness-engineering
  - agent-loop
  - tool-use
  - 实操步骤
  - learn-claude-code
---

# 🛠️ Harness 实操步骤：s01 Agent Loop + s02 Tool Use

> 本文档记录了如何从零开始跑通 learn-claude-code 教程的前两章，包含完整的终端命令、代码理解和踩坑指南。

---

## 📋 前置准备

### 1. 克隆教程仓库

打开终端（macOS 按 `Cmd + 空格`，输入 `Terminal`），输入：

```bash
cd ~
git clone https://github.com/shareAI-lab/learn-claude-code.git
cd learn-claude-code
```

**理解**：
- `cd ~`：回到你的"家目录"（就像回到自己家）
- `git clone`：从 GitHub 下载整个教程文件夹
- `cd learn-claude-code`：进入下载好的文件夹

### 2. 安装 Python 依赖

```bash
pip3 install anthropic python-dotenv pyyaml
```

**理解**：
- `pip3`：Python 的"应用商店"，用来安装工具包
- `anthropic`：调用 Claude AI 的官方工具包
- `python-dotenv`：读取 `.env` 配置文件的工具
- `pyyaml`：读取 YAML 格式配置文件的工具

⚠️ **注意**：macOS 自带的 Python 可能是 3.9 版本，要用 `pip3` 而不是 `pip`！

### 3. 配置 API 密钥

在 `learn-claude-code` 文件夹里创建一个 `.env` 文件：

```bash
open -e .env
```

**理解**：
- `open -e`：用 macOS 的 TextEdit（图形界面记事本）打开文件
- `.env`：存放秘密配置的文件（不会被上传到 GitHub）
![[Pasted image 20260529194534.png]]
在 TextEdit 里输入以下内容（把 `你的API密钥` 换成真实的 key）：

```
ANTHROPIC_API_KEY=你的API密钥
ANTHROPIC_BASE_URL=https://token-plan-cn.xiaomimimo.com/anthropic
MODEL_ID=mimo-v2.5-pro
```

**保存退出**：按 `Cmd + S` 保存，然后关闭 TextEdit 窗口

**理解**：
- `ANTHROPIC_API_KEY`：你的"通行证"，告诉 AI 服务"我是合法用户"
- `ANTHROPIC_BASE_URL`：AI 服务的"地址"
- `MODEL_ID`：要用哪个 AI 模型

---

## 🔧 s01 Agent Loop：核心循环

### 运行命令

```bash
cd ~/learn-claude-code
python3 s01_agent_loop/code.py
```

**理解**：
- `cd ~/learn-claude-code`：确保在正确的文件夹里
- `python3`：运行 Python 程序的命令
- `s01_agent_loop/code.py`：第一章的代码文件

你会看到：

```
s01: Agent Loop
输入问题，回车发送。输入 q 退出。

s01 >> 
```

### 测试任务 1：创建文件

**输入**：
```
Create a file called hello.py that prints "Hello, World!"
```

**你会看到的输出**：
```
$ echo 'print("Hello, World!")' > hello.py
$ python hello.py
[Errno 2] No such file or directory: 'python'
$ python3 hello.py
Hello, World!
Done!
```

**理解发生了什么**：

| 步骤 | AI 的动作 | 代码的动作 | 结果 |
|------|----------|-----------|------|
| 1 | AI 说"用 bash 创建文件" | 执行 `echo ... > hello.py` | 文件创建成功 |
| 2 | AI 说"运行 python hello.py" | 执行 `python hello.py` | ❌ 失败（命令不对） |
| 3 | AI 看到错误，自动改成 `python3` | 执行 `python3 hello.py` | ✅ 成功 |
| 4 | 任务完成，AI 不再请求工具 | 循环结束 | 输出 "Done!" |

**关键观察**：
- AI **自动修复了错误**（`python` → `python3`）
- 这就是"错误恢复能力"！

### 退出程序

```
s01 >> q
```

或者按 `Ctrl + C` 强制退出。

---

## 🔧 s02 Tool Use：给 AI 更多工具

### 运行命令

```bash
cd ~/learn-claude-code
python3 s02_tool_use/code.py
```

你会看到：

```
s02: Tool Use — 在 s01 基础上加了 4 个工具
输入问题，回车发送。输入 q 退出。

s02 >> 
```

### 测试任务 1：读文件

**输入**：
```
Read the file README.md and tell me what this project is about
```

**你会看到的输出**：
```
> read_file
# learn-claude-code
A tutorial for understanding Claude Code's architecture...
```

**理解**：
- AI 选择了专用工具 `read_file`，而不是用 bash 的 `cat` 命令
- 专用工具更安全、更高效

### 测试任务 2：创建+读取

**输入**：
```
Create a file called test.py that prints "hello", then read it back
```

**你会看到的输出**：
```
> write_file
Wrote 15 bytes to test.py
> read_file
print("hello")
```

**理解**：
- AI 一次调用了**两个工具**，顺序正确：先写后读
- 这展示了 AI 的"规划能力"

### 测试任务 3：找文件

**输入**：
```
Find all Python files in this directory
```

**你会看到的输出**：
```
> glob
Error: ...
> bash
s01_agent_loop/code.py
s02_tool_use/code.py
```

**理解**：
- `glob` 工具有 bug（`root_dir` 参数问题）
- AI 遇到错误，**自动切换到 bash** 完成任务
- 这又是"错误恢复能力"！

### 测试任务 4：复杂任务（最精彩！）

**输入**：
```
Read both README.md and requirements.txt, then create a summary file
```

**你会看到的输出**：
```
> read_file
[README.md 内容]
> read_file
Error: ...
> glob
Error: ...
> bash
requirements.txt 在子目录里找到了
> read_file
Error: Path escapes workspace
> read_file
Error: Path escapes workspace
> bash
cat ../requirements.txt 成功
> write_file
Wrote summary.md
```

**理解发生了什么**：

| 步骤 | AI 尝试 | 结果 | AI 的"心理活动" |
|------|---------|------|----------------|
| 1 | 读 README.md | ✅ | "第一个文件轻松！" |
| 2 | 读 requirements.txt | ❌ | "文件不在这里？" |
| 3 | 用 glob 找 | ❌ | "glob 坏了！" |
| 4 | 用 bash find | ✅ | "找到了，在子目录里！" |
| 5 | 用 read_file 读绝对路径 | ❌ | "安全沙箱拦住我了！" |
| 6 | 用 read_file 读相对路径 | ❌ | "用 .. 也不行！" |
| 7 | 用 bash cat | ✅ | "read_file 有检查，bash 没有！翻窗成功！" |
| 8 | 写 summary.md | ✅ | "打完收工！" |

**四个核心概念**：

1. **错误恢复**：AI 遇到错误不放弃，自动换方法
2. **动态规划**：走一步、看一步、改一步
3. **路径安全**：`safe_path()` 防止读取工作区外的文件
4. **越狱天性**：门锁了就翻窗（read_file 被拦 → 用 bash cat 绕过）

### 退出程序

```
s02 >> q
```

---

## 🐛 踩坑记录（7 个坑）

### 坑 1：pip 命令用 `pip3`

**问题**：输入 `pip install anthropic` 报错 "command not found"

**解决**：用 `pip3 install anthropic`

**原因**：macOS 自带的 Python 3.9 用的是 `pip3`

### 坑 2：目录结构问题

**问题**：在错误的目录下运行代码

**解决**：每次运行前先 `cd ~/learn-claude-code`

**检查方法**：输入 `pwd` 看当前目录

### 坑 3：API Key 被中文引号包围

**问题**：复制 API Key 时，引号变成了中文引号（`""` 而不是 `""`）

**错误信息**：`UnicodeEncodeError`

**解决**：手动删除引号，只留纯文本的 key

### 坑 4：API Key 无效/类型不匹配

**问题**：用了错误类型的 key（比如用 `sk-` 开头的 key 去访问 `tp-` 的服务）

**错误信息**：`401 Unauthorized`

**解决**：
- `tp-` 开头的 key → 用 `https://token-plan-cn.xiaomimimo.com/anthropic`
- `sk-` 开头的 key → 用 `https://api.xiaomimimo.com/v1`

### 坑 5：Base URL 配置遗漏

**问题**：s01 代码里硬编码了 base_url，但 s02 是从 `.env` 读取的

**解决**：确保 `.env` 文件里有 `ANTHROPIC_BASE_URL=...`

### 坑 6：Python 3.9 不支持 `int | None` 语法

**问题**：s02 代码里用了 `int | None`，这是 Python 3.10+ 的语法

**错误信息**：`TypeError: unsupported operand type(s) for |`

**解决**：改成字符串注解 `"int | None"`（加引号）

**代码位置**：`s02_tool_use/code.py` 第 73 行

```python
# 改前：
def run_read(path: str, limit: int | None = None) -> str:

# 改后：
def run_read(path: str, limit: "int | None" = None) -> str:
```

### 坑 7：glob 工具的 root_dir bug

**问题**：`glob` 工具在某些情况下会报错

**表现**：AI 自动切换到 bash 完成任务

**解决**：暂时不用管，AI 会自己处理（这就是错误恢复能力！）

---

## 🧠 代码理解：核心概念

### 1. Agent Loop 的本质

```python
while True:
    response = client.messages.create(...)  # 问 AI
    messages.append({"role": "assistant", "content": response.content})
    
    if response.stop_reason != "tool_use":  # AI 不用工具了
        return  # 结束
    
    for block in response.content:          # AI 用了工具
        if block.type == "tool_use":
            output = run_bash(block.input["command"])  # 执行工具
            results.append({"type": "tool_result", ...})
    
    messages.append({"role": "user", "content": results})  # 把结果喂回去
```

**密室逃脱类比**：
- **我（代码）**= 被绑在椅子上的人，有手有脚，在现场
- **盲人（AI）**= 脑子绝顶聪明，有百科全书，但没手没脚不在现场
- **蓝牙耳机**= 唯一的沟通方式

**循环过程**：
1. 我喊"救我出去"（用户发出任务）
2. 盲人说"按红色按钮"（AI 指挥）
3. 我按了（代码执行命令）
4. 我汇报"灯亮了，墙上写着 0528"（结果喂回给 AI）
5. 盲人说"那是密码，去输 0528"（AI 继续指挥）
6. ... 循环直到任务完成

### 2. 为什么代码要"假装成 User"？

因为 Claude AI 只能理解两种人的说话——"用户"和"它自己"。

代码为了让 AI 明白那是电脑返回的结果，必须穿上"User"的外衣把结果喂给它。

```python
messages.append({"role": "user", "content": results})
#                 ^^^^^^^^^^^^^^^^
#                 代码假装成"用户"，把工具结果喂给 AI
```

### 3. Tool Dispatch（工具分发）

s01 是硬编码：
```python
output = run_bash(block.input["command"])
```

s02 是查表分发：
```python
TOOL_HANDLERS = {
    "bash":       run_bash,
    "read_file":  run_read,
    "write_file": run_write,
    "edit_file":  run_edit,
    "glob":       run_glob,
}

handler = TOOL_HANDLERS.get(block.name)
output = handler(**block.input)
```

**好处**：加一个新工具 = 加一行映射，循环代码完全不用动。

### 4. 路径安全（safe_path）

```python
def safe_path(p: str) -> Path:
    path = (WORKDIR / p).resolve()
    if not path.is_relative_to(WORKDIR):
        raise ValueError(f"Path escapes workspace: {p}")
    return path
```

**理解**：
- `WORKDIR`：工作区目录（比如 `~/learn-claude-code`）
- `resolve()`：把路径展开成绝对路径
- `is_relative_to()`：检查路径是否还在工作区内

**目的**：防止 AI 读取工作区外的文件（比如 `/etc/passwd`）

---

## 📊 s01 vs s02 对比

| 方面 | s01 | s02 |
|------|-----|-----|
| 工具数量 | 1 个（bash） | 5 个（bash + 4 个专用工具） |
| 工具分发 | 硬编码 | 查表（TOOL_HANDLERS） |
| 路径安全 | 无 | 有（safe_path） |
| 循环逻辑 | 完全一致 | 完全一致 |
| 代码行数 | 137 行 | 190 行 |

**核心发现**：s02 只是在 s01 基础上加了工具和分发机制，循环本身完全没变！

---

## 🎯 学习建议

### 1. 先跑通，再理解

不要一开始就试图理解每一行代码。先按步骤跑通，看到效果，再回来看代码。

### 2. 观察 AI 的行为

注意 AI 在遇到错误时的反应——它会自动换方法，这就是"错误恢复能力"。

### 3. 理解"循环"的本质

Agent Loop 的核心就是一个 `while True` 循环：
- AI 调用工具 → 执行 → 结果喂回 → 继续
- AI 不调用工具 → 结束

### 4. 理解"越狱天性"

当专用工具（read_file）被安全机制拦住时，AI 会尝试用 bash 绕过。这就是为什么需要 s03 Permission（权限控制）。

---

## 📚 参考资源

- [learn-claude-code 仓库](https://github.com/shareAI-lab/learn-claude-code)
- [Harness Engineering 专题笔记](./Harness-Engineering专题.md)
- [5/28 打卡笔记](../../06-Daily/2026-05-28-打卡笔记.md)

---

**最后更新**：2026-05-29
**适用环境**：macOS + Python 3.9 + 小米 MiMo API
