# JavaScript 面试核心知识点

## 📚 目录

- [作用域与闭包](#作用域与闭包)
- [this 绑定规则](#this绑定规则)
- [原型与原型链](#原型与原型链)
- [事件循环与异步](#事件循环与异步)
- [内存管理与垃圾回收](#内存管理与垃圾回收)
- [类型转换与比较](#类型转换与比较)
- [模块化发展史](#模块化发展史)
- [性能优化](#性能优化)
- [错误处理](#错误处理)
- [调试技巧](#调试技巧)
- [实际应用场景](#实际应用场景)
- [面试常考问题](#面试常考问题)

---

## 作用域与闭包

### 📝 作用域类型

| 类型           | 特点         | 示例                                       | 用途     |
| -------------- | ------------ | ------------------------------------------ | -------- |
| **全局作用域** | 全局可访问   | `var global = 1`                           | 全局变量 |
| **函数作用域** | 函数内可访问 | `function() { var local = 1 }`             | 局部变量 |
| **块级作用域** | 块内可访问   | `{ let block = 1 }`                        | 块级变量 |
| **词法作用域** | 定义时确定   | `function outer() { function inner() {} }` | 作用域链 |

### 🔧 详细说明

**作用域链 - 变量查找**

> **🎯 核心概念**：JavaScript 使用词法作用域，变量的作用域在定义时确定，通过作用域链查找变量。

```javascript
// 作用域链示例
var globalVar = "全局变量";

function outerFunction() {
  var outerVar = "外部变量";

  function innerFunction() {
    var innerVar = "内部变量";

    // 变量查找顺序：innerVar -> outerVar -> globalVar
    console.log(innerVar); // "内部变量"
    console.log(outerVar); // "外部变量"
    console.log(globalVar); // "全局变量"
  }

  innerFunction();
}

outerFunction();

// 实际应用：模块模式
const Module = (function () {
  let privateVar = "私有变量";

  return {
    getPrivateVar: function () {
      return privateVar;
    },
    setPrivateVar: function (value) {
      privateVar = value;
    },
  };
})();
```

**闭包 - 函数与作用域**

```javascript
// 闭包基础
function createCounter() {
  let count = 0;

  return function () {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3

// 实际应用：数据私有化
function createBankAccount(initialBalance) {
  let balance = initialBalance;

  return {
    deposit: function (amount) {
      balance += amount;
      return balance;
    },
    withdraw: function (amount) {
      if (amount <= balance) {
        balance -= amount;
        return balance;
      } else {
        throw new Error("余额不足");
      }
    },
    getBalance: function () {
      return balance;
    },
  };
}

const account = createBankAccount(1000);
console.log(account.getBalance()); // 1000
account.deposit(500);
console.log(account.getBalance()); // 1500
```

---

## this 绑定规则

### 📝 this 绑定规则

| 规则         | 调用方式        | this 指向          | 示例             |
| ------------ | --------------- | ------------------ | ---------------- |
| **默认绑定** | 直接调用        | 全局对象/undefined | `func()`         |
| **隐式绑定** | 对象调用        | 调用对象           | `obj.func()`     |
| **显式绑定** | call/apply/bind | 指定对象           | `func.call(obj)` |
| **new 绑定** | 构造函数        | 新创建对象         | `new Func()`     |
| **箭头函数** | 词法作用域      | 外层 this          | `() => {}`       |

### 🔧 详细说明

**this 绑定规则 - 四种绑定方式**

> **🎯 核心概念**：this 的指向在函数调用时确定，遵循四种绑定规则。

```javascript
// 1. 默认绑定
function defaultBinding() {
  console.log(this); // 全局对象或undefined
}

defaultBinding(); // 默认绑定

// 2. 隐式绑定
const obj = {
  name: "张三",
  sayHello: function () {
    console.log(`Hello, ${this.name}`);
  },
};

obj.sayHello(); // 隐式绑定，this指向obj

// 3. 显式绑定
function sayHello() {
  console.log(`Hello, ${this.name}`);
}

const person1 = { name: "李四" };
const person2 = { name: "王五" };

sayHello.call(person1); // 显式绑定，this指向person1
sayHello.apply(person2); // 显式绑定，this指向person2

const boundSayHello = sayHello.bind(person1);
boundSayHello(); // 显式绑定，this指向person1

// 4. new绑定
function Person(name) {
  this.name = name;
  this.sayHello = function () {
    console.log(`Hello, ${this.name}`);
  };
}

const person = new Person("赵六");
person.sayHello(); // new绑定，this指向新创建的对象
```

**this 绑定优先级**

```javascript
// 绑定优先级：new > 显式 > 隐式 > 默认
function test() {
  console.log(this.name);
}

const obj1 = { name: "obj1" };
const obj2 = { name: "obj2" };

// 显式绑定优先级高于隐式绑定
obj1.test = test;
obj1.test.call(obj2); // "obj2"

// new绑定优先级最高
function Person(name) {
  this.name = name;
}

const obj = { name: "obj" };
const BoundPerson = Person.bind(obj);
const person = new BoundPerson("person");
console.log(person.name); // "person" (new绑定)
```

---

## 原型与原型链

### 📝 原型概念

| 概念            | 说明         | 示例                      | 用途         |
| --------------- | ------------ | ------------------------- | ------------ |
| **prototype**   | 函数原型对象 | `Person.prototype`        | 共享方法     |
| ****proto****   | 对象原型链   | `obj.__proto__`           | 原型链查找   |
| **constructor** | 构造函数     | `obj.constructor`         | 构造函数引用 |
| **原型链**      | 继承链       | `obj.__proto__.__proto__` | 属性查找     |

### 🔧 详细说明

**原型链 - 继承机制**

> **🎯 核心概念**：JavaScript 通过原型链实现继承，对象通过**proto**属性查找属性和方法。

```javascript
// 原型链示例
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hello, ${this.name}`);
};

const person = new Person("张三");

// 原型链查找
console.log(person.name); // "张三" (自有属性)
console.log(person.sayHello); // function (原型方法)
console.log(person.toString); // function (Object原型方法)

// 原型链结构
console.log(person.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true

// 实际应用：继承
function Student(name, grade) {
  Person.call(this, name);
  this.grade = grade;
}

Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;

Student.prototype.study = function () {
  console.log(`${this.name} is studying`);
};

const student = new Student("李四", "高三");
student.sayHello(); // "Hello, 李四"
student.study(); // "李四 is studying"
```

**原型方法 - 共享机制**

```javascript
// 原型方法共享
function Car(brand) {
  this.brand = brand;
}

Car.prototype.start = function () {
  console.log(`${this.brand} is starting`);
};

Car.prototype.stop = function () {
  console.log(`${this.brand} is stopping`);
};

const car1 = new Car("Toyota");
const car2 = new Car("Honda");

// 方法共享
console.log(car1.start === car2.start); // true

// 实际应用：扩展内置对象
Array.prototype.last = function () {
  return this[this.length - 1];
};

const arr = [1, 2, 3, 4, 5];
console.log(arr.last()); // 5
```

---

## 事件循环与异步

### 📝 事件循环机制

| 概念         | 说明         | 示例       | 用途     |
| ------------ | ------------ | ---------- | -------- |
| **调用栈**   | 执行上下文栈 | 函数调用   | 同步执行 |
| **任务队列** | 异步任务队列 | setTimeout | 异步执行 |
| **微任务**   | 高优先级任务 | Promise    | 优先执行 |
| **宏任务**   | 低优先级任务 | setTimeout | 后执行   |

### 🔧 详细说明

**事件循环 - 执行机制**

> **🎯 核心概念**：JavaScript 是单线程的，通过事件循环处理异步操作，微任务优先于宏任务。

```javascript
// 事件循环示例
console.log("1"); // 同步任务

setTimeout(() => console.log("2"), 0); // 宏任务

Promise.resolve().then(() => console.log("3")); // 微任务

console.log("4"); // 同步任务

// 输出顺序：1, 4, 3, 2
// 解释：
// 1. 同步任务先执行
// 2. 微任务优先于宏任务
// 3. 事件循环：同步 -> 微任务 -> 宏任务

// 实际应用：异步处理
async function handleAsync() {
  console.log("开始");

  // 微任务
  await Promise.resolve();
  console.log("微任务完成");

  // 宏任务
  setTimeout(() => {
    console.log("宏任务完成");
  }, 0);

  console.log("结束");
}

handleAsync();
```

**Promise 与事件循环**

```javascript
// Promise执行顺序
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve()
  .then(() => {
    console.log("3");
    return Promise.resolve();
  })
  .then(() => {
    console.log("4");
  });

console.log("5");

// 输出顺序：1, 5, 3, 4, 2
// 解释：
// 1. 同步任务：1, 5
// 2. 微任务：3, 4
// 3. 宏任务：2
```

---

## 内存管理与垃圾回收

### 📝 内存管理

| 概念         | 说明         | 示例           | 用途     |
| ------------ | ------------ | -------------- | -------- |
| **栈内存**   | 基本类型存储 | `let a = 1`    | 快速访问 |
| **堆内存**   | 引用类型存储 | `let obj = {}` | 复杂数据 |
| **引用计数** | 引用数量统计 | 循环引用       | 垃圾回收 |
| **标记清除** | 可达性分析   | 根对象查找     | 垃圾回收 |

### 🔧 详细说明

**内存泄漏 - 常见问题**

> **🎯 核心概念**：JavaScript 自动管理内存，但某些情况下会导致内存泄漏。

```javascript
// 1. 全局变量泄漏
function createGlobalVar() {
  globalVar = "全局变量"; // 忘记声明var/let/const
}

// 2. 闭包泄漏
function createClosure() {
  const largeData = new Array(1000000).fill(0);

  return function () {
    // 闭包持有largeData的引用
    console.log("闭包函数");
  };
}

// 3. 事件监听器泄漏
function addEventListener() {
  const button = document.getElementById("button");
  button.addEventListener("click", function () {
    console.log("点击");
  });
  // 忘记移除事件监听器
}

// 4. 定时器泄漏
function createTimer() {
  const timer = setInterval(() => {
    console.log("定时器");
  }, 1000);
  // 忘记清除定时器
}

// 实际应用：内存优化
function createOptimizedClosure() {
  const largeData = new Array(1000000).fill(0);

  return function () {
    console.log("优化后的闭包");
    // 使用完后清除引用
    largeData.length = 0;
  };
}
```

**垃圾回收 - 自动管理**

```javascript
// 垃圾回收示例
function createObject() {
  const obj = {
    data: new Array(1000000).fill(0),
  };

  // 函数执行完后，obj不再被引用，会被垃圾回收
  return obj;
}

const myObj = createObject();
// myObj持有引用，不会被回收

myObj = null; // 清除引用，对象可以被回收

// 实际应用：WeakMap和WeakSet
const weakMap = new WeakMap();
const weakSet = new WeakSet();

function useWeakCollections() {
  const obj = { data: "重要数据" };

  weakMap.set(obj, "关联数据");
  weakSet.add(obj);

  // 当obj不再被引用时，WeakMap和WeakSet中的条目会自动清除
}
```

---

## 类型转换与比较

### 📝 类型转换

| 转换         | 规则             | 示例            | 结果                |
| ------------ | ---------------- | --------------- | ------------------- |
| **隐式转换** | 自动转换         | `1 + "2"`       | `"12"`              |
| **显式转换** | 手动转换         | `Number("123")` | `123`               |
| **布尔转换** | 真值/假值        | `Boolean(0)`    | `false`             |
| **对象转换** | toString/valueOf | `String({})`    | `"[object Object]"` |

### 🔧 详细说明

**类型转换 - 隐式转换**

> **🎯 核心概念**：JavaScript 是弱类型语言，会自动进行类型转换，需要理解转换规则。

```javascript
// 1. 字符串转换
console.log(1 + "2"); // "12"
console.log("2" + 1); // "21"
console.log(1 + 2 + "3"); // "33"
console.log("1" + 2 + 3); // "123"

// 2. 数字转换
console.log("123" - 1); // 122
console.log("123" * 2); // 246
console.log("123" / 2); // 61.5
console.log("abc" - 1); // NaN

// 3. 布尔转换
console.log(Boolean(0)); // false
console.log(Boolean("")); // false
console.log(Boolean(null)); // false
console.log(Boolean(undefined)); // false
console.log(Boolean(NaN)); // false
console.log(Boolean(false)); // false

// 其他值都是true
console.log(Boolean(1)); // true
console.log(Boolean("hello")); // true
console.log(Boolean({})); // true
console.log(Boolean([])); // true

// 实际应用：类型检查
function add(a, b) {
  if (typeof a !== "number" || typeof b !== "number") {
    throw new Error("参数必须是数字");
  }
  return a + b;
}
```

**比较操作 - 严格比较**

```javascript
// 1. 相等比较
console.log(1 == "1"); // true (类型转换)
console.log(1 === "1"); // false (严格比较)
console.log(null == undefined); // true
console.log(null === undefined); // false

// 2. 对象比较
const obj1 = { a: 1 };
const obj2 = { a: 1 };
const obj3 = obj1;

console.log(obj1 == obj2); // false (不同对象)
console.log(obj1 === obj2); // false (不同对象)
console.log(obj1 === obj3); // true (同一对象)

// 3. 数组比较
const arr1 = [1, 2, 3];
const arr2 = [1, 2, 3];
const arr3 = arr1;

console.log(arr1 == arr2); // false (不同数组)
console.log(arr1 === arr2); // false (不同数组)
console.log(arr1 === arr3); // true (同一数组)

// 实际应用：深度比较
function deepEqual(obj1, obj2) {
  if (obj1 === obj2) return true;

  if (obj1 == null || obj2 == null) return false;

  if (typeof obj1 !== typeof obj2) return false;

  if (typeof obj1 !== "object") return obj1 === obj2;

  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);

  if (keys1.length !== keys2.length) return false;

  for (const key of keys1) {
    if (!keys2.includes(key)) return false;
    if (!deepEqual(obj1[key], obj2[key])) return false;
  }

  return true;
}
```

---

## 模块化发展史

### 📝 模块化发展

| 阶段    | 方案       | 特点         | 示例                |
| ------- | ---------- | ------------ | ------------------- |
| **ES5** | IIFE       | 立即执行函数 | `(function() {})()` |
| **ES5** | CommonJS   | 同步加载     | `module.exports`    |
| **ES5** | AMD        | 异步加载     | `define()`          |
| **ES6** | ES Modules | 标准模块     | `import/export`     |

### 🔧 详细说明

**模块化发展 - 从 IIFE 到 ES Modules**

> **🎯 核心概念**：JavaScript 模块化经历了从 IIFE 到 ES Modules 的发展，解决了代码组织和依赖管理问题。

```javascript
// 1. IIFE模块化
const Module = (function () {
  let privateVar = "私有变量";

  function privateFunction() {
    return "私有函数";
  }

  return {
    publicVar: "公共变量",
    publicFunction: function () {
      return privateFunction();
    },
  };
})();

// 2. CommonJS模块化
// math.js
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

module.exports = {
  add,
  subtract,
};

// main.js
const { add, subtract } = require("./math");
console.log(add(1, 2)); // 3

// 3. ES Modules
// utils.js
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

// main.js
import Utils, { formatDate, formatCurrency } from "./utils.js";

const user = { name: "张三", age: 25 };
console.log(Utils.formatUser(user));
```

---

## 性能优化

### 📝 性能优化策略

| 策略             | 方法          | 示例                      | 效果           |
| ---------------- | ------------- | ------------------------- | -------------- |
| **减少重排重绘** | 批量 DOM 操作 | `documentFragment`        | 提高渲染性能   |
| **事件委托**     | 事件冒泡      | `parent.addEventListener` | 减少事件监听器 |
| **防抖节流**     | 限制执行频率  | `debounce/throttle`       | 优化性能       |
| **懒加载**       | 按需加载      | `IntersectionObserver`    | 减少初始加载   |

### 🔧 详细说明

**性能优化 - 实际应用**

> **🎯 核心概念**：前端性能优化涉及多个方面，需要综合考虑加载、渲染、交互等性能。

```javascript
// 1. 防抖函数
function debounce(func, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// 2. 节流函数
function throttle(func, delay) {
  let lastCall = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastCall >= delay) {
      lastCall = now;
      return func.apply(this, args);
    }
  };
}

// 3. 事件委托
document.addEventListener("click", function (event) {
  if (event.target.matches(".button")) {
    handleButtonClick(event.target);
  }
});

// 4. 懒加载
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src;
      observer.unobserve(img);
    }
  });
});

document.querySelectorAll("img[data-src]").forEach((img) => {
  observer.observe(img);
});

// 5. 虚拟滚动
class VirtualScroll {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.visibleCount = Math.ceil(container.clientHeight / itemHeight);
    this.startIndex = 0;
    this.endIndex = this.visibleCount;

    this.render();
  }

  render() {
    const visibleItems = this.items.slice(this.startIndex, this.endIndex);
    this.container.innerHTML = visibleItems
      .map(
        (item, index) =>
          `<div style="height: ${this.itemHeight}px">${item}</div>`
      )
      .join("");
  }
}
```

---

## 错误处理

### 📝 错误处理策略

| 策略              | 方法           | 示例               | 用途         |
| ----------------- | -------------- | ------------------ | ------------ |
| **try/catch**     | 同步错误捕获   | `try { } catch {}` | 同步错误处理 |
| **Promise.catch** | 异步错误捕获   | `promise.catch()`  | 异步错误处理 |
| **全局错误**      | 全局错误监听   | `window.onerror`   | 全局错误处理 |
| **错误边界**      | React 错误边界 | `ErrorBoundary`    | 组件错误处理 |

### 🔧 详细说明

**错误处理 - 全面覆盖**

> **🎯 核心概念**：错误处理是前端开发的重要环节，需要覆盖同步、异步、全局等各种错误场景。

```javascript
// 1. 同步错误处理
function divide(a, b) {
  try {
    if (b === 0) {
      throw new Error("除数不能为零");
    }
    return a / b;
  } catch (error) {
    console.error("计算错误:", error.message);
    return null;
  }
}

// 2. 异步错误处理
async function fetchData(url) {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    console.error("请求错误:", error.message);
    throw error;
  }
}

// 3. Promise错误处理
fetchData("/api/data")
  .then((data) => {
    console.log("数据:", data);
  })
  .catch((error) => {
    console.error("处理错误:", error.message);
  });

// 4. 全局错误处理
window.addEventListener("error", function (event) {
  console.error("全局错误:", event.error);
  // 发送错误到服务器
  sendErrorToServer(event.error);
});

window.addEventListener("unhandledrejection", function (event) {
  console.error("未处理的Promise拒绝:", event.reason);
  // 发送错误到服务器
  sendErrorToServer(event.reason);
});

// 5. 错误边界（React）
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error("组件错误:", error, errorInfo);
    // 发送错误到服务器
    sendErrorToServer(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>出现错误，请刷新页面</h1>;
    }

    return this.props.children;
  }
}
```

---

## 调试技巧

### 📝 调试方法

| 方法             | 工具         | 示例                      | 用途         |
| ---------------- | ------------ | ------------------------- | ------------ |
| **console 调试** | console 对象 | `console.log/debug/error` | 基础调试     |
| **断点调试**     | DevTools     | 设置断点                  | 代码执行调试 |
| **性能分析**     | Performance  | 性能分析                  | 性能调试     |
| **网络调试**     | Network      | 网络请求                  | 网络调试     |

### 🔧 详细说明

**调试技巧 - 高效调试**

> **🎯 核心概念**：调试是开发过程中的重要技能，需要掌握各种调试工具和技巧。

```javascript
// 1. console调试
console.log("普通日志");
console.debug("调试信息");
console.info("信息");
console.warn("警告");
console.error("错误");

