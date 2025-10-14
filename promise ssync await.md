# Promise

## 一、Promise 是什么？

`Promise` 是 JavaScript 中用于**处理异步操作**的对象。
 它可以让异步任务以“同步写法”表达，避免层层嵌套的“回调地狱”。

### Promise 的三种状态

| 状态        | 含义             | 是否可变   |
| ----------- | ---------------- | ---------- |
| `pending`   | 初始状态，进行中 | ✅ 可变     |
| `fulfilled` | 操作成功完成     | ❌ 不可再变 |
| `rejected`  | 操作失败         | ❌ 不可再变 |

状态一旦改变（从 pending → fulfilled 或 rejected）就**不可逆**。

------

## 二、基本语法

```js
const promise = new Promise((resolve, reject) => {
  // 一些异步操作
  const success = true

  if (success) {
    resolve('操作成功')
  } else {
    reject('操作失败')
  }
})
```

使用方式：

```js
promise
  .then(result => console.log(result))  // 成功回调
  .catch(error => console.error(error)) // 失败回调
  .finally(() => console.log('操作结束')) // 无论成功或失败都会执行
```

------

## 三、链式调用（then 链）

`then()` 会返回一个**新的 Promise**，从而可以链式调用：

```js
new Promise(resolve => resolve(1))
  .then(num => {
    console.log(num) // 1
    return num + 1
  })
  .then(num => {
    console.log(num) // 2
    return num + 1
  })
  .then(num => {
    console.log(num) // 3
  })
```

> 🔹 注意：
>
> - 每个 `.then()` 都返回新的 Promise；
> - 若没有 `return`，下一个 then 会接收到 `undefined`。

------

## 四、注意事项

| 注意点                                       | 说明                                        |
| -------------------------------------------- | ------------------------------------------- |
| **必须调用 resolve/reject**                  | 否则 Promise 永远不会结束                   |
| **建议在 then 中返回值（用于向下传递数据）** | 否则链式调用中会丢失数据（得到 undefined）  |
| **错误会冒泡**                               | 如果不在 then 中捕获，会被下一个 catch 捕获 |
| **finally 不影响值传递**                     | 只做清理操作                                |
| **返回 Promise 会被等待**                    | then 返回的 Promise 会“展开等待”            |

------

## 五、常用静态方法

| 方法                        | 说明                                                   |
| --------------------------- | ------------------------------------------------------ |
| `Promise.resolve(value)`    | 返回一个成功的 Promise                                 |
| `Promise.reject(reason)`    | 返回一个失败的 Promise                                 |
| `Promise.all([...])`        | 全部成功才成功，任意一个失败则失败                     |
| `Promise.race([...])`       | 谁先完成（成功或失败）就返回谁的结果                   |
| `Promise.allSettled([...])` | 等所有都结束（成功或失败），返回每个状态               |
| `Promise.any([...])`        | 只要有一个成功就成功，全部失败才报错（AggregateError） |

------

## 六、手写 Promise 实现（简易 + 完整）

其他事例

```js
// 得到的是一个成功的Promise对象
Promise.resolve = function (val) {
    return new Promise((resolve, reject) => {
        resolve(val);
    });
};

// 得到的是一个失败的Promise对象
Promise.reject = function (val) {
    return new Promise((resolve, reject) => {
        reject(val);
    });
};

// race：竞速原则，谁先满足条件，谁先处理，其他忽略
// Promise.race([promise1,promise2,promise3]).then(res=>{})
Promise.race = function (promises) {
    return new Promise((resolve, reject) => {
        // 遍历promises，看谁先满足条件就先处理
        // Promise的状态只能改变一次，只要第一个修改了，后续再改也没用
        promises.forEach((p) => {
            p.then(resolve, reject);
        });
    });
};

// all：等待原则，传入多个promise，等所有的promise都满足条件，返回所有的成功结果
Promise.all = function (promises) {
    let arrResult = []; // 存放结果
    let count = 0; // 计数器

    function processData(index, val, resolve) {
        arrResult[index] = val; // 按顺序存取结果
        count++;
        if (count === promises.length) {
            resolve(arrResult);
        }
    }

    return new Promise((resolve, reject) => {
        for (let i = 0; i < promises.length; i++) {
            promises[i].then((res) => {
                processData(i, res, resolve);
            }, reject);
        }
    });
};

let p1 = new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000));
let p2 = new Promise((resolve, reject) => setTimeout(() => resolve(2), 2000));
let p3 = new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000));

Promise.race([p1, p2, p3]).then((res) => {
    console.log(res); 1
});

Promise.all([p1, p2, p3]).then((res) => {
    console.log(res); [1,2,3]
});
```





这是一个符合 Promise/A+ 思想的简易实现👇
 （包含：then、catch、finally、resolve、reject、all、race、allSettled、any）

