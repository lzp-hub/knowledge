## 一、基础与语言特性

### 1. JavaScript 的数据类型有哪些？ 🔥

**一句话回答**
JavaScript 有 7 种基本类型和 1 种引用类型（对象）。

**要点**

- 基本类型：number、string、boolean、null、undefined、symbol、bigint。
- 特殊：NaN 属于 number。
- 引用类型：Object（包括 Array、Function、Date、RegExp 等）。

**详细回答**

- 基本类型存储在栈中，按值传递。
- 引用类型存储在堆中，变量保存的是引用地址。

**扩展/对比**

- 面试官常问：“为什么 typeof null === 'object'？” 👉 历史遗留 bug，底层存储类型标签错位。

---

### 2. `typeof` vs `instanceof` vs `Object.prototype.toString.call` 🔥

**一句话回答**
`typeof` 判断基本类型，`instanceof` 判断实例关系，`toString.call` 更精确判断对象类型。

**要点**

- typeof：基本类型（null 除外）。
- instanceof：基于原型链。
- toString.call：精准判断。

**详细回答**

```js
typeof [] // 'object'
[] instanceof Array // true
Object.prototype.toString.call([]) // '[object Array]'
```

**扩展/对比**

- 面试官可能考：“怎么判断数组？” 👉 `Array.isArray(arr)`。

---

### 3. null 和 undefined 的区别

**一句话回答**
undefined 表示“未定义”，null 表示“空值”。

**要点**

- undefined：变量声明未赋值。
- null：手动赋值，表示空对象。
- typeof null === "object"。

**详细回答**

- undefined 出现在变量未赋值、函数无返回值、对象属性不存在。
- null 通常用来主动清空引用。

**扩展/对比**

- 面试官常问：“为什么要用 null 清空对象？” 👉 释放引用，方便 GC。

---

### 4. == 与 === 的区别 🔥

**一句话回答**
\== 会进行类型转换，=== 严格比较不转换。

**要点**

- \===：值和类型必须相同。
- \==：存在隐式转换规则。

**详细回答**

```js
0 == false; // true
0 === false; // false
null == undefined; // true
null === undefined; // false
```

**扩展/对比**

- 建议始终用 ===，避免隐式转换带来 bug。

---

### 5. 深拷贝与浅拷贝 🔥

**一句话回答**
浅拷贝只复制一层，深拷贝会递归复制所有层级。

**要点**

- 浅拷贝：Object.assign、展开运算符。
- 深拷贝：递归、JSON、structuredClone。

**详细回答**

```js
// 浅拷贝
const obj2 = { ...obj1 };

// 深拷贝
const deep = structuredClone(obj1);
```

**扩展/对比**

- 面试官常问：为什么 JSON 深拷贝有限制？ 👉 不能拷贝函数、Symbol、循环引用。

---

### 6. this 的绑定规则 🔥

**一句话回答**
this 绑定有默认、隐式、显式、new 绑定，箭头函数绑定外层。

**要点**

- 默认：全局 this。
- 隐式：调用对象。
- 显式：call/apply/bind。
- new：绑定到新对象。
- 箭头函数：继承外层。

**详细回答**

```js
function f() {
  console.log(this);
}
f(); // window (严格模式 undefined)
obj.f(); // obj
f.call(x); // x
new f(); // 新对象
```

**扩展/对比**

- 面试官可能问：“箭头函数能当构造函数吗？” 👉 不行，因为没有 this 和 prototype。

---

### 7. 执行上下文与作用域链 🔥

**一句话回答**
执行上下文是代码执行环境，作用域链决定变量查找规则。

**要点**

- 全局、函数、eval 三种上下文。
- VO/AO（变量对象/活动对象）。
- 作用域链：当前作用域 → 上层作用域 → 全局。

**详细回答**
执行上下文包括变量环境、词法环境、this 绑定。
作用域链是多层嵌套函数的变量查找规则。

**扩展/对比**

- 面试官常考“提升”机制：`var` 会提升，`let/const` 存在暂时性死区。

---

### 8. 闭包 🔥

**一句话回答**
闭包是函数和其外部词法作用域的组合。

**要点**

- 访问函数外变量。
- 避免全局变量污染。
- 典型应用：防抖、节流、私有变量。

