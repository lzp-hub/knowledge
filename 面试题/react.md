# ⚛️ React 面试题 · 3–5 年工程师全展开版（第 1 批：基础 → Hooks → 状态）

## 基础

### 1) 什么是 React ✅

**一句话回答**
用于构建 UI 的 JS 库，强调组件化、声明式渲染和单向数据流。

**要点**

- 组件化组合；声明式状态驱动 UI；
- 单向数据流，父到子，事件向上；
- VDOM + diff（Fiber 调度）。

**详细回答**

- **声明式**：描述“给定状态下的界面”，由 React 负责最小化 DOM 更新；更可预测，更易测试。
- **组件化**：按领域拆分（展示/容器/布局/原子 UI），降低耦合、提升复用。
- **单向数据流**：数据下行、事件上行，状态集中在“最靠近使用处的组件”，减少共享状态。
- **VDOM & 调和**：用 JS 对象描述 UI（`{type, props, children}`），通过 diff 找差异，在 **commit 阶段**一次性应用。

**扩展/对比**

- **React vs Vue**：Vue 自带响应式和 SFC 语法糖；React 更“编程模型化”，状态与数据流显式、配合 TS 更强。
- **React vs Svelte/Solid**：后者细粒度/编译期更省开销；但生态、SSR/RSC、工程化与大型团队经验，React 仍具优势。

---

### 2) 虚拟 DOM 与渲染流程（含性能与边界） ✅

**一句话回答**
状态变化 → 生成新 VDOM→ 与旧树 diff→ 得到 effectList→commit 最小变更到真实 DOM。

**要点**

- JSX 转 `createElement` 产出 VDOM；
- **Render 阶段**可中断（Fiber 时间切片）；
- **Commit 阶段**不可中断（DOM 真实变更）。

**详细回答**

1. **Render**：比较新旧树，打上“插入/删除/更新”标记（effectTag）；并发调度可分片处理、被高优先级任务打断。
2. **Commit**：统一执行 DOM 操作（插入/移动/属性更新/解绑/绑定）。
3. **性能边界**：VDOM 并非永远更快——小而简单的 DOM 操作原地更新也很快；VDOM 的优势是**复杂 UI 的下限与可维护性**。

**扩展/对比**

- React 通过 **Fiber** 支持“可打断的渲染”，让交互优先；Vue 主要靠响应式剪枝减少节点比较。
- 反例：在 render 中频繁 `map` 巨量元素、无记忆化，VDOM diff 本身也会成为瓶颈，应结合**虚拟列表/分片渲染**。

---

### 3) React 的 diff（调和）与 key 规则 ✅

**一句话回答**
三假设：**同层比较**；类型不同**直接替换**；子列表由 **key** 决定复用/删除/移动。

**要点**

- **同层**：跨层移动几乎等同删除+新建；
- **类型相同**：复用节点、递归比较子树；
- **稳定 key**：同层识别身份。

**详细回答**

- key 不稳定（如数组下标）会导致错误复用/重建：输入框丢失焦点、动画错乱、内部状态丢失。
- 插入/删除列表中间项时，错误 key 会造成**整列“移动”误判**，引发大量重渲染。

**扩展/对比**

- **分页/虚拟列表**：key 必须跨页稳定（业务 ID）；
- **SSR 水合**：key 一致性有助于稳定绑定，减少 mismatch。

---

### 4) React 18/19：并发渲染、自动批处理、Streaming SSR、`use`/Actions 🔥

**一句话回答**
18 提升调度：可中断/恢复、自动批处理、流式 SSR；19 让数据获取/表单交互更“无胶水”（`use`、Actions）。

**要点**

- `useTransition`/`useDeferredValue`；
- 自动批处理覆盖 Promise/定时器；
- 流式推送骨架 + 内容；
- `use` 等待 Promise、表单 `action={fn}`。

**详细回答**