```js
const queueMicrotask = globalThis.queueMicrotask || (fn => Promise.resolve().then(fn))

class MyPromise {
    constructor(executor) {
        this.state = 'pending'
        this.value = undefined
        this.reason = undefined
        this.onFulfilledCallbacks = []
        this.onRejectedCallbacks = []

        const resolve = value => {
            if (this.state !== 'pending') return
            this.state = 'fulfilled'
            this.value = value
            this.onFulfilledCallbacks.forEach(fn => fn(value))
        }

        const reject = reason => {
            if (this.state !== 'pending') return
            this.state = 'rejected'
            this.reason = reason
            this.onRejectedCallbacks.forEach(fn => fn(reason))
        }

        try {
            executor(resolve, reject)
        } catch (err) {
            reject(err)
        }
    }

    then(onFulfilled, onRejected) {
        onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
        onRejected = typeof onRejected === 'function' ? onRejected : e => { throw e }

        const p2 = new MyPromise((resolve, reject) => {
            const wrap = (cb, arg) => queueMicrotask(() => {
                try {
                    const x = cb(arg)
                    resolvePromise(p2, x, resolve, reject)
                } catch (e) {
                    reject(e)
                }
            })
            if (this.state === 'fulfilled') wrap(onFulfilled, this.value)
            else if (this.state === 'rejected') wrap(onRejected, this.reason)
            else {
                this.onFulfilledCallbacks.push(v => wrap(onFulfilled, v))
                this.onRejectedCallbacks.push(r => wrap(onRejected, r))
            }
        })

        return p2
    }

    catch(onRejected) {
        return this.then(undefined, onRejected)
    }

    finally(onFinally) {
        const handler = typeof onFinally === 'function' ? onFinally : () => { }
        return this.then(
            value => MyPromise.resolve(handler()).then(() => value),
            reason => MyPromise.resolve(handler()).then(() => { throw reason })
        )
    }

    // ---------- 静态方法 ----------
    static resolve(value) {
        if (value instanceof MyPromise) return value
        return new MyPromise(resolve => resolve(value))
    }

    static reject(reason) {
        return new MyPromise((_, reject) => reject(reason))
    }

    static all(iterable) {
        const items = Array.from(iterable)
        const results = []
        let count = 0
        return new MyPromise((resolve, reject) => {
            items.forEach((p, i) => {
                MyPromise.resolve(p).then(
                    v => {
                        results[i] = v
                        if (++count === items.length) resolve(results)
                    },
                    err => reject(err)
                )
            })
        })
    }

    static race(iterable) {
        return new MyPromise((resolve, reject) => {
            for (const p of iterable) {
                MyPromise.resolve(p).then(resolve, reject)
            }
        })
    }

    static allSettled(iterable) {
        const items = Array.from(iterable)
        const results = []
        let count = 0
        return new MyPromise(resolve => {
            items.forEach((p, i) => {
                MyPromise.resolve(p)
                    .then(v => (results[i] = { status: 'fulfilled', value: v }))
                    .catch(e => (results[i] = { status: 'rejected', reason: e }))
                    .finally(() => {
                        if (++count === items.length) resolve(results)
                    })
            })
        })
    }

    static any(iterable) {
        const items = Array.from(iterable)
        const errors = []
        let failed = 0
        return new MyPromise((resolve, reject) => {
            items.forEach((p, i) => {
                MyPromise.resolve(p).then(resolve, err => {
                    errors[i] = err
                    if (++failed === items.length)
                        reject(new AggregateError(errors, 'All promises were rejected'))
                })
            })
        })
    }
}

function resolvePromise(p2, x, resolve, reject) {
    try {
        console.log(p2, x, p2 === x)
        if (p2 === x) {
            throw new TypeError('Chaining cycle detected')
        }
        if (x instanceof MyPromise) {
            x.then(resolve, reject)
        } else {
            resolve(x)
        }
    } catch (err) {
        reject(err)
    }
}

const p = new MyPromise(resolve => resolve())

const p2 = p.then(() => {
    // ❌ 返回了自己
    console.log(123)
    return p2
})

p2.catch(e => console.error('caught:', e.message));

```

---

#### 1）构造器 `new MyPromise(executor)`

**含义**
 创建 Promise；`executor` 立即执行，接收 `resolve`/`reject` 两个回调；状态仅能从 `pending` → `fulfilled/rejected`，且**不可逆**。

**使用用例**

```js
const p = new MyPromise((resolve, reject) => {
  setTimeout(() => resolve('OK'), 200)
})
p.then(v => console.log(v)) // 'OK'
```

**实现片段**

