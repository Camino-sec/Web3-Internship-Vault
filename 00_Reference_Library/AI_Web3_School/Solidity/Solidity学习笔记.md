# Solidity 学习笔记

> **学习时间**：2026-07-08  
> **学习方式**：课程视频 + AI 辅助理解 + 实操验证  
> **目标**：掌握 Solidity 基础语法，能读懂和编写简单智能合约

---

## 一、基础概念

### 1.1 关键字速查

| 关键字 | 含义 | 类比 |
|--------|------|------|
| `public` | 公开，外部和内部都能调用 | 公开的店铺 |
| `external` | 只允许外部调用，省 Gas | 只对外营业的窗口 |
| `pure` | 纯函数，不读不写链上状态 | 计算器（只算不管外面） |
| `view` | 只读不写链上状态 | 查看菜单（不点菜） |
| `memory` | 临时存储，函数执行完就销毁 | 便利贴 |
| `storage` | 永久存储在链上 | 书架上的书 |
| `calldata` | 只读临时存储，比 memory 更省 Gas | 只读的快递单 |
| `constant` | 常量，定义后不可修改 | 刻在石头上的字 |
| `payable` | 允许接收 ETH | 收银台 |

### 1.2 数据类型

```solidity
bool public flag = true;           // 布尔值
uint256 public num = 42;           // 无符号整数（0 ~ 2^256-1）
int256 public neg = -10;           // 有符号整数
address public addr = msg.sender;  // 地址（20字节，0x开头）
bytes32 public hash;               // 32字节数据
string public name = "hello";      // 字符串
```

**默认值**：
- `bool` → `false`
- `uint` → `0`
- `address` → `0x0000...0000`
- `bytes32` → `0x0000...0000`

### 1.3 值类型 vs 引用类型

| 类型 | 例子 | 特点 |
|------|------|------|
| **值类型** | `uint`, `address`, `bool`, `uint[5]` | 像小纸条，直接存在栈里，不用指定存储位置 |
| **引用类型** | `string`, `uint[]`, `struct`, `mapping` | 像大文件，必须明确告诉编译器"存在哪里"（memory / storage） |

**简单记**：只要参数是**引用类型**的，就必须写 `memory` / `storage`；其它值类型，直接写类型名。

---

## 二、状态变量与函数

### 2.1 状态变量

```solidity
uint public count;  // 状态变量，存储在链上，永久存在
```

- 约等于 Python 的全局变量，但存储在区块链上
- 区块链不死，就一直存在
- 操作不可逆，修改要 Gas
- 函数内部可以直接修改，不需要 `global` 关键字

### 2.2 函数修饰符

```solidity
// 无修饰符：可以修改状态变量
function set(uint _x) external {
    count = _x;  // ✅ 可以修改
}

// view：只读不写
function get() external view returns (uint) {
    return count;  // ✅ 可以读
}

// pure：不读不写，只能用局部变量和参数
function add(uint a, uint b) external pure returns (uint) {
    return a + b;  // ✅ 只用参数
}
```

**判断流程**：
1. 改状态？→ 无修饰符
2. 不改，但读状态？→ `view`
3. 不改也不读？→ `pure`

### 2.3 权限修饰符

| 修饰符 | 外部调用 | 内部调用 | Gas 消耗 |
|--------|---------|---------|---------|
| `public` | ✅ | ✅ | 较高 |
| `external` | ✅ | ❌ | 较低 |
| `internal` | ❌ | ✅ | 最低 |
| `private` | ❌ | ✅ | 最低 |

**选择建议**：高频外部调用用 `external`，内部逻辑用 `internal`。

---

## 三、错误处理

### 3.1 require（条件满足才继续）

```solidity
require(_i <= 10, "i > 10");
// 满足条件 → 继续执行
// 不满足 → 报错 + 回滚 + 退还剩余 Gas
```

### 3.2 revert（条件满足就报错）

```solidity
if (_i > 10) {
    revert("i > 10");
}
// 满足错误条件 → 报错 + 回滚
// 不满足 → 继续执行
```

### 3.3 assert（内部逻辑校验）