- **并发渲染**：输入优先，昂贵渲染延后，不阻塞关键交互；
- **自动批处理**：合并多次 setState，减少不必要 commit；
- **Streaming SSR**：先流式输出骨架，随后填充缓慢区块（配合 Suspense 更佳）；
- **19 `use` & Actions**：直接 `await` 数据/调用函数，减少繁琐事件样板，结合框架（Next.js）更顺滑。

**扩展/对比**

- 场景举例：搜索输入 + 大列表过滤 → `useTransition`；图表/编辑器 → 懒加载 + Suspense + 流式 SSR。
- 反例：将所有更新都标记为 transition 反而延迟反馈；要抓住“紧急 vs 非紧急”的边界。

---

## Hooks

### 5) `useEffect` vs `useLayoutEffect` vs `useInsertionEffect` 🔥

**一句话回答**
Effect 异步绘制后；Layout 同步绘制前（读写布局）；Insertion 更早用于注入样式（多由 CSS-in-JS 库内部使用）。

**要点**

- **Effect**：数据/订阅/日志，不阻塞绘制；
- **Layout**：测量并立刻改样式，避免闪烁（阻塞帧）；
- **Insertion**：样式在 Layout 前插入，避免 FOUC。

**详细回答**

- **闭包陷阱**：依赖数组遗漏导致使用旧值；用函数式更新或补全依赖。
- **SSR 警告**：没有 DOM 时 `useLayoutEffect` 会警告，条件降级或仅浏览器执行。
- 典型场景：**展开面板**初次渲染需要同步测量高度并设置样式 → `useLayoutEffect`。

**扩展/对比**

- 性能权衡：Layout 会阻塞绘制，**能用 Effect 就别用 Layout**。
- Vue 用 `nextTick()` 可实现类似“渲染后”钩子，但没有三分层区分。

---

### 6) 自动批处理 & `flushSync` 🔥

**一句话回答**
18 起多源异步也会自动批处理 setState；必须立即读新 DOM 时可用 `flushSync` 强制同步提交（慎用）。

**要点**

- 事件/Promise/`setTimeout`… 都批处理；
- `flushSync(cb)`：立刻 commit，便于测量/滚动。

**详细回答**

```ts
setCount((c) => c + 1);
setFlag(true);
// 18：一次 commit 完成
```

需要“**先开弹窗，立刻测量尺寸**”：

```ts
import { flushSync } from "react-dom";
flushSync(() => setOpen(true));
measureModal(); // 使用最新 DOM
```

频繁 `flushSync` 会破坏并发优势，只有当“**读 DOM 立刻用**”才使用。

**扩展/对比**

- 批处理 ≠ 跳过渲染，只是合并到一次提交；
- 与“防抖/节流”不同，批处理发生在 React 调度层。

---

### 7) Hooks 规则与内部机制（为什么“不能放 if 里”） ✅

**一句话回答**
React 按**调用顺序**为 Hook 分配存储槽，顺序一旦变化就读错“格子”。

**要点**

- 只在顶层调用；
- 依赖 `eslint-plugin-react-hooks` 保证顺序；
- 违反会出现状态错乱/无限循环。

**详细回答**
每次渲染 React 都“按顺序”匹配 Hook，if/循环中调用会造成偏移；
无限循环常因 setState 未加依赖/记忆化、Effect 依赖写错引起。

**扩展/对比**

- `useState` 返回数组（非对象）便于重命名，避免字段冲突。

---

### 8) `useEffect` 闭包陷阱、取消与幂等 🔥

**一句话回答**
Effect 捕获旧值要靠依赖/函数式更新修复；异步副作用需**可取消**或**幂等**，以适配 StrictMode 与多次触发。

**要点**

- 依赖数组完整；
- **AbortController** 取消请求；
- 清理函数释放订阅/定时器。

**详细回答**

```ts
useEffect(() => {
  const ac = new AbortController();
  fetch('/api', { signal: ac.signal }).then(...).catch(e => {
    if (e.name !== 'AbortError') throw e;
  });
  return () => ac.abort();
}, [query]);
```

StrictMode DEV 下会“装卸再装”，未取消会导致重复请求/内存泄漏。