// 2. 条件调试
console.assert(condition, "条件不满足时的消息");

// 3. 分组调试
console.group("用户信息");
console.log("姓名: 张三");
console.log("年龄: 25");
console.groupEnd();

// 4. 表格调试
console.table([
  { name: "张三", age: 25 },
  { name: "李四", age: 30 },
]);

// 5. 时间调试
console.time("操作耗时");
// 执行一些操作
console.timeEnd("操作耗时");

// 6. 堆栈跟踪
function functionA() {
  functionB();
}

function functionB() {
  functionC();
}

function functionC() {
  console.trace("调用堆栈");
}

functionA();

// 7. 性能调试
function performanceTest() {
  const start = performance.now();

  // 执行一些操作
  for (let i = 0; i < 1000000; i++) {
    Math.random();
  }

  const end = performance.now();
  console.log(`操作耗时: ${end - start} 毫秒`);
}

// 8. 内存调试
function memoryTest() {
  const start = performance.memory;
  console.log("开始内存:", start);

  // 执行一些操作
  const largeArray = new Array(1000000).fill(0);

  const end = performance.memory;
  console.log("结束内存:", end);
  console.log("内存变化:", end.usedJSHeapSize - start.usedJSHeapSize);
}
```

---

## 实际应用场景

### 1. 现代前端框架应用

**React Hooks + 闭包**

```javascript
import React, { useState, useEffect, useCallback } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  // 使用useCallback避免不必要的重新渲染
  const fetchUser = useCallback(async (id) => {
    try {
      setLoading(true);
      const response = await fetch(`/api/users/${id}`);
      const userData = await response.json();
      setUser(userData);
    } catch (error) {
      console.error("获取用户失败:", error);
    } finally {
      setLoading(false);
    }
  }, []);

  useEffect(() => {
    fetchUser(userId);
  }, [userId, fetchUser]);

  if (loading) return <div>Loading...</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Age: {user.age}</p>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

