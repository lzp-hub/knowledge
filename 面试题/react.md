## 目录（TOC）

1. 什么是 React
2. 什么是虚拟 DOM（VDOM）
3. 虚拟 DOM 的结构与 createElement
4. 虚拟 DOM 如何变为真实 DOM（渲染流程）
5. 为什么虚拟 DOM 常能提升性能
6. React 的 diff 算法（核心思想）
7. 虚拟 DOM 一定更快吗
8. 为什么 key 不能用数组下标
9. 类组件 vs 函数组件（含 Hooks）
10. 高阶组件（HOC）
11. constructor 中的 super(props)
12. 受控组件 vs 非受控组件
13. JSX 是什么
14. React 中的样式方案
15. 生命周期（类组件）与错误边界
16. 父子组件生命周期顺序（现代说明）
17. PureComponent / Component 的区别
18. 在 SCU / cWU 中 setState 会怎样
19. 为什么不能直接改 this.state
20. 事件系统：合成事件与原生事件
21. 合成事件与原生事件混用的影响
22. 合成事件与原生事件差异对比
23. ref 的用途与现代写法
24. dangerouslySetInnerHTML 与 XSS（DOMPurify）
25. Hooks 带来了什么
26. 常见 Hooks
27. useEffect 与生命周期映射
28. 自定义 Hook 的理解与示例
29. 路由懒加载（React.lazy / Suspense）
30. 性能优化清单（实践建议）

---

## 1. 什么是 React

**一句话回答**
React 是一个用于构建用户界面的 JavaScript 库，强调组件化、声明式渲染和单向数据流。

**要点**

- 只关注视图层（非“全家桶”）。
- 组件化 + 声明式渲染。
- 单向数据流（父 → 子）。
- 虚拟 DOM 与调和提升复杂场景的性能下限。

**详细回答**
React 由 Facebook 开源，专注 UI 层。核心理念：

- **声明式**：根据 state 渲染视图，减少命令式 DOM 操作。
- **组件化**：将界面拆分为可复用、可组合的组件。
- **单向数据流**：数据自上而下流动，状态管理清晰。
- **虚拟 DOM**：用 JS 对象描述 UI，变化时对比新旧 VDOM，仅对真实 DOM 做最小化更新。

**扩展**

- React 本身不含路由/状态管理，常搭配 React Router、Redux、Zustand、React Query 等。
- 函数组件 + Hooks 是现代首选；类组件用于维护旧代码或特定场景（错误边界等）。

---

## 2. 什么是虚拟 DOM（VDOM）

**一句话回答**
虚拟 DOM 是对真实 DOM 的轻量级 JS 对象表示，用于在内存中计算差异并以最小成本更新真实 DOM。

**要点**

- VDOM 是“模式/理念”，非单一定义的技术。
- 树形结构：`{ type, props, children }`。
- 与真实 DOM 同步过程叫 **调和（reconciliation）**。

**详细回答**
VDOM 以纯 JS 对象描述 UI。状态变化时，生成新的 VDOM，React 通过 diff 找差异并把补丁应用到真实 DOM。这样把昂贵的 DOM 改动变成在内存对比 + 合理批量更新，提升复杂界面下的可维护性与性能下限。

**扩展**

- Vue 也使用 VDOM，但实现策略不同。
- React 16 引入 Fiber 架构以支持可中断渲染与任务切片。

---

## 3. 虚拟 DOM 的结构与 createElement

**一句话回答**
`React.createElement(type, props, ...children)` 创建虚拟节点对象（VNode），持有类型、属性与子节点。

**要点**

- JSX 是 `createElement` 的语法糖。
- VNode = `{ type, props, children }`。
- `type` 可为字符串（原生标签）或组件。

**详细回答**
当你写 `<div className="x">hi</div>`，Babel 会编译为：

```js
React.createElement("div", { className: "x" }, "hi");
```

最终得到一个普通 JS 对象（VNode），参与 VDOM 树的构建和 diff。

**扩展**

- VDOM 节点可能包含 `key`、`ref` 等特殊属性辅助调和与实例获取。

---

## 4. 虚拟 DOM 如何变为真实 DOM（渲染流程）

**一句话回答**
React 在内存构建 VDOM → diff → 生成补丁 → 由 ReactDOM 把补丁应用到真实 DOM。

**要点**

- React 18 推荐 `createRoot`。
- 首次挂载：完整构建 → 挂载。
- 更新：对比新旧 VDOM，最小化变更。

**详细回答**