**扩展/对比**

- 数据请求若前移到框架（Next.js RSC/Route Handler），前端 Effect 压力更小。

---

### 9) `useMemo` / `useCallback` / `React.memo` 用法与误用 🔥

**一句话回答**
`useMemo` 缓值、`useCallback` 缓函数、`React.memo` 缓组件；先稳引用再记忆化，避免“亏本优化”。

**要点**

- 渲染瓶颈才上 memo；
- props 引用稳定是前提；
- 大计算才值得 `useMemo`。

**详细回答**

- 把常量/配置移到组件外或 `useMemo`；
- 对子组件使用 `React.memo` 并结合 **稳定 props**；
- 对昂贵计算再 `useMemo`，避免每次重复算。

**扩展/对比**

- “到处加 memo”的反模式：比较成本 + 内存开销可能更高；
- 用 Profiler 找真正热区再优化。

---

### 10) 自定义 Hook（抽离复用逻辑） ✅

**一句话回答**
以 `use` 开头抽离“横切逻辑”，返回状态/方法，注意副作用清理与参数化。

**要点**

- 防抖/节流、轮询、权限、网络状态；
- 输入参数+返回值契约清晰；
- 组合多个 Hook 构建领域 Hook。

**详细回答**

```ts
function useDebouncedValue<T>(v: T, delay = 300) {
  const [val, setVal] = useState(v);
  useEffect(() => {
    const id = setTimeout(() => setVal(v), delay);
    return () => clearTimeout(id);
  }, [v, delay]);
  return val;
}
```

工程上形成“**领域 Hook**”：`useAuth()`、`useCart()`、`useFeatureFlag()`。

**扩展/对比**

- 与 **HOC/Render Props** 相比，Hook 代码更线性、组合性更好；HOC 更适合封装跨库适配。

---

## 状态

### 11) 受控 vs 非受控组件 ✅

**一句话回答**
受控用 React state 驱动（value/onChange）；非受控交给 DOM（defaultValue/ref）。复杂表单更偏受控或 RHF 方案。

**要点**

- 受控：易校验/联动/回显；
- 非受控：接近原生、性能好；
- `react-hook-form`：注册机制减少重渲染。

**详细回答**
大型表单**按字段拆分**、只渲染视区、异步校验“可取消”；对中文输入（IME）做防抖或“输入完成再校验”。

**扩展/对比**

- 受控会触发更多渲染；RHF 通过非受控+注册绕开这一点（只变更的字段参与渲染）。

---

### 12) 状态提升与“最小共享原则” 🔥

**一句话回答**
把需要被多个子组件使用的状态放到**最近的公共父级**；能局部就不全局，能计算就不存储。

**要点**

- 最小共享：减少不必要渲染；
- 派生值不入库（即算即用）；
- 复杂再引入全局 store。

**详细回答**

- 反例：将简单局部状态放到 Redux，导致全局订阅抖动；
- 派生数据（计数、筛选结果）由 `useMemo` 计算，而不是冗余存储。

**扩展/对比**

- 提升过度会导致“**提升地狱**”，考虑子 → 父回调或 Context/外部 store。

---

### 13) 全局状态方案对比：Redux / MobX / Zustand / Recoil 🔥

**一句话回答**
Redux 规范严格、样板多；MobX 响应式易用；Zustand 轻量高性能；Recoil 原子粒度细。

**要点**

- Redux：不可变、时间旅行、DevTools；
- MobX：自动追踪依赖；
- Zustand：`create` store + selector，Hook 化；
- Recoil：atom/selectors 粒度细致。

**详细回答**

- 中大型协作/严格审计：Redux；
- 复杂交互但想上手快：MobX；
- 新项目小中型/TS 友好：Zustand；
- 需要跨组件**细粒度**更新：Recoil。

**扩展/对比**

- 关键点：**selector + 比较函数** 降低重渲染；Context 传播大对象是常见性能坑。

---

### 14) `useReducer` vs `useState`（复杂度与可测试性） 🔥

