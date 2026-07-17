---
tags:
  - week2
  - 共学营
  - x402
  - cobo-caw
  - agent-payment
  - pact
  - architecture
date: 2026-06-01
assignment: Week 2 Module B 进阶
---

# 🔄 x402 + Cobo CAW：Agent 自主支付闭环设计

> **学员**: Luvia
> **共学营**: AI × Web3 School Cohort 0
> **Week 2 Module B 进阶**: x402 Paywall + Cobo CAW Agent 自主支付闭环
> **日期**: 2026-06-01
> **状态**: 架构设计 + 伪代码 + 交互流程（非真实 demo）

---

## 一、这个方案要解决什么问题？

### 🎯 一句话说明

> 让 AI Agent 能**自主地**为 API 服务付费，但付费行为必须在**人类预设的预算、范围和时间窗口内**——不是"自动付款"，而是"受控自动交易"。

### 🤔 为什么需要这个？

想象你雇了一个研究助理帮你做调研：

- **没有 x402 + CAW 的世界**：你要先注册 10 个 API 账号、绑定信用卡、买 API Key，然后把 Key 告诉助理——每一步都是你手动做的
- **有了 x402 + CAW 的世界**：你给助理一张"限额信用卡"（Pact），说"今天最多花 10 美元，只能买数据类 API"。助理自己去调用 API、自己付款、自己拿结果——全程不需要你介入，但钱不会超支

### 📊 核心价值对比

```
传统方式:
  用户 → 注册账号 → 绑卡 → 买 API Key → 给 Agent Key → Agent 调用
  ⏱️ 每个 API 都要走一遍  😩
  🔴 安全风险：Agent 持有 API Key = 无限权限

x402 + CAW 方式:
  用户 → 创建 Pact（预算+规则）→ Agent 自己调用+付款
  ⏱️ 一次设置，Agent 自主执行  ✅
  🟢 安全：Pact 限制了金额、范围、时间
```

---

## 二、四个关键角色

```
┌─────────────────────────────────────────────────────────────────┐
│                        角色一览                                   │
├──────────────┬──────────────────────────────────────────────────┤
│ 🧑 人类主人   │ 设定预算、规则、审批 Pact。最终控制权在人。         │
│ 🤖 AI Agent  │ 发起请求、识别付款、执行支付、获取结果。            │
│ 🏪 服务提供方 │ 提供受 x402 保护的 API 或 AI 推理服务。            │
│ 🏦 Cobo CAW  │ Agent 的钱包，通过 MPC + Pact 执行受控支付。       │
└──────────────┴──────────────────────────────────────────────────┘
```

### 它们的关系就像：

- **人类主人** = 公司老板（制定预算和采购规则）
- **AI Agent** = 采购经理（在规则内自主采购）
- **服务提供方** = 供应商（提供商品，收钱才发货）
- **Cobo CAW** = 公司财务（按规则付款，超预算就拒绝）

---

## 三、完整交互流程（8 步）

### 阶段 1：准备阶段（人类操作，一次性）

```
步骤 1：人类创建 Pact
━━━━━━━━━━━━━━━━━━━

🧑 人类 → Cobo CAW:
   "给这个 Agent 创建一个 Pact：
    - 预算：每天最多 10 USDC
    - 范围：只能调用数据 API（不能转账、不能炒币）
    - 链：Base 主网
    - 时间：本周有效
    - 单笔上限：2 USDC"

🏦 Cobo CAW → 返回 Pact ID: "pact_abc123"
   Agent 现在有了一个"受控钱包" ✅
```

**为什么这一步很重要？**
- 这就像你给员工一张公司信用卡——但你设了限额、限制了消费类别、设了有效期
- Agent **永远不会拿到私钥**——它拿到的是一个"授权令牌"，只能在 Pact 范围内操作

---

### 阶段 2：执行阶段（Agent 自主操作，循环执行）

```
步骤 2：Agent 发起 API 请求
━━━━━━━━━━━━━━━━━━━━━━━

🤖 Agent → 服务提供方:
   GET /api/market-research?query=奶茶店+新街口
   (普通 HTTP 请求，不带任何付款信息)

🏪 服务提供方 → Agent:
   HTTP 402 Payment Required
   Headers:
     PAYMENT-REQUIRED: {
       "schemes": ["exact"],
       "price": "$0.50",
       "network": "eip155:8453",  // Base 主网
       "payTo": "0xServiceProviderAddress",
       "description": "Market research API call"
     }
```

**生活类比**：
- 你走进超市拿了一瓶水，到收银台——收银员说"这瓶水 2 块钱"
- 这个"2 块钱"就是 402 响应：告诉你"要付钱才能拿到东西"