```js
class MyPromise {
  constructor(executor) {
    this.state = 'pending'
    this.value = undefined
    this.reason = undefined
    this.onFulfilled = []
    this.onRejected = []

    const resolve = value => {
      if (this.state !== 'pending') return
      this.state = 'fulfilled'
      this.value = value
      this.onFulfilled.forEach(fn => fn(value))
    }

    const reject = reason => {
      if (this.state !== 'pending') return
      this.state = 'rejected'
      this.reason = reason
      this.onRejected.forEach(fn => fn(reason))
    }

    try { executor(resolve, reject) } catch (e) { reject(e) }
  }
}
```

------

#### 2）`then(onFulfilled, onRejected)`

**含义**
 注册成功/失败回调；**返回新的 Promise**；返回普通值会自动被“包”成 `Promise.resolve(value)`；返回 Promise 会“等待”其落定；异常会进入下一个 `catch`。

**使用用例**

```js
MyPromise.resolve(1)
  .then(v => v + 1)     // 2
  .then(v => Promise.resolve(v + 1)) // 3
  .then(v => { throw new Error('boom') })
  .catch(e => console.log(e.message)) // 'boom'
```

**实现片段（核心是微任务与“展开”逻辑）**

```js
const queueMicrotask = globalThis.queueMicrotask || (fn => Promise.resolve().then(fn))

then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
    onRejected = typeof onRejected === 'function' ? onRejected : e => { throw e }

    const p2 = new MyPromise((resolve, reject) => {
        const wrap = (cb, arg) => queueMicrotask(() => {
            try {
                const x = cb(arg)
                resolvePromise(p2, x, resolve, reject)
            } catch (e) {
                reject(e)
            }
        })
        if (this.state === 'fulfilled') wrap(onFulfilled, this.value)
        else if (this.state === 'rejected') wrap(onRejected, this.reason)
        else {
            this.onFulfilledCallbacks.push(v => wrap(onFulfilled, v))
            this.onRejectedCallbacks.push(r => wrap(onRejected, r))
        }
    })

    return p2
}
```

------

#### 3）`catch(onRejected)`

**含义**
 `then(undefined, onRejected)` 的语法糖；用于捕获链上任何阶段抛出的错误。

**使用用例**

```js
MyPromise.reject('err')
  .catch(e => 'recovered')  // 返回普通值 → 进入后续 then
  .then(v => console.log(v)) // 'recovered'
```

**实现片段**

```js
catch(onRejected) {
  return this.then(undefined, onRejected)
}
```

------

#### 4）`finally(onFinally)`

**含义**
 不改变链上传递的最终值/错误；用于清理；若 `onFinally` 返回 Promise，会**等待**它完成，再把原值/原错继续向下传。

**使用用例**

```js
MyPromise.resolve('OK')
  .finally(() => new MyPromise(res => setTimeout(res, 100)))
  .then(v => console.log(v)) // 'OK'（等 finally 的异步收尾结束后再继续）
```

**实现片段**

```js
finally(onFinally) {
  const h = typeof onFinally === 'function' ? onFinally : () => {}
  return this.then(
    v => MyPromise.resolve(h()).then(() => v),
    e => MyPromise.resolve(h()).then(() => { throw e })
  )
}
```

------

#### 5）`MyPromise.resolve(value)`

**含义**
 把任意值“提升”为 Promise；若本身已是 `MyPromise`，直接返回。

**使用用例**

```js
MyPromise.resolve(42).then(v => console.log(v)) // 42
```

**实现片段**

```js
static resolve(x) {
  if (x instanceof MyPromise) return x
  return new MyPromise(res => res(x))
}
```

------

#### 6）`MyPromise.reject(reason)`

**含义**
 返回一个立即 `rejected` 的 Promise。

**使用用例**

```js
MyPromise.reject('Nope').catch(e => console.log(e)) // 'Nope'
```

**实现片段**

```js
static reject(r) {
  return new MyPromise((_, rej) => rej(r))
}
```

------

#### 7）`MyPromise.all(iterable)`

**含义**
 全部成功才成功（按输入顺序返回数组结果）；任一失败则立刻失败。

**使用用例**

```js
MyPromise.all([MyPromise.resolve(1), 2, 3])
  .then(arr => console.log(arr)) // [1, 2, 3]
```

**实现片段**

```js
static all(iter) {
  const arr = Array.from(iter)
  if (arr.length === 0) return MyPromise.resolve([])
  return new MyPromise((resolve, reject) => {
    const out = new Array(arr.length)
    let done = 0
    arr.forEach((it, i) => {
      MyPromise.resolve(it).then(
        v => {
          out[i] = v
          if (++done === arr.length) resolve(out)
        },
        reject
      )
    })
  })
}
```

------

#### 8）`MyPromise.race(iterable)`

**含义**
 谁先落定（成功/失败）就用谁的结果；空迭代通常返回永远 `pending` 的 Promise。

**使用用例**

