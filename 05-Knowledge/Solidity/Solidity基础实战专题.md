---
created: 2026-07-10
tags: [Solidity, 基础, 专题, 学习笔记]
---

# 🔢 Solidity 基础实战专题

> 从零开始学习 Solidity，记录每个实验的核心概念和踩坑经验

---

## 📋 学习进度

**学习来源**：thinkingsolidity.com — Solidity 智能合约基础实战篇

**已完成实验**：
- ✅ 实验 1.1：Hello World
- ✅ 实验 1.2：pure 与 view
- ✅ 实验 1.3：bool 值
- ✅ 实验 1.4：整型的运算
- ✅ 实验 1.5：底层位运算
- ⏳ 实验 1.6：...

---

## 🎯 核心概念详解

### 1. bool 值（实验 1.3）

**定义**：
- `bool` 类型只有两个值：`true` 和 `false`
- 默认值是 `false`
- 用于条件判断和状态控制

**代码示例**：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract BoolExample {
    bool public isActive = true;
    bool public isCompleted; // 默认 false
    
    function toggleStatus() public {
        isActive = !isActive; // 取反操作
    }
    
    function andOperation() public view returns (bool) {
        return isActive && isCompleted; // 逻辑与
    }
    
    function orOperation() public view returns (bool) {
        return isActive || isCompleted; // 逻辑或
    }
}
```

**关键知识点**：
- `!`：取反（NOT）
- `&&`：逻辑与（AND）
- `||`：逻辑或（OR）
- bool 不能直接参与数值运算，需要转换：`uint(isActive)` → 1 或 0

**生活类比**：
就像开关——要么开（true），要么关（false），没有第三种状态。

**常见错误**：
```solidity
// ❌ 错误：bool 不能直接参与数值运算
uint result = isActive + 1; // 编译错误

// ✅ 正确：先转换类型
uint result = uint(isActive) + 1; // 结果：2
```

---

### 2. 整型的运算（实验 1.4）

**类型分类**：
- `uint`：无符号整数（0, 1, 2, ...）
- `int`：有符号整数（..., -2, -1, 0, 1, 2, ...）
- 不同位数：uint8, uint16, uint32, ..., uint256

**代码示例**：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract IntegerExample {
    uint public a = 10;
    uint public b = 3;
    
    function calculate() public view returns (
        uint sum, 
        uint diff, 
        uint product, 
        uint quotient, 
        uint remainder
    ) {
        sum = a + b;        // 加法：13
        diff = a - b;       // 减法：7
        product = a * b;    // 乘法：30
        quotient = a / b;   // 除法：3（整数除法，截断小数）
        remainder = a % b;  // 取余：1
    }
}
```

**关键知识点**：

#### 整数除法
```solidity
uint result = 10 / 3; // 结果：3，不是 3.333
```
- 整数除法会截断小数部分
- 如果需要小数，要用定点数库（如 ABDKMath64x64）

#### 溢出检查
```solidity
uint8 maxVal = 255; // uint8 最大值
uint8 result = maxVal + 1; // Solidity ^0.8.0 会 revert，不会溢出到 0
```
- Solidity ^0.8.0 默认检查溢出
- 如果需要不检查溢出（节省 Gas），用 `unchecked` 块

#### 除零错误
```solidity
uint result = a / 0; // 编译错误或运行时 revert
```

**生活类比**：
就像分蛋糕——10 块蛋糕分给 3 个人，每人得 3 块，剩 1 块。不能说每人得 3.333 块。

**常见错误**：
```solidity
// ❌ 错误：忘记整数除法会截断
uint price = 100;
uint tax = price * 10 / 100; // 10% 税，结果：10（正确）

// ⚠️ 注意：运算顺序很重要
uint tax2 = price * (10 / 100); // 10 / 100 = 0，结果：0（错误！）
```

---

### 3. 底层位运算（实验 1.5）

**定义**：
- 位运算直接操作二进制位
- 常用操作：AND（&）、OR（|）、XOR（^）、NOT（~）、左移（<<）、右移（>>）

**代码示例**：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract BitwiseExample {
    uint8 a = 5;  // 二进制：0101
    uint8 b = 3;  // 二进制：0011
    
    function bitOperations() public pure returns (
        uint8 andResult,
        uint8 orResult,
        uint8 xorResult,
        uint8 notResult,
        uint8 leftShift,
        uint8 rightShift
    ) {
        andResult = a & b;   // AND：0001 = 1
        orResult = a | b;    // OR：0111 = 7
        xorResult = a ^ b;   // XOR：0110 = 6
        notResult = ~a;      // NOT：11111010 = 250（uint8 取反）
        leftShift = a << 1;  // 左移：1010 = 10
        rightShift = a >> 1; // 右移：0010 = 2
    }
}
```

**位运算详解**：

#### AND（&）
```
  0101 (5)
& 0011 (3)
------
  0001 (1)
```
- 两个都是 1 才是 1
- 类比：两个条件都满足才通过

#### OR（|）
```
  0101 (5)