---

```
步骤 3：Agent 识别付款要求
━━━━━━━━━━━━━━━━━━━━━━━

🤖 Agent 内部逻辑:
   1. 解析 402 响应 → 价格 $0.50，Base 链 USDC
   2. 检查 Pact → 预算还剩 $9.50 ✅，单笔限额 $2 ✅
   3. 检查服务方地址 → 在白名单内 ✅
   4. 决定：可以付款 ✅
```

**关键点**：Agent 不是无脑付款！它会先检查 Pact 规则：
- 预算够不够？
- 这笔钱在不在允许范围内？
- 服务方地址是不是白名单里的？

如果任何一条不满足 → Agent **拒绝付款**，返回错误给用户

---

```
步骤 4：Agent 通过 CAW 签名付款
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🤖 Agent → Cobo CAW:
   "请用 pact_abc123 签名这笔支付：
    - 金额：0.50 USDC
    - 收款方：0xServiceProviderAddress
    - 链：Base"

🏦 Cobo CAW 内部:
   1. 检查 Pact 规则 → 符合 ✅
   2. MPC 签名（Agent Key Share + Cobo Key Share）
   3. 广播交易到 Base 链
   4. 返回交易签名（Payment Payload）

🏦 Cobo CAW → Agent:
   PAYMENT-SIGNATURE: {
     "transaction": "0x...",  // 已签名的交易
     "amount": "500000",      // 0.50 USDC (6 decimals)
     "to": "0xServiceProviderAddress"
   }
```

**为什么用 MPC？**
- Agent 的私钥被拆成两半：Agent 持有一半，Cobo 持有一半
- 两半合在一起才能签名——任何一方单独都无法动钱
- 这就像银行保险柜需要两把钥匙🔑🔑，一把在你手里，一把在银行

---

```
步骤 5：Agent 带着付款证明重试请求
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🤖 Agent → 服务提供方:
   GET /api/market-research?query=奶茶店+新街口
   Headers:
     PAYMENT-SIGNATURE: <Base64 编码的付款签名>
```

---

```
步骤 6：服务提供方验证并结算
━━━━━━━━━━━━━━━━━━━━━━━━━━

🏪 服务提供方 → x402 Facilitator:
   "请验证并结算这笔付款"

🏦 x402 Facilitator:
   1. 验证签名有效 ✅
   2. 验证金额匹配 ✅
   3. 在链上执行结算（资金从 CAW 转到服务方）
   4. 返回 SettlementResponse

🏪 服务提供方 → Agent:
   HTTP 200 OK
   Headers:
     PAYMENT-RESPONSE: {
       "status": "settled",
       "transactionHash": "0x...",
       "amount": "500000"
     }
   Body:
     {
       "report": {
         "stores": [...],     // 奶茶店调研数据
         "analysis": "..."
       }
     }
```

---

```
步骤 7：Agent 获取结果
━━━━━━━━━━━━━━━━━━━━━

🤖 Agent 拿到了调研数据 ✅
   记录：花了 0.50 USDC，剩余预算 9.50 USDC
```

---

```
步骤 8：循环或结束
━━━━━━━━━━━━━━━━━

如果还有更多 API 要调 → 回到步骤 2 继续
如果任务完成 → 输出结果 + 消费报告给用户

最终报告:
  "本次调研共调用 8 个 API，总花费 4.00 USDC
   剩余预算 6.00 USDC
   所有交易记录：[链上链接...]"
```

---

## 四、完整流程图

```
🧑 人类                    🤖 Agent                  🏪 服务方              🏦 CAW / 链
  │                          │                         │                      │
  │  1. 创建 Pact            │                         │                      │
  │  (预算$10/天,范围,时间)   │                         │                      │
  │ ──────────────────────→ │                         │                      │
  │                          │                         │                      │
  │                          │  2. GET /api/data       │                      │
  │                          │ ─────────────────────→  │                      │
  │                          │                         │                      │
  │                          │  3. HTTP 402            │                      │
  │                          │     price: $0.50        │                      │
  │                          │ ←───────────────────── │                      │
  │                          │                         │                      │
  │                          │  4. 检查 Pact 规则       │                      │
  │                          │     预算✅ 限额✅ 白名单✅  │                      │
  │                          │                         │                      │
  │                          │  5. 请求签名             │                      │
  │                          │ ────────────────────────────────────────────→ │
  │                          │                         │                      │
  │                          │     MPC 签名             │                      │
  │                          │     Pact 检查 ✅          │                      │
  │                          │ ←──────────────────────────────────────────── │
  │                          │                         │                      │
  │                          │  6. GET /api/data       │                      │
  │                          │     + PAYMENT-SIGNATURE │                      │
  │                          │ ─────────────────────→  │                      │
  │                          │                         │                      │
  │                          │     验证签名 + 结算       │                      │
  │                          │                         │ ──────────────────→  │
  │                          │                         │                      │
  │                          │                         │  链上转账 0.50 USDC   │
  │                          │                         │ ←──────────────────  │
  │                          │                         │                      │
  │                          │  7. HTTP 200 + 数据      │                      │
  │                          │     + PAYMENT-RESPONSE  │                      │
  │                          │ ←───────────────────── │                      │
  │                          │                         │                      │
  │  8. 最终报告              │                         │                      │
  │  "花了$4,剩余$6"          │                         │                      │
  │ ←────────────────────── │                         │                      │
  │                          │                         │                      │
```