```js
MyPromise.race([
  new MyPromise(res => setTimeout(() => res('A'), 50)),
  new MyPromise(res => setTimeout(() => res('B'), 100))
]).then(v => console.log(v)) // 'A'
```

**实现片段**

```js
static race(iter) {
  const arr = Array.from(iter)
  if (arr.length === 0) return new MyPromise(() => {})
  return new MyPromise((resolve, reject) => {
    arr.forEach(it => MyPromise.resolve(it).then(resolve, reject))
  })
}
```

------

#### 9）`MyPromise.allSettled(iterable)`

**含义**
 等待所有都“落定”；返回每项 `{status, value}` 或 `{status, reason}`。

**使用用例**

```js
MyPromise.allSettled([MyPromise.resolve(1), MyPromise.reject('x')])
  .then(res => console.log(res))
// [{status:'fulfilled', value:1}, {status:'rejected', reason:'x'}]
```

**实现片段**

```js
static allSettled(iter) {
  const arr = Array.from(iter)
  if (arr.length === 0) return MyPromise.resolve([])
  return new MyPromise(resolve => {
    const out = new Array(arr.length)
    let done = 0
    arr.forEach((it, i) => {
      MyPromise.resolve(it)
        .then(v => { out[i] = { status: 'fulfilled', value: v } })
        .catch(e => { out[i] = { status: 'rejected', reason: e } })
        .finally(() => { if (++done === arr.length) resolve(out) })
    })
  })
}
```

------

#### 10）`MyPromise.any(iterable)`

**含义**
 只要有一个成功就成功；**全部失败**时，抛出 `AggregateError`。

**使用用例**

```js
MyPromise.any([MyPromise.reject('a'), MyPromise.resolve('ok')])
  .then(v => console.log(v)) // 'ok'
```

**实现片段**

```js
static any(iter) {
  const arr = Array.from(iter)
  if (arr.length === 0) {
    return MyPromise.reject(new AggregateError([], 'All promises were rejected'))
  }
  return new MyPromise((resolve, reject) => {
    const errors = new Array(arr.length)
    let failed = 0
    arr.forEach((it, i) => {
      MyPromise.resolve(it).then(
        resolve,
        e => {
          errors[i] = e
          if (++failed === arr.length) {
            reject(new AggregateError(errors, 'All promises were rejected'))
          }
        }
      )
    })
  })
}
```

------

#### 11）结果“展开”辅助：`resolvePromise(x, resolve, reject)`

**含义**
 把 `then` 回调返回的 `x`（普通值 / MyPromise）**展开**成 `resolve`/`reject` 的最终落定。

**实现片段**

```js
function resolvePromise(x, resolve, reject) {
  try {
    if (x instanceof MyPromise) x.then(resolve, reject)
    else resolve(x)
  } catch (e) {
    reject(e)
  }
}
```

------

## 七、运行示例

```js
MyPromise.resolve(1)
  .then(v => v + 1)
  .then(v => {
    console.log('结果:', v)
    throw new Error('出错了')
  })
  .catch(e => console.error('捕获:', e.message))
  .finally(() => console.log('清理完成'))
```

输出：

```
结果: 2
捕获: 出错了
清理完成
```

------

## 八、总结

| 分类         | 说明                                        |
| ------------ | ------------------------------------------- |
| **核心用途** | 处理异步逻辑，避免回调地狱                  |
| **状态流转** | pending → fulfilled / rejected（不可逆）    |
| **关键方法** | then、catch、finally                        |
| **静态方法** | resolve、reject、all、race、allSettled、any |
| **错误机制** | 自动冒泡至最近的 catch                      |
| **实现要点** | 状态单向、微任务执行、链式返回新 Promise    |

------

非常好，这次我给你一份更**系统化、底层化、可面试级别使用**的
 `async / await` 深入版知识整合笔记。

它包含：

1. 核心语法与行为机制
2. 底层运行原理（事件循环与微任务）
3. async/await 实现原理（Generator 自动执行器）
4. 手写实现全过程
5. 细节陷阱与优化建议

------

# async / await

------

## 一、概念总览

`async/await` 是基于 **Promise** 实现的语法糖，用于让异步逻辑看起来像同步逻辑。
 它的核心目标是：**让异步流程更易读、更可控、更接近同步代码结构**。

------

## 二、核心语法特性

### async 函数

- 任何被 `async` 修饰的函数都会返回一个 Promise。
- 若显式返回一个值，会被包装为 `Promise.resolve(value)`。
- 若抛出异常，则会返回 `Promise.reject(error)`。

示例：

```js
async function foo() {
  return 42
}
foo().then(console.log) // => 42

async function bar() {
  throw new Error('出错了')
}
bar().catch(err => console.log(err.message)) // => 出错了
```

------

### await 表达式

- 用于**暂停 async 函数的执行**，直到等待的 Promise 完成。
- 只能在 async 函数中使用。
- 右侧的值会被自动包装为 Promise.resolve(x)。