```js
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import App from "./App";

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

更新时，React 比较新旧树并仅在必要处改动真实 DOM，以减少回流/重绘开销。

**扩展**

- `ReactDOM.render`（旧 API）在 React 18 中不再推荐。
- 严格模式会在开发期触发额外检查（如重复调用某些生命周期）以暴露副作用。

---

## 5. 为什么虚拟 DOM 常能提升性能

**一句话回答**
因为 VDOM 对比能避免大量不必要的真实 DOM 操作，并通过批量/最小化更新降低渲染开销。

**要点**

- 真实 DOM 改动昂贵。
- diff 将复杂度从 O(n³) 降至近似 O(n)。
- 最小化补丁 + 批量更新。

**详细回答**
React 假设 UI 树重排通常发生在同层级，节点类型变化意味着子树差异巨大，借此设计 O(n) 的 diff 策略加速比对，并在 commit 阶段聚合变更应用于 DOM，避免频繁操作。

**扩展**

- 在极简场景（单个节点更新），直接 DOM 操作可能更快；VDOM 侧重大型界面稳定性能。

---

## 6. React 的 diff 算法（核心思想）

**一句话回答**
React diff 基于三大假设：同层比较、用 key 标识同级子节点、不同类型直接替换。

**要点**

- 同层比较，不跨层。
- key 唯一定位同级子节点，识别插入/删除/移动。
- 节点类型不同，整棵子树替换。

**详细回答**

- **tree diff**：只比较同层，跨层视作删除 + 新建。
- **component diff**：同类型继续深比；不同类型直接替换。
- **element diff**：同层子节点列表依赖 `key` 判断移动/插入/删除，最大化复用。

**扩展**

- 若未提供稳定 key（如使用下标），会导致不必要重建或状态错位。

---

## 7. 虚拟 DOM 一定更快吗

**一句话回答**
不一定，VDOM 有自身计算开销，优势在于复杂界面下的整体性能下限与可维护性。

**要点**

- 极简场景：直接 DOM 更快。
- 大型应用：VDOM 更稳更省心。
- 浏览器本身也有批处理优化。

**详细回答**
VDOM 的额外 diff 计算在简单更新中可能是负担；但在复杂 UI 里，VDOM 能把频繁小更新合并，减少真实 DOM 操作次数，保证“不会太慢”。

**扩展**

- 面试时可举“一个按钮计数器”的反例来说明边界。

---

## 8. 为什么 key 不能用数组下标

**一句话回答**
用下标会在插入/删除时导致错误复用，引发不必要重渲染或状态错位。

**要点**

- key 用于同层匹配同一节点。
- 插入/删除中间项会让下标错位。
- 最佳实践：稳定且唯一的业务 id。

**详细回答**
在可变列表中，索引会随着插入/删除移动，React 以为节点“换了”，从而重建或错配状态。稳定 id 可让 React 正确识别节点移动而非替换。

**扩展**

- 静态列表且永不变动的场景，索引可行，但不推荐。

---

## 9. 类组件 vs 函数组件（含 Hooks）

**一句话回答**
类组件通过生命周期与 `this.state` 管理状态；函数组件借助 Hooks 完成同样能力且更简洁。

**要点**

- 现代项目优先函数组件 + Hooks。
- 类组件保留在旧代码与错误边界等场景。
- Hooks 解决逻辑分散与复用难的问题。

**详细回答**

- 类组件：`extends React.Component`，生命周期方法、`this.setState` 管理状态。
- 函数组件：直接返回 JSX，使用 `useState/useEffect` 等 Hooks 管理状态与副作用。

**扩展**

- Hooks 规则：只能在顶层调用，不在条件/循环/嵌套中调用。
- 自定义 Hook 用于抽离复用逻辑。

---

## 10. 高阶组件（HOC）

**一句话回答**
HOC 是接收组件并返回新组件的函数，用于复用组件逻辑的一种模式。

**要点**

- 典型写法：`const Enhanced = withX(Base)`。
- 不修改原组件，包装增强。
- 与 Render Props、Hooks 同属逻辑复用技术。

**详细回答**
HOC 通过包裹原组件，在外层注入额外的 props/逻辑（如鉴权、数据注入、埋点）。与 Hooks 相比，HOC 会增加组件层级，可能形成“嵌套地狱”。

**扩展**

- Hooks 出现后，HOC 使用频率降低，但在老项目仍常见。

---

## 11. constructor 中的 super(props)

**一句话回答**
在类组件构造函数中必须先 `super(props)` 才能在构造阶段访问 `this.props`。

**要点**

- ES6 子类构造器中必须先调用 `super` 才能使用 `this`。
- `super(props)` 提供 props 给父类构造函数初始化。
- 构造器之外访问 `this.props` 不受影响。

**详细回答**
`super(props)` 让 `this.props` 在构造阶段可用；如果只 `super()`，此时 `this.props` 为 `undefined`（但构造器入参 `props` 仍能用）。构造器外部不受影响。

**扩展**

- 仅在需要初始化 state / 绑定方法时才写 `constructor`。现代写法尽量用字段语法 + 箭头函数或 Hooks。

---

## 12. 受控组件 vs 非受控组件

**一句话回答**
受控组件由 React state 驱动表单值；非受控组件由 DOM 自行管理值并通过 ref 读取。

**要点**

- 受控：`value + onChange`。
- 非受控：`defaultValue` + `ref` 读取。
- 复杂校验/联动场景优先受控。

**详细回答**
受控组件把表单输入与 state 同步，便于校验、回显与联动；非受控更接近原生，用起来简单但不利于复杂逻辑。

**扩展**

- 大型表单可用 `react-hook-form`, `Formik`, `antd Form` 等库简化受控管理。

---

## 13. JSX 是什么

**一句话回答**
JSX 是 `React.createElement` 的语法糖，让你在 JS 中写类 XML 结构。

**要点**

- 提升可读性与开发效率。
- 依旧是 JS（编译为函数调用）。
- 支持表达式插值与条件渲染。

**详细回答**
JSX 让组件结构直观、声明式；Babel 将其编译为 `createElement` 调用，生成 VNode。你也可以不使用 JSX（手写 `createElement`），但不推荐。

**扩展**

- 可通过 `<>...</>`（Fragment）避免额外 DOM 包裹。

---

## 14. React 中的样式方案

**一句话回答**
可选外链 CSS、CSS Modules、CSS-in-JS（styled-components、emotion）、内联 style 等多种方案。

**要点**

- className（外链/Modules）是主流。
- 内联 style 是对象（用 `backgroundColor`）。
- CSS-in-JS 适合组件粒度样式与主题化。

**详细回答**

- 外链 CSS：结构清晰，需避免全局冲突。
- CSS Modules：本地作用域，命名冲突少。
- CSS-in-JS：动态样式、主题、变量注入方便。
- 内联样式：简单但缺乏伪类/媒体查询（需配合额外方案）。

**扩展**

- Tailwind、UnoCSS 等原子化方案也很流行。

---

## 15. 生命周期（类组件）与错误边界

**一句话回答**
常用生命周期：`constructor` → `render` → `componentDidMount`；更新期 `shouldComponentUpdate` → `render` → `componentDidUpdate`；卸载期 `componentWillUnmount`；错误边界用 `componentDidCatch`/`getDerivedStateFromError`。

**要点**

- 现代开发优先 Hooks，对应到 `useEffect`。
- 错误边界只能用类组件实现。
- 已废弃/不推荐：`componentWillMount/ReceiveProps/Update`。

**详细回答**

- 挂载：`constructor` → `render` → `componentDidMount`（适合请求/订阅）。
- 更新：`shouldComponentUpdate`（性能优化）→ `render` → `componentDidUpdate`（依赖 DOM 的操作）。
- 卸载：`componentWillUnmount`（清理计时器/订阅等）。
- 错误边界：`componentDidCatch` 捕获后代错误，渲染兜底 UI。

**扩展**

- React 18 的严格模式会二次调用某些生命周期以帮助发现副作用（开发环境）。

---

## 16. 父子组件生命周期顺序（现代说明）

**一句话回答**
挂载时：父 render → 子挂载完毕 → 父 `componentDidMount`；更新与卸载顺序亦遵循自顶向下协调、自底向上提交的机制。

**要点**

- 旧的 `componentWillMount` 已废弃，不再作为标准考点。
- 记忆：先父 render 再子挂载，最后父 didMount。
- Hooks 场景下由 `useEffect` 映射。

**详细回答**
父 render 触发子组件创建与 render，子完成挂载后，父再进入 didMount。更新阶段会遵循类似的先协调后提交顺序。

**扩展**

- 面试中如果被问到旧顺序，可指出废弃方法并给出现代对应。

---

## 17. PureComponent / Component 的区别

**一句话回答**
`PureComponent` 内置浅比较 `shouldComponentUpdate`，减少不必要更新；`Component` 没有。

**要点**

- 浅比较仅比较第一层引用。
- 适合不可变数据与基本类型 props。
- 引用变化但值相同也会触发更新（需注意）。

**详细回答**
`PureComponent` 用浅比较决定是否 re-render；若传入对象/数组在逻辑上“没变”但引用变了（新建了新对象），也会认为“变了”。

**扩展**

- 函数组件用 `React.memo` 达到类似效果；必要时传入自定义比较函数。

---

## 18. 在 SCU / cWU 中 setState 会怎样

**一句话回答**
在 `shouldComponentUpdate` 或旧的 `componentWillUpdate` 中调用 `setState` 可能引发循环更新，应避免。

**要点**

- SCU 应是纯函数，**不要**触发状态更新。
- 旧 API `componentWillUpdate` 已不推荐。
- 条件更新放在 `componentDidUpdate`（加条件判断）或事件回调中。

**详细回答**
在这些钩子里更新 state 会让组件反复进入更新队列，导致死循环或性能问题。建议在 `componentDidUpdate` 中通过比较 `prevProps/prevState` 再决定是否 `setState`。

**扩展**

- Hooks 场景中在 `useEffect` 内根据依赖变化做条件更新。

---

## 19. 为什么不能直接改 this.state

**一句话回答**
直接修改 `this.state` 不会触发重渲染且会打破 React 的状态队列机制，应使用 `setState`。

**要点**

- `setState` 维护批量更新队列。
- 直接改值不会通知 React。
- 可能造成状态不一致或难以追踪。

**详细回答**
`setState` 支持合并与批处理，减少重复渲染；直接改 `this.state` 绕过了调度机制，UI 与数据可能不一致。

**扩展**

- Hooks 用 `setXxx` 更新；依赖前值要用函数式更新：`setCount(c => c + 1)`。

---

## 20. 事件系统：合成事件与原生事件

**一句话回答**
React 使用合成事件（跨浏览器包装）与事件委托（17 起绑定到根容器而非 document），提供一致的事件 API。

**要点**

- 合成事件跨浏览器兼容。
- React 17 调整委托目标到应用根容器。
- 捕获/冒泡均支持（`onClickCapture` 等）。

**详细回答**
React 不在每个节点上单独绑定监听器，而是在根容器上统一监听并分发。合成事件对象池在旧版本里存在回收逻辑（17 后已废弃池化），注意不要异步滥用事件对象。

**扩展**

- 与原生事件混用要了解触发顺序与 `stopPropagation` 的差异（见下条）。

---

## 21. 合成事件与原生事件混用的影响

**一句话回答**
混用时要注意触发顺序与冒泡拦截的范围：原生可阻止合成的冒泡，合成阻止不影响原生已发生的冒泡。

**要点**

- 原生 `e.stopPropagation()` 会阻断后续合成冒泡。
- 合成 `e.stopPropagation()` 不影响已触发的原生冒泡。
- 尽量避免混用，保持一致性。

**详细回答**
因为两套系统的调度地点与时机不同，拦截位置不同会产生“只拦截其一”的情况，导致行为与预期不一致。

**扩展**

- 如果必须混用，明确事件绑定层级与触发顺序，并在代码评审中特别标注。

---

## 22. 合成事件与原生事件差异对比

**一句话回答**
合成事件提供统一 API、委托到根容器处理；原生事件直接由浏览器派发到节点，二者在传播与拦截细节上存在差异。

**要点**

- API 一致性：合成优于原生跨浏览器。
- 绑定方式：合成集中；原生多样。
- 传播阶段与拦截：细节不同。

**详细回答**
合成事件封装了兼容性，React 可在其生命周期中做批量更新与优先级调度；原生事件直连 DOM，灵活但跨浏览器兼容与管理成本更高。

**扩展**

- 某些事件（如媒体事件）不在合成系统里，需用原生。

---

## 23. ref 的用途与现代写法

**一句话回答**
ref 用于直接访问 DOM 或组件实例（类），现代函数组件用 `useRef`。

**要点**

- DOM 操作（焦点、滚动、媒体控制）典型使用场景。
- `createRef`（类）与 `useRef`（函数）。
- 回调 ref 在特殊场景更灵活。

**详细回答**

```jsx
// 函数组件
const inputRef = useRef(null);
useEffect(() => inputRef.current?.focus(), []);
return <input ref={inputRef} />;
```

避免滥用 ref，优先声明式，只有必须直接操控 DOM 时使用。

**扩展**

- 函数组件没有实例，`ref` 指向子组件时需使用 `forwardRef`。

---

## 24. dangerouslySetInnerHTML 与 XSS（DOMPurify）

**一句话回答**
`dangerouslySetInnerHTML` 直接注入 HTML，存在 XSS 风险；需用 DOMPurify 等库先净化。

**要点**

- API：`<div dangerouslySetInnerHTML={{ __html: html }} />`。
- 勿与子节点并用（会被忽略）。
- 始终净化不可信内容。

**详细回答**
在富文本渲染或 CMS 场景常见。注入前先 `DOMPurify.sanitize(html)`，减少恶意脚本风险。

**扩展**

- 服务端同样需要防护（不要只在前端做净化）。

---

## 25. Hooks 带来了什么

**一句话回答**
Hooks 让函数组件也能使用状态与生命周期逻辑，解决类组件逻辑分散与复用难题。

**要点**

- 逻辑聚合在同一 Hook 内，更易复用与测试。
- 避免嵌套层级（相较 HOC/Render Props）。
- 规则：只在顶层调用，不在循环/条件中调用。

**详细回答**
Hooks 把“状态 + 副作用 + 订阅”等相关逻辑聚合在一起，便于抽象为自定义 Hook 并在多组件复用。

**扩展**

- 类组件不会废弃，两者可共存。错误边界仍需类组件。

---

## 26. 常见 Hooks

**一句话回答**
常见 Hooks：`useState`、`useEffect`、`useMemo`、`useCallback`、`useRef`、`useContext` 等。

**要点**

- `useMemo`/`useCallback`：缓存值/函数，避免子组件不必要渲染。
- `useRef`：持久可变容器，不触发渲染。
- `useContext`：跨层传参。

**详细回答**
根据依赖数组控制重算与重建，注意闭包陷阱（依赖遗漏）。

**扩展**

- 生态：`useReducer`、`useLayoutEffect`、`useImperativeHandle`、`useTransition`（并发相关）。

---

## 27. useEffect 与生命周期映射

**一句话回答**
`useEffect` 可视作 `componentDidMount`/`DidUpdate`/`WillUnmount` 的组合，依赖数组控制执行时机。

**要点**

- `[]`：仅挂载与卸载（清理函数）。
- `[deps]`：依赖变化时执行。
- 无依赖：每次渲染后执行。

**详细回答**

```jsx
useEffect(() => {
  // mount + deps 变化时执行
  return () => {
    // unmount 或下次执行前清理
  };
}, [deps]);
```

避免在 effect 中修改依赖本身导致循环；必要时用函数式更新或拆分 effect。

**扩展**

- `useLayoutEffect` 在 DOM 提交后、浏览器绘制前同步执行（谨慎使用，防止卡顿）。

---

## 28. 自定义 Hook 的理解与示例

**一句话回答**
自定义 Hook 用于提取可复用的状态逻辑（以 `use` 开头），通过参数/返回值与组件交互。

**要点**

- 通过闭包暴露状态与方法。
- 组合多个 Hook 构成高级逻辑。
- 便于单测与复用。

**详细回答**

```tsx
// 倒计时（setInterval 版）
function useCountDown(init: number) {
  const timer = useRef<number | null>(null);
  const [count, setCount] = useState(init);

  useEffect(() => {
    timer.current = window.setInterval(() => setCount((c) => c - 1), 1000);
    return () => {
      if (timer.current) clearInterval(timer.current);
    };
  }, []);

  useEffect(() => {
    if (count <= 0 && timer.current) clearInterval(timer.current);
  }, [count]);

  return count;
}
```

**扩展**

- 输出一个值直接返回，两个值建议数组，三个及以上建议对象。
- 注意清理副作用与边界条件（如倒计时到 0）。

---

## 29. 路由懒加载（React.lazy / Suspense）

**一句话回答**
`React.lazy` + `Suspense` 可以按需加载组件，降低首屏包体与渲染压力。

**要点**

- `lazy(() => import(...))`。
- `Suspense` 的 `fallback` 渲染加载态。
- React Router v5 用 `Route component`，v6 改为 `element`。

**详细回答**

```tsx
const User = React.lazy(() => import("./User"));