---

## 五、伪代码实现

### 5.1 人类：创建 Pact

```python
# 🧑 人类通过 Cobo CAW 创建 Pact
# 这是一次性操作，之后 Agent 就在这个 Pact 范围内自主执行

pact = cobo_caw.create_pact(
    name="数据API采购Pact",
    
    # 💰 预算控制
    budget=Budget(
        per_transaction_max=2.0,      # 单笔最多 2 USDC
        daily_max=10.0,               # 每天最多 10 USDC
        total_max=50.0,               # 总计最多 50 USDC
        currency="USDC"
    ),
    
    # 🔒 范围控制
    scope=Scope(
        allowed_chains=["BASE_ETH"],                   # 只允许 Base 链
        allowed_operations=["x402_payment"],           # 只允许 x402 支付
        destination_whitelist=[                         # 只能付给这些地址
            "0x数据API服务商A",
            "0x数据API服务商B",
            "0xAI推理服务商C"
        ],
        denied_operations=["token_transfer", "swap", "contract_write"]  # 禁止转账/交易
    ),
    
    # ⏰ 时间窗口
    time_window=TimeWindow(
        start="2026-06-01",
        end="2026-06-07",             # 本周有效
        timezone="Asia/Shanghai"
    ),
    
    # 📋 完成条件
    completion=CompletionCondition(
        max_api_calls=50,             # 最多调用 50 次 API
        auto_revoke_on_complete=True  # 完成后自动撤销 Pact
    )
)

print(f"Pact 创建成功: {pact.id}")
print(f"Agent 钱包地址: {pact.agent_wallet_address}")
```

---

### 5.2 Agent：自主支付循环

```python
# 🤖 Agent 的主循环
# 在 Pact 范围内自主调用 API 并付款

import x402_client
from cobo_caw import CAWClient

# 初始化（Agent 不持有私钥，只持有 Pact 授权）
caw = CAWClient(pact_id="pact_abc123")
fetch = x402_client.wrap_fetch_with_payment(caw.get_signer())

# 任务清单
tasks = [
    "GET https://data-api-a.com/research?query=奶茶店",
    "GET https://data-api-b.com/ratings?area=新街口",
    "GET https://ai-api-c.com/analyze?type=competition",
]

results = []
total_spent = 0

for task_url in tasks:
    try:
        # 步骤 1: 发起请求（如果遇到 402，SDK 自动处理付款）
        response = fetch(task_url)
        
        # 步骤 2: 检查是否需要额外确认
        if response.payment:
            # SDK 已经自动完成了步骤 3-6
            cost = response.payment.amount_usd
            total_spent += cost
            print(f"✅ 已付款 {cost} USDC → {task_url}")
            print(f"   交易哈希: {response.payment.tx_hash}")
            print(f"   剩余预算: {10.0 - total_spent} USDC")
        
        # 步骤 3: 获取结果
        results.append(response.json())
        
    except x402.PaymentRejected as e:
        # Pact 检查不通过（超预算/不在白名单等）
        print(f"❌ 付款被 Pact 拒绝: {e.reason}")
        break
    
    except x402.InsufficientFunds as e:
        # 预算用完了
        print(f"❌ 预算不足: 已花 {total_spent} USDC")
        break

# 最终报告
print(f"\n📊 任务完成报告")
print(f"   调用次数: {len(results)}")
print(f"   总花费: {total_spent} USDC")
print(f"   Pact 状态: {caw.get_pact_status()}")
```

---

### 5.3 服务提供方：x402 Paywall