**详细回答**

```js
function outer() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}
const fn = outer();
fn(); // 1
fn(); // 2
```

**扩展/对比**

- 面试官可能追问：“闭包可能导致什么问题？” 👉 内存泄漏。

---

### 9. 原型与原型链 🔥

**一句话回答**
每个对象都有原型，原型链是对象属性查找的链条。

**要点**

- `__proto__` → prototype。
- Object 是原型链顶端。
- 方法查找沿链向上。

**详细回答**

```js
function Foo() {}
const f = new Foo();
f.__proto__ === Foo.prototype; // true
```

**扩展/对比**

- 面试官常问：“class 是原型的语法糖吗？” 👉 是的，本质还是基于原型。

---

### 10. new 操作符的原理 🔥

**一句话回答**
new 会创建对象、绑定原型、执行构造函数并返回对象。

**要点**

- 创建空对象。
- 绑定原型。
- 执行构造函数。
- 返回对象。

**详细回答**

```js
function myNew(fn, ...args) {
  const obj = Object.create(fn.prototype);
  const res = fn.apply(obj, args);
  return res instanceof Object ? res : obj;
}
```

**扩展/对比**

- 面试官常考：手写 new 实现。

---

## 二、异步与事件循环

### 11. JS 事件循环机制 🔥

**一句话回答**
事件循环负责协调宏任务和微任务的执行，保证 JS 单线程的异步运行。

**要点**

- 单线程。
- 宏任务（setTimeout、setInterval、I/O）。
- 微任务（Promise.then、queueMicrotask、MutationObserver）。

**详细回答**
JS 执行栈清空后，先执行所有微任务队列，再取一个宏任务执行，然后继续循环。

```js
console.log(1);
setTimeout(() => console.log(2));
Promise.resolve().then(() => console.log(3));
console.log(4);
// 输出：1 4 3 2
```

**扩展/对比**

- 面试官常问：“Node.js 的 Event Loop 和浏览器一样吗？” 👉 不一样，Node.js 基于 libuv，有额外阶段（timers、I/O、check）。

---

### 12. Promise 的实现原理 🔥

**一句话回答**
Promise 是一个状态机，状态只能从 pending → fulfilled/rejected，一旦确定不可逆。

**要点**

- 三态：pending / fulfilled / rejected。
- then/catch/finally。
- 链式调用。

**详细回答**
Promise 内部用回调队列存储 then，状态变化后执行。
手写简易 Promise：

```js
class MyPromise {
  constructor(fn) {
    this.state = "pending";
    this.value = null;
    this.callbacks = [];
    fn(this.resolve.bind(this));
  }
  resolve(v) {
    if (this.state === "pending") {
      this.state = "fulfilled";
      this.value = v;
      this.callbacks.forEach((cb) => cb(v));
    }
  }
  then(cb) {
    if (this.state === "fulfilled") cb(this.value);
    else this.callbacks.push(cb);
  }
}
```

**扩展/对比**

- 面试官可能要求“手写 Promise.all”。

---

### 13. async/await 的本质 🔥

**一句话回答**
async/await 是基于 Promise 的语法糖，让异步代码看起来像同步。

**要点**

- async 返回 Promise。
- await 等待 Promise 结果。
- try/catch 捕获异常。

**详细回答**

```js
async function foo() {
  try {
    const data = await fetch("/api");
    return data.json();
  } catch (e) {
    console.error(e);
  }
}
```

**扩展/对比**

- 面试官常问：“await 会阻塞线程吗？” 👉 不会阻塞 JS 线程，只是暂停当前 async 函数的执行。

---

### 14. 宏任务和微任务的执行顺序 🔥

**一句话回答**
每个宏任务执行完后，立即清空微任务队列，再进入下一个宏任务。

**要点**

- 微任务优先。
- 经典顺序题。

**详细回答**

```js
setTimeout(() => console.log("宏1"));
Promise.resolve().then(() => console.log("微1"));
console.log("主");
// 输出：主 → 微1 → 宏1
```

**扩展/对比**

- 面试官喜欢考“顺序输出题”，要熟练分析。

---

### 15. Event Loop 在 Node.js 与浏览器的区别