**一句话回答**
更新逻辑复杂/多分支时用 `useReducer`（纯函数易测试）；简单局部用 `useState`。

**要点**

- reducer 集中管理状态迁移；
- 适合步骤流、表单/向导、状态机；
- 可配合 Immer 简化不可变写法。

**详细回答**

```ts
type Action =
  | { type: "next" }
  | { type: "reset" }
  | { type: "goto"; step: number };
function reducer(s: { step: number }, a: Action) {
  switch (a.type) {
    case "next":
      return { step: s.step + 1 };
    case "reset":
      return { step: 0 };
    case "goto":
      return { step: a.step };
  }
}
const [state, dispatch] = useReducer(reducer, { step: 0 });
```

reducer 让行为统一、便于单测与回溯；**别为小状态过度抽象**。

**扩展/对比**

- 与 Redux 思想一致，未来需要全局化时迁移成本低；
- Immer（`produce`）能降低不可变更新样板。

---

# ⚛️ React 面试题 · 3–5 年工程师全展开版（第 2 批：性能 → 架构 → 并发 → 工程化）

## 性能优化

### 15) 虚拟列表与分片渲染 🔥

**一句话回答**
虚拟列表：只渲染可见元素；分片渲染：大任务拆小批，避免阻塞主线程。

**要点**

- 虚拟列表：react-window / react-virtualized；减少 DOM 数量。
- 分片渲染：利用 `requestIdleCallback` / `scheduler` / `startTransition`。
- 结合骨架屏，提升感知性能。

**详细回答**

- **虚拟列表**：根据滚动位置只渲染 viewport 区域，其他用占位符。

  - 固定高度：计算简单。
  - 可变高度：需动态测量（ResizeObserver）。

- **分片渲染**：渲染大表格/树形结构时，将渲染任务拆成小块，在空闲时调度。

  - React 18+ 可用 `startTransition` 将低优先级任务延后。

**扩展/对比**

- 面试官可能要求手写简易虚拟滚动实现。
- **Vue** 没有内置分片渲染 API，通常靠第三方库或手动 `nextTick` 拆分。

---

### 16) React Profiler 与 Web Vitals 🔥

**一句话回答**
Profiler 检测组件渲染性能；Web Vitals 度量用户体验（LCP/FID/CLS）。

**要点**

- Profiler API：`<Profiler onRender={cb}>`。
- React DevTools → “Profiler” 面板。
- Web Vitals：LCP、FID、CLS、TTFB、INP。

**详细回答**

- **Profiler**：能看到组件渲染次数、耗时，找出“频繁 re-render”瓶颈。
- **Web Vitals**：谷歌标准化指标，用于衡量真实用户体验。

  - LCP：最大内容绘制。
  - FID/INP：输入延迟。
  - CLS：布局偏移。

**扩展/对比**

- 常见面试追问：**“如何定位 React 性能问题？”**
  👉 DevTools Profiler + Web Vitals 上报 + 代码级优化（memo/拆分）。

---

## 架构与健壮性

### 17) ErrorBoundary 与 Suspense 🔥

**一句话回答**
ErrorBoundary 捕获渲染错误；Suspense 处理异步渲染与懒加载。

**要点**

- ErrorBoundary：类组件 `componentDidCatch`；捕获渲染/生命周期错误。
- Suspense：搭配 lazy 或数据请求，展示 fallback。
- 二者可组合：错误 → 兜底 UI；等待 → Loading。

**详细回答**

- **ErrorBoundary**：

  - 无法捕获异步错误（需 try/catch）和事件处理器错误。

- **Suspense**：

  - 组件返回 Promise → React 挂起 → 渲染 fallback。
  - 常用于数据请求（React 19 `use`）、lazy 动态 import。

**扩展/对比**

- Vue 3 提供 `<Suspense>` 类似能力，但错误边界机制更弱。
- 工程实践：统一“错误/加载兜底 UI 组件”，减少重复代码。

---

### 18) Portal 与焦点管理 🔥