```python
# 🏪 服务提供方的 API 代码（Express.js 风格伪代码）
# 只需要一行代码就能把 API 变成"付费 API"

from x402_express import paymentMiddleware

# 设置 x402 paywall
app.use(paymentMiddleware({
    "GET /api/research": {
        "accepts": [{
            "scheme": "exact",
            "price": "$0.50",                    # 每次调用 0.50 USDC
            "network": "eip155:8453",            # Base 主网
            "payTo": "0xServiceProviderAddress"  # 收款地址
        }],
        "description": "奶茶店竞品调研 API",
        "mimeType": "application/json"
    }
}))

# 你的正常业务代码，完全不需要改
@app.get("/api/research")
def research(query: str):
    data = do_market_research(query)
    return {"report": data}
```

---

## 六、关键接口说明

### 6.1 x402 协议接口

```
┌────────────────────────────────────────────────────────────────┐
│ HTTP 头部（x402 V2 协议）                                       │
├──────────────────┬─────────────────────────────────────────────┤
│ PAYMENT-REQUIRED │ 服务方 → 客户端                              │
│                  │ 包含：价格、网络、收款地址、支持的支付方案       │
├──────────────────┼─────────────────────────────────────────────┤
│ PAYMENT-SIGNATURE│ 客户端 → 服务方                              │
│                  │ 包含：已签名的付款授权                         │
├──────────────────┼─────────────────────────────────────────────┤
│ PAYMENT-RESPONSE │ 服务方 → 客户端                              │
│                  │ 包含：结算状态、交易哈希、金额确认              │
└──────────────────┴─────────────────────────────────────────────┘
```

### 6.2 Cobo CAW 接口

```
┌────────────────────────────────────────────────────────────────┐
│ CAW CLI 命令                                                    │
├────────────────────────────┬───────────────────────────────────┤
│ caw fetch <url>            │ 像 curl 一样发请求，自动处理 x402  │
│ caw tx transfer ...        │ 发起代币转账                      │
│ caw meta tokens            │ 查看支持的代币列表                 │
│ caw pact create            │ 创建新的 Pact                     │
│ caw pact list              │ 查看所有 Pact                     │
│ caw pact status <id>       │ 查看 Pact 执行状态                │
└────────────────────────────┴───────────────────────────────────┘

┌────────────────────────────────────────────────────────────────┐
│ Pact Policy 控制项                                              │
├────────────────────────────┬───────────────────────────────────┤
│ chain_in                   │ 限制可用链（如 BASE_ETH, SOL）     │
│ destination_address_in     │ 白名单收款地址                     │
│ deny_if.amount_usd_gt      │ 单笔金额上限（USD）               │
│ deny_if.usage_limits       │ 滚动 24h / 7d / 30d 消费上限      │
│ allowed_operations         │ 允许的操作类型                     │
│ time_window                │ 生效时间范围                       │
└────────────────────────────┴───────────────────────────────────┘
```

### 6.3 x402 Facilitator 接口

```
┌────────────────────────────────────────────────────────────────┐
│ Facilitator（结算服务）                                         │
├────────────────────────────┬───────────────────────────────────┤
│ 测试网                       │ https://x402.org/facilitator     │
│ 功能                        │ 验证付款签名 + 链上结算            │
│ 支持网络                     │ Base Sepolia, Solana Devnet       │
│ 协议费用                     │ 0（仅链上 gas 费）                │
└────────────────────────────┴───────────────────────────────────┘
```

---

## 七、安全边界与风险控制

### 7.1 Pact 三层防护

```
第 1 层：Pact 规则（Agent 自查）
  ├── Agent 发起付款前，先检查 Pact 是否允许
  ├── 超预算 → 不发起请求
  └── 不在白名单 → 不发起请求

第 2 层：CAW MPC 签名（钱包层）
  ├── Pact 检查不通过 → 拒绝签名
  ├── 金额超过限额 → 拒绝签名
  └── 时间窗口外 → 拒绝签名

第 3 层：链上结算（Facilitator）
  ├── 签名无效 → 结算失败
  ├── 余额不足 → 结算失败
  └── 所有交易可追溯 → 审计
```

### 7.2 风险矩阵

