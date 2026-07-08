# 📝 部署最小智能合约：提交记录

> AI × Web3 School Week 1 · Luvia
> 在 Sepolia 测试网部署 SimpleStorage 合约

---

## 一、合约信息

| 项目 | 内容 |
|------|------|
| **合约名称** | SimpleStorage |
| **合约地址** | `0x265e61c8422D9dE1de2C45b3A659619E16C056eD` |
| **部署网络** | Sepolia 测试网 |
| **创建者地址** | `0x7F1a8ddbdECCA0945c392297e97CFe4694A524DB` |
| **Etherscan 链接** | [查看合约](https://sepolia.etherscan.io/address/0x265e61c8422D9dE1de2C45b3A659619E16C056eD) |

---

## 二、合约功能

这个合约只有一个功能：**存储和读取一个数字**。

```solidity
contract SimpleStorage {
    uint256 private storedNumber;  // 存储一个数字
    
    function set(uint256 _number) public {  // 写入数字
        storedNumber = _number;
    }
    
    function get() public view returns (uint256) {  // 读取数字
        return storedNumber;
    }
}
```

---

## 三、操作记录

### 1. 部署合约
- **操作**：在 Remix IDE 中编译并部署到 Sepolia 测试网
- **需要人工确认**：✅ 是（MetaMask 签名 + Gas 费）
- **结果**：成功部署，获得合约地址

### 2. 调用 `set(42)` 写入数字
- **操作**：调用合约的 `set` 函数，写入数字 42
- **需要人工确认**：✅ 是（MetaMask 签名 + Gas 费）
- **交易哈希**：`0x84f9704e6fad45b2839d7d30cab1bf9b55ab581d17f68b06693109aaec3e74fc`
- **Etherscan 链接**：[查看交易](https://sepolia.etherscan.io/tx/0x84f9704e6fad45b2839d7d30cab1bf9b55ab581d17f68b06693109aaec3e74fc)

### 3. 调用 `get()` 读取数字
- **操作**：调用合约的 `get` 函数，读取存储的数字
- **需要人工确认**：❌ 否（只读操作，免费）
- **返回值**：`42`
- **说明**：成功读取到之前写入的数字

---

## 四、写入 vs 读取的区别

| 操作 | 需要签名？ | 需要 Gas？ | 会改变链上状态？ | 有交易哈希？ |
|------|-----------|-----------|----------------|-------------|
| `set(42)` | ✅ 是 | ✅ 是 | ✅ 是 | ✅ 是 |
| `get()` | ❌ 否 | ❌ 否（免费） | ❌ 否 | ❌ 否 |

**核心理解**：
- **写入** = 改变世界状态 = 需要共识 = 需要成本
- **读取** = 查看世界状态 = 不需要共识 = 免费

---

## 五、需要人工确认的步骤

| 步骤 | 需要人工确认？ | 原因 |
|------|--------------|------|
| 编译合约 | ❌ | 只是代码编译，不涉及链上操作 |
| 部署合约 | ✅ | **链上交易，需要签名 + Gas** |
| 调用 `set()` | ✅ | **链上交易，需要签名 + Gas** |
| 调用 `get()` | ❌ | 只读操作，免费 |
| 查看 Etherscan | ❌ | 只是查看公开信息 |

---

## 六、验证结果

| 验证项目 | 验证方法 | 结果 |
|----------|----------|------|
| 合约是否部署成功 | Etherscan 查看合约地址 | ✅ 成功 |
| 写入是否成功 | 调用 `get()` 返回 `42` | ✅ 成功 |
| 交易是否确认 | Etherscan 查看交易状态 | ✅ 已确认 |
| 合约代码是否正确 | Etherscan 查看合约代码 | ✅ 正确 |

---

## 七、学到的知识

1. **智能合约** = 部署在区块链上的自动执行程序
2. **部署** = 把程序放到区块链上，获得一个地址
3. **写入操作** = 改变链上状态 = 需要签名 + Gas
4. **读取操作** = 查看链上状态 = 免费
5. **交易哈希** = 每笔链上操作的唯一标识
6. **区块浏览器** = 查看所有链上操作的工具

---

## 八、代码位置

合约代码已提交到 GitHub：
- [SimpleStorage.sol](https://github.com/Monica06161127/ai-web3-school-cohort-0/blob/master/contracts/SimpleStorage.sol)

---

*提交时间：2026-05-23 · AI × Web3 School Week 1*