```solidity
assert(num == 123);
// 条件成立 → 继续
// 不成立 → 合约有 bug，强制回滚，不退还 Gas
```

**对比**：
| 方法 | 用途 | Gas 退还 |
|------|------|---------|
| `require` | 校验外部输入 | ✅ 退还剩余 |
| `revert` | 复杂条件判断 | ✅ 退还剩余 |
| `assert` | 内部逻辑断言 | ❌ 全部消耗 |

### 3.4 自定义错误（省 Gas）

```solidity
error MyError(address caller, uint i);

function test(uint _i) public view {
    if (_i > 10) {
        revert MyError(msg.sender, _i);
    }
}
```

好处：更省 Gas、更灵活、更规范。

---

## 四、函数修饰器 modifier

```solidity
contract FunctionModifier {
    bool public paused;
    uint public count;
    
    function setPaused(bool _paused) external {
        paused = _paused;
    }
    
    // 定义修饰器：检查是否暂停
    modifier whenNotPaused {
        require(!paused, "paused");
        _;  // 占位符，函数体插入到这里
    }
    
    // 使用修饰器
    function inc() external whenNotPaused {
        count += 1;
    }
    
    function dec() external whenNotPaused {
        count -= 1;
    }
    
    // 带参数的修饰器
    modifier cap(uint _x) {
        require(_x < 100, "x >= 100");
        _;
    }
    
    // 多个修饰器：从左往右顺序执行
    function incBy(uint _x) external whenNotPaused cap(_x) {
        count += _x;
    }
    
    // 三明治修饰器
    modifier sandwich() {
        count += 10;  // 先执行
        _;            // 函数体
        count *= 2;   // 后执行
    }
    
    function foo() external sandwich {
        count += 1;
    }
}
```

**理解**：modifier 就是"逻辑模板/共享规则"，把重复的判断逻辑封装起来，多个函数共用。

---

## 五、构造函数 constructor

```solidity
contract Constructor {
    address public owner;
    uint public x;
    
    constructor(uint _x) {
        owner = msg.sender;  // 部署时绑定部署者地址
        x = _x;             // 部署时传入初始值
    }
}
```

- 只在 Deploy 时执行一次
- 之后不能修改
- 最常见用途：绑定 `owner` 地址

---

## 六、数组

### 6.1 基础用法

```solidity
contract Array {
    uint[] public nums = [1, 2, 3];      // 动态数组
    uint[3] public numsFixed = [4, 5, 6]; // 固定数组
    
    function examples() external {
        nums.push(4);        // 添加元素
        uint x = nums[1];    // 读取元素
        nums[2] = 777;       // 修改元素
        delete nums[1];      // 删除（变成0，不改变长度）
        nums.pop();          // 弹出最后一个（改变长度）
        uint len = nums.length;  // 获取长度
    }
    
    // 内存中定义数组（必须固定长度）
    function memoryArray() external pure {
        uint[] memory a = new uint[](5);
        a[0] = 123;
        // a.push(456);  // ❌ 内存数组不能 push/pop
    }
    
    // 返回数组全部内容
    function returnArray() external view returns (uint[] memory) {
        return nums;
    }
}
```

### 6.2 删除元素 - 移动法

```solidity
// [1, 2, 3, 4, 5] → remove(2) → [1, 2, 4, 5]
function remove(uint _index) public {
    require(_index < arr.length, "index out of bound");
    for (uint i = _index; i < arr.length - 1; i++) {
        arr[i] = arr[i + 1];  // 右侧元素左移
    }
    arr.pop();  // 弹出最后一个
}
```

### 6.3 删除元素 - 替换法（更省 Gas）

```solidity
// [1, 2, 3, 4] → remove(1) → [1, 4, 3]
// 用最后一个元素替换要删除的元素，然后 pop
function remove(uint _index) public {
    arr[_index] = arr[arr.length - 1];
    arr.pop();
}
```

**注意**：替换法会改变元素顺序。

---

## 七、映射 mapping

