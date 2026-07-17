---
created: 2026-07-08
tags: [sop, 智能合约, 部署, Monad, Testnet, Remix, MetaMask]
---

# 🚀 SOP：智能合约部署到 Monad Testnet

> **目标**：完整走一遍智能合约部署流程，从编译到链上验证
> **适用环境**：macOS + Remix IDE + MetaMask + Monad Testnet
> **预计时间**：30-45 分钟

---

## 📋 前置准备

### 1. 安装 MetaMask 浏览器扩展
- 访问 [metamask.io](https://metamask.io/) 下载安装
- 创建或导入钱包
- 确保有足够的测试网 ETH（Monad Testnet）

### 2. 添加 Monad Testnet 到 MetaMask
- 打开 MetaMask → 设置 → 网络 → 添加网络
- 填写以下信息：
  - 网络名称：`Monad Testnet`
  - RPC URL：`https://testnet-rpc.monad.xyz`
  - 链 ID：`41454`
  - 货币符号：`MON`
  - 区块浏览器：`https://testnet.monadexplorer.com`

### 3. 打开 Remix IDE
- 访问 [remix.ethereum.org](https://remix.ethereum.org/)
- 创建新文件：`SimpleStorage.sol`

---

## 🔧 步骤 1：编写智能合约

在 Remix IDE 中创建 `SimpleStorage.sol` 文件：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 private storedValue;

    // 设置值（需要 Gas）
    function set(uint256 _value) external {
        storedValue = _value;
    }

    // 读取值（免费）
    function get() external view returns (uint256) {
        return storedValue;
    }
}
```

**理解**：
- `set()` 函数：修改链上状态，需要签名 + Gas
- `get()` 函数：只读操作，免费调用
- `external`：只能外部调用，省 Gas
- `view`：只读不写链上状态

---

## 🔧 步骤 2：编译合约

1. 在 Remix 左侧菜单点击 **Solidity Compiler**（第二个图标）
2. 确认编译器版本与合约声明一致（`^0.8.0`）
3. 点击 **Compile SimpleStorage.sol**
4. 看到绿色对勾 ✅ 表示编译成功

**常见错误**：
- 版本不匹配：调整编译器版本
- 语法错误：检查代码拼写和符号

---

## 🔧 步骤 3：部署合约

1. 在 Remix 左侧菜单点击 **Deploy & Run Transactions**（第三个图标）
2. **Environment** 选择 `Injected Provider - MetaMask`
3. MetaMask 弹窗连接请求，点击 **连接**
4. 确认 MetaMask 已切换到 **Monad Testnet**
5. **Contract** 选择 `SimpleStorage`
6. 点击 **Deploy**
7. MetaMask 弹窗确认交易，点击 **确认**
8. 等待交易上链（几秒到几十秒）

**关键观察**：
- 部署合约会产生一个 **交易 hash**（66 字符）
- 同时生成一个 **合约地址**（42 字符）
- 交易 hash = "我做了一件事"（部署合约）
- 合约地址 = "这个合约在哪里"（链上的位置）

---

## 🔧 步骤 4：验证部署成功

### 方法 1：在 Remix 中验证
1. 在 **Deployed Contracts** 区域看到已部署的合约
2. 点击合约地址旁边的复制按钮
3. 点击 `get` 按钮，返回 `0`（初始值）
4. 在 `set` 输入框输入 `42`，点击 `set` 按钮
5. MetaMask 弹窗确认交易
6. 再次点击 `get`，返回 `42`

### 方法 2：使用 RPC 直接查询
```bash
curl -s -X POST https://testnet-rpc.monad.xyz \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"eth_getTransactionReceipt","params":["你的交易hash"],"id":1}'
```

**返回数据解读**：
- `status`: `0x1` 表示成功
- `contractAddress`: 合约地址
- `gasUsed`: 实际消耗的 Gas

### 方法 3：区块浏览器验证
1. 访问 [testnet.monadexplorer.com](https://testnet.monadexplorer.com/)
2. 搜索交易 hash 或合约地址
3. 查看交易详情和合约代码

---

## 🔧 步骤 5：与合约交互

### 写入数据（需要 Gas）
1. 在 Remix 的 `set` 输入框输入值（如 `42`）
2. 点击 `set` 按钮
3. MetaMask 弹窗确认交易
4. 等待交易上链

### 读取数据（免费）
1. 点击 `get` 按钮
2. 直接返回存储的值

**关键洞察**：
- 写入操作（set）就像去工商局变更信息（要交费）
- 读取操作（get）就像在网上查企业信息（免费）

---

## 🐛 常见问题排查

### Q1：MetaMask 连接失败
**原因**：未切换到 Monad Testnet
**解决**：在 MetaMask 中手动切换网络

### Q2：部署交易失败
**原因**：Gas 费不足或网络拥堵
**解决**：
- 增加 Gas Limit
- 稍后重试
- 检查钱包余额

### Q3：合约地址找不到
**原因**：交易未确认或网络延迟
**解决**：
- 在区块浏览器查看交易状态
- 等待几分钟后重试

### Q4：无法调用合约函数
**原因**：ABI 未加载或合约地址错误
**解决**：
- 在 Remix 中重新加载合约
- 检查合约地址是否正确

---

## 💡 最佳实践

1. **先测试后部署**：在测试网充分测试后再部署到主网
2. **备份合约代码**：部署后保存 Solidity 源码
3. **记录合约地址**：部署后立即记录合约地址和交易 hash
4. **验证合约代码**：在区块浏览器上验证合约源码
5. **监控 Gas 费**：选择合适的时间部署以节省 Gas

---

## 🔗 相关概念

- [[智能合约]] - 智能合约基础概念
- [[Gas费]] - Gas 费计算和优化
- [[MetaMask]] - 钱包使用指南
- [[Monad]] - Monad 网络特性

---

> 📝 **最后更新**：2026-07-08
> 🏷️ **适用环境**：macOS + Remix IDE + MetaMask + Monad Testnet
> 👨‍💻 **创建者**：Luvia's AI Assistant