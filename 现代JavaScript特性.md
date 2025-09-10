# 现代 JavaScript 特性（ES2017+）

## 📚 目录

- [ES2017 特性](#es2017-特性)
- [ES2018 特性](#es2018-特性)
- [ES2019 特性](#es2019-特性)
- [ES2020 特性](#es2020-特性)
- [ES2021 特性](#es2021-特性)
- [ES2022 特性](#es2022-特性)
- [ES2023 特性](#es2023-特性)
- [实际应用场景](#实际应用场景)
- [性能对比与最佳实践](#性能对比与最佳实践)
- [面试常考问题](#面试常考问题)

---

## ES2017 特性

### 📝 ES2017 新特性

| 特性                                 | 语法                                    | 示例                            | 用途       |
| ------------------------------------ | --------------------------------------- | ------------------------------- | ---------- |
| **async/await**                      | `async function() {}`                   | `async function fetchData() {}` | 异步编程   |
| **Object.entries**                   | `Object.entries(obj)`                   | `Object.entries({a: 1})`        | 对象遍历   |
| **Object.values**                    | `Object.values(obj)`                    | `Object.values({a: 1})`         | 获取值数组 |
| **String.padStart**                  | `str.padStart(length)`                  | `"5".padStart(3, "0")`          | 字符串填充 |
| **String.padEnd**                    | `str.padEnd(length)`                    | `"5".padEnd(3, "0")`            | 字符串填充 |
| **Object.getOwnPropertyDescriptors** | `Object.getOwnPropertyDescriptors(obj)` | 获取属性描述符                  | 对象操作   |

### 🔧 详细说明

**async/await - 异步编程革命**

> **🎯 解决的问题**：解决了 Promise 链式调用的问题，让异步代码看起来像同步代码。

```javascript
// async/await基础用法
async function fetchUserData(userId) {
  try {
    const user = await fetchUser(userId);
    const permissions = await fetchPermissions(user.id);
    const settings = await fetchSettings(user.id);

    return {
      user,
      permissions,
      settings,
    };
  } catch (error) {
    console.error("获取用户数据失败:", error);
    throw error;
  }
}

// 并发执行
async function fetchUserDataParallel(userId) {
  try {
    const user = await fetchUser(userId);

    // 并发执行
    const [permissions, settings] = await Promise.all([
      fetchPermissions(user.id),
      fetchSettings(user.id),
    ]);

    return { user, permissions, settings };
  } catch (error) {
    console.error("获取用户数据失败:", error);
    throw error;
  }
}

// 实际应用：错误处理
async function safeFetch(url) {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    console.error("Fetch error:", error);
    return null;
  }
}
```

**Object.entries/Object.values - 对象遍历**

```javascript
// Object.entries - 获取键值对
const person = {
  name: "张三",
  age: 25,
  city: "北京",
};

console.log(Object.entries(person));
// [["name", "张三"], ["age", 25], ["city", "北京"]]

// 实际应用：对象遍历
Object.entries(person).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});

// 实际应用：对象转换
function transformObject(obj, transformer) {
  return Object.fromEntries(
    Object.entries(obj).map(([key, value]) => [key, transformer(value)])
  );
}

// Object.values - 获取值数组
console.log(Object.values(person)); // ["张三", 25, "北京"]

// 实际应用：值统计
function getObjectStats(obj) {
  const values = Object.values(obj);
  return {
    count: values.length,
    types: [...new Set(values.map((v) => typeof v))],
    hasNumbers: values.some((v) => typeof v === "number"),
  };
}
```

---

## ES2018 特性

### 📝 ES2018 新特性

| 特性                                 | 语法                                    | 示例                                      | 用途         |
| ------------------------------------ | --------------------------------------- | ----------------------------------------- | ------------ |
| **展开运算符**                       | `...obj`                                | `{...obj1, ...obj2}`                      | 对象展开     |
| **异步迭代器**                       | `for await...of`                        | `for await (const item of asyncIterable)` | 异步迭代     |
| **正则增强**                         | `s`标志                                 | `/pattern/s`                              | 点号匹配换行 |
| **Promise.finally**                  | `promise.finally()`                     | `promise.finally(() => {})`               | 最终处理     |
| **Object.getOwnPropertyDescriptors** | `Object.getOwnPropertyDescriptors(obj)` | 获取属性描述符                            | 对象操作     |

### 🔧 详细说明

**展开运算符 - 对象展开**

> **🎯 解决的问题**：解决了对象合并和克隆的问题，让对象操作更加简洁。

```javascript
// 对象展开
const defaultConfig = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  retries: 3,
};

const userConfig = {
  timeout: 10000,
  debug: true,
};

const finalConfig = {
  ...defaultConfig,
  ...userConfig,
};
// {apiUrl: 'https://api.example.com', timeout: 10000, retries: 3, debug: true}

// 实际应用：状态更新
function updateUser(user, updates) {
  return {
    ...user,
    ...updates,
    updatedAt: new Date(),
  };
}

// 实际应用：数组展开
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// 实际应用：函数参数展开
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

**异步迭代器 - 流式处理**

```javascript
// 异步迭代器
async function* fetchUsers() {
  let page = 1;
  while (true) {
    const response = await fetch(`/api/users?page=${page}`);
    const data = await response.json();

    if (data.users.length === 0) break;

    for (const user of data.users) {
      yield user;
    }

    page++;
  }
}

// 使用异步迭代器
async function processUsers() {
  for await (const user of fetchUsers()) {
    console.log("处理用户:", user.name);
    await processUser(user);
  }
}

// 实际应用：文件流处理
async function* readFileLines(filename) {
  const file = await openFile(filename);

  try {
    while (!file.eof) {
      const line = await file.readLine();
      yield line;
    }
  } finally {
    await file.close();
  }
}
```

---

## ES2019 特性

### 📝 ES2019 新特性

| 特性                   | 语法                          | 示例                             | 用途         |
| ---------------------- | ----------------------------- | -------------------------------- | ------------ |
| **Array.flat**         | `arr.flat(depth)`             | `[1, [2, 3]].flat()`             | 数组扁平化   |
| **Array.flatMap**      | `arr.flatMap(callback)`       | `arr.flatMap(x => [x, x*2])`     | 映射后扁平化 |
| **Object.fromEntries** | `Object.fromEntries(entries)` | `Object.fromEntries([["a", 1]])` | 数组转对象   |
| **String.trimStart**   | `str.trimStart()`             | `"  hello".trimStart()`          | 去除开头空格 |
| **String.trimEnd**     | `str.trimEnd()`               | `"hello  ".trimEnd()`            | 去除结尾空格 |
| **可选 catch 绑定**    | `catch {}`                    | `try {} catch {}`                | 可选错误绑定 |

### 🔧 详细说明

**Array.flat/Array.flatMap - 数组扁平化**

> **🎯 解决的问题**：解决了嵌套数组处理的痛点，让数组操作更加简洁。

```javascript
// Array.flat - 数组扁平化
const nested = [1, [2, 3], [4, [5, 6]]];
console.log(nested.flat()); // [1, 2, 3, 4, [5, 6]]
console.log(nested.flat(2)); // [1, 2, 3, 4, 5, 6]
console.log(nested.flat(Infinity)); // [1, 2, 3, 4, 5, 6]

// 实际应用：处理嵌套数据
function flattenData(data) {
  return data.flat(Infinity);
}

// Array.flatMap - 映射后扁平化
const words = ["hello world", "javascript is awesome"];
const letters = words.flatMap((word) => word.split(" "));
console.log(letters); // ['hello', 'world', 'javascript', 'is', 'awesome']

// 实际应用：处理嵌套数组
const numbers = [1, 2, 3, 4];
const doubled = numbers.flatMap((n) => [n, n * 2]);
console.log(doubled); // [1, 2, 2, 4, 3, 6, 4, 8]
```

**Object.fromEntries - 数组转对象**

```javascript
// Object.fromEntries - 数组转对象
const entries = [
  ["name", "张三"],
  ["age", 25],
  ["city", "北京"],
];

const obj = Object.fromEntries(entries);
console.log(obj); // {name: "张三", age: 25, city: "北京"}

// 实际应用：Map转对象
const map = new Map([
  ["name", "张三"],
  ["age", 25],
]);
const objFromMap = Object.fromEntries(map);

// 实际应用：对象转换
function transformObject(obj, transformer) {
  return Object.fromEntries(
    Object.entries(obj).map(([key, value]) => [key, transformer(value)])
  );
}

// 实际应用：URL参数解析
function parseUrlParams(url) {
  const params = new URLSearchParams(url.split("?")[1]);
  return Object.fromEntries(params);
}
```

---

## ES2020 特性

### 📝 ES2020 新特性

| 特性                   | 语法                   | 示例                           | 用途         |
| ---------------------- | ---------------------- | ------------------------------ | ------------ |
| **可选链**             | `obj?.prop`            | `user?.name`                   | 安全属性访问 |
| **空值合并**           | `a ?? b`               | `name ?? "默认"`               | 空值处理     |
| **BigInt**             | `123n`                 | `BigInt(123)`                  | 大整数       |
| **动态导入**           | `import()`             | `import('./module')`           | 动态模块加载 |
| **globalThis**         | `globalThis`           | `globalThis.setTimeout`        | 全局对象     |
| **Promise.allSettled** | `Promise.allSettled()` | `Promise.allSettled([p1, p2])` | 等待所有完成 |

### 🔧 详细说明

**可选链 - 安全属性访问**

> **🎯 解决的问题**：解决了深层属性访问的问题，避免了因属性不存在而导致的错误。

```javascript
// 可选链基础用法
const user = {
  name: "张三",
  address: {
    city: "北京",
    district: "朝阳区",
  },
};

// 安全访问
console.log(user?.name); // "张三"
console.log(user?.address?.city); // "北京"
console.log(user?.address?.street); // undefined
console.log(user?.address?.street?.number); // undefined

// 实际应用：API响应处理
function processApiResponse(response) {
  const data = response?.data?.users?.[0]?.profile?.name;
  return data || "未知用户";
}

// 实际应用：DOM操作
function getElementText(selector) {
  return document.querySelector(selector)?.textContent || "";
}

// 实际应用：函数调用
function callMethod(obj, methodName, ...args) {
  return obj?.[methodName]?.(...args);
}
```

**空值合并 - 空值处理**

```javascript
// 空值合并基础用法
const name = null ?? "默认名称";
const age = undefined ?? 18;
const city = "" ?? "默认城市"; // ""不是null或undefined，所以返回""

console.log(name); // "默认名称"
console.log(age); // 18
console.log(city); // ""

// 与逻辑或的区别
const value1 = 0 || "默认"; // "默认"
const value2 = 0 ?? "默认"; // 0

const value3 = "" || "默认"; // "默认"
const value4 = "" ?? "默认"; // ""

// 实际应用：配置处理
function createConfig(userConfig) {
  return {
    apiUrl: userConfig.apiUrl ?? "https://api.example.com",
    timeout: userConfig.timeout ?? 5000,
    retries: userConfig.retries ?? 3,
    debug: userConfig.debug ?? false,
  };
}

// 实际应用：函数参数
function greet(name, greeting) {
  const finalGreeting = greeting ?? "Hello";
  const finalName = name ?? "World";
  return `${finalGreeting}, ${finalName}!`;
}
```

**BigInt - 大整数**

```javascript
// BigInt基础用法
const bigNumber = 123456789012345678901234567890n;
const anotherBig = BigInt("123456789012345678901234567890");

console.log(bigNumber); // 123456789012345678901234567890n
console.log(anotherBig); // 123456789012345678901234567890n

// BigInt运算
const a = 123n;
const b = 456n;

console.log(a + b); // 579n
console.log(a * b); // 56088n
console.log(a / b); // 0n (整数除法)

// 实际应用：大数计算
function calculateLargeSum(a, b) {
  return BigInt(a) + BigInt(b);
}

// 实际应用：ID生成
function generateId() {
  return BigInt(Date.now()) * BigInt(Math.random() * 1000000);
}
```

---

## ES2021 特性

### 📝 ES2021 新特性

| 特性                  | 语法               | 示例                       | 用途         |
| --------------------- | ------------------ | -------------------------- | ------------ |
| **逻辑赋值**          | `a &&= b`          | `x &&= y`                  | 逻辑与赋值   |
| **逻辑赋值**          | `a \|\|= b`        | `x \|\|= y`                | 逻辑或赋值   |
| **逻辑赋值**          | `a ??= b`          | `x ??= y`                  | 空值合并赋值 |
| **数字分隔符**        | `1_000_000`        | `1_000_000`                | 数字可读性   |
| **String.replaceAll** | `str.replaceAll()` | `str.replaceAll("a", "b")` | 全局替换     |

### 🔧 详细说明

**逻辑赋值 - 简洁赋值**

> **🎯 解决的问题**：解决了条件赋值的繁琐问题，让代码更加简洁。

```javascript
// 逻辑与赋值
let x = 1;
x &&= 2; // 等价于 x = x && 2
console.log(x); // 2

let y = 0;
y &&= 2; // 等价于 y = y && 2
console.log(y); // 0

// 逻辑或赋值
let a = 1;
a ||= 2; // 等价于 a = a || 2
console.log(a); // 1

let b = 0;
b ||= 2; // 等价于 b = b || 2
console.log(b); // 2

// 空值合并赋值
let c = 1;
c ??= 2; // 等价于 c = c ?? 2
console.log(c); // 1

let d = null;
d ??= 2; // 等价于 d = d ?? 2
console.log(d); // 2

// 实际应用：对象属性初始化
function createUser(name, options = {}) {
  const user = { name };
  user.age ??= 18;
  user.city ??= "北京";
  user.isActive ??= true;
  return user;
}

// 实际应用：配置合并
function mergeConfig(defaultConfig, userConfig) {
  const config = { ...defaultConfig };
  config.apiUrl ||= userConfig.apiUrl;
  config.timeout ??= userConfig.timeout;
  config.debug &&= userConfig.debug;
  return config;
}
```

**数字分隔符 - 提高可读性**

```javascript
// 数字分隔符
const million = 1_000_000;
const billion = 1_000_000_000;
const hex = 0xff_ec_de_5e;
const binary = 0b1010_0001_1000_0101;

console.log(million); // 1000000
console.log(billion); // 1000000000
console.log(hex); // 4278258270
console.log(binary); // 41349

// 实际应用：常量定义
const MAX_FILE_SIZE = 10_000_000; // 10MB
const TIMEOUT_MS = 30_000; // 30秒
const MAX_RETRIES = 3_000; // 3000次

// 实际应用：配置
const config = {
  maxUsers: 1_000_000,
  timeout: 30_000,
  retries: 3,
};
```

---

## ES2022 特性

### 📝 ES2022 新特性

| 特性                | 语法                       | 示例                         | 用途       |
| ------------------- | -------------------------- | ---------------------------- | ---------- |
| **私有字段**        | `#field`                   | `class { #private = 1 }`     | 私有属性   |
| **静态块**          | `static {}`                | `class { static {} }`        | 静态初始化 |
| **Array.at**        | `arr.at(index)`            | `arr.at(-1)`                 | 索引访问   |
| **Object.hasOwn**   | `Object.hasOwn(obj, prop)` | `Object.hasOwn(obj, "prop")` | 属性检查   |
| **Top-level await** | `await`                    | `await fetch()`              | 顶层等待   |

### 🔧 详细说明

**私有字段 - 真正的私有**

> **🎯 解决的问题**：解决了 JavaScript 类中属性无法真正私有的问题，提供了真正的私有属性。

```javascript
// 私有字段
class BankAccount {
  #balance = 0;
  #accountNumber;

  constructor(accountNumber) {
    this.#accountNumber = accountNumber;
  }

  deposit(amount) {
    this.#balance += amount;
  }

  withdraw(amount) {
    if (amount <= this.#balance) {
      this.#balance -= amount;
    } else {
      throw new Error("余额不足");
    }
  }

  getBalance() {
    return this.#balance;
  }

  getAccountNumber() {
    return this.#accountNumber;
  }
}

const account = new BankAccount("123456789");
account.deposit(1000);
console.log(account.getBalance()); // 1000
// console.log(account.#balance); // SyntaxError: Private field '#balance' must be declared in an enclosing class
```

**静态块 - 静态初始化**

```javascript
// 静态块
class Database {
  static #connection;
  static #config;

  static {
    // 静态初始化块
    this.#config = {
      host: process.env.DB_HOST,
      port: process.env.DB_PORT,
      database: process.env.DB_NAME,
    };

    this.#connection = this.createConnection();
  }

  static createConnection() {
    // 创建数据库连接
    return new Connection(this.#config);
  }

  static getConnection() {
    return this.#connection;
  }
}

// 实际应用：单例模式
class Singleton {
  static #instance;

  static {
    this.#instance = new Singleton();
  }

  static getInstance() {
    return this.#instance;
  }

  constructor() {
    if (Singleton.#instance) {
      throw new Error("Singleton already exists");
    }
  }
}
```

---

## ES2023 特性

### 📝 ES2023 新特性

| 特性                    | 语法                          | 示例                            | 用途             |
| ----------------------- | ----------------------------- | ------------------------------- | ---------------- |
| **Array.findLast**      | `arr.findLast(callback)`      | `arr.findLast(x => x > 5)`      | 查找最后一个     |
| **Array.findLastIndex** | `arr.findLastIndex(callback)` | `arr.findLastIndex(x => x > 5)` | 查找最后一个索引 |
| **Hashbang 语法**       | `#!/usr/bin/env node`         | `#!/usr/bin/env node`           | 脚本解释器       |

### 🔧 详细说明

**Array.findLast/Array.findLastIndex - 反向查找**

> **🎯 解决的问题**：解决了从数组末尾查找元素的问题，让数组操作更加完整。

```javascript
// Array.findLast - 查找最后一个匹配元素
const numbers = [1, 2, 3, 4, 5, 4, 3, 2, 1];
const lastEven = numbers.findLast((x) => x % 2 === 0);
console.log(lastEven); // 2

// Array.findLastIndex - 查找最后一个匹配元素的索引
const lastEvenIndex = numbers.findLastIndex((x) => x % 2 === 0);
console.log(lastEvenIndex); // 7

// 实际应用：日志查找
const logs = [
  { level: "info", message: "User logged in" },
  { level: "error", message: "Database connection failed" },
  { level: "info", message: "User logged out" },
  { level: "error", message: "File not found" },
];

const lastError = logs.findLast((log) => log.level === "error");
console.log(lastError); // {level: 'error', message: 'File not found'}

const lastErrorIndex = logs.findLastIndex((log) => log.level === "error");
console.log(lastErrorIndex); // 3
```

---

## 实际应用场景

### 1. 现代前端应用

**React Hooks + 现代特性**

```javascript
import React, { useState, useEffect, useCallback } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // 使用可选链和空值合并
  const fetchUser = useCallback(async (id) => {
    try {
      setLoading(true);
      const response = await fetch(`/api/users/${id}`);
      const userData = await response?.json();

      if (userData?.success) {
        setUser(userData.data);
      } else {
        setError(userData?.error ?? "获取用户失败");
      }
    } catch (err) {
      setError(err?.message ?? "网络错误");
    } finally {
      setLoading(false);
    }
  }, []);

  useEffect(() => {
    fetchUser(userId);
  }, [userId, fetchUser]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div>
      <h1>{user?.name ?? "未知用户"}</h1>
      <p>Age: {user?.age ?? "未知"}</p>
      <p>Email: {user?.email ?? "未知"}</p>
    </div>
  );
}
```

### 2. 数据处理应用

**现代数组操作**

```javascript
// 使用现代数组方法处理数据
class DataProcessor {
  constructor(data) {
    this.data = data;
  }

  // 使用flatMap处理嵌套数据
  flatten() {
    return this.data.flatMap((item) => (Array.isArray(item) ? item : [item]));
  }

  // 使用findLast查找最后匹配项
  findLastMatch(predicate) {
    return this.data.findLast(predicate);
  }

  // 使用Object.fromEntries转换数据
  toObject(keyField, valueField) {
    return Object.fromEntries(
      this.data.map((item) => [item[keyField], item[valueField]])
    );
  }

  // 使用展开运算符合并数据
  merge(otherData) {
    return [...this.data, ...otherData];
  }
}

// 实际应用：用户数据处理
const users = [
  { id: 1, name: "张三", age: 25, active: true },
  { id: 2, name: "李四", age: 30, active: false },
  { id: 3, name: "王五", age: 35, active: true },
];

const processor = new DataProcessor(users);

// 查找最后一个活跃用户
const lastActiveUser = processor.findLastMatch((user) => user.active);
console.log(lastActiveUser); // {id: 3, name: '王五', age: 35, active: true}

// 转换为对象
const userMap = processor.toObject("id", "name");
console.log(userMap); // {1: '张三', 2: '李四', 3: '王五'}
```

### 3. 异步编程应用

**现代异步模式**

```javascript
// 使用现代异步特性
class ApiService {
  constructor(baseURL) {
    this.baseURL = baseURL;
  }

  // 使用async/await和可选链
  async fetchData(endpoint) {
    try {
      const response = await fetch(`${this.baseURL}${endpoint}`);
      const data = await response?.json();

      if (data?.success) {
        return data.data;
      } else {
        throw new Error(data?.error ?? "请求失败");
      }
    } catch (error) {
      console.error("API错误:", error?.message ?? "未知错误");
      throw error;
    }
  }

  // 使用Promise.allSettled处理并发请求
  async fetchMultipleData(endpoints) {
    const promises = endpoints.map((endpoint) =>
      this.fetchData(endpoint).catch((error) => ({ error }))
    );

    const results = await Promise.allSettled(promises);

    return results.map((result, index) => ({
      endpoint: endpoints[index],
      success: result.status === "fulfilled",
      data: result.status === "fulfilled" ? result.value : null,
      error: result.status === "rejected" ? result.reason : null,
    }));
  }

  // 使用异步迭代器处理流式数据
  async *fetchStreamData(endpoint) {
    let page = 1;
    let hasMore = true;

    while (hasMore) {
      const data = await this.fetchData(`${endpoint}?page=${page}`);

      if (data?.items?.length > 0) {
        for (const item of data.items) {
          yield item;
        }
        page++;
      } else {
        hasMore = false;
      }
    }
  }
}

// 使用示例
const api = new ApiService("https://api.example.com");

// 处理流式数据
async function processStreamData() {
  for await (const item of api.fetchStreamData("/users")) {
    console.log("处理用户:", item.name);
    await processUser(item);
  }
}
```

---

## 性能对比与最佳实践

### 1. 性能对比

```javascript
// 性能测试
function performanceTest() {
  const iterations = 1000000;

  // 可选链 vs 传统检查
  const obj = { a: { b: { c: 1 } } };

  console.time("传统检查");
  for (let i = 0; i < iterations; i++) {
    const value = obj && obj.a && obj.a.b && obj.a.b.c;
  }
  console.timeEnd("传统检查");

  console.time("可选链");
  for (let i = 0; i < iterations; i++) {
    const value = obj?.a?.b?.c;
  }
  console.timeEnd("可选链");

  // 空值合并 vs 逻辑或
  const value = null;

  console.time("逻辑或");
  for (let i = 0; i < iterations; i++) {
    const result = value || "default";
  }
  console.timeEnd("逻辑或");

  console.time("空值合并");
  for (let i = 0; i < iterations; i++) {
    const result = value ?? "default";
  }
  console.timeEnd("空值合并");
}

// 典型结果：现代特性性能略低于传统方法，但差异很小
```

### 2. 最佳实践建议

**现代 JavaScript 使用最佳实践：**

```javascript
// ✅ 推荐：使用可选链
const userName = user?.profile?.name ?? "未知用户";

// ✅ 推荐：使用空值合并
const config = {
  apiUrl: userConfig.apiUrl ?? "https://api.example.com",
  timeout: userConfig.timeout ?? 5000,
};

// ✅ 推荐：使用展开运算符
const mergedConfig = { ...defaultConfig, ...userConfig };

// ✅ 推荐：使用现代数组方法
const activeUsers = users.filter((user) => user.active);
const userNames = users.map((user) => user.name);
const lastActiveUser = users.findLast((user) => user.active);

// ✅ 推荐：使用async/await
async function fetchData() {
  try {
    const response = await fetch("/api/data");
    return await response.json();
  } catch (error) {
    console.error("Error:", error);
    throw error;
  }
}

// ✅ 推荐：使用逻辑赋值
function updateUser(user, updates) {
  user.name ??= updates.name;
  user.email ??= updates.email;
  user.isActive &&= updates.isActive;
  return user;
}

// ❌ 避免：过度使用现代特性
// 不好的做法
const result = obj?.a?.b?.c?.d?.e?.f?.g?.h?.i?.j;

// ✅ 好的做法
const result = obj?.a?.b?.c;
if (result) {
  // 处理结果
}
```

---

## 面试常考问题

### 1. 可选链和空值合并

```javascript
// 问题：解释以下代码的输出
const obj = {
  a: {
    b: {
      c: 1,
    },
  },
};

console.log(obj?.a?.b?.c); // 1
console.log(obj?.a?.b?.d); // undefined
console.log(obj?.a?.b?.d ?? "default"); // 'default'

// 答案：可选链提供安全访问，空值合并处理null/undefined
```

### 2. 展开运算符

```javascript
// 问题：解释以下代码的输出
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 };
const merged = { ...obj1, ...obj2 };

console.log(merged); // {a: 1, b: 3, c: 4}

// 答案：展开运算符合并对象，后面的属性覆盖前面的
```

### 3. 逻辑赋值

```javascript
// 问题：解释以下代码的输出
let x = 1;
let y = 0;
let z = null;

x &&= 2;
y &&= 2;
z &&= 2;

console.log(x, y, z); // 2, 0, null

// 答案：逻辑与赋值只在左侧为真值时执行
```

### 4. 异步迭代器

```javascript
// 问题：解释以下代码的执行
async function* generateNumbers() {
  for (let i = 1; i <= 3; i++) {
    await new Promise((resolve) => setTimeout(resolve, 100));
    yield i;
  }
}

async function consumeNumbers() {
  for await (const num of generateNumbers()) {
    console.log(num);
  }
}

consumeNumbers(); // 1, 2, 3 (每100ms输出一个)

// 答案：异步迭代器允许异步生成和消费数据
```

---

## 📋 总结

### 记忆技巧

1. **时间线记忆**：按 ES 版本时间线记忆
2. **功能记忆**：按功能分类记忆
3. **对比记忆**：新旧语法对比记忆
4. **实例记忆**：通过实例理解概念
5. **实践记忆**：通过实际项目加深理解

### 使用建议

1. **渐进式使用**：从基础特性开始，逐步使用高级特性
2. **注意兼容性**：考虑目标环境支持
3. **合理使用**：不要过度使用现代特性
4. **性能考虑**：在性能敏感场景中谨慎使用
5. **团队协作**：保持团队代码风格一致