```solidity
contract Mapping {
    mapping(address => uint) public balances;  // 地址 → 余额
    mapping(address => mapping(address => bool)) public isFriend;  // 嵌套映射
    
    function examples() external {
        balances[msg.sender] = 123;           // 赋值
        uint bal = balances[msg.sender];      // 读取
        uint bal2 = balances[address(1)];     // 不存在的键返回默认值0
        balances[msg.sender] += 456;          // 累加
        delete balances[msg.sender];          // 重置为0
        isFriend[msg.sender][address(this)] = true;  // 嵌套赋值
    }
}
```

**关键点**：
- mapping 不能遍历（没有 .length，没有 for 循环）
- 不存在的键返回默认值（不是报错）
- `address(this)` 代表当前合约地址

### 解决"映射不能遍历"：列表 + 映射组合

```solidity
contract IterableMapping {
    mapping(address => uint) public balances;
    mapping(address => bool) public inserted;
    address[] public keys;  // 用数组记录所有键
    
    function set(address _key, uint _val) external {
        balances[_key] = _val;
        if (!inserted[_key]) {
            inserted[_key] = true;
            keys.push(_key);
        }
    }
    
    function getSize() external view returns (uint) {
        return keys.length;
    }
}
```

---

## 八、结构体 struct

```solidity
contract Structs {
    struct Car {
        string model;
        uint year;
        address owner;
    }
    
    Car public car;           // 单辆车
    Car[] public cars;        // 车库（多辆车）
    mapping(address => Car[]) public carsByOwner;  // 每个车主的车
    
    function examples() external {
        // 创建实例
        Car memory toyota = Car("Toyota", 1990, msg.sender);
        Car memory lambo = Car({year: 1990, model: "Lambo", owner: msg.sender});
        Car memory tesla;
        tesla.model = "Tesla";
        tesla.year = 2000;
        tesla.owner = msg.sender;
        
        // 添加到车库
        cars.push(toyota);
        cars.push(lambo);
        cars.push(tesla);
        cars.push(Car("Ferrari", 2020, msg.sender));
        
        // 修改（需要 storage）
        Car storage _car = cars[0];
        _car.year = 2026;
        delete _car.owner;  // 清空车主
        
        delete cars[1];  // 清空整辆车
    }
}
```

---

## 九、枚举 enum

```solidity
contract Enum {
    enum Status {
        None,       // 0
        Pending,    // 1
        Shipped,    // 2
        Completed,  // 3
        Rejected,   // 4
        Canceled    // 5
    }
    
    Status public status;
    
    function set(Status _status) external {
        status = _status;
    }
    
    function ship() external {
        status = Status.Shipped;  // 固定修改
    }
    
    function reset() external {
        delete status;  // 回归默认值（None）
    }
}
```

**理解**：枚举是"固定选项的集合"，只能用定义好的值。

---

## 十、继承

### 10.1 基础继承

```solidity
contract A {
    function foo() public pure virtual returns (string memory) {
        return "A";
    }
    function bar() public pure returns (string memory) {
        return "A";
    }
}

contract B is A {
    function foo() public pure override returns (string memory) {
        return "B";
    }
    // bar() 被完整继承，不需要重写
}
```

- `virtual`：允许被重写
- `override`：声明这是重写

### 10.2 多线继承

```solidity
contract X {
    function foo() public pure virtual returns (string memory) { return "X"; }
}

contract Y is X {
    function foo() public pure virtual override returns (string memory) { return "Y"; }
}

contract Z is X, Y {
    function foo() public pure override(X, Y) returns (string memory) { return "Z"; }
}
```

**注意**：继承顺序很重要，`Z is X, Y` 中 X 要写在前面。

### 10.3 构造函数继承

```solidity
contract S {
    string public name;
    constructor(string memory _name) { name = _name; }
}

contract T {
    string public text;
    constructor(string memory _text) { text = _text; }
}

// 方式1：直接在继承时指定
contract U is S("s"), T("t") {}

// 方式2：在构造函数中传参
contract V is S, T {
    constructor(string memory _name, string memory _text) S(_name) T(_text) {}
}
```

**执行顺序**：按继承时的顺序执行构造函数。

---

## 十一、事件 event