示例：

```js
async function test() {
  console.log('A')
  await null  // await Promise.resolve(null)
  console.log('B')
}
test()
console.log('C')
// 输出：A → C → B
```

👉 await 后面的代码会进入**微任务队列**。

------

## 三、事件循环（Event Loop）中的执行机制

理解 async/await 一定要结合 **事件循环与微任务机制**：

1. **执行 async 函数**，遇到第一个 await：
   - 当前函数暂停；
   - await 表达式的 Promise 被放入微任务队列；
2. **主线程继续执行同步任务**；
3. 同步任务完成后，执行微任务（例如 await 后的回调）；
4. 若有多个 await，则依次被放入微任务队列执行。

示例：

```js
console.log(1)
async function f() {
  console.log(2)
  await Promise.resolve()
  console.log(3)
}
f()
console.log(4)
```

输出顺序：

```
1
2
4
3
```

> 原因：await 后的部分在当前微任务队列尾部执行。

------

## 四、顺序执行 vs 并行执行

### ❌ 顺序执行（慢）

```js
await task1()
await task2()
await task3()
```

每一步都等待前一步完成。

### ✅ 并行执行（快）

```js
const [a, b, c] = await Promise.all([task1(), task2(), task3()])
```

三个任务同时开始执行，整体等待最慢的那个完成。

------

## 五、async/await 的底层原理

`async/await` 本质上由 **Generator + 自动执行器（runtime）** 实现。

**概念流程**

1. `async function` 会被 Babel 等编译器转译为 Generator 函数；
2. 每个 `await` 会被替换为 `yield`；
3. JavaScript 引擎内部使用自动执行器（类似 `co` 库）来自动运行这些 `yield Promise`；
4. 每个 `yield` 的 Promise 结果被取出，再继续执行下一步；
5. 最终返回一个 Promise。

------

**举个例子（转译思想）**

```js
async function foo() {
  const a = await 1
  const b = await 2
  return a + b
}
```

转译成：

```js
function foo() {
  return new Promise((resolve, reject) => {
    const gen = (function* () {
      const a = yield 1
      const b = yield 2
      return a + b
    })()

    function step(key, arg) {
      let res
      try {
        res = gen[key](arg)
      } catch (e) {
        return reject(e)
      }
      const { value, done } = res
      if (done) return resolve(value)
      Promise.resolve(value).then(
        v => step('next', v),
        e => step('throw', e)
      )
    }

    step('next')
  })
}
```

------

## 六、手写实现 async/await（完整版）

这就是核心的“自动执行器（Generator Runner）”。

```js
function asyncToGenerator(generatorFn) {
  return function (...args) {
    const gen = generatorFn.apply(this, args)

    return new Promise((resolve, reject) => {
      function step(key, arg) {
        let result
        try {
          result = gen[key](arg)
        } catch (err) {
          return reject(err)
        }

        const { value, done } = result
        if (done) return resolve(value)

        Promise.resolve(value).then(
          val => step('next', val),
          err => step('throw', err)
        )
      }

      step('next')
    })
  }
}
```

------

**使用示例**

```js
function* myTask() {
  const a = yield Promise.resolve(1)
  const b = yield Promise.resolve(a + 1)
  const c = yield Promise.resolve(b + 1)
  return c
}

const run = asyncToGenerator(myTask)
run().then(console.log) // 输出 3
```

------

## 七、完整执行过程解析

以 `myTask` 为例：

 1️⃣ 执行 `asyncToGenerator(myTask)` 得到一个函数。
 2️⃣ 调用时返回一个 Promise，内部运行 `gen.next()`。
 3️⃣ 取出第一个 yield（Promise.resolve(1)），等待它 resolve。
 4️⃣ 将结果传回 generator，继续执行下一步。
 5️⃣ 重复这个过程直到 `done === true`。
 6️⃣ resolve 最终结果。

这就是 `await` 暂停 + 继续 的底层实现机制。

------

## 八、async/await 的核心特性总结

| 特性       | 说明                                          |
| ---------- | --------------------------------------------- |
| 语法糖     | 基于 Promise 封装                             |
| 执行顺序   | await 会暂停 async 函数，后续语句进微任务队列 |
| 错误处理   | 使用 try...catch 捕获 rejected Promise        |
| 并行优化   | 用 Promise.all 提升性能                       |
| 底层原理   | Generator + Promise 自动执行器                |
| 编译后结构 | Babel 转译成 yield 语法与 state machine       |

------

## 九、常见陷阱与注意事项