**一句话回答**
Portal 渲染到 DOM 树外但保持 React 树联系；常用于弹窗/悬浮层，需额外管理焦点与可访问性。

**要点**

- `createPortal(children, container)`。
- 常见容器：`document.body`。
- 需要处理：焦点陷阱、键盘导航、ARIA 属性。

**详细回答**

- **场景**：Modal、Tooltip、Dropdown → 需要覆盖父容器布局。
- **问题**：Tab 焦点可能“跑出弹窗”，需要焦点管理（focus trap）。
- **解决**：使用 `react-aria`、`focus-trap` 或手写逻辑。

**扩展/对比**

- 面试官可能问：“为什么 Modal 要用 Portal？” 👉 避免被父容器 overflow/z-index 干扰。
- 工程实践：配合 `aria-modal="true"`、`role="dialog"` 保证 A11y。

---

### 19) Context 性能问题与 selector 优化 🔥

**一句话回答**
Context 更新会导致所有消费组件重渲染；用 selector/拆分 Context 优化。

**要点**

- Context → 全量广播。
- 优化方式：

  - 拆分多个 Context。
  - `useContextSelector` / Zustand。

- 不要把频繁更新的值放全局 Context。

**详细回答**

- **性能坑**：全局 Context（如大对象 store）频繁变化 → 所有消费者 re-render。
- **优化**：

  - 将静态配置与动态状态分离。
  - 使用第三方库（Zustand/Recoil）做 selector 精细订阅。

**扩展/对比**

- 面试常问：“Context 和 Redux 区别？” 👉 Redux 有精细 selector 与 DevTools；Context 更轻量，但缺少优化机制。

---

## 并发特性

### 20) useSyncExternalStore 🔥

**一句话回答**
React 18 推荐订阅外部 store 的 API，避免 Context 广播导致的性能问题。

**要点**

- `useSyncExternalStore(subscribe, getSnapshot)`。
- 确保并发模式下快照一致。
- Redux、Zustand 等库已基于此实现。

**详细回答**

```ts
const state = useSyncExternalStore(store.subscribe, store.getSnapshot);
```

保证在并发渲染时，读取到的快照和订阅是一致的，不会出现“撕裂”（tearing）。

**扩展/对比**

- React 18 以前用 `useEffect+useState` 手动订阅，存在竞态。
- 面试亮点：说明 tearing 问题与并发渲染关系。

---

### 21) 水合与 mismatch 问题 🔥

**一句话回答**
水合：SSR HTML 与客户端 React 树绑定；Mismatch：两者不一致，可能导致报错或 UI 闪烁。

**要点**

- SSR：提升首屏体验。
- Hydration：附加事件绑定，React 树接管。
- Mismatch：HTML 与渲染结果不同。

**详细回答**

- 常见 mismatch 原因：

  - 时间戳/随机数不一致。
  - 条件渲染逻辑不同（window 对象依赖）。

- React 会尝试修复 mismatch，但可能造成闪烁。

**扩展/对比**

- 面试追问：“如何避免水合 mismatch？” 👉 保证 SSR 与 CSR 渲染逻辑一致，敏感代码放 `useEffect`。

---

### 22) StrictMode 的作用与坑 🔥

**一句话回答**
StrictMode 在 DEV 环境帮助发现问题：副作用执行两次、弃用 API 警告。

**要点**

- 检测不安全生命周期。
- 模拟卸载/重装 effect。
- 检测过时 API（如 findDOMNode）。

**详细回答**

- **Effect 双执行**：模拟 mount/unmount，暴露副作用未清理问题。
- **并发模式准备**：保证组件幂等性。
- **生产环境不会双执行**。

**扩展/对比**

- 面试官常问：“为什么 effect 执行两次？” 👉 StrictMode 故意的，帮助发现问题。

---

## 工程化与测试

### 23) TS 类型设计：Props 与状态 🔥

**一句话回答**
Props 用接口/泛型定义；状态推导自 `useState`；避免 any，善用联合/条件类型。

**要点**

- Props 接口 + 泛型组件。
- `ReturnType<typeof fn>`。
- 组件库需提供声明文件。

