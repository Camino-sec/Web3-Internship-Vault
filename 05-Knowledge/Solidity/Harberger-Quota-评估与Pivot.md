---
created: 2026-07-14
tags: [Solidity, Harberger-Tax, AI-Agent, 机制设计, ERC-8004, Pivot, 项目评估]
project: https://github.com/Camino-sec/harberger-agent-quota
---

# Harberger Agent Quota 项目评估与 Pivot 改造笔记

> 基于对 [Camino-sec/harberger-agent-quota](https://github.com/Camino-sec/harberger-agent-quota) 的评审讨论整理
> 整理日期：2026-07-14

---

## 一、原方向评估结论

判断：将哈伯格税用于"通用 AI Agent 算力/API 配额分配"，方向不成立；但底层机制工程质量合格，值得 pivot 复用。

### 核心问题（为什么不成立）

| 问题 | 说明 |
|---|---|
| 资产属性错配 | 哈伯格税适合"单一不可分、长期持有、供给刚性"的资产（土地、频谱、域名）。API/算力配额是可分、可弹性扩容的服务型资源，行业已有更简单的方案（spot 定价、分层订阅）能达到类似效率，不需要"强制买断"这种剧烈机制 |
| 买断风险性质不同 | 土地被买走可以换地；Agent 优先通道被买走可能导致任务执行中途失败——这是架构级单点失败风险，不是"焦虑"能靠产品补丁解决的 |
| 自报价对 Agent 是额外负担 | 需要 Agent 自己建模博弈策略，与"降低决策延迟"的初衷矛盾 |
| 去中心化程度存疑 | 若底层算力供给仍来自中心化服务商，本质只是"优先级凭证转卖层"，未真正让算力本身市场化定价 |

### 机制层面仍认可的工程细节
- Lazy evaluation 税金公式（先乘后除，精度控制合理）
- Bit-packing 压缩存储（uint128 价格 + uint32 时间戳）
- CEI 模式 + ReentrancyGuard

---

## 二、Pivot 方向建议（三选一）

1. 算力长期租约（GPU 节点独占权，解决 Akash/Render 类网络"占而不用"的闲置治理问题）
2. **Agent 身份/命名权注册表**（ENS 式数字地产）← **已选定此方向**
3. L2 排序器优先权 / MEV 相关权益

三个方向都能复用现有合约核心逻辑（自报价、lazy tax、escrow、荷兰拍清算、bit-packing），改动量远小于推倒重来。

---

## 三、通用机制修复（不论 pivot 到哪个方向都要先修）

### 1. 买断骚扰漏洞（当前最大漏洞）
问题：买断只需匹配自报价，零溢价、零冷却，可被反复低成本骚扰。

修复：**买断溢价 + 最短持有期，两者都加**
```solidity
uint256 public constant BUYOUT_PREMIUM_BPS = 1000; // 10% 溢价，打给原持有者
uint256 public constant MIN_HOLD_PERIOD = 6 hours; // 视场景可调
```

### 2. 荷兰拍卖精确狙击
问题：线性衰减是确定性函数，机器人可提前算好狙击时刻。

修复：改为**离散阶梯衰减**（如每 5 分钟才降价一次）作为低成本方案；根本性修复是改为**批量密封拍卖（commit-reveal + 统一清算）**。

### 3. taxRateBps 无强制上限
```solidity
uint256 public constant MAX_TAX_RATE_BPS = 5000; // 50% 年化上限
// constructor 中 require(_taxRateBps <= MAX_TAX_RATE_BPS)
```
需与 uint32 时间戳 2106 年溢出边界一起写入安全文档，避免审计时被动补答。

### 4. Yield Vault（ERC-4626）系统性风险
- 改为 **opt-in**（每个 tokenId 自选是否接入收益金库），而非默认强制
- 加 `pauseYieldRouting()` 熔断开关，份额净值异常下跌时暂停新资金路由

---

## 四、命名权注册表（Pivot 目标方向）具体改造方案

### 生态背景
- **ERC-8004**（Trustless Agents 标准）已于 2026 年 1 月底正式部署至以太坊主网，由 Ethereum Foundation / Google / Coinbase 背书，已有大量 Agent 完成链上身份注册
- ERC-8004 提供 Identity / Reputation / Validation 三大注册表，每个 Agent 的身份是一个 ERC-721 代币，指向包含名称、能力、服务端点的 "Agent Card" JSON 元数据
- **空档**：ERC-8004 解决"可验证身份 + 可携带声誉"，但没有解决"人类可读、防抢注、按真实价值流转"的命名层问题——这正是 ENS 之于以太坊地址、也是哈伯格税最早验证有效的经典场景（域名）

### 定位
不与 ERC-8004 竞争身份/声誉赛道，而是做 **"ERC-8004 的命名层"**——类比 ENS 之于以太坊地址，把好记的名字通过哈伯格税经济学分配给 Agent，并 resolve 到 ERC-8004 身份注册表。

### 需要新增/修改的模块

| 模块 | 改动内容 |
|---|---|
| 资产模型 | tokenId 从"配额编号"改为 `keccak256(normalizedName)`；新增 `resolvedIdentity` 映射指向 ERC-8004 Identity |
| 名字归一化 | **必须新增**：参考 ENSIP-15 思路，拒绝混合脚本/零宽字符/大小写混淆，防止同形异义字符钓鱼抢注（Agent 经济中人类肉眼更难分辨，风险更高） |
| 注册流程 | **新增 commit-reveal 两阶段注册**，防止热门名字被 mempool 抢跑机器人截胡 |
| 违约保护 | **新增宽限期状态机**：escrow 耗尽后先给 7-14 天不可买断的宽限期，再进入（修复版）荷兰拍卖——比配额场景更宽松，因为名字被拍卖的紧迫性远低于任务中断 |
| 税率参数 | 显著调低（建议年化 1%-3%），体现"名字是长持有资产"而非"高频周转资源"的定位；买断冷却期可相应缩短（2-3 天） |
| 首发保留区 | 对知名品牌/超短名字设置初始英式或荷兰式拍卖，区别于违约清算阶段的荷兰拍（后者才是需要修复精确狙击问题的地方） |

### 代码复用评估
自报价机制、lazy tax 结算数学、escrow 结构、bit-packing、ReentrancyGuard 均可直接复用，**预计 70% 以上现有代码可保留**，新增工作量主要在归一化库、commit-reveal 流程、resolver 指针、宽限期状态机。

---

## 五、建议动手顺序

1. 先加通用修复：买断溢价/冷却、taxRateBps 上限、Yield Vault opt-in（这三项与最终方向无关，现在就能改）
2. 加命名场景安全底线：名字归一化库 + commit-reveal（没有这两项会被安全审计直接打回）
3. 改 tokenId 语义 + resolver 指向 ERC-8004
4. 最后调参数：税率、溢价比例、冷却期、宽限期时长
5. 补测试用例：骚扰性买断应 revert（冷却期内）、taxRateBps 超限部署应 revert、宽限期内不可被拍卖

---

## 待办 / 下一步可请求内容
- [ ] 具体 Solidity 接口草稿（resolver 模式 + commit-reveal 流程）
- [ ] 名字归一化库的具体实现方案对比
- [ ] 与 ERC-8004 Identity Registry 的集成接口设计

---

## 相关笔记
- [[2026-07-11_实验1.6-1.9_学习笔记|Solidity 实验 1.6-1.9]]
- [[2026-07-13|AI 开发 × Web3 方法论]]

#Harberger-Tax #ERC-8004 #Pivot #项目评估 #Solidity