| 场景                     | 问题                                 | 解决方案                             |
| ------------------------ | ------------------------------------ | ------------------------------------ |
| for 循环里使用 await     | 每次循环都会等待上一次               | 使用 `Promise.all()` 或 `map(async)` |
| 忘记 try...catch         | await 抛出 rejected Promise 时报错   | 使用 try...catch 包裹                |
| await 不是 Promise       | JS 自动包装成 Promise.resolve(value) | 合理利用这个特性                     |
| async 函数中嵌套普通函数 | 普通函数不会自动返回 Promise         | 显式返回 Promise                     |

------

## 十、一句话总结

> `async/await` 是一种对 Promise 语法的“语义封装”，
>  让异步逻辑以同步形式表达，底层仍是基于 Promise 和微任务机制实现的。

------

# 面试题

### 1. Promise 是什么？🔥

**一句话回答**
 表示一个异步操作的最终结果，状态不可逆：`pending → fulfilled | rejected`。

**要点**

- 本质：一个状态机对象。
- 三态：`pending / fulfilled / rejected`。
- 状态只变一次，变更不可逆。

**详细回答**
 `Promise` 封装异步结果，提供 `.then/.catch/.finally` 注册回调。创建时接收 `executor(resolve, reject)`，由你在合适时机触发成功或失败。一旦从 `pending` 变为 `fulfilled` 或 `rejected`，就不会再变化。

**扩展/对比**

- 对比回调：Promise 可链式、可合并，避免回调地狱。
- 对比 `async/await`：后者是前者的语法糖。

------

### 2. Promise 的链式调用是如何工作的？

**一句话回答**
 `.then()` 总是返回**新的** Promise；回调返回值会被**传递**给下一个 `.then()`。

**要点**

- 不返回值 → 下一个 then 的入参为 `undefined`。
- 返回普通值 → 自动 `Promise.resolve(value)`。
- 返回 Promise → 等它 settle 后再传。
- 抛错/`Promise.reject` → 走 `.catch()`。

**详细回答**
 每个 `.then(onFulfilled, onRejected)` 会创建并返回新 Promise。当前回调的返回值（或抛出的异常）决定新 Promise 的命运：普通值→resolve；抛错→reject；返回 Promise→“展开等待”。

**扩展/对比**

- 建议统一用 `.then().catch()` 而非传第二参 `onRejected`，可读性更好。

------

### 3. Promise 的错误传播与 `.catch()` 行为？

**一句话回答**
 链上任一环抛错都会向下冒泡，被最近的 `.catch()` 捕获。

**要点**

- `.catch(fn)` 等价于 `.then(undefined, fn)`。
- `.catch` 后可继续 `.then`。
- `.finally` 不改变结果，仅清理。

**详细回答**
 如果某个 `.then` 中抛错/返回拒绝的 Promise，控制流会跳到最近的 `.catch`。在 `.catch` 里返回值则可恢复链；若再次抛错，继续向后冒泡。

**扩展/对比**

- `try...catch` 用于 `async/await`；`.catch` 用于 Promise 链。

------

### 4. `.finally()` 有什么语义？

**一句话回答**
 无论成功或失败都执行；不会改变链上最终值/错误。

**要点**

- 常用于关闭 loading、释放资源。
- 若 `finally` 返回 Promise，会**等待**它完成再传递原结果。

**详细回答**
 `promise.finally(onFinally)` 在 settle 后调用 `onFinally`。实现通常是：
 `then(v => Promise.resolve(onFinally()).then(() => v), e => Promise.resolve(onFinally()).then(() => { throw e }))`

**扩展/对比**

- 类似 `try...finally`；区别是这里的执行点是“异步 settle 时”。

------

### 5. Promise 常用静态方法（何时使用）？

**一句话回答**
 `resolve/reject` 创建；`all` 并行全成才成；`race` 谁先 settle 用谁；`allSettled` 汇总；`any` 任一成功就成功。

**要点**

- `resolve(x)`：包装为成功 Promise（x 为 thenable 会被展开）。
- `reject(e)`：立即拒绝。
- `all([...])`：有一个失败即失败。
- `race([...])`：第一个 settle 的结果。
- `allSettled([...])`：返回每个结果的状态数组。
- `any([...])`：任一成功就成功；全失败抛 `AggregateError`。

**详细回答**
 批处理选择：

- 聚合且要求全成功：`all`；
- 只要最先得到结果就行：`race`；
- 想要完整报告（不因失败短路）：`allSettled`；
- 多路冗余、容错：`any`。

**扩展/对比**

- `any` 与 `all` 互为“成功/失败容忍”的镜像。

------

### 6. async/await 是什么？

**一句话回答**
 基于 Promise 的语法糖，让异步代码以同步形式书写。

**要点**

- `async` 函数**必定返回 Promise**。
- `await` 会**暂停当前 async 函数**，等待表达式（会被 `Promise.resolve` 包装）。
- 错误用 `try...catch` 捕获。

