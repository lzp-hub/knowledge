# react基础

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



