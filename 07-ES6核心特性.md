# ES6 核心特性详解

## 📚 目录

- [变量声明](#变量声明)
- [解构赋值](#解构赋值)
- [模板字符串](#模板字符串)
- [箭头函数](#箭头函数)
- [函数增强](#函数增强)
- [对象增强](#对象增强)
- [类](#类)
- [模块化](#模块化)
- [迭代器](#迭代器)
- [生成器](#生成器)
- [Promise](#promise)
- [Proxy/Reflect](#proxyreflect)
- [Symbol](#symbol)
- [Set/Map](#setmap)
- [WeakSet/WeakMap](#weaksetweakmap)
- [实际应用场景](#实际应用场景)
- [性能对比与最佳实践](#性能对比与最佳实践)
- [面试常考问题](#面试常考问题)

---

## 变量声明

### 📝 变量声明对比

| 特性           | var          | let            | const          |
| -------------- | ------------ | -------------- | -------------- |
| **作用域**     | 函数作用域   | 块级作用域     | 块级作用域     |
| **提升**       | 提升并初始化 | 提升但不初始化 | 提升但不初始化 |
| **重复声明**   | 允许         | 不允许         | 不允许         |
| **重新赋值**   | 允许         | 允许           | 不允许         |
| **暂时性死区** | 无           | 有             | 有             |

### 🔧 详细说明

**let/const - 块级作用域**

> **🎯 解决的问题**：解决了 var 的函数作用域问题，提供了真正的块级作用域，避免了变量污染和提升问题。

```javascript
// 块级作用域
{
  let blockVar = "块级变量";
  const blockConst = "块级常量";
}
// console.log(blockVar); // ReferenceError

// 循环中的块级作用域
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 0, 1, 2
}

// 对比 var
for (var j = 0; j < 3; j++) {
  setTimeout(() => console.log(j), 100); // 3, 3, 3
}

// 实际应用：模块化
(function () {
  let privateVar = "私有变量";
  const privateConst = "私有常量";

  window.publicAPI = {
    getPrivateVar: () => privateVar,
  };
})();
```

**暂时性死区**

```javascript
// 暂时性死区
console.log(typeof undeclaredVar); // "undefined"
console.log(typeof letVar); // ReferenceError

let letVar = "let变量";

// 实际应用：避免意外访问
function example() {
  // 这里访问 letVar 会报错
  // console.log(letVar); // ReferenceError

  let letVar = "重新声明";
}
```

---

## 解构赋值

### 📝 解构赋值类型

| 类型         | 语法                 | 示例                      | 用途         |
| ------------ | -------------------- | ------------------------- | ------------ |
| **数组解构** | `[a, b] = array`     | `[a, b] = [1, 2]`         | 数组元素提取 |
| **对象解构** | `{a, b} = obj`       | `{name, age} = user`      | 对象属性提取 |
| **嵌套解构** | `{a: {b}} = obj`     | `{user: {name}} = data`   | 嵌套结构提取 |
| **默认值**   | `{a = 1} = obj`      | `{name = "默认"} = user`  | 默认值设置   |
| **重命名**   | `{a: b} = obj`       | `{name: userName} = user` | 属性重命名   |
| **剩余**     | `{a, ...rest} = obj` | `{name, ...other} = user` | 剩余属性收集 |

### 🔧 详细说明

**数组解构 - 元素提取**

> **🎯 解决的问题**：解决了数组元素访问繁琐的问题，让数组操作更加简洁。

```javascript
// 基础解构
const [first, second, third] = [1, 2, 3];
console.log(first, second, third); // 1, 2, 3

// 跳过元素
const [a, , c] = [1, 2, 3];
console.log(a, c); // 1, 3

// 默认值
const [x, y, z = 0] = [1, 2];
console.log(x, y, z); // 1, 2, 0

// 剩余元素
const [head, ...tail] = [1, 2, 3, 4, 5];
console.log(head, tail); // 1, [2, 3, 4, 5]

// 实际应用：函数返回值
function getCoordinates() {
  return [10, 20];
}
const [x, y] = getCoordinates();
```

**对象解构 - 属性提取**

```javascript
// 基础解构
const { name, age, city } = {
  name: "张三",
  age: 25,
  city: "北京",
};

// 重命名
const { name: userName, age: userAge } = {
  name: "张三",
  age: 25,
};

// 默认值
const {
  name,
  age,
  country = "中国",
} = {
  name: "张三",
  age: 25,
};

// 嵌套解构
const {
  user: {
    name,
    address: { city },
  },
} = {
  user: {
    name: "张三",
    address: {
      city: "北京",
    },
  },
};

// 实际应用：函数参数
function createUser({ name, age, city = "北京" }) {
  return { name, age, city };
}
```

---

## 模板字符串

### 📝 模板字符串特性

| 特性           | 语法            | 示例                          | 用途       |
| -------------- | --------------- | ----------------------------- | ---------- |
| **字符串插值** | `${expression}` | `Hello ${name}`               | 变量插入   |
| **多行字符串** | 反引号          | `` `多行\n字符串` ``          | 多行文本   |
| **标签模板**   | `tag`string``   | `html`<div>${content}</div>`` | 自定义处理 |
| **原始字符串** | `String.raw`    | `String.raw`C:\path``         | 原始字符串 |

### 🔧 详细说明

**模板字符串 - 字符串插值**

> **🎯 解决的问题**：解决了字符串拼接繁琐的问题，让字符串操作更加简洁和可读。

```javascript
// 基础用法
const name = "张三";
const age = 25;
const message = `你好，我是${name}，今年${age}岁`;
console.log(message); // "你好，我是张三，今年25岁"

// 多行字符串
const html = `
  <div class="user-card">
    <h3>${name}</h3>
    <p>年龄: ${age}</p>
  </div>
`;

// 表达式计算
const a = 10;
const b = 20;
const result = `${a} + ${b} = ${a + b}`;
console.log(result); // "10 + 20 = 30"

// 实际应用：HTML模板
function createUserCard(user) {
  return `
    <div class="user-card">
      <h3>${user.name}</h3>
      <p>年龄: ${user.age}</p>
      <p>邮箱: ${user.email}</p>
    </div>
  `;
}
```

**标签模板 - 自定义处理**

```javascript
// 标签模板函数
function highlight(strings, ...values) {
  return strings.reduce((result, string, i) => {
    const value = values[i] ? `<mark>${values[i]}</mark>` : "";
    return result + string + value;
  }, "");
}

// 使用标签模板
const name = "张三";
const age = 25;
const html = highlight`用户 ${name} 今年 ${age} 岁`;
console.log(html); // "用户 <mark>张三</mark> 今年 <mark>25</mark> 岁"

// 实际应用：SQL查询构建
function sql(strings, ...values) {
  return strings.reduce((query, string, i) => {
    const value = values[i] ? `'${values[i]}'` : "";
    return query + string + value;
  }, "");
}

const tableName = "users";
const id = 123;
const query = sql`SELECT * FROM ${tableName} WHERE id = ${id}`;
console.log(query); // "SELECT * FROM 'users' WHERE id = '123'"
```

---

## 箭头函数

### 📝 箭头函数特性

| 特性             | 语法       | 示例               | 用途      |
| ---------------- | ---------- | ------------------ | --------- |
| **简洁语法**     | `() => {}` | `x => x * 2`       | 简洁函数  |
| **this 绑定**    | 词法 this  | `() => this.value` | 固定 this |
| **无 arguments** | 无         | 使用剩余参数       | 参数处理  |
| **无 prototype** | 无         | 不能作为构造函数   | 函数类型  |

### 🔧 详细说明

**箭头函数 - 简洁语法**

> **🎯 解决的问题**：解决了函数定义繁琐的问题，提供了更简洁的函数语法，同时解决了 this 绑定问题。

```javascript
// 基础用法
const add = (a, b) => a + b;
console.log(add(2, 3)); // 5

// 单参数
const square = (x) => x * x;
console.log(square(4)); // 16

// 无参数
const greet = () => "Hello World";
console.log(greet()); // "Hello World"

// 多行函数体
const process = (data) => {
  const result = data.map((item) => item * 2);
  return result.filter((item) => item > 10);
};

// 实际应用：数组方法
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((n) => n * 2);
const evens = numbers.filter((n) => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);
```

**this 绑定 - 词法作用域**

```javascript
// 传统函数的this问题
const obj = {
  name: "张三",
  traditional: function () {
    setTimeout(function () {
      console.log(this.name); // undefined
    }, 1000);
  },
  arrow: function () {
    setTimeout(() => {
      console.log(this.name); // "张三"
    }, 1000);
  },
};

// 实际应用：事件处理
class Button {
  constructor(element) {
    this.element = element;
    this.clickCount = 0;

    // 箭头函数保持this绑定
    this.element.addEventListener("click", () => {
      this.clickCount++;
      console.log(`点击次数: ${this.clickCount}`);
    });
  }
}
```

---

## 函数增强

### 📝 函数增强特性

| 特性         | 语法                   | 示例                                  | 用途       |
| ------------ | ---------------------- | ------------------------------------- | ---------- |
| **默认参数** | `function(a = 1) {}`   | `function greet(name = "World") {}`   | 参数默认值 |
| **剩余参数** | `function(...args) {}` | `function sum(...numbers) {}`         | 收集参数   |
| **参数解构** | `function({a, b}) {}`  | `function createUser({name, age}) {}` | 参数解构   |

### 🔧 详细说明

**默认参数 - 参数默认值**

> **🎯 解决的问题**：解决了参数 undefined 检查的问题，让函数调用更加简洁。

```javascript
// 基础用法
function greet(name = "World") {
  return `Hello, ${name}!`;
}
console.log(greet()); // "Hello, World!"
console.log(greet("张三")); // "Hello, 张三!"

// 表达式作为默认值
function createUser(name, createdAt = new Date()) {
  return { name, createdAt };
}

// 实际应用：配置对象
function createAPI(config = {}) {
  return {
    baseURL: config.baseURL || "https://api.example.com",
    timeout: config.timeout || 5000,
    retries: config.retries || 3,
  };
}
```

**剩余参数 - 收集参数**

> **🎯 解决的问题**：解决了 arguments 对象的问题，提供了更清晰的参数收集方式。

```javascript
// 基础用法
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}
console.log(sum(1, 2, 3, 4, 5)); // 15

// 与其他参数结合
function createUser(name, age, ...hobbies) {
  return { name, age, hobbies };
}
console.log(createUser("张三", 25, "读书", "游泳", "编程"));

// 实际应用：函数组合
function compose(...fns) {
  return function (value) {
    return fns.reduceRight((acc, fn) => fn(acc), value);
  };
}

const addOne = (x) => x + 1;
const multiplyTwo = (x) => x * 2;
const composed = compose(addOne, multiplyTwo);
console.log(composed(5)); // 11
```

---

## 对象增强

### 📝 对象增强特性

| 特性           | 语法             | 示例                    | 用途       |
| -------------- | ---------------- | ----------------------- | ---------- |
| **属性简写**   | `{name}`         | `{name, age}`           | 属性简写   |
| **方法简写**   | `{method() {}}`  | `{greet() {}}`          | 方法简写   |
| **计算属性名** | `{[key]: value}` | `{[`key-${i}`]: value}` | 动态属性名 |

### 🔧 详细说明

**属性简写 - 简洁对象**

> **🎯 解决的问题**：解决了对象属性定义繁琐的问题，让对象创建更加简洁。

```javascript
// 基础用法
const name = "张三";
const age = 25;

// 传统写法
const user1 = {
  name: name,
  age: age,
};

// 简写
const user2 = { name, age };

// 方法简写
const user3 = {
  name,
  age,
  greet() {
    return `Hello, I'm ${this.name}`;
  },
};

// 实际应用：API响应
function createResponse(data) {
  const { id, name, email } = data;
  return {
    id,
    name,
    email,
    timestamp: Date.now(),
  };
}
```

**计算属性名 - 动态属性**

```javascript
// 基础用法
const prefix = "user";
const id = 123;
const obj = {
  [`${prefix}_${id}`]: "张三",
  [`${prefix}_name`]: "李四",
};

// 实际应用：动态配置
function createConfig(environment) {
  return {
    [`${environment}_api_url`]: `https://${environment}.api.com`,
    [`${environment}_debug`]: environment === "development",
  };
}
```

---

## 类

### 📝 类特性

| 特性         | 语法                 | 示例                          | 用途     |
| ------------ | -------------------- | ----------------------------- | -------- |
| **类声明**   | `class Name {}`      | `class User {}`               | 类定义   |
| **构造函数** | `constructor() {}`   | `constructor(name) {}`        | 初始化   |
| **方法**     | `method() {}`        | `greet() {}`                  | 实例方法 |
| **静态方法** | `static method() {}` | `static create() {}`          | 静态方法 |
| **继承**     | `extends`            | `class Admin extends User {}` | 类继承   |
| **super**    | `super()`            | `super(name)`                 | 调用父类 |

### 🔧 详细说明

**类声明 - 面向对象**

> **🎯 解决的问题**：解决了 JavaScript 面向对象编程的问题，提供了更清晰的类语法。

```javascript
// 基础类
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hello, I'm ${this.name}`;
  }

  static create(name, age) {
    return new User(name, age);
  }
}

// 继承
class Admin extends User {
  constructor(name, age, permissions) {
    super(name, age);
    this.permissions = permissions;
  }

  hasPermission(permission) {
    return this.permissions.includes(permission);
  }
}

// 实际应用：组件类
class Component {
  constructor(element) {
    this.element = element;
    this.init();
  }

  init() {
    this.bindEvents();
  }

  bindEvents() {
    // 绑定事件
  }

  render() {
    // 渲染逻辑
  }
}
```

---

## 模块化

### 📝 模块化特性

| 特性             | 语法             | 示例                               | 用途         |
| ---------------- | ---------------- | ---------------------------------- | ------------ |
| **导出**         | `export`         | `export const name = "value"`      | 模块导出     |
| **导入**         | `import`         | `import {name} from "./module"`    | 模块导入     |
| **默认导出**     | `export default` | `export default class`             | 默认导出     |
| **命名空间导入** | `import * as`    | `import * as utils from "./utils"` | 命名空间导入 |

### 🔧 详细说明

**模块化 - 代码组织**

> **🎯 解决的问题**：解决了 JavaScript 代码组织的问题，提供了标准的模块化方案。

```javascript
// utils.js - 工具模块
export const formatDate = (date) => {
  return date.toLocaleDateString();
};

export const formatCurrency = (amount) => {
  return `¥${amount.toFixed(2)}`;
};

export default class Utils {
  static formatUser(user) {
    return `${user.name} (${user.age})`;
  }
}

// user.js - 用户模块
import Utils, { formatDate, formatCurrency } from "./utils.js";

export class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  getFormattedInfo() {
    return Utils.formatUser(this);
  }
}

// main.js - 主模块
import { User } from "./user.js";
import { formatDate } from "./utils.js";

const user = new User("张三", 25);
console.log(user.getFormattedInfo());
```

---

## 迭代器

### 📝 迭代器特性

| 特性                | 语法                     | 示例                      | 用途       |
| ------------------- | ------------------------ | ------------------------- | ---------- |
| **Symbol.iterator** | `[Symbol.iterator]()`    | `obj[Symbol.iterator]()`  | 迭代器方法 |
| **for...of**        | `for (item of iterable)` | `for (let item of array)` | 迭代遍历   |
| **自定义迭代器**    | `next()`                 | `{value, done}`           | 自定义迭代 |

### 🔧 详细说明

**迭代器 - 统一遍历**

> **🎯 解决的问题**：解决了不同数据结构遍历方式不统一的问题，提供了统一的迭代接口。

```javascript
// 数组迭代
const arr = [1, 2, 3, 4, 5];
for (const item of arr) {
  console.log(item);
}

// 字符串迭代
const str = "Hello";
for (const char of str) {
  console.log(char);
}

// 自定义迭代器
class Range {
  constructor(start, end) {
    this.start = start;
    this.end = end;
  }

  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;

    return {
      next() {
        if (current <= end) {
          return { value: current++, done: false };
        } else {
          return { done: true };
        }
      },
    };
  }
}

// 使用自定义迭代器
const range = new Range(1, 5);
for (const num of range) {
  console.log(num); // 1, 2, 3, 4, 5
}
```

---

## 生成器

### 📝 生成器特性

| 特性           | 语法                  | 示例                       | 用途       |
| -------------- | --------------------- | -------------------------- | ---------- |
| **function\*** | `function* name() {}` | `function* generator() {}` | 生成器函数 |
| **yield**      | `yield value`         | `yield 1`                  | 产出值     |
| **yield\***    | `yield* iterable`     | `yield* [1, 2, 3]`         | 委托生成器 |

### 🔧 详细说明

**生成器 - 状态机**

> **🎯 解决的问题**：解决了复杂状态管理的问题，提供了更优雅的状态机实现。

```javascript
// 基础生成器
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
console.log(gen.next().value); // 3

// 实际应用：斐波那契数列
function* fibonacci() {
  let a = 0,
    b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

const fib = fibonacci();
for (let i = 0; i < 10; i++) {
  console.log(fib.next().value);
}
```

---

## Promise

### 📝 Promise 特性

| 特性             | 语法                | 示例                                    | 用途         |
| ---------------- | ------------------- | --------------------------------------- | ------------ |
| **Promise 构造** | `new Promise()`     | `new Promise((resolve, reject) => {})`  | 创建 Promise |
| **then**         | `promise.then()`    | `promise.then(onFulfilled, onRejected)` | 处理结果     |
| **catch**        | `promise.catch()`   | `promise.catch(onRejected)`             | 错误处理     |
| **finally**      | `promise.finally()` | `promise.finally(onFinally)`            | 最终处理     |
| **静态方法**     | `Promise.all()`     | `Promise.all([p1, p2])`                 | 并发处理     |

### 🔧 详细说明

**Promise - 异步编程**

> **🎯 解决的问题**：解决了回调地狱的问题，提供了更优雅的异步编程方案。

```javascript
// 基础Promise
function fetchData(url) {
  return new Promise((resolve, reject) => {
    fetch(url)
      .then((response) => response.json())
      .then((data) => resolve(data))
      .catch((error) => reject(error));
  });
}

// Promise链式调用
fetchData("/api/users")
  .then((users) => {
    console.log("Users:", users);
    return fetchData("/api/posts");
  })
  .then((posts) => {
    console.log("Posts:", posts);
  })
  .catch((error) => {
    console.error("Error:", error);
  });

// 实际应用：并发请求
Promise.all([
  fetchData("/api/users"),
  fetchData("/api/posts"),
  fetchData("/api/comments"),
])
  .then(([users, posts, comments]) => {
    console.log("All data:", { users, posts, comments });
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```

---

## Proxy/Reflect

### 📝 Proxy/Reflect 特性

| 特性           | 语法                         | 示例                          | 用途         |
| -------------- | ---------------------------- | ----------------------------- | ------------ |
| **Proxy 构造** | `new Proxy(target, handler)` | `new Proxy(obj, {})`          | 创建代理     |
| **get 陷阱**   | `get(target, prop)`          | `get(target, prop) {}`        | 属性访问拦截 |
| **set 陷阱**   | `set(target, prop, value)`   | `set(target, prop, value) {}` | 属性设置拦截 |
| **Reflect**    | `Reflect.get()`              | `Reflect.get(target, prop)`   | 反射操作     |

### 🔧 详细说明

**Proxy - 元编程**

> **🎯 解决的问题**：解决了对象操作控制的问题，提供了更精细的对象操作拦截。

```javascript
// 基础Proxy
const target = { name: "张三", age: 25 };
const proxy = new Proxy(target, {
  get(target, prop) {
    console.log(`访问属性: ${prop}`);
    return target[prop];
  },
  set(target, prop, value) {
    console.log(`设置属性: ${prop} = ${value}`);
    target[prop] = value;
    return true;
  },
});

console.log(proxy.name); // 访问属性: name, 张三
proxy.age = 30; // 设置属性: age = 30

// 实际应用：数据验证
function createValidatedObject(schema) {
  return new Proxy(
    {},
    {
      set(target, prop, value) {
        if (schema[prop] && !schema[prop](value)) {
          throw new Error(`Invalid value for ${prop}`);
        }
        target[prop] = value;
        return true;
      },
    }
  );
}

const userSchema = {
  name: (value) => typeof value === "string",
  age: (value) => typeof value === "number" && value > 0,
};

const user = createValidatedObject(userSchema);
user.name = "张三"; // OK
user.age = 25; // OK
// user.age = -1; // Error
```

---

## Symbol

### 📝 Symbol 特性

| 特性              | 语法                  | 示例                   | 用途            |
| ----------------- | --------------------- | ---------------------- | --------------- |
| **Symbol 创建**   | `Symbol()`            | `const sym = Symbol()` | 创建唯一标识    |
| **Symbol 描述**   | `Symbol(description)` | `Symbol('id')`         | 带描述的 Symbol |
| **Symbol.for**    | `Symbol.for(key)`     | `Symbol.for('global')` | 全局 Symbol     |
| **Symbol.keyFor** | `Symbol.keyFor(sym)`  | `Symbol.keyFor(sym)`   | 获取 Symbol 键  |

### 🔧 详细说明

**Symbol - 唯一标识符**

> **🎯 解决的问题**：解决了属性名冲突的问题，提供了唯一的属性标识符。

```javascript
// 基础Symbol
const id = Symbol("id");
const user = {
  [id]: 123,
  name: "张三",
};

console.log(user[id]); // 123
console.log(Object.keys(user)); // ["name"] (Symbol属性不可枚举)

// 实际应用：私有属性
const _private = Symbol("private");
class MyClass {
  constructor() {
    this[_private] = "私有数据";
  }

  getPrivate() {
    return this[_private];
  }
}

// 实际应用：元数据
const METADATA = Symbol("metadata");
function addMetadata(obj, data) {
  obj[METADATA] = data;
}

function getMetadata(obj) {
  return obj[METADATA];
}
```

---

## Set/Map

### 📝 Set/Map 特性

| 特性        | 语法            | 示例                          | 用途       |
| ----------- | --------------- | ----------------------------- | ---------- |
| **Set**     | `new Set()`     | `new Set([1, 2, 3])`          | 唯一值集合 |
| **Map**     | `new Map()`     | `new Map([['key', 'value']])` | 键值对映射 |
| **WeakSet** | `new WeakSet()` | `new WeakSet([obj])`          | 弱引用集合 |
| **WeakMap** | `new WeakMap()` | `new WeakMap([[obj, value]])` | 弱引用映射 |

### 🔧 详细说明

**Set - 唯一值集合**

> **🎯 解决的问题**：解决了数组去重和集合操作的问题，提供了高效的集合数据结构。

```javascript
// 基础Set
const set = new Set([1, 2, 3, 3, 4, 4]);
console.log(set); // Set {1, 2, 3, 4}

// Set方法
set.add(5);
set.delete(1);
console.log(set.has(2)); // true
console.log(set.size); // 4

// 实际应用：数组去重
function uniqueArray(arr) {
  return [...new Set(arr)];
}

// 实际应用：集合运算
function union(setA, setB) {
  return new Set([...setA, ...setB]);
}

function intersection(setA, setB) {
  return new Set([...setA].filter((x) => setB.has(x)));
}
```

**Map - 键值对映射**

```javascript
// 基础Map
const map = new Map([
  ["name", "张三"],
  ["age", 25],
]);

// Map方法
map.set("city", "北京");
map.delete("age");
console.log(map.get("name")); // 张三
console.log(map.has("city")); // true

// 实际应用：缓存
class Cache {
  constructor() {
    this.cache = new Map();
  }

  set(key, value) {
    this.cache.set(key, value);
  }

  get(key) {
    return this.cache.get(key);
  }

  has(key) {
    return this.cache.has(key);
  }
}
```

---

## 实际应用场景

### 1. 现代前端框架应用

**React 组件开发**

```javascript
// 使用ES6特性开发React组件
import React, { useState, useEffect } from "react";

class UserList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      users: [],
      loading: true,
    };
  }

  async componentDidMount() {
    try {
      const users = await this.fetchUsers();
      this.setState({ users, loading: false });
    } catch (error) {
      console.error("Error:", error);
    }
  }

  async fetchUsers() {
    const response = await fetch("/api/users");
    return response.json();
  }

  render() {
    const { users, loading } = this.state;

    if (loading) {
      return <div>Loading...</div>;
    }

    return (
      <div>
        {users.map((user) => (
          <div key={user.id}>
            <h3>{user.name}</h3>
            <p>Age: {user.age}</p>
          </div>
        ))}
      </div>
    );
  }
}
```

### 2. 异步编程应用

**Promise 链式调用**

```javascript
// 使用Promise处理异步操作
class ApiService {
  constructor(baseURL) {
    this.baseURL = baseURL;
  }

  async request(endpoint, options = {}) {
    const url = `${this.baseURL}${endpoint}`;
    const response = await fetch(url, {
      headers: {
        "Content-Type": "application/json",
        ...options.headers,
      },
      ...options,
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    return response.json();
  }

  async getUser(id) {
    return this.request(`/users/${id}`);
  }

  async getUserPosts(userId) {
    return this.request(`/users/${userId}/posts`);
  }

  async getUserWithPosts(userId) {
    try {
      const [user, posts] = await Promise.all([
        this.getUser(userId),
        this.getUserPosts(userId),
      ]);

      return { user, posts };
    } catch (error) {
      console.error("Error fetching user data:", error);
      throw error;
    }
  }
}
```

### 3. 函数式编程应用

**函数组合和管道**

```javascript
// 使用ES6特性实现函数式编程
const pipe =
  (...fns) =>
  (value) =>
    fns.reduce((acc, fn) => fn(acc), value);

const addOne = (x) => x + 1;
const multiplyTwo = (x) => x * 2;
const square = (x) => x * x;

const transform = pipe(addOne, multiplyTwo, square);
console.log(transform(3)); // 64

// 实际应用：数据处理管道
const processUserData = pipe(
  (user) => ({ ...user, fullName: `${user.firstName} ${user.lastName}` }),
  (user) => ({ ...user, isAdult: user.age >= 18 }),
  (user) => ({ ...user, displayName: user.isAdult ? user.fullName : "Child" })
);

const users = [
  { firstName: "John", lastName: "Doe", age: 25 },
  { firstName: "Jane", lastName: "Smith", age: 16 },
];

const processedUsers = users.map(processUserData);
```

---

## 性能对比与最佳实践

### 1. 性能对比

```javascript
// 性能测试
function performanceTest() {
  const iterations = 1000000;

  // 变量声明性能
  console.time("var声明");
  for (let i = 0; i < iterations; i++) {
    var x = i;
  }
  console.timeEnd("var声明");

  console.time("let声明");
  for (let i = 0; i < iterations; i++) {
    let x = i;
  }
  console.timeEnd("let声明");

  // 函数性能
  console.time("传统函数");
  for (let i = 0; i < iterations; i++) {
    function add(a, b) {
      return a + b;
    }
    add(1, 2);
  }
  console.timeEnd("传统函数");

  console.time("箭头函数");
  for (let i = 0; i < iterations; i++) {
    const add = (a, b) => a + b;
    add(1, 2);
  }
  console.timeEnd("箭头函数");
}

// 典型结果：let > var, 箭头函数 > 传统函数
```

### 2. 最佳实践建议

**ES6 使用最佳实践：**

```javascript
// ✅ 推荐：使用const和let
const API_URL = "https://api.example.com";
let currentUser = null;

// ✅ 推荐：使用箭头函数处理this绑定
class Component {
  constructor() {
    this.handleClick = () => {
      console.log(this); // 正确绑定
    };
  }
}

// ✅ 推荐：使用解构赋值
const { name, age } = user;
const [first, second] = array;

// ✅ 推荐：使用模板字符串
const message = `Hello, ${name}!`;

// ✅ 推荐：使用默认参数
function greet(name = "World") {
  return `Hello, ${name}!`;
}

// ✅ 推荐：使用剩余参数
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

// ✅ 推荐：使用Promise处理异步
async function fetchData() {
  try {
    const response = await fetch("/api/data");
    return await response.json();
  } catch (error) {
    console.error("Error:", error);
  }
}

// ❌ 避免：使用var
// var x = 1; // 不推荐

// ❌ 避免：使用arguments对象
// function badSum() {
//   let total = 0;
//   for (let i = 0; i < arguments.length; i++) {
//     total += arguments[i];
//   }
//   return total;
// }

// ✅ 好的做法
function goodSum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}
```

---

## 面试常考问题

### 1. 变量提升和暂时性死区

```javascript
// 问题：解释以下代码的输出
console.log(typeof a); // undefined
console.log(typeof b); // ReferenceError
console.log(typeof c); // ReferenceError

var a = 1;
let b = 2;
const c = 3;

// 答案：
// 1. var声明会提升并初始化为undefined
// 2. let/const声明会提升但不初始化，形成暂时性死区
// 3. 在暂时性死区内访问变量会抛出ReferenceError
```

### 2. this 绑定问题

```javascript
// 问题：解释以下代码的输出
const obj = {
  name: "张三",
  traditional: function () {
    console.log(this.name);
  },
  arrow: () => {
    console.log(this.name);
  },
};

obj.traditional(); // 张三
obj.arrow(); // undefined

// 答案：
// 1. 传统函数的this指向调用对象
// 2. 箭头函数的this指向词法作用域的this
// 3. 箭头函数没有自己的this，会继承外层作用域的this
```

### 3. Promise 执行顺序

```javascript
// 问题：解释以下代码的输出顺序
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// 输出顺序：1, 4, 3, 2
// 解释：
// 1. 同步代码先执行
// 2. Promise微任务优先于setTimeout宏任务
// 3. 事件循环：同步 -> 微任务 -> 宏任务
```

### 4. 模块化问题

```javascript
// 问题：解释以下模块化代码
// module1.js
export const name = "module1";
export default class Module1 {}

// module2.js
import Module1, { name } from "./module1.js";
import * as utils from "./utils.js";

// 答案：
// 1. export导出，import导入
// 2. 默认导出和命名导出
// 3. 命名空间导入
// 4. ES6模块是静态的，编译时确定依赖关系
```

---

## 📋 总结

### 记忆技巧

1. **分类记忆**：按功能分类（变量、函数、对象、异步等）
2. **对比记忆**：新旧语法的区别
3. **this 绑定记忆**：箭头函数不绑定 this
4. **异步记忆**：Promise > 回调函数
5. **模块化记忆**：export/import 配对使用

### 使用建议

1. **优先使用 ES6+语法**：更简洁、更安全
2. **注意兼容性**：考虑目标环境支持
3. **合理使用特性**：不要过度使用
4. **注意性能影响**：选择合适的方法
5. **保持代码一致性**：团队统一标准