function App() {
  return (
    <React.Suspense fallback={<div>loading...</div>}>
      <User />
    </React.Suspense>
  );
}
```

在路由层按 chunk 维度拆分可显著优化首屏。

**扩展**

- 数据请求的 Suspense 仍在生态演进中，生产可用方案多依赖第三方库封装。

- React Router v6：

  ```tsx
  <Routes>
    <Route path="/user" element={<User />} />
  </Routes>
  ```

---

## 30. 性能优化清单（实践建议）

**一句话回答**
用拆分组件、缓存（memo/useMemo/useCallback）、正确的 key、懒加载与减少不必要 DOM 操作等手段优化。

**要点**

- 合理拆分：让渲染粒度更小。
- 缓存：`React.memo`、`useMemo`、`useCallback`。
- 避免内联对象/匿名函数导致引用变更。
- 懒加载 & 代码分割。
- 保持组件存在，用 CSS 控制显示（重组件卸载/挂载昂贵时）。

**详细回答**

- **React.memo**：仅在 props 变化时渲染子组件。
- **useMemo / useCallback**：缓存昂贵计算与回调，降噪、避免子组件不必要重渲染。
- **key**：稳定唯一，避免下标。
- **样式控制可见性**：对于重型组件，用 `opacity/visibility` 切换替代频繁卸载挂载（场景化选择）。
- **避免内联对象/匿名函数**：提取到组件外或使用 `useMemo/useCallback` 保持引用稳定。

**扩展**

- 使用 React Profiler、Performance 面板定位瓶颈。
- 优先使用不可变数据结构与持久化引用（减少浅比较误判）。



## 1. React 18 / 19 新特性

**一句话回答**
 React 18 引入并发渲染和自动批处理；React 19 带来 `use` Hook 与表单 Actions，使数据获取和表单交互更自然。

**要点**

- React 18：并发渲染、`useId`、自动批处理、Streaming SSR。
- React 19：`use`、表单 Actions、改进 Suspense、Server Components。

**详细回答**
 React 18 的核心是 **并发渲染**，允许 React 在必要时中断渲染任务，把紧急任务（如输入）优先处理。自动批处理让多个 `setState` 合并执行，减少渲染次数。
 React 19 则更关注 **开发体验**：`use` 允许在组件直接等待 Promise 结果（无需再额外 Hook），表单 Actions 让提交逻辑直接绑定到服务端函数，避免繁琐事件绑定。

**扩展**

- 面试官可能会追问：这些新特性对 **SSR/CSR 一致性** 有什么帮助？
- 你可以结合项目回答，比如“在 Next.js 15 中用 `use` 简化了数据获取逻辑”。

------

## 2. 并发渲染（Concurrent Rendering）

**一句话回答**
 并发渲染让 React 渲染可中断、可恢复，从而避免长任务阻塞 UI。

**要点**

- 基于 Fiber 架构。
- `startTransition` / `useTransition` 控制优先级。
- 输入优先，渲染可延后。

**详细回答**
 React 把渲染切分成小任务（time slicing），如果有更高优先级的任务（比如输入），可以中断当前渲染，先处理交互，再继续之前的渲染。这保证了复杂应用在弱设备上也能保持流畅。

**扩展**

- 可结合实际场景：输入框 + 大列表搜索。
- 对比 Vue：Vue 仍是同步更新，而 React 通过 Fiber 实现异步调度。

------

## 3. 状态管理方案对比

**一句话回答**
 Redux 偏重、MobX 响应式、Zustand 轻量，Recoil/Jotai 原子化。

**要点**

- Redux：可预测、严格，但样板代码多。
- MobX：响应式，学习成本低。
- Zustand：现代项目流行，Hooks 风格。
- Recoil/Jotai：灵活，适合细粒度管理。

**详细回答**
 Redux 强调单一数据源与不可变性，适合大型复杂项目；MobX 通过观察者模式让状态自动更新；Zustand 用 Hooks，代码简洁；Recoil 把状态拆成原子，便于组合。

**扩展**

- 面试时可结合经验回答：“在小团队项目里我用过 Zustand 替代 Redux，大幅减少了样板代码”。

------

## 4. 性能优化进阶

**一句话回答**
 通过组件拆分、memo 化、懒加载、避免重复渲染来提升性能。

**要点**

- React.memo、useMemo、useCallback。
- 正确 key，避免匿名函数。
- 懒加载、代码分割。

**详细回答**

- 使用 React.memo 包裹子组件，避免不必要更新。
- useMemo 缓存计算结果，useCallback 缓存函数引用。
- 列表渲染中要用稳定 key，避免下标。
- 懒加载路由和组件，减少首屏包体。

**扩展**

- 面试官可能追问：`React.memo` 和 `useMemo` 有什么区别？
- 可以答：React.memo 是组件级别，useMemo 是值级别。

------

## 5. SSR / SSG / CSR 与 Next.js

**一句话回答**
 CSR 渲染快但首屏慢，SSR 首屏快但耗服务器，SSG 适合静态内容，Next.js 集成三者并支持 App Router。

**要点**

- CSR：首次渲染依赖 JS，SEO 差。
- SSR：SEO 友好，服务器开销大。
- SSG：构建时生成，性能极佳。
- Next.js：统一抽象，支持服务端组件。

**详细回答**
 Next.js 提供 `getServerSideProps`（SSR）、`getStaticProps`（SSG），在 App Router 下改为 server functions。服务端组件让数据获取和渲染更紧密，减少客户端包体。

**扩展**

- 面试官爱问：“如果一个页面有实时数据，你会用 SSR 还是 CSR？”
   👉 建议答：SSR 初始渲染 + CSR 实时刷新，结合使用。

------

## 6. Fiber 架构

**一句话回答**
 Fiber 是一种可中断的 VDOM 实现，使 React 支持并发渲染。

**要点**

- 链表结构，每个节点都是 Fiber。
- 支持任务切片、优先级调度。
- 分为 render 阶段（可中断）和 commit 阶段（不可中断）。

**详细回答**
 Fiber 把原本同步的 VDOM diff 拆成可暂停任务，React 可以在 render 阶段中断渲染。commit 阶段才一次性更新 DOM。

**扩展**

- 对比旧版：React 15 之前无法打断渲染，复杂页面容易卡顿。

------

## 7. Hooks 内部机制

**一句话回答**
 Hooks 通过数组/链表顺序记录调用，必须保持一致性，才能正确绑定 state。

**要点**

- 只能在顶层调用。
- 顺序保证：依赖组件的渲染顺序。
- 自定义 Hook：提取复用逻辑。

**详细回答**
 React 内部用链表存储 Hooks，每次渲染时按顺序匹配。如果把 Hook 写在条件语句里，顺序错位会导致数据混乱。

**扩展**

- 面试官爱问：“为什么 useState 要返回数组而不是对象？”
   👉 答：数组解构更简洁，可自由命名；对象解构容易冲突。

------

## 8. 错误边界与异常处理

**一句话回答**
 错误边界只能通过类组件实现，用来捕获子组件渲染错误并显示兜底 UI。

**要点**

- 通过 `componentDidCatch`、`getDerivedStateFromError`。
- 只能捕获渲染/生命周期错误，不能捕获异步。
- React 19 依旧没有 Hook 替代方案。

**详细回答**
 错误边界组件可以防止整个应用崩溃，典型用法是在根组件包裹 ErrorBoundary。

**扩展**

- 可以结合 Sentry/LogRocket 做日志上报。
- 面试官可能问：“Promise 中的错误能捕获吗？” 👉 不能，要用 try/catch 或全局监听。





## 高频提醒清单

- key 绝不用数组下标：稳定 id 是最佳实践。  
- 事件委托位置：React 17 起绑定 root 容器。  
- StrictMode 双调用：仅开发环境。  
- 合成事件池化已移除：事件对象异步安全。  
- createRoot 替代 ReactDOM.render。  
- useEffect 依赖数组完整，避免闭包陷阱。  
- useRef 不触发渲染，用于持久可变值。  
- memo/useMemo/useCallback 不要滥用。  
- 受控组件优先，复杂表单推荐 react-hook-form。  
- dangerouslySetInnerHTML 必净化。  
- SSR/SSG/CSR 可组合：SSR 首屏 + CSR 实时。  
- 并发特性：useTransition/useDeferredValue 提升流畅度。  
- React 19：use Hook、表单 Actions、改进 Suspense。  
- 错误边界只能类组件实现。  

---

# React 基础

## 1. 什么是 React ✅

**一句话回答**  
React 是一个用于构建用户界面的 JavaScript 库，强调组件化、声明式渲染和单向数据流。

**要点**  

- 组件化、声明式。  
- 单向数据流。  
- 虚拟 DOM 与调和机制。  

**详细回答**  
React 由 Meta 开源，核心理念是：  

1. **声明式**：开发者只需描述 UI 状态，React 负责更新 DOM。  
2. **组件化**：界面拆分为独立组件，支持复用与组合。  
3. **单向数据流**：数据由父组件传递给子组件，状态管理清晰。  
4. **虚拟 DOM**：用 JS 对象描述 UI，通过 diff 算法高效更新。  

**扩展/对比**  

- React 本身只关注 UI，常搭配 React Router、Zustand、Redux。  
- 和 Vue 对比：Vue 有响应式系统，React 更多依赖显式状态更新。  

---

## 2. 虚拟 DOM 与渲染流程（含 createElement、性能与边界） ✅

**一句话回答**  
虚拟 DOM 是真实 DOM 的轻量级 JS 映射，状态变化时在内存中 diff，再将最小补丁应用到真实 DOM。  

**要点**  

- VDOM：`{ type, props, children }`。  
- React.createElement：JSX 的底层实现。  
- 渲染流程：VDOM → diff → 最小 DOM 更新。  
- 优势：提升复杂场景性能下限，但不一定总更快。  

**详细回答**  
当编写 JSX 时，Babel 会转译成 `React.createElement`：

```js
React.createElement("div", { className: "x" }, "hi");
```

React 生成 VDOM 树，更新时与旧树 diff，找出差异并批量提交到真实 DOM。  
这避免了频繁直接 DOM 操作，保证在复杂应用中性能稳定。  

**扩展/对比**  

- React 16 引入 Fiber 架构，使渲染可中断和恢复。  
- 对比 Vue：同样基于 VDOM，但策略不同（Vue 使用响应式依赖收集）。  
- 简单场景下，直接操作 DOM 可能更快。  

---

## 3. React 的 diff 算法 ✅

**一句话回答**  
React diff 基于三大假设：同层比较、用 key 标识同级子节点、不同类型直接替换。  

**要点**  

- tree diff：只比较同层。  
- component diff：同类型深入比较，不同类型直接替换。  
- element diff：同层子节点依赖 key 判断移动/插入/删除。  

**详细回答**  

- **tree diff**：跨层结构视为删除+新建。  
- **component diff**：相同组件类型继续比较子树。  
- **element diff**：通过 key 确定子节点是否可复用。  

**扩展/对比**  

- 不稳定 key（如数组下标）可能导致重建或状态错乱。  
- 这就是为什么 key 必须唯一稳定。  

---

## 4. key 的作用与陷阱 🔥

**一句话回答**  
key 用来唯一标识同层节点，避免重建或错误复用。  

**要点**  

- 稳定唯一的业务 id。  
- 避免用数组下标。  
- 动态列表必须提供 key。  

**详细回答**  
React 通过 key 判断子元素是否相同，错误的 key 会导致：  

- 不必要的重新渲染。  
- 受控输入框状态丢失。  
- 动画/过渡错乱。  

**扩展/对比**  

- 静态列表可用下标，但不推荐。  
- 服务端渲染/分页合并时，key 必须跨页稳定。  

---

# React 18 / 19 新特性

## 5. React 18/19 新特性 🔥

**一句话回答**  
React 18 引入并发渲染与自动批处理；React 19 带来 `use` Hook 和表单 Actions，让数据获取与交互更自然。  

**要点**  

- React 18：并发渲染、自动批处理、useId、Streaming SSR。  
- React 19：`use` Hook、表单 Actions、改进 Suspense、Server Components 增强。  

**详细回答**  
React 18 的并发渲染允许中断/恢复任务；自动批处理合并多个 setState。  
React 19 的 use Hook 可直接等待 Promise，表单 Actions 让 `<form>` 直接调用服务端函数，减少事件绑定样板。  

**扩展/对比**  

- 面试常问：如何用 `useTransition` 保持输入流畅？  
- Next.js 15 已深度集成这些特性。  

---

## 6. 并发渲染与调度：useTransition / useDeferredValue 🔥

**一句话回答**  
通过将更新划分优先级，保证紧急任务（输入）优先，复杂渲染延后。  

**要点**  

- useTransition：标记非紧急更新。  
- useDeferredValue：延迟派生值计算。  
- 基于 Fiber 任务切片。  

**详细回答**  

- `useTransition`：如搜索输入 + 大列表过滤，输入立即更新，过滤延后。  
- `useDeferredValue`：延迟繁重计算，避免输入抖动。  

**代码示例**  

```tsx
const [text, setText] = useState("");
const deferred = useDeferredValue(text);
const items = useMemo(() => heavyFilter(list, deferred), [deferred, list]);
```

**扩展/对比**  

- 类似防抖，但由 React 调度，UI 更流畅。  
- Vue 暂无等效 API。  

---

# Hooks 与函数组件

## 7. 常见 Hooks ✅

**一句话回答**  
常见 Hooks 包括 useState、useEffect、useMemo、useCallback、useRef、useContext、useTransition 等。  

**要点**  

- useState：状态管理。  
- useEffect：副作用处理。  
- useMemo/useCallback：缓存。  
- useRef：持久引用，不触发渲染。  
- useContext：跨层传参。  
- useTransition/useDeferredValue：并发优化。  

**详细回答**  
Hooks 让函数组件具备状态和生命周期能力，逻辑聚合在同一函数中，便于复用。  

**扩展/对比**  

- 类组件通过生命周期拆分逻辑，Hooks 聚合更直观。  

---

## 8. useEffect 与生命周期映射 🔥

**一句话回答**  
useEffect 可模拟 componentDidMount / DidUpdate / WillUnmount，不同依赖数组控制执行时机。  

**要点**  

- []：仅挂载 + 卸载。  
- [deps]：依赖变化时执行。  
- 无依赖：每次渲染执行。  

**详细回答**  

- 依赖遗漏易造成闭包陷阱。  
- 应在清理函数中移除订阅/计时器。  

**代码示例（闭包修正）**  

```tsx
useEffect(() => {
  const id = setInterval(() => setCount(c => c + 1), 1000);
  return () => clearInterval(id);
}, []);
```

**扩展/对比**  

- useLayoutEffect 在 DOM 提交后、绘制前同步执行，需谨慎使用。  

---

## 9. 自定义 Hook ✅

**一句话回答**  
自定义 Hook 提取复用逻辑，以 use 开头，通过参数/返回值与组件交互。  

**要点**  

- 提取逻辑，便于复用。  
- 可组合多个 Hook。  
- 需注意副作用清理。  

**代码示例**  

```tsx
function useCountDown(init) {
  const [count, setCount] = useState(init);
  useEffect(() => {
    const id = setInterval(() => setCount(c => c - 1), 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```

**扩展/对比**  

- 工程经验：封装防抖/节流、数据轮询、表单逻辑。  

---

## 10. Hooks 内部机制 🔥

**一句话回答**  
React 用链表记录 Hook 调用顺序，必须保证调用一致性。  

**要点**  

- Hooks 只能在顶层调用。  
- 按调用顺序绑定状态。  
- 条件语句可能破坏顺序。  

**详细回答**  
React 内部用链表存储 Hooks，每次渲染时依次匹配。如果顺序变化，状态会错乱。  

**扩展/对比**  

- useState 返回数组而非对象：解构更灵活。  

---

# 状态管理

## 11. 状态管理方案对比 🔥

**一句话回答**  
Redux 严格但繁琐，MobX 响应式，Zustand 轻量，Recoil 原子化。  

**要点**  

- Redux：单一数据源 + 不可变数据。  
- MobX：响应式，学习成本低。  
- Zustand：Hooks 风格，轻量。  
- Recoil：适合细粒度管理。  

**详细回答**  
Redux 适合大型团队；MobX 上手快；Zustand 简洁灵活；Recoil 通过 atom 拆分状态，便于组合。  

**扩展/对比**  

- 项目经验：小团队用 Zustand 替代 Redux。  

---

## 12. 受控 vs 非受控组件 ✅

**一句话回答**  
受控组件由 React state 驱动，非受控组件由 DOM 自行管理值。  

**要点**  

- 受控：value + onChange。  
- 非受控：defaultValue + ref。  
- 复杂表单推荐受控。  

**详细回答**  
受控组件便于校验与回显；非受控更接近原生。  
大型表单常用 react-hook-form 处理受控逻辑。  

**扩展/对比**  

- 面试常问：为什么选受控？答：便于校验/联动。  

---

# 性能优化

## 13. React.memo / useMemo / useCallback 🔥

**一句话回答**  
memo 控制组件渲染，useMemo 缓存值，useCallback 缓存函数。  

**要点**  

- React.memo：浅比较 props。  
- useMemo：缓存计算结果。  
- useCallback：缓存函数引用。  

**详细回答**  

- React.memo 包裹子组件。  
- useMemo 避免重复昂贵计算。  
- useCallback 稳定回调引用。  

**扩展/对比**  

- 过度 memo 会增加开销。  
- 与 PureComponent 类似。  

---

## 14. 列表优化与虚拟化 🔥

**一句话回答**  
虚拟化只渲染可视区域，大幅减少 DOM 数量。  

**要点**  

- 窗口化、占位符。  
- 固定高度 vs 可变高度。  

**详细回答**  
用 react-window 实现虚拟列表；固定高度简单，可变高度需测量。  

**扩展/对比**  

- 与分页结合。  
- SSR 时仅返回必要数据。  

---

## 15. Suspense 与懒加载 ✅

**一句话回答**  
React.lazy + Suspense 支持按需加载组件，减少首屏包体。  

**要点**  

- lazy 动态 import。  
- Suspense fallback 加载态。  

**代码示例**  

```tsx
const User = React.lazy(() => import("./User"));
<Suspense fallback={<div>loading...</div>}>
  <User />
</Suspense>
```

**扩展/对比**  

- 数据 Suspense 在 React 19 有更多支持。  

---

# 工程化与生态

## 16. Next.js App Router + Server Components 🔥

**一句话回答**  
App Router 默认 Server Components，数据获取在服务端，减少客户端包体。  

**要点**  

- RSC、Server Actions。  
- SSR/SSG/CSR。  

**详细回答**  
表单 Actions 允许 `<form action={fn}>` 直接调用服务端函数。  

**扩展/对比**  

- 与 React 19 use Hook 搭配更自然。  

---

## 17. 错误边界与异常处理 🔥

**一句话回答**  
错误边界只能类组件实现，用于捕获子组件渲染错误。  

**要点**  

- componentDidCatch / getDerivedStateFromError。  
- 不能捕获异步错误。  

**代码示例**  

```tsx
class ErrorBoundary extends React.Component {
  static getDerivedStateFromError() { return { hasError: true }; }
  componentDidCatch(err, info) { console.error(err, info); }
  render() { return this.state?.hasError ? <h1>出错了</h1> : this.props.children; }
}
```

**扩展/对比**  

- Promise 错误需 try/catch 或全局监听。  

---

# 安全与事件系统

## 18. 合成事件与原生事件 ✅

**一句话回答**  
合成事件提供统一 API，绑定在 root 容器；混用需注意传播顺序差异。  

**要点**  

- React 17 起委托 root。  
- stopPropagation 差异。  

**详细回答**  
原生 stopPropagation 会阻止合成冒泡；合成 stopPropagation 不影响原生。  

**扩展/对比**  

- 某些事件（如媒体）非合成系统。  

---

## 19. dangerouslySetInnerHTML 与 XSS ✅

**一句话回答**  
渲染富文本必须先净化，避免 XSS。  

**要点**  

- DOMPurify。  
- CSP 配合。  

**详细回答**  
使用 `dangerouslySetInnerHTML` 时，需先 DOMPurify.sanitize。  

**扩展/对比**  

- SSR 时同样需要防护。  

---