```solidity
contract Event {
    event Log(string message, uint val);
    event IndexedLog(address indexed sender, uint val);  // indexed 可被索引过滤
    event Message(address indexed _from, address indexed _to, string message);
    
    function example() external {
        emit Log("off", 1234);
        emit IndexedLog(msg.sender, 789);
    }
    
    function sendMessage(address _to, string calldata message) external {
        emit Message(msg.sender, _to, message);
    }
}
```

**关键点**：
- 事件存储在链上日志中，比状态变量更省 Gas
- `indexed` 参数可被链下程序过滤（最多 3 个）
- `emit` 触发事件

---

## 十二、Gas 优化技巧

### 12.1 数据类型选择

```solidity
uint8 age;      // 0~255，够用就行
uint16 height;  // 0~65535
// EVM 以 256 位为一个存储单位
// uint128 可以在一个单位里塞两个数，省空间
```

### 12.2 循环优化

```solidity
// ❌ i++（先返回再加）
for (uint i = 0; i < 10; i++) { ... }

// ✅ ++i（先加再返回，更省 Gas）
for (uint i = 0; i < 10; ++i) { ... }
```

### 12.3 calldata vs memory

```solidity
// calldata：只读，更省 Gas
function set(string calldata _text) external { ... }

// memory：可修改，Gas 较高
function set2(string memory _text) external { ... }
```

**实测差距**：calldata 比 memory 省约 40% Gas。

### 12.4 常量优化

```solidity
address public constant OWNER = 0x123...;  // 读取 Gas: 373
address public owner = 0x123...;           // 读取 Gas: 2485
```

---

## 十三、安全：重入攻击

### 攻击原理

```
攻击者存钱 → 调用取款 → 合约检查余额（>0）→ 合约转账 → 攻击者回调 → 再次取款（余额还没清零）→ 重复...
```

### 漏洞代码

```solidity
function withdraw() external {
    uint256 bal = balances[msg.sender];
    require(bal > 0, "No Balance");
    (bool sent, ) = msg.sender.call{value: bal}("");  // 危险！外部调用
    require(sent, "Failed to send Ether");
    balances[msg.sender] = 0;  // 太晚了！攻击者已经回调了
}
```

### 修复方法

```solidity
function withdraw() external {
    uint256 bal = balances[msg.sender];
    require(bal > 0, "No Balance");
    balances[msg.sender] = 0;  // ✅ 先改状态
    (bool sent, ) = msg.sender.call{value: bal}("");  // 再转账
    require(sent, "Failed to send Ether");
}
```

**原则**：先修改状态，再进行外部调用。

---

## 十四、memory / storage / calldata 总结

### 判断流程

```
变量是【合约体中的状态变量】 → storage（默认）
变量是【函数参数】 → memory 或 calldata（只读用 calldata，可修改用 memory）
变量是【函数内局部变量】 →
    引用合约体中的状态变量 → storage
    全新创建 → memory
```

### 实战对比

| 场景 | 存储方式 | 原因 |
|------|---------|------|
| 状态变量 | storage | 永久存储在链上 |
| 函数参数（只读） | calldata | 最省 Gas |
| 函数参数（需修改） | memory | 临时存储 |
| 局部变量（引用状态） | storage | 直接修改链上数据 |
| 局部变量（新建） | memory | 临时使用 |

---

## 十五、加工厂类比

```
函数 = 流水线
参数 = 原材料
函数体 = 生产工序
返回值 = 成品

多条流水线组合 = 一个生产车间
一个成品可能需要多道工序 = 函数返回值传给另一个函数当参数
没有返回值 = 已经是最终成品
```

---

## 十六、写函数三步法

1. **明确目标**：是"存、读、改"中的哪一种？
2. **确定参数**：需要哪些参数来"定位数据"和"传递数据"？
3. **套用模板**：按模板写核心逻辑，注意 calldata / memory / view

### 万能模板

**存（新增）**：
```solidity
// 数组：容器名.push(结构体实例)
// mapping：容器名[键] = 值
```

**读（查询）**：
```solidity
// view 修饰符，不消耗 Gas
// 定位符.字段名
```

**改（更新）**：
```solidity
// 定位符.字段名 = 新值
// 布尔切换：定位符.字段名 = !定位符.字段名
```

---

*学习时间：2026-07-08*
*笔记来源：课程视频 + AI 辅助 + 实操验证*
