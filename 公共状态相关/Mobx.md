## 一、mobx 基本介绍

mobx 是一个简单、可扩展状态工具，相比 redux，具有以下特点

- 简洁、无模板代码（redux 需要写大量模板代码）
- 响应式数据，可直接修改（redux 需要保证不可变）
- 可直接处理异步（redux 需要中间件处理异步）
- 适合简单、规模不大的应用（redux 约束强，更适合大型多人协作开发）

**注**：mobx6 默认不开启修饰器语法

### 核心原理

利用**defineProperty**(<=v5)或**Proxy**(v6)拦截对象属性的变化，实现数据的**Observable**，在 get 中依赖收集，set 中触发依赖绑定的监听函数。
假如你之前关注过 Vue.js、Knockout 等的一些 MVVM 框架的响应式原理，那么你应该会感到非常熟悉。是的，它们的原理如出一辙。

## 二、mobx 的使用

### 1、搭建项目 mobx mobx-react-lite

新建一个 ts 的 react 项目

```shell
npm create vite@latest

> npx
> create-vite

│
◇  Project name:
│  react-mobx-demo
│
◇  Select a framework:
│  React
│
◇  Select a variant:
│  TypeScript + SWC
│
◇  Use rolldown-vite (Experimental)?:
│  No
│
◇  Install with npm and start now?
│  No
│
◇  Scaffolding project in /Users/lizhanpeng/Desktop/demo/react-mobx-demo...
│
└  Done. Now run:

  cd react-mobx-demo
  yarn
  yarn dev
```

安装 mobx 库

```shell
yarn add mobx mobx-react-lite --save
```

- mobx-react 可支持类组件和函数组件；
- 轻量级的 mobx-react-lite 只支持函数组件
  - 去掉了对class components的支持，
  - 并且移除了provider、inject：这两个HOC在React官方已经提供了React.createContext之后变得不是那么必要了

### 2、核心概念