**一句话回答**
浏览器只有宏/微任务，Node.js Event Loop 基于 libuv，有更多阶段。

**要点**

- 浏览器：宏任务+微任务。
- Node.js：timers → pending → idle → poll → check → close。

**详细回答**
Node.js 的 Promise 微任务依然优先，但宏任务分多个阶段。

**扩展/对比**

- 面试官可能问：“setImmediate vs setTimeout？” 👉 setImmediate 在 check 阶段，setTimeout 在 timers 阶段。

---

## 三、面向对象与函数

### 16. 继承的几种方式 🔥

**一句话回答**
常见继承方式：原型链、构造函数、组合继承、ES6 class extends。

**要点**

- 原型链：子类 prototype = 父类实例。
- 构造函数：父类函数 call 子类。
- 组合继承：两者结合。
- class extends：语法糖。

**详细回答**

```js
class A {
  constructor(x) {
    this.x = x;
  }
}
class B extends A {
  constructor(x, y) {
    super(x);
    this.y = y;
  }
}
```

**扩展/对比**

- 现代开发首选 class extends。

---

### 17. call、apply、bind 的区别与实现 🔥

**一句话回答**
call 和 apply 立即执行，bind 返回新函数。

**要点**

- call：参数列表。
- apply：参数数组。
- bind：返回绑定 this 的新函数。

**详细回答**

```js
fn.call(obj, 1, 2);
fn.apply(obj, [1, 2]);
const newFn = fn.bind(obj);
```

**扩展/对比**

- 面试官常考“手写 bind”。

---

### 18. 防抖与节流 🔥

**一句话回答**
防抖：一定时间内只触发一次；节流：一定时间内触发一次。

**要点**

- 防抖：如输入搜索。
- 节流：如滚动监听。

**详细回答**

```js
function debounce(fn, delay) {
  let timer = null;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
function throttle(fn, delay) {
  let last = 0;
  return (...args) => {
    const now = Date.now();
    if (now - last > delay) {
      fn(...args);
      last = now;
    }
  };
}
```

**扩展/对比**

- 防抖强调“最后一次”，节流强调“间隔执行”。

---

### 19. 柯里化函数（Currying） 🔥

**一句话回答**
柯里化是把多参数函数转换成一系列单参数函数。

**要点**

- 分步调用。
- 参数复用。
- 提高函数通用性。

**详细回答**

```js
function curry(fn) {
  return function curried(...args) {
    return args.length >= fn.length
      ? fn(...args)
      : (...rest) => curried(...args, ...rest);
  };
}
```

**扩展/对比**

- 常用于函数式编程。

---

### 20. 深度比较（deepEqual）的实现

**一句话回答**
deepEqual 比较对象/数组的值是否完全相等，而不仅是引用。

**要点**

- 递归遍历。
- 特殊值处理。
- 循环引用处理。

**详细回答**

```js
function deepEqual(a, b) {
  if (a === b) return true;
  if (typeof a !== "object" || typeof b !== "object") return false;
  const keysA = Object.keys(a),
    keysB = Object.keys(b);
  if (keysA.length !== keysB.length) return false;
  return keysA.every((k) => deepEqual(a[k], b[k]));
}
```

**扩展/对比**

- 面试官可能让你写“深比较实现”。

---

## 四、ES6+ 与新特性

### 21. let / const / var 的区别 🔥

**一句话回答**
var 有函数作用域和变量提升，let/const 有块级作用域和暂时性死区，const 不可重新赋值。

**要点**

- var：函数作用域，声明提升，默认值 undefined。
- let：块级作用域，TDZ。
- const：必须初始化，不可重新赋值。

**详细回答**

```js
console.log(a); // undefined
var a = 1;

console.log(b); // ReferenceError
let b = 2;
```

let/const 在块作用域内更安全，避免变量提升问题。

**扩展/对比**

- 面试官常问：“const 对象能改属性吗？” 👉 可以，const 只是绑定引用。

---

### 22. 解构赋值与扩展运算符

**一句话回答**
解构用于快速提取值，扩展运算符合并或展开数组/对象。

**要点**

- 解构：数组/对象解构。
- 扩展：浅拷贝。

**详细回答**