**详细回答**
 `async` 相当于把返回值用 `Promise.resolve` 包装；函数内抛错等价 `Promise.reject`。`await` 右侧若是普通值也会自动转为已完成的 Promise，恢复后拿到结果继续执行。

**扩展/对比**

- 与 `.then()` 等价：`const x = await p` ≈ `p.then(x => ...)`。

------

### 7. await 的执行时机（事件循环视角）？

**一句话回答**
 `await` 将后续代码安排到**微任务**，先跑完同步代码与现有微任务，再继续。

**要点**

- 顺序：同步 → 微任务(then/await 后续) → 宏任务(setTimeout)。
- `await` 不阻塞线程，只暂停当前 `async` 函数。

**详细回答**
 遇到 `await`，当前 `async` 函数挂起；待右侧 Promise resolve 后，其后的代码入微任务队列。下一轮微任务阶段会继续执行。

**扩展/对比**

- `queueMicrotask` 与 `Promise.then` 后续同属微任务。

------

### 8. 用 async/await 做并发的正确写法？

**一句话回答**
 先并发发起，再用 `await Promise.all` 一次等待。

**要点**

- ❌ `await a(); await b();`（串行）
- ✅ `const [ra, rb] = await Promise.all([a(), b()])`（并发）

**详细回答**
 `await` 会暂停当前函数；连续写多个会导致串行。把 Promise 先创建好，再聚合等待可显著减少总时长。

**扩展/对比**

- 需要“部分失败也拿结果” → 用 `allSettled`。

------

### 9. async/await 的底层原理（为什么能“暂停”）？

**一句话回答**
 编译器把 `async/await` 转成 **Generator + 自动执行器**（或状态机）驱动的 Promise 链。

**要点**

- `await expr` ≈ `yield Promise.resolve(expr)`。
- 运行时用一个“runner”不断 `next/throw`，把每次 `yield` 的 Promise 结果塞回去。

**详细回答**
 Babel/TS 会把 `async` 函数编译成生成器和状态机代码；每个 `await` 位置编译成 `yield`，由一个自动执行器（类似 `co`）递归处理：拿到 Promise→等待→把值传回→推进到下一个 `yield`。最终返回一个 Promise。

**扩展/对比**

- 这就是“`await` 是 `.then` 语法糖”的更底层解释。

------

### 10. 手写 async/await 的核心思路（Generator Runner）

**一句话回答**
 写一个 `asyncToGenerator`：接收生成器函数，返回一个返回 Promise 的函数，内部递归驱动 `next/throw`。

**要点**

- `step('next', val)` / `step('throw', err)`。
- 对 `yield` 值做 `Promise.resolve`，等待后再推进。

**详细回答（核心实现）**

```js
function asyncToGenerator(genFn) {
  return function (...args) {
    const it = genFn.apply(this, args)
    return new Promise((resolve, reject) => {
      function step(key, arg) {
        let res
        try { res = it[key](arg) } catch (e) { return reject(e) }
        const { value, done } = res
        if (done) return resolve(value)
        Promise.resolve(value).then(
          v => step('next', v),
          e => step('throw', e)
        )
      }
      step('next')
    })
  }
}
```

**扩展/对比**

- 原生 `async` 的语义即是此思路的内建实现。

------

### 11. 手写 Promise 的关键点？

**一句话回答**
 实现状态机、回调队列、微任务调度、链式解析（返回值展开/错误冒泡）。

**要点**

- 状态单向：`pending → fulfilled|rejected`。
- then 返回新 Promise。
- 解析返回值：普通值/Promise/抛错。
- 回调异步执行（微任务）。

**详细回答（最小实现骨架）**