### 2. 状态管理应用

**Redux + 中间件**

```javascript
// Redux store配置
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk";
import logger from "redux-logger";

const initialState = {
  users: [],
  loading: false,
  error: null,
};

function userReducer(state = initialState, action) {
  switch (action.type) {
    case "FETCH_USERS_START":
      return { ...state, loading: true, error: null };
    case "FETCH_USERS_SUCCESS":
      return { ...state, loading: false, users: action.payload };
    case "FETCH_USERS_ERROR":
      return { ...state, loading: false, error: action.payload };
    default:
      return state;
  }
}

const store = createStore(userReducer, applyMiddleware(thunk, logger));

// 异步action
const fetchUsers = () => {
  return async (dispatch) => {
    dispatch({ type: "FETCH_USERS_START" });
    try {
      const response = await fetch("/api/users");
      const users = await response.json();
      dispatch({ type: "FETCH_USERS_SUCCESS", payload: users });
    } catch (error) {
      dispatch({ type: "FETCH_USERS_ERROR", payload: error.message });
    }
  };
};
```

### 3. 性能优化应用

**虚拟滚动 + 防抖**

```javascript
class VirtualScrollList {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.visibleCount = Math.ceil(container.clientHeight / itemHeight);
    this.startIndex = 0;
    this.endIndex = this.visibleCount;

    this.debouncedScroll = this.debounce(this.handleScroll.bind(this), 16);
    this.container.addEventListener("scroll", this.debouncedScroll);

    this.render();
  }

  debounce(func, delay) {
    let timeoutId;
    return function (...args) {
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
  }

  handleScroll() {
    const scrollTop = this.container.scrollTop;
    this.startIndex = Math.floor(scrollTop / this.itemHeight);
    this.endIndex = Math.min(
      this.startIndex + this.visibleCount,
      this.items.length
    );
    this.render();
  }

  render() {
    const visibleItems = this.items.slice(this.startIndex, this.endIndex);
    const offsetY = this.startIndex * this.itemHeight;

    this.container.innerHTML = `
      <div style="height: ${
        this.items.length * this.itemHeight
      }px; position: relative;">
        <div style="transform: translateY(${offsetY}px);">
          ${visibleItems
            .map(
              (item, index) =>
                `<div style="height: ${this.itemHeight}px; padding: 10px;">${item}</div>`
            )
            .join("")}
        </div>
      </div>
    `;
  }
}
```