**详细回答**

```ts
interface ButtonProps {
  type?: "primary" | "secondary";
  onClick?: () => void;
}
function Button({ type = "primary", onClick }: ButtonProps) {
  return <button>{type}</button>;
}
```

- 复杂场景：用泛型 + 条件类型，保证类型安全。
- 状态：`useState<null | User>()`。

**扩展/对比**

- 面试常问：“如何在 TS 里设计组件 Props？” 👉 使用 `Pick`/`Omit` 组合已有类型，减少重复。

---

### 24) React 测试与可测性 🔥

**一句话回答**
测试关注“行为”而非实现，推荐 RTL + Jest；可测性来自解耦与状态下沉。

**要点**

- 单元测试：逻辑函数。
- 组件测试：React Testing Library。
- E2E：Playwright / Cypress。

**详细回答**

- RTL 提倡测试用户行为：点击/输入/看到文本。
- 避免测试内部 state，关注输出 UI。
- 可测性原则：状态单一来源、组件职责清晰。

**扩展/对比**

- 面试追问：“RTL 和 Enzyme 区别？” 👉 RTL 注重行为；Enzyme 偏实现细节，逐渐被淘汰。

---

### 25) Next.js：RSC、Actions、缓存策略 🔥

**一句话回答**
RSC 让组件在服务端渲染，Actions 让表单直连服务端，缓存策略保证性能与一致性。

**要点**

- RSC：减少客户端包体。
- Actions：`<form action={fn}>`。
- 缓存：`fetch` 默认缓存，`revalidate` 控制。

**详细回答**

- **RSC**：数据获取在服务端 → 前端包更轻。
- **Actions**：避免手写 API 请求，直接调用服务端函数。
- **缓存策略**：

  - `force-cache`（默认）。
  - `no-store`（实时）。
  - `revalidate=60`（ISR）。

**扩展/对比**

- 面试常问：“Next.js 如何保证页面性能？” 👉 RSC + Streaming + 缓存分层。

---

# ⚛️ React 面试题 · 3–5 年工程师全展开版（第 3 批：进阶专题）

## 一、事件系统

### 26) React 合成事件 vs 原生事件 🔥

**一句话回答**
合成事件是 React 的跨浏览器统一封装，委托到 root 容器；和原生事件在冒泡/解绑时有差异。

**要点**

- React 17 起事件委托到 **root**，非 document。
- `stopPropagation`：合成与原生事件可能互不影响。
- 统一 API：兼容性好，性能可控。

**详细回答**

- React 将事件包装为 `SyntheticEvent`，回收利用以降低 GC 压力。
- 在 React 事件里调用 `stopPropagation()`，不会阻止原生监听器；反之同理。
- 部分事件（如 `scroll`, `media events`）是原生绑定。

**扩展/对比**

- 面试追问：“为什么 React 要自己实现合成事件？” 👉 统一兼容、方便事件优先级调度。
- 工程实践：全局监听（如 `window.resize`）应用原生事件。

---

## 二、动画与过渡

### 27) React 中的动画方案对比 🔥

**一句话回答**
常见方案有 CSS Transition、ReactTransitionGroup、Framer Motion；前两者轻量，后者强大。

**要点**

- CSS Transition：声明式，性能好，受限。
- ReactTransitionGroup：组件挂载/卸载过渡。
- Framer Motion：手势/动画/物理模型。

**详细回答**

- 简单 hover/渐隐 → CSS 即可。
- 需要组件进入/离开动画 → `CSSTransition`/`TransitionGroup`。
- 高交互（拖拽/弹性动画） → Framer Motion。

**扩展/对比**

- 面试追问：“为什么推荐 transform/opacity 动画？” 👉 只影响合成层，不触发布局。
- Vue 也有内置 `<transition>`，React 更依赖库。

---

## 三、可访问性（A11y）

### 28) React 中的可访问性实践 🔥

**一句话回答**
通过语义化标签、ARIA 属性、键盘可达性，保证所有用户都能操作 UI。