```
┌─────────────────────┬──────────────┬──────────────────────────────┐
│ 风险                 │ 严重程度      │ 缓解措施                      │
├─────────────────────┼──────────────┼──────────────────────────────┤
│ Agent 被 prompt      │ 🔴 高        │ Pact 白名单限制收款地址        │
│ injection 骗去付款   │              │ 不在白名单的地址无法收款        │
├─────────────────────┼──────────────┼──────────────────────────────┤
│ 服务方返回虚假数据   │ 🟡 中        │ x402 有 offer-receipt 扩展     │
│                     │              │ 可要求服务方签名交付承诺        │
├─────────────────────┼──────────────┼──────────────────────────────┤
│ 402 洪水攻击        │ 🟡 中        │ Agent 设定单日调用次数上限      │
│ （无限循环付款）     │              │ Pact completion.max_api_calls  │
├─────────────────────┼──────────────┼──────────────────────────────┤
│ Gas 价格飙升        │ 🟢 低        │ Base 链 gas 费极低             │
│                     │              │ 且 x402 支持 EIP-2612 赞助 gas │
├─────────────────────┼──────────────┼──────────────────────────────┤
│ CAW 服务不可用       │ 🟢 低        │ MPC 设计：即使 Cobo 宕机       │
│                     │              │ 用户仍可通过恢复流程取回资金     │
└─────────────────────┴──────────────┴──────────────────────────────┘
```

### 7.3 审计记录

每一笔交易都有完整的可审计链路：

```
审计链:
  Agent 请求 → 402 响应 → Pact 检查日志 → MPC 签名 → 链上交易 → 结算确认

可追溯要素:
  ├── 谁发起的？     → Agent ID + Pact ID
  ├── 付了多少钱？   → 链上交易金额（不可篡改）
  ├── 付给谁了？     → 链上收款地址（不可篡改）
  ├── 什么时候付的？ → 区块时间戳（不可篡改）
  ├── 为什么付的？   → x402 PAYMENT-REQUIRED 描述
  └── 在什么规则下？ → Pact 策略记录
```

---

## 八、当前无法完成真实 Demo 的原因

### 为什么暂时不能跑通真实交易？

```
技术限制:
  ├── 1. Cobo CAW 需要安装 App + 配对 Agent
  │      → 需要真实设备，当前环境无法模拟
  │
  ├── 2. x402 需要 Base 链上有 USDC
  │      → 测试网需要 faucet 领币，主网需要真实资金
  │
  ├── 3. Agent 侧需要集成 x402 SDK + CAW CLI
  │      → 需要 Node.js/Python 运行环境 + 私钥管理
  │
  └── 4. 服务提供方需要部署 x402 middleware
       → 需要一个可访问的 HTTP 服务器

当前可行的验证方式:
  ├── ✅ 架构设计完整（本文档）
  ├── ✅ 交互流程清晰（8 步流程图）
  ├── ✅ 伪代码可转换为真实代码
  ├── ✅ 接口说明基于官方文档
  └── ⬜ 真实 demo → 需要 Week 3-4 动手实现
```

### Week 3-4 实现路径

```
Week 3 目标:
  1. 安装 Cobo CAW App + 配对 Agent
  2. 领取 Base Sepolia 测试币
  3. 用 caw fetch 调用一个 x402 测试端点
  4. 验证交易在区块浏览器上可见

Week 4 目标:
  1. 部署一个自己的 x402 paywall API
  2. 创建 Pact（预算 + 白名单）
  3. 让 Agent 自主完成 5 次 API 调用 + 付款
  4. 生成完整的消费报告 + 链上审计记录
```

---

## 九、核心洞察 💡

### 为什么 x402 + CAW 是 Agent Commerce 的关键拼图？

```
x402 解决了什么？
  → 让 API 变成"扫码付费"模式，不需要注册账号、绑卡、买 Key
  → Agent 可以像人类扫码一样，按次付费调用任何服务

CAW 解决了什么？
  → 让 Agent "有钱但不能乱花"
  → 通过 Pact 把"自主权"和"控制权"分开

它们合在一起 = Agent 经济的基础设施
  → 服务方可以放心卖（x402 保证收款）
  → 买家可以放心让 Agent 买（Pact 保证不超支）
  → 一切可追溯、可审计、可编程
```

### 一句话总结

> **x402 是"收银台"，CAW 是"限额信用卡"，Pact 是"消费规则"。**
> 三者合一，才让 AI Agent 的"自主交易"从"不可控风险"变成"受控自动化"。

---

## 十、参考资源

- [x402 官方文档](https://docs.x402.org/)
- [x402 GitHub](https://github.com/x402-foundation/x402)
- [Cobo Agentic Wallet](https://www.cobo.com/agentic-wallet)
- [Cobo x402 Recipe](https://www.cobo.com/agentic-wallet/recipes/x402-payment)
- [Cobo Token Transfer Recipe](https://www.cobo.com/agentic-wallet/recipes/usdc-transfer)
- [HTTP 402 规范 (RFC 7231)](https://datatracker.ietf.org/doc/html/rfc7231#section-6.5.2)

---

*Created: 2026-06-01 | AI × Web3 School Cohort 0*
*基于 x402 V2 协议文档 + Cobo CAW 官方 Recipe 整理*