![在这里插入图片描述](https://zh.mobx.js.org/assets/zh.flow.png)

#### observable

定义一个存储 state 的可追踪字段（Proxy）

#### action

将一个方法标记为可以修改 state 的 action

#### computed

标记一个可以由 state 派生出新值并且缓存其输出的计算属性

### 3、创建 store

#### makeObservable

**注**：mobx 中的每一个 store 都应该只初始化一次

以一个计数器为例，创建文件 store/Counter.ts，新建一个 Counter 类，使用 makeObservable 方法将类的属性和方法变成响应式，并导出实例

```typescript
// src/store/Counter.ts
import { action, makeObservable, observable } from "mobx";
class Counter {
  constructor() {
    // 参数1：target，把谁变成响应式（可观察）
    // 参数2：指定哪些属性或者方法变成可观察
    makeObservable(this, {
      count: observable,
      increment: action,
      decrement: action,
      reset: action,
    });
  }
  count = 0;
  increment() {
    this.count++;
  }
  decrement() {
    this.count--;
  }
  reset() {
    this.count = 0;
  }
}
const counter = new Counter();
export default counter;
```

### 4. 在组件中使用：observer

需要在 App.tsx 文件中引入 store，即可使用其属性方法

```tsx
// /src/App.tsx
import counter from "./store/Counter";
// observer是一个高阶组件函数，需要包裹一个组件，这样组件才会更新
import { observer } from "mobx-react-lite";

function App() {
  return (
    <div className="App">
      <h3>计数器案例</h3>
      <div>点击次数：{counter.count}</div>
      <button onClick={() => counter.increment()}>加1</button>
      <button onClick={() => counter.decrement()}>减1</button>
      <button onClick={() => counter.reset()}>重置</button>
    </div>
  );
}
export default observer(App);
```

### 5、this 指向问题：action.bound

默认 class 中的方法不会绑定 this，this 指向取决于如何调用。Counter 里面的方法的 this 没有绑定，因此需要通过箭头函数的形式使用

```typescript
<button onClick={() => counter.increment()}>加1</button>
```

要想直接使用，需要在 Counter 里面的 makeObservable 的使用通过 action.bound 绑定 this 的指向

```typescript
makeObservable(this, {
  count: observable,
  increment: action.bound,
  reset: action.bound,
});
```

此时组件中即可直接使用 store 的方法（否则 increment 里面的 this 在严格模式下是 undefined）

```tsx
<button onClick={counter.increment}>加1</button>
```

### 6、计算属性：get

mobx 的 computed 可以用来从其他可观察对象中派生信息，具有以下特点：

- 采用惰性求值，会缓存其输出，并且只有当其依赖的可观察对象被改变是才会重新计算
- 其前面必须使用 get 进行修饰
- 还需要通过 makeObservable 方法指定
  以 double 为例

```typescript
...
    makeObservable(this, {
       count: observable,
       increment: action.bound,
       reset: action.bound,
       double: computed,
     })
...
get double(){
  return this.count * 2
}
```

### 7、makeAutoObservable(target, overrides?, options?)

makeAutoObservable 是加强版的 makeObservable，在默认情况下它将推断所有属性。推断规格如下：

- 所有属性值字段都成为 observable
- 普通方法都成为 action
- get 修饰的方法，会推断为computed）
  可以通过第二个参数 overrides 排除不需要被观察的属性和方法，第三个参数 autoBind 可以绑定 this 指向

```typescript
// 参数1：target，把谁变成响应式（可观察）
// 参数2：排除属性和方法
// 参数3：指定自动绑定this
makeAutoObservable(this, {}, { autoBind: true });
```

## 三、mobx 监听属性

mobx 中有两个监听方法 autorun 和 reaction，其中：

### autorun

autorun 函数接受一个函数作为参数，

- 在创建以及每当该函数所观察的值发生变化时，它都应该运行；
- 会自动收集并订阅**所有**可观察属性，一旦有改变发生，autorun 将会再次触发

```typescript
autorun(() => {
  console.log("counter", counter.count);
});
```

### reaction

其接受两个函数作为参数，参数 1 为 data 函数，其返回值将会作为第二个函数输入，参数 2 为回调函数

- 类似 autorun，但在初始化时不会自动运行，且可以让你更加精细地控制要跟踪的可观察对象，

```typescript
reaction(
  () => counter.count,
  (newValue, oldValue) => {
    console.log("counter.count变化了");
  }
);
```

## 四、异步处理：runInAction

如果可观察对象的修改不是在 action 函数中，控制台会报警告，

```
[MobX] Since strict-mode is enabled, changing (observed) observable values without using an action is not allowed. Tried to modify: Counter@2.count
```

方法 1：不推荐，通过消除控制警告

```ts
import { configure } from "mobx";

configure({
  // observed 可观察状态必须通过action修改,默认
  // never 可观察状态可以在任何地方修改
  enforceActions: "never",
});
```

方法 2：可以通过 runInAction 保证所有异步更新可观察对象步骤都标识为 action

```typescript
  incrementAsync(){
    setTimeout(() => {
      runInAction(() => {
        this.count++
      })
    }, 2000)
  }
```

## 五、模块化

mobx 模块化管理即通过一个根 store 统一管理所有 store

新建 store/index.ts 文件，导入所有 store，使用 useContext 机制，自定义 useStore hook，统一导出 store

```typescript
// src/store/index.ts
import { useContext, createContext } from "react";
import cart from "./Cart";
import counter from "./Counter";

class RootStore {
  cart = cart;
  counter = counter;
}
const store = new RootStore();
const Context = createContext(store);
export const useStore = () => {
  return useContext(Context);
};
```

在 App.tsx 中统一导入，解构得到相应 store

```tsx
import { useStore } from './store'
...
  const { cart, counter } = useStore()
...
```

## 总结

### src/store/index.ts

```ts
import { useContext, createContext } from "react";
import cart from "./Cart";
import counter from "./Counter";

class RootStore {
  cart = cart;
  counter = counter;
}
const store = new RootStore();
const Context = createContext(store);
export const useStore = () => {
  return useContext(Context);
};
```

### src/store/Cart.ts

```ts
import { makeAutoObservable } from "mobx";

class Cart {
  constructor() {
    makeAutoObservable(this, {}, { autoBind: true });
  }
  list = [];
}

const cart = new Cart();

export default cart;
```

### src/store/Counter.ts

```TS
import {
  runInAction,
  reaction,
  autorun,
  makeAutoObservable,
  configure,
} from "mobx";

// configure({
//   // observed 可观察状态必须通过action修改
//   // never 可观察状态可以在任何地方修改
//   enforceActions: "observed",
// });

class Counter {
  constructor() {
    // 参数1：target，把谁变成响应式（可观察）
    // 参数2：排除属性和方法
    // 参数3：指定自动绑定this
    makeAutoObservable(this, {}, { autoBind: true });
  }
  count = 0;

  increment() {
    this.count++;
  }

  incrementAsync() {
    setTimeout(() => {
      runInAction(() => {
        this.count++;
      });
    }, 2000);
  }

  decrement() {
    this.count--;
  }

  reset() {
    this.count = 0;
  }

  get double() {
    return this.count * 2;
  }
}

const counter = new Counter();

autorun(() => {
  console.log("counter.count1", counter.count);
});

reaction(
  () => counter.count,
  (newValue, oldValue) => {
    console.log("counter.count2");
  }
);

export default counter;
```

### src/App/jsx

在组件中使用，需要在 App.tsx 文件中引入 store，即可使用其属性方法

```tsx
import { useStore } from "./store";
import { observer } from "mobx-react-lite";

function App() {
  const { cart, counter } = useStore();
  return (
    <div className="App">
      <h3>计数器案例</h3>
      <div>点击次数：{counter.count}</div>
      <div>double: {counter.double}</div>
      <div>cart数量: {cart.list?.length}</div>
      <button onClick={counter.increment}>加1</button>
      <button onClick={counter.incrementAsync}>异步加1</button>
      <button onClick={counter.decrement}>减1</button>
      <button onClick={counter.reset}>重置</button>
    </div>
  );
}

export default observer(App);
```

## 面试题

收到！你要的“**扩展/对比**”必须是**纯文本段落**（不加列表点）。我把 15 道 MobX 高频题按你要求的格式全部修正好了（要点仍用列表，扩展/对比改成单段文本）。

------

### 1) MobX 的核心思想（与 Redux 最大差异）

**一句话回答**
 MobX 用透明响应式让可观察状态变化时自动更新依赖；Redux 强调显式 action→reducer→新 state 的单向流。

**要点**

- 可观察（observable）
- 计算（computed）
- 反应（autorun/reaction/observer）

**详细回答**
 MobX 在 `get` 收集依赖，在 `set` 通知更新；被 `observer` 包裹的组件会自动订阅其渲染期间读取到的 observable 字段，只有真正依赖变更的组件会重渲染，从而实现细粒度更新。

**扩展/对比**
 Redux 适合强约束与可审计（时间旅行、日志链路），MobX 适合快速开发与更少样板，二者取舍看团队规范与业务复杂度。

------

### 2) 为什么 `computed`（getter）能“缓存”？

**一句话回答**
 `computed` 追踪依赖并做脏标记，依赖未变直接复用上次结果。

**要点**

- 惰性求值
- 依赖跟踪
- 脏标记（dirty）

**详细回答**
 首次读取时收集依赖并计算；依赖不变则命中缓存，依赖变更才重新计算。这样减少重复计算与渲染压力，提升性能。

**扩展/对比**
 Redux 里常用 `reselect/createSelector` 做记忆化派生；MobX 的 `computed` 等价但为内置一等公民。

------

### 3) `autorun` 与 `reaction` 的区别

**一句话回答**
 `autorun` 立即执行并跟踪函数体所有依赖；`reaction` 只跟踪显式表达式且首次不执行 effect。

**要点**

- autorun：全量依赖、立刻执行
- reaction：精确依赖、首次跳过 effect
- 触发控制粒度

**详细回答**
 `autorun(fn)` 会跟踪 `fn` 内读取的全部 observable；`reaction(expr, effect)` 仅在 `expr()` 返回值变化时触发 `effect`，对副作用时机与频率的控制更可预期。

**扩展/对比**
 UI 更新优先用 `observer`，日志/副作用用 `reaction` 更合适；`autorun` 多用于调试或全局监听。

------

### 4) `makeAutoObservable` vs `makeObservable` 的取舍

**一句话回答**
 默认用 `makeAutoObservable`；需要精确注解或排除时用 `makeObservable`。

**要点**

- 自动推断：字段→observable、getter→computed、方法→action
- overrides 可定制
- autoBind 绑定 this

**详细回答**
 `makeAutoObservable(this, overrides, { autoBind })` 覆盖大多数场景；当需标注 `flow`、`computed.struct` 或排除某些成员时，使用 `makeObservable` 明确注解更稳妥。

**扩展/对比**
 v6 不再主推装饰器默认开启，原因是工程配置与可移植性差；两者功能等价，自动推断更省心。

------

### 5) `action.bound` 与箭头函数如何选择

**一句话回答**
 二选一：用 `action.bound` 自动绑定实例 `this`，或把方法定义为箭头函数；不要叠加。

**要点**

- `action.bound` 绑定 this
- 箭头函数闭包 this
- 规范统一

**详细回答**
 类方法配合 `action.bound` 可直接 `onClick={store.inc}`；若用箭头函数定义方法则已天然绑定，不再需要 `bound`，同时避免混用导致困惑。

**扩展/对比**
 TypeScript 场景下 `action.bound` 可读性与类型提示更统一；团队统一约束最重要。

------

### 6) 为什么异步更新要用 `runInAction`（或 `flow`）

**一句话回答**
 严格模式下观测到的状态必须在 action 中修改，异步回调不在 action 环境，需要 `runInAction` 或 `flow`。

**要点**

- `enforceActions: 'observed'`
- Promise/async 边界
- `runInAction`/`flow`/`flow.bound`

**详细回答**
 在 `then/await` 之后修改状态已脱离原 action，需用 `runInAction(() => state.x = v)` 包裹，或改写为 `flow(function*(){ yield ...; state.x = v })` 使整个过程处于 action 之内。

**扩展/对比**
 把严格模式改成 `never` 虽能消警告，但容易引入难查的隐式修改；生产建议保持 observed 并按规范处理异步。

------

### 7) 组件不更新的常见原因

**一句话回答**
 忘记 `observer`、渲染时未读取 observable、在 render 外解构导致断订阅。

**要点**

- 必须 `observer`
- 渲染期间读取依赖
- 避免把 observable 解构成普通引用

**详细回答**
 只有渲染期间读取到的 observable 才会被订阅；将 observable 提前解构为普通变量（或用 `toJS`）会失去追踪，导致不更新。

**扩展/对比**
 建议在 JSX 里直接读取 `store.x.y`，或把派生逻辑放 `computed`，组件仅消费 `computed`。

------

### 8) `toJS` 的正确使用时机

**一句话回答**
 仅在需要快照/序列化/与非 MobX 库交互时使用；渲染期频繁 `toJS` 会伤性能。

**要点**

- 深克隆成本高
- 序列化/日志/传参场景
- 尽量直接读 observable

**详细回答**
 `toJS` 会生成新的普通结构体，适合日志、持久化与跨库交互；渲染中应避免大量 `toJS`，可将转换放 `computed` 缓存。

**扩展/对比**
 Redux 的 state 天然可序列化；MobX 只有在必要时才转为普通对象。

------

### 9) observable 的数组/Map/Set 注意事项

**一句话回答**
 使用可观察集合类型，避免把其中元素解出后长期以普通引用使用。

**要点**

- `observable.array/map/set`
- `slice/map/filter` 返回普通数组
- 在 `computed` 做派生

**详细回答**
 对可观察集合做变换得到的是普通集合，失去追踪；建议把变换逻辑放 `computed`，组件读取 `computed` 保证订阅链正确。

**扩展/对比**
 Redux 倾向规范化与不可变更新；MobX 可以直接修改集合，但要关注订阅粒度与引用生命周期。

------

### 10) `observer` 的工作机制

**一句话回答**
 `observer` 在渲染时收集本组件读取到的 observable 依赖，依赖变更时仅重渲染该组件。

**要点**

- 渲染即订阅
- 精准重渲染
- 依赖最小化

**详细回答**
 `observer` 通过跟踪渲染访问路径，构建组件与 observable 的依赖图；当依赖变化时只更新受影响的组件，提升性能。

**扩展/对比**
 相比手写订阅，`observer` 自动管理订阅/解绑，避免内存泄漏与重复订阅的坑。

------

### 11) `inject`/`Provider` 是否还推荐

**一句话回答**
 新项目优先 `useContext` + 自定义 Hook；`inject/Provider` 保留用于类组件和历史代码。

**要点**

- 函数组件：`useContext`
- 旧项目/类组件：`inject`
- 迁移路线清晰

**详细回答**
 React 官方已提供 Context；函数组件配合 `observer` 与自定义 Hook 足以覆盖绝大多数注入需求；旧项目可逐步迁移以减少 HOC 叠层。

**扩展/对比**
 与 react-redux 的演进类似：现代主推 Hooks，但 HOC 仍是有效存量方案。

------

### 12) MobX 与 Redux 的选型建议

**一句话回答**
 追求快速与更少样板选 MobX；追求强规范、可审计与跨团队一致性选 Redux/RTK。

**要点**

- 自由度 vs 约束
- 小中型 vs 大型协作
- 工程规范与工具链

**详细回答**
 MobX 适合 CRUD 表单、桌面类、需要快速迭代的业务；Redux/RTK 提供时间旅行、统一异步与缓存方案，适合复杂流程与审计需求。

**扩展/对比**
 RTK Query 让“请求+缓存”几乎零样板；MobX 需要自建网络层规范与缓存策略。

------

### 13) 如何组织大型项目的 Store

**一句话回答**
 采用 RootStore 聚合子 Store，经 `useContext` 注入，避免循环依赖与跨模块耦合。

**要点**

- RootStore 模式
- 子 Store 解耦
- 构造注入/方法传参

**详细回答**
 每个领域一个类 Store，RootStore 统一创建与组合；跨模块依赖通过构造注入或函数传参，避免直接相互引用导致循环依赖。

**扩展/对比**
 Redux 的 slice 天然模块化；MobX 需自律组织边界，保持依赖方向单向。

------

### 14) SSR 与并发请求如何避免单例污染

**一句话回答**
 SSR 每个请求都实例化新的 RootStore，并把数据注水到客户端。

**要点**

- 工厂函数创建 store
- 不在模块顶层持久化可变单例
- hydrate 流程

**详细回答**
 在服务端渲染函数中创建新 store 并填充数据，序列化注入 HTML，客户端再反序列化与实例同构；避免将 store 作为模块级单例跨请求共享。

**扩展/对比**
 Redux/RTK 的 SSR 同理：每请求一套 store + 预取数据，防止脏数据串台。

------

### 15) 性能优化的三条准则

**一句话回答**
 小粒度 `observer`、把派生放 `computed`、避免 render 外“断订阅”的解构与频繁 `toJS`。

**要点**

- 订阅边界小
- 记忆化派生
- 引用生命周期管理

**详细回答**
 将大组件拆为多个 `observer` 子组件，各自只读所需字段；复杂派生集中在 `computed`；不要把 observable 提前解构成普通值长期使用，避免在渲染中频繁深拷贝。

**扩展/对比**
 与 React 一般优化原则一致：减少不必要渲染、缓存计算、明确边界；得益于 `observer` 的依赖跟踪，MobX 在细粒度更新上有天然优势。