| 0011 (3)
------
  0111 (7)
```
- 有一个 1 就是 1
- 类比：满足一个条件就通过

#### XOR（^）
```
  0101 (5)
^ 0011 (3)
------
  0110 (6)
```
- 不同才是 1
- 类比：只能选一个选项

#### NOT（~）
```
~ 0101 (5)
------
  1010 (250 for uint8)
```
- 0 变 1，1 变 0
- 类比：取反

#### 左移（<<）
```
0101 << 1
------
1010 (10)
```
- 所有位向左移动，右边补 0
- 相当于乘以 2

#### 右移（>>）
```
0101 >> 1
------
0010 (2)
```
- 所有位向右移动，左边补 0
- 相当于除以 2

**为什么需要位运算**：

#### 1. Gas 优化
```solidity
// 乘法（Gas 较高）
uint result = a * 2;

// 左移（Gas 较低）
uint result = a << 1;
```
- 位运算比算术运算更省 Gas
- 在循环中使用效果更明显

#### 2. 权限管理
```solidity
// 权限常量
uint8 public constant CAN_READ = 1;    // 0001
uint8 public constant CAN_WRITE = 2;   // 0010
uint8 public constant CAN_DELETE = 4;  // 0100
uint8 public constant CAN_ADMIN = 8;   // 1000

// 用户权限
uint8 public userPermission = CAN_READ | CAN_WRITE; // 0011

// 检查权限
function canDelete() public view returns (bool) {
    return (userPermission & CAN_DELETE) != 0; // false
}

function canRead() public view returns (bool) {
    return (userPermission & CAN_READ) != 0; // true
}

// 添加权限
function addPermission(uint8 permission) public {
    userPermission = userPermission | permission;
}

// 移除权限
function removePermission(uint8 permission) public {
    userPermission = userPermission & ~permission;
}
```

#### 3. 状态压缩
```solidity
// 用一个 uint256 存储 256 个布尔值
uint256 public flags;

function setFlag(uint8 index, bool value) public {
    if (value) {
        flags = flags | (1 << index); // 设置第 index 位为 1
    } else {
        flags = flags & ~(1 << index); // 设置第 index 位为 0
    }
}

function getFlag(uint8 index) public view returns (bool) {
    return (flags & (1 << index)) != 0;
}
```

**生活类比**：
- **AND（&）**：两个条件都满足才通过（如：有身份证 AND 有签证）
- **OR（|）**：满足一个条件就通过（如：有学生证 OR 有老年卡）
- **XOR（^）**：只有一个条件满足才通过（如：只能选一个选项）
- **NOT（~）**：取反（如：不是会员 → 是会员）

---

## 🧪 实验代码仓库

**本地路径**：`~/Desktop/monad-contract/`

**已完成实验**：
- `experiment-1-3-bool.sol`
- `experiment-1-4-integer.sol`
- `experiment-1-5-bitwise.sol`

**运行方式**：
```bash
# 编译
npx hardhat compile

# 运行测试
npx hardhat test test/experiment-1-3.test.js
```

---

## 📊 学习进度追踪

### 已掌握概念
- ✅ bool 类型的使用和转换
- ✅ 整型运算的规则（特别是整数除法）
- ✅ 位运算的原理和实际应用
- ✅ 位运算在 Gas 优化和权限管理中的价值

### 待深入学习
- [ ] uint 和 int 的溢出边界具体计算
- [ ] 位运算在实际项目中的更多应用场景
- [ ] 定点数库的使用（ABDKMath64x64）
- [ ] unchecked 块的使用场景

### 实践计划
- [ ] 用位运算实现一个简单的权限系统
- [ ] 测试不同整型的溢出行为
- [ ] 对比位运算和算术运算的 Gas 消耗

---

## 💡 学习心得

### 1. 理解"为什么"比"怎么用"更重要
- 不只是记语法，要理解背后的设计原理
- 为什么 Solidity 要检查溢出？为了安全
- 为什么位运算更省 Gas？因为 CPU 指令不同

### 2. 生活类比是最好的老师
- bool → 开关
- 整数除法 → 分蛋糕
- AND → 两个条件都满足
- OR → 满足一个就行

### 3. 踩坑是最好的学习方式
- 忘记整数除法会截断
- 忘记运算顺序会影响结果
- 忘记 bool 不能直接参与数值运算

---

## 🔗 相关链接

### 学习资源
- [Thinking Solidity](https://www.thinkingsolidity.com/)
- [Solidity 官方文档](https://docs.soliditylang.org/)
- [OpenZeppelin Contracts](https://docs.openzeppelin.com/)

### 工具
- [Remix IDE](https://remix.ethereum.org/)
- [Hardhat](https://hardhat.org/)
- [Foundry](https://book.getfoundry.sh/)

### 参考项目
- [[Harberger-Tax-NFT]] — 使用了位运算的完整项目
- [[AI-Agent安全防护框架专题]] — 安全相关学习

---

*专题创建时间：2026-07-10*
*学习状态：持续进行中*

#Solidity #基础 #专题 #学习笔记