**要点**

- `role="dialog"`, `aria-modal="true"` 用于 Modal。
- 焦点管理：打开弹窗 → 聚焦到第一个可交互元素。
- 键盘支持：`onKeyDown` 捕获 `Enter`/`Space`。

**详细回答**

- React 不会自动处理可访问性，需要开发者手动添加 `aria-*` 属性。
- 工具：`eslint-plugin-jsx-a11y` 检查常见错误。
- 案例：关闭按钮 → `<button aria-label="关闭">×</button>`。

**扩展/对比**

- 面试追问：“React 和 Vue 在 A11y 上区别？” 👉 差不多，关键是工程规范与自动化检查。
- 大厂项目中 A11y 是合规必查项。

---

## 四、监控与埋点

### 29) React 错误监控与埋点 🔥

**一句话回答**
错误监控：ErrorBoundary + 日志上报；埋点：手动/无埋点结合，上传用户行为与性能指标。

**要点**

- ErrorBoundary：捕获渲染错误。
- window\.onerror / unhandledrejection：捕获全局错误。
- 性能监控：Performance API，上报 LCP/FID/CLS。

**详细回答**

- 常用平台：Sentry、阿里 ARMS、字节 OneAPM。
- 埋点方案：手动埋点（按钮点击）、无埋点（全局代理）。
- React 特性：配合 Context 提供埋点 hook → `useTrackEvent("click_button")`。

**扩展/对比**

- 面试官常问：“如何定位线上白屏？” 👉 ErrorBoundary 捕获 + SourceMap 反解 + 性能日志。

---

## 五、安全与防护

### 30) React 中的 XSS 风险 🔥

**一句话回答**
默认插值安全，`dangerouslySetInnerHTML` 有风险，需转义或使用 DOMPurify。

**要点**

- React JSX 默认转义 → `<div>{userInput}</div>` 安全。
- `dangerouslySetInnerHTML={{__html: ...}}` 需净化。
- 服务端 + CSP 配合。

**详细回答**

- 攻击者可通过评论区等输入注入 `<script>`。
- 防护：转义输出 + DOMPurify 白名单。
- CSP（Content-Security-Policy）进一步限制脚本来源。

**扩展/对比**

- 面试追问：“为什么叫 dangerously？” 👉 强调这是危险 API，只在必要场景（富文本）使用。

---

## 六、测试与可测性

### 31) React 组件测试策略 🔥

**一句话回答**
单测逻辑函数，组件用 React Testing Library 行为驱动，端到端用 Cypress/Playwright。

**要点**

- RTL：测试用户行为（点击/输入/看到文本）。
- Jest：Mock + 断言。
- E2E：真实浏览器环境。

**详细回答**

- RTL 鼓励 `screen.getByText` 而非 `.state`。
- Mock API：`msw`（Mock Service Worker）。
- 可测性设计：拆小组件、状态外置。

**扩展/对比**

- Enzyme 测试内部实现，已逐渐过时。
- 面试官可能问：“如何保证复杂表单可测性？” 👉 把校验逻辑抽离成纯函数单测。

---

## 七、团队工程经验

### 32) 组件库设计经验 🔥

**一句话回答**
组件库需 API 简洁、可扩展、支持主题化与无障碍，避免过度封装。

**要点**

- API 设计：props 清晰，支持泛型。
- 样式：CSS Variables / Tailwind / CSS-in-JS。
- 无障碍：默认带 aria 属性。

**详细回答**

- Button：支持 `as` prop、不同 size/variant。
- Input：forwardRef + error 状态 + 描述文本。
- 提供 TypeScript 类型声明。

**扩展/对比**

- 面试官可能问：“如何避免库被锁死？” 👉 提供 `className`/`style` 透传。

---

## 八、总结

这一批（26–32 题）把 React 在 **事件系统、动画、A11y、监控、安全、测试、组件库经验** 都补齐了。到这里三批加起来已经 **40+ 高频考点**，基本覆盖 3–5 年工程师的 React 技术面试范围。

---