```js
const [a, b] = [1, 2];
const { x, ...rest } = { x: 1, y: 2, z: 3 };
```

**扩展/对比**

- 扩展运算符只做浅拷贝。

---

### 23. Symbol 与 BigInt

**一句话回答**
Symbol 是唯一值，BigInt 解决超大整数问题。

**要点**

- Symbol：对象属性唯一 key。
- BigInt：超过 2^53 的安全整数。

**详细回答**

```js
const s = Symbol("id");
const big = 12345678901234567890n;
```

**扩展/对比**

- 面试官可能问：“Symbol 能被遍历吗？” 👉 不会被 for...in 遍历。

---

### 24. Proxy 与 Reflect 🔥

**一句话回答**
Proxy 用于拦截对象操作，Reflect 提供原生方法支持。

**要点**

- Proxy：拦截 get/set。
- Reflect：与 Proxy 配合。

**详细回答**

```js
const obj = { a: 1 };
const p = new Proxy(obj, {
  get(t, k) {
    return k in t ? t[k] : 0;
  },
});
console.log(p.b); // 0
```

**扩展/对比**

- Vue3 响应式基于 Proxy。

---

### 25. Iterator 与 Generator

**一句话回答**
Iterator 是遍历协议，Generator 是语法糖生成迭代器。

**要点**

- Iterator：必须实现 next()。
- Generator：`function*`，可 yield 值。

**详细回答**

```js
function* gen() {
  yield 1;
  yield 2;
}
const it = gen();
console.log(it.next()); // {value:1,done:false}
```

**扩展/对比**

- 面试官可能问：“Generator 和 async/await 区别？” 👉 async/await 基于 Generator 封装。

---

### 26. Map、Set、WeakMap、WeakSet 的区别 🔥

**一句话回答**
Map/Set 存储强引用，WeakMap/WeakSet 存储弱引用，不阻止 GC。

**要点**

- Map：键值对。
- Set：唯一值集合。
- WeakMap/WeakSet：键必须是对象，弱引用。

**详细回答**

```js
const wm = new WeakMap();
let obj = {};
wm.set(obj, "val");
obj = null; // 可被回收
```

**扩展/对比**

- WeakMap 常用于存储私有数据。

---

### 27. 模块化方案（CommonJS vs ES Module） 🔥

**一句话回答**
CommonJS 是运行时同步加载，ESM 是编译时静态分析，支持 tree-shaking。

**要点**

- CommonJS：require/module.exports。
- ESM：import/export。
- ESM 支持静态依赖分析。

**详细回答**

- CommonJS 同步加载，不适合浏览器。
- ESM 支持静态分析，可优化打包。

**扩展/对比**

- 面试官常问：“为什么 ESM 更适合前端？” 👉 支持按需加载、tree-shaking。

---

## 五、性能与工程化

### 28. 垃圾回收机制（GC） 🔥

**一句话回答**
JS 使用自动垃圾回收，常见算法有标记清除和引用计数。

**要点**

- 标记清除：标记可达对象。
- 引用计数：被引用数为 0 即回收。

**详细回答**

- V8 主要用标记清除 + 分代回收。

**扩展/对比**

- 引用计数可能导致循环引用内存泄漏。

---

### 29. 内存泄漏的场景与排查方式 🔥

**一句话回答**
常见场景：未清除的定时器、闭包滥用、全局变量、DOM 引用未释放。

**要点**

- setInterval 未清除。
- 全局变量。
- 闭包引用过多。

**详细回答**

- 使用 Chrome Performance/Memory 工具排查。

**扩展/对比**

- React 项目中，常见泄漏是组件卸载未清理事件监听。

---

### 30. V8 引擎的优化机制 🔥

**一句话回答**
V8 通过隐藏类、内联缓存、JIT 编译来优化执行速度。

**要点**

- 隐藏类：对象形状。
- 内联缓存：缓存函数调用位置的类型信息。
- JIT：热点代码编译为机器码。

**详细回答**

- V8 会根据对象属性形状优化访问速度。
- 内联缓存减少查找开销。

**扩展/对比**

- 面试官可能追问：“为什么对象属性最好固定顺序？” 👉 保持隐藏类稳定，提高性能。