```js
const q = globalThis.queueMicrotask || (f => Promise.resolve().then(f))

class MyPromise {
  constructor(executor) {
    this.state='pending'; this.value=undefined; this.reason=undefined
    this.onF=[]; this.onR=[]
    const resolve = v => {
      if (this.state!=='pending') return
      this.state='fulfilled'; this.value=v; this.onF.forEach(fn=>fn(v))
    }
    const reject = e => {
      if (this.state!=='pending') return
      this.state='rejected'; this.reason=e; this.onR.forEach(fn=>fn(e))
    }
    try { executor(resolve, reject) } catch(e){ reject(e) }
  }
  then(onF, onR) {
    onF = typeof onF==='function' ? onF : v=>v
    onR = typeof onR==='function' ? onR : e=>{ throw e }
    return new MyPromise((resolve, reject) => {
      const handle = (cb, val, isErr) => q(() => {
        try { resolvePromise(cb(val), resolve, reject) }
        catch (e) { reject(e) }
      })
      if (this.state==='fulfilled') handle(onF, this.value, false)
      else if (this.state==='rejected') handle(onR, this.reason, true)
      else { this.onF.push(v=>handle(onF,v,false)); this.onR.push(e=>handle(onR,e,true)) }
    })
  }
  catch(onR){ return this.then(undefined,onR) }
  finally(fn){
    const f = typeof fn==='function' ? fn : () => {}
    return this.then(
      v => MyPromise.resolve(f()).then(()=>v),
      e => MyPromise.resolve(f()).then(()=>{ throw e })
    )
  }
  static resolve(x){ return x instanceof MyPromise ? x : new MyPromise(r=>r(x)) }
  static reject(e){ return new MyPromise((_,r)=>r(e)) }
  static all(arr){
    const A=[...arr], n=A.length; if(!n) return MyPromise.resolve([])
    return new MyPromise((res,rej)=>{
      const out=Array(n); let c=0
      A.forEach((p,i)=>MyPromise.resolve(p).then(v=>{ out[i]=v; if(++c===n) res(out) }, rej))
    })
  }
  static race(arr){
    return new MyPromise((res,rej)=>{ for(const p of arr) MyPromise.resolve(p).then(res,rej) })
  }
  static allSettled(arr){
    const A=[...arr], n=A.length; if(!n) return MyPromise.resolve([])
    return new MyPromise(res=>{
      const out=Array(n); let c=0
      A.forEach((p,i)=>MyPromise.resolve(p).then(
        v=>out[i]={status:'fulfilled',value:v},
        e=>out[i]={status:'rejected',reason:e}
      ).finally(()=>{ if(++c===n) res(out) }))
    })
  }
  static any(arr){
    const A=[...arr], n=A.length; if(!n) return MyPromise.reject(new AggregateError([],'All rejected'))
    return new MyPromise((res,rej)=>{
      const errs=Array(n); let r=0
      A.forEach((p,i)=>MyPromise.resolve(p).then(res, e=>{ errs[i]=e; if(++r===n) rej(new AggregateError(errs,'All rejected')) }))
    })
  }
}

function resolvePromise(x, resolve, reject){
  try {
    if (x instanceof MyPromise) x.then(resolve, reject)
    else resolve(x)
  } catch(e){ reject(e) }
}
```

**扩展/对比**

- 完全对齐 A+ 需 thenable 检测与自引用保护（此简化略去 thenable 递归与环检测）。

------

### 12. await 不是 Promise 会怎样？

**一句话回答**
 会被自动包装成 `Promise.resolve(value)`，立即当作已完成。

**要点**

- `await 42` 合法，得到 `42`。
- 这使得封装器更通用。

**详细回答**
 标准要求 `await` 对表达式做 `Promise.resolve`；如果值是 thenable，将被展开；否则立即继续、拿到该值。

**扩展/对比**

- 这与 `.then()` 中对返回值的“展开”一致。

------

### 13. async 函数里 try...catch 的边界？

**一句话回答**
 只能捕获**被 `await` 的 Promise** 的拒绝或同步抛错；未 await 的异步错误不会被当前 try...catch 捕获。

**要点**

- 旁支异步需单独 `.catch()` 或外层 `try...catch(await ...)`。

**详细回答**

```js
async function f() {
  try {
    doSomethingAsync().catch(handle) // 未 await，这个错误必须自己 catch
    const v = await mayReject()      // 这个能被 try...catch 捕获
  } catch(e) { /* ... */ }
}
```

**扩展/对比**

- Node 可用 `unhandledRejection` 监听未捕获拒绝。

------

### 14. 何时用 `all`，何时用 `allSettled/any/race`？

**一句话回答**
 看你的容错与需求：是否允许部分失败、要不要最快结果、是否要完整报告。

**要点**

- 全部必成：`all`。
- 最快即可：`race`。
- 需要每个条目状态：`allSettled`。
- 只要有成功：`any`。

**详细回答**
 例如“多个镜像源取一个最快”用 `race`；“CDN 备份方案取任一成功”用 `any`；“批处理后要展示成功/失败列表”用 `allSettled`；“强一致聚合加载”用 `all`。

**扩展/对比**

- UI 并发加载常用 `allSettled` 来避免单点失败影响整体渲染。

------

### 15. 事件循环相关的一道典型顺序题（心智锚点）

**一句话回答**
 `await` 后面的语句是**微任务**，会在同步任务与当前队列微任务之后、下一个宏任务之前运行。

**要点**

- 记忆：**同步 → 微 → 宏**。
- `then/await/queueMicrotask` 同属微任务。

**详细回答**

```js
console.log(1)
async function t(){ console.log(2); await null; console.log(3) }
t()
Promise.resolve().then(()=>console.log(4))
setTimeout(()=>console.log(5))
console.log(6)
// 输出：1,2,6,3,4,5 或 1,2,6,4,3,5（具体微任务相对顺序依实现）但总在 5 之前。
```

**扩展/对比**

- 不同引擎对同一轮中微任务插入顺序可能略有差异，但都在宏任务前。

------