---

## 面试常考问题

### 1. 作用域和闭包

```javascript
// 问题：解释以下代码的输出
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}

// 输出：3, 3, 3
// 解释：var没有块级作用域，循环结束后i为3，所有定时器都输出3

// 解决方案1：使用let
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}

// 解决方案2：使用闭包
for (var i = 0; i < 3; i++) {
  (function (j) {
    setTimeout(() => console.log(j), 100);
  })(i);
}
```

### 2. this 绑定

```javascript
// 问题：解释以下代码的输出
const obj = {
  name: "张三",
  sayHello: function () {
    console.log(this.name);
  },
  sayHelloArrow: () => {
    console.log(this.name);
  },
};

obj.sayHello(); // 张三
obj.sayHelloArrow(); // undefined

// 解释：
// 1. 传统函数的this指向调用对象
// 2. 箭头函数的this指向词法作用域的this
```

### 3. 原型链

```javascript
// 问题：解释以下代码的输出
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hello, ${this.name}`);
};

const person = new Person("张三");

console.log(person.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true
```

### 4. 事件循环

```javascript
// 问题：解释以下代码的输出顺序
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// 输出顺序：1, 4, 3, 2
// 解释：同步任务 -> 微任务 -> 宏任务
```

### 5. 类型转换

```javascript
// 问题：解释以下代码的输出
console.log([] + []); // ""
console.log([] + {}); // "[object Object]"
console.log({} + []); // "[object Object]"
console.log({} + {}); // "[object Object][object Object]"

// 解释：对象会先调用toString()方法转换为字符串
```

---

## 📋 总结

### 记忆技巧

1. **分类记忆**：按知识点分类记忆
2. **对比记忆**：相似概念对比记忆
3. **实例记忆**：通过实例理解概念
4. **实践记忆**：通过实践加深理解
5. **总结记忆**：定期总结和复习

### 使用建议

1. **深入理解**：不要死记硬背，要理解原理
2. **多实践**：通过实际项目加深理解
3. **关注细节**：注意细节和边界情况
4. **持续学习**：关注新技术和发展趋势
5. **总结归纳**：定期总结和归纳知识点
