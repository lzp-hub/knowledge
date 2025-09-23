# ⚛️ React 面试题 · 3–5 年工程师全展开版

### 1) 什么是 React ✅

**一句话回答**
用于构建 UI 的 JS 库，强调组件化、声明式渲染和单向数据流。

**要点**

- 组件化组合；声明式状态驱动 UI；
- 单向数据流，父到子，事件向上；
- VDOM + diff（Fiber 调度）。

**详细回答**

- **声明式**：描述“给定状态下的界面”，**由 React 负责最小化 DOM 更新**；更可预测，更易测试。
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

### 5) `useEffect` vs `useLayoutEffect` vs `useInsertionEffect` 🔥

**一句话回答**
Effect 异步绘制后；Layout 同步绘制前（读写布局）；Insertion 更早用于注入样式（多由 CSS-in-JS 库内部使用）。

**要点**

- **Effect**：数据/订阅/日志，不阻塞绘制；
- **Layout**：测量并立刻改样式，避免闪烁（阻塞帧）；获取元素宽高、滚动位置，然后立即调整布局
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

### 8) `useEffect` 的“过期闭包”、重复触发与竞态 🔥

**一句话版**
 `useEffect` 会捕获**当次渲染**的值；依赖不全会读到**旧值**。在 StrictMode/并发场景里，副作用可能**多次触发**，因此要做到**可取消**或**结果幂等**。

**要点**

- 依赖数组**必须完整**（配合 `eslint-plugin-react-hooks`）。
- 异步副作用要**可取消**（`AbortController`）或**只采纳最新结果**（请求序号/时间戳）。
- 在清理函数里**释放订阅/定时器/观察器**，避免泄漏。

**详细说明**

- **过期闭包**：Effect 读取的是触发它那次渲染的变量快照；若依赖缺失，会用到旧快照。
- **多次触发**：StrictMode（开发环境）会“挂载→卸载→再挂载”，并且并发渲染也可能重复启动异步流程；如果不做取消或幂等，就会**重复请求**或**覆盖最新数据**。
- **幂等结果**：无论触发多少次，只让**最后一次**的结果落地。

**代码模板（可取消 + 捕捉最新）**

```ts
useEffect(() => {
  const ac = new AbortController();
  let seq = Symbol();            // 标记本次请求的唯一序号
  const current = seq;

  (async () => {
    try {
      const res = await fetch(`/api?query=${encodeURIComponent(query)}`, { signal: ac.signal });
      const data = await res.json();
      // 只在“仍是最新的一次”且“未被取消”时落地
      if (!ac.signal.aborted && current === seq) {
        setData(prev => data);   // 使用函数式更新，避免读到旧 state
      }
    } catch (e: any) {
      if (e.name !== 'AbortError') throw e;
    }
  })();

  return () => ac.abort();        // 组件卸载或依赖变化：取消未完成的请求
}, [query]);
```

**常见变体**

- **请求竞态只采纳最新**（不方便用 `AbortController` 的场景）：
  - 用 `useRef<number>` 维护自增 `requestId`；落地前对比 `requestId === latestIdRef.current`。
- **订阅/计时器**：在清理函数里统一 `unsubscribe()` / `clearTimeout()` / `disconnect()`。
- **能不用 Effect 就不用**：纯派生数据用 `useMemo`，布局用 `useLayoutEffect`，样式注入交给库（`useInsertionEffect`）。

**StrictMode 说明**

- 开发环境会“先装再卸再重装”，用于发现副作用的泄漏与不纯；**生产环境不会双挂载**，但并发/切换依赖仍会让副作用多次启动，因此**可取消 / 幂等**依然必要。

**扩展/对比**

- 将数据请求前移到服务端（如 **Next.js RSC / Route Handler**）可减少前端 `useEffect` 的复杂度与竞态问题。

---

### 9) `useMemo` / `useCallback` / `React.memo` 用法与误用 🔥

**一句话回答**
 `useMemo` 缓存**值**，`useCallback` 缓存**函数**，`React.memo` 缓存**组件渲染结果**；先保证 props 引用稳定，再考虑记忆化，否则就是“亏本优化”。

**要点**

- 优化不是免费的：缓存维护、依赖对比都有成本；
- `useMemo` 适合**昂贵计算**，而不是小数值；
- `useCallback` 只在函数作为 props/依赖时有价值；
- `React.memo` 前提是 props **引用稳定**；
- 用 Profiler 找瓶颈，不要无脑加 memo。

**详细回答**

- **useMemo**：缓存计算结果，避免每次渲染重复执行复杂逻辑，例如排序、大数据聚合、图表数据预处理；

  ```
  const sorted = useMemo(() => heavySort(list), [list]);
  ```

  ⚠️ 小计算不必用，缓存反而更慢。

- **useCallback**：缓存函数引用，常用于把函数传递给子组件，避免因为“函数每次都是新引用”导致子组件重渲染；

  ```
  const handleClick = useCallback(() => doSomething(value), [value]);
  ```

  ⚠️ 忘记加依赖会遇到闭包陷阱。

- **React.memo**：缓存子组件渲染输出，只有 props 变化时才重新渲染，常用于展示型组件、列表项；

  ```
  const Child = React.memo(({ value }) => <div>{value}</div>);
  ```

  ⚠️ 如果 props 是 `{}` / `[]` / `()=>{}` 这种 inline 表达式，memo 无效。

- **配合关系**：

  - 用 `useCallback` / `useMemo` 让 props 稳定；
  - 再用 `React.memo` 包子组件，避免无意义重渲染。

**扩展/对比**

- “到处加 memo”是一种反模式：比较 props + 内存维护可能比直接渲染更耗；
- 使用 **React Profiler / why-did-you-render** 定位真实瓶颈；
- 虚拟列表（react-window 等）对性能提升更显著；
- RSC/SSR 场景下，前端负担小，memo 使用要更保守。

------

### 10) 自定义 Hook（抽离复用逻辑） ✅

**一句话回答**
 以 `use` 开头抽离可复用的“横切逻辑”，通过输入参数与返回值形成契约；内部注意副作用清理与依赖管理，组合多个基础 Hook 可形成“领域 Hook”。

**要点**

- 常见模式：防抖/节流、轮询、权限校验、网络状态监听、表单管理；
- 输入参数清晰（可配置），返回值明确（状态 + 操作方法）；
- 组合多个内置 Hook 构建领域 Hook（如 `useAuth`、`useCart`）；
- 内部副作用要保证 **清理函数** 完整，避免内存泄漏；
- 参数变化时副作用需要正确响应，依赖数组必须完整。

**详细回答**

- 自定义 Hook 是 **逻辑层的抽象**，不涉及 UI 渲染，只组织状态与副作用。
- 典型例子 —— 防抖值：

```ts
function useDebouncedValue<T>(value: T, delay = 300) {
  const [val, setVal] = useState(value);

  useEffect(() => {
    const id = setTimeout(() => setVal(value), delay);
    return () => clearTimeout(id); // 清理副作用，避免内存泄漏
  }, [value, delay]);

  return val;
}
```

- 可以进一步构建更高层的 **领域 Hook**：
  - `useAuth()`：封装登录、登出、鉴权状态；
  - `useCart()`：购物车状态与操作；
  - `useFeatureFlag()`：实验开关与灰度逻辑。

这样在业务组件中只需调用 `const { user, login } = useAuth()`，逻辑简洁且统一。

**扩展/对比**

- 相比 **HOC（高阶组件）/Render Props**：
  - Hook 代码更**线性**（顺序执行，无嵌套地狱）；
  - 组合性更强，可直接在一个 Hook 内部调用多个 Hook；
- HOC 更适合用于跨库适配（如 Redux `connect`），Hook 更适合本地化逻辑复用。

---

### 11) 受控 vs 非受控组件 ✅

**一句话回答**
 受控组件由 React state 全程驱动（`value` + `onChange`），非受控组件交给 DOM 自己管理（`defaultValue` + `ref`）；复杂表单通常偏向受控或用 RHF 这样的混合方案。

**要点**

- **受控组件**：值存储在 React state，方便做校验、联动、回显；
- **非受控组件**：值存储在 DOM，自然性能更好、实现更接近原生；
- **第三方方案**：`react-hook-form` 通过“注册 + 非受控”为主，减少无关字段渲染。

**详细回答**

- **受控组件**：

  - 输入框、选择框的值由 React state 管理。
  - 适合需要实时联动（如动态校验、跨字段依赖、输入回显）。
  - 缺点：每次输入都会触发重新渲染，性能压力大，尤其在大表单场景。

  ```tsx
  const [name, setName] = useState('');
  <input value={name} onChange={e => setName(e.target.value)} />
  ```

- **非受控组件**：

  - 初始值通过 `defaultValue` / `defaultChecked` 设置，后续状态由 DOM 自己维护。
  - 最终通过 `ref` 获取值。
  - 性能更好，适合简单表单或性能敏感场景。

  ```tsx
  const ref = useRef<HTMLInputElement>(null);
  <input defaultValue="Tom" ref={ref} />
  // 提交时读取 ref.current?.value
  ```

- **复杂表单策略**：

  - **按字段拆分**：避免整个表单一个 state，减少无谓渲染。
  - **虚拟化渲染**：只渲染视口内的表单项。
  - **异步校验可取消**：配合 `AbortController` 或“最后一次 wins”。
  - **中文输入法（IME）**：防抖校验，或 `onCompositionEnd` 时再校验，避免输入过程频繁触发。

**扩展/对比**

- **受控组件**：逻辑更统一，但输入频繁会导致 React 重渲染。
- **非受控组件**：性能优，但不利于做复杂校验与联动。
- **React Hook Form (RHF)**：底层以非受控为主，通过 `register` 机制只让变更字段更新，避免整表单重渲染；结合 `Controller` 也能与受控组件共存。

------

### 12) 状态提升与“最小共享原则” 🔥

**一句话回答**
 把需要被多个子组件共同依赖的状态提升到**最近的公共父级**；能局部就不全局，能推导就不存储，遵循“最小共享原则”。

**要点**

- **最小共享**：只把真正需要共享的状态提升，减少渲染波及面；
- **派生值不入库**：能通过现有 state 推导出的数据，用 `useMemo` / 计算属性生成，而不是重复存储；
- **复杂度递进**：从局部 state → 父级提升 → Context → 外部 store，逐步增加抽象。

**详细回答**

- **状态提升**：

  - 当两个子组件需要共享状态时，将该状态放到它们的最近公共父组件，由父组件通过 props 下发。
  - 示例：搜索框输入关键字，列表需要过滤显示 → 搜索关键字提升到父组件，父传子。

- **反例**：

  - 把一个只在局部组件使用的布尔状态（如 modal 打开/关闭）放到 Redux → 全局订阅都被动触发渲染，性能浪费。
  - **正确做法**：保持局部化，避免状态膨胀。

- **派生数据**：

  - 例如计数、过滤结果、排序列表，不需要放进 state，而是用 `useMemo` 在渲染时按需计算：

    ```tsx
    const filtered = useMemo(() => items.filter(i => i.visible), [items]);
    ```

  - 避免“源数据 + 派生数据”双重维护导致的同步问题。

- **复杂场景**：

  - 当状态跨层级过深时，可考虑 **Context** 提供共享；
  - 当状态量巨大、逻辑复杂（例如权限、用户信息、多模块数据）时，再引入 **全局 store（Redux, Zustand, Jotai 等）**。

**扩展/对比**

- **过度提升 = 提升地狱**：所有逻辑都集中在顶层父组件，组件耦合度高、难以维护；
- 解决办法：
  - **子传父回调**：让子组件把事件回传给父级，避免父级状态过重；
  - **Context / 外部 store**：只在跨多层级、多模块时使用，不要一开始就全局化。

------

### 13) 全局状态方案对比：Redux / MobX / Zustand / Recoil 🔥

**一句话回答**
 Redux 规范严格、生态成熟；MobX 响应式易用、学习曲线低；Zustand 轻量高性能、函数式 API；Recoil 原子化粒度细，天然适配复杂依赖关系。

**要点**

- **Redux**：单一 store、不可变数据、纯函数 reducer；支持时间旅行、强大的 DevTools，适合团队协作与审计。
- **MobX**：响应式编程，自动追踪依赖，修改即生效；代码量少，但约束弱。
- **Zustand**：函数式 API，`create` store + selector，轻量、无样板代码，天然 Hook 化，TS 体验好。
- **Recoil**：原子（atom） + 派生（selector），粒度细，跨组件依赖关系自然解耦。

**详细回答**

- **Redux**
  - 优势：严格单向数据流、强工具链（Redux DevTools）、中间件生态（redux-saga、redux-thunk）。
  - 适用场景：中大型项目、多人协作、需要严格可追踪/回溯的状态管理。
  - 劣势：样板代码多（action、reducer、dispatch），心智负担偏大。
- **MobX**
  - 优势：基于 observable 的响应式系统，声明式且自动追踪依赖；写法贴近 OOP，入门快。
  - 适用场景：需要快速上手、对复杂交互（表单、UI 状态）友好。
  - 劣势：过于灵活，状态变更不可见；调试和审计能力弱于 Redux。
- **Zustand**
  - 优势：极简 API（`create` store），天然支持 Hook 调用；选择器 + 比较函数避免不必要重渲染；与 TS 结合优秀。
  - 适用场景：新项目、小中型应用、性能敏感场景。
  - 劣势：生态相对 Redux 小，缺乏统一规范，团队协作需自定约束。
- **Recoil**
  - 优势：以 atom 为最小状态单元，天然支持细粒度更新；selector 可做派生计算，跨组件依赖管理优雅。
  - 适用场景：组件粒度复杂、状态依赖链较长的应用（如大型表单、可视化编辑器）。
  - 劣势：仍在发展中，生态工具和社区沉淀不如 Redux 成熟。

**扩展/对比**

- **性能优化核心点**：
  - Redux：依赖 `connect` + `mapStateToProps` 的精细选择；
  - MobX：自动依赖收集，但要注意滥用导致不可预测更新；
  - Zustand：推荐使用 selector + `shallow` 比较，避免全组件重渲染；
  - Recoil：天生原子化更新，但要注意 selector 链条过长可能增加调度成本。
- **Context 的局限**：
  - 直接用 Context 存大对象，会导致 Provider 下所有消费组件重新渲染；
  - 状态库的价值就在于**更精细的订阅与更新控制**。

------

### 14) `useReducer` vs `useState`（复杂度与可测试性） 🔥

**一句话回答**
 逻辑简单时用 `useState`；当状态更新涉及多分支/多条件时，用 `useReducer` 把迁移逻辑集中在纯函数里，更易维护与测试。

**要点**

- **useState**：适合局部、简单状态（布尔开关、输入值等）；
- **useReducer**：适合复杂逻辑（步骤流、状态机、表单），更新逻辑集中统一；
- reducer 天然是 **纯函数**，可单测、易回溯；
- 可结合 **Immer** 避免手写不可变对象更新。

**详细回答**

- **useState**

  - 用于简单场景：一个布尔、计数器、输入框。

  - 优点：语义清晰、样板少，适合 80% 的局部状态管理。

  - 示例：

    ```tsx
    const [open, setOpen] = useState(false);
    ```

- **useReducer**

  - 把“状态迁移逻辑”集中在一个 reducer 函数里，用 `dispatch` 触发更新。

  - 适合有多种事件、更新规则复杂的场景。

  - 示例：

    ```ts
    type Action =
      | { type: "next" }
      | { type: "reset" }
      | { type: "goto"; step: number };
    
    function reducer(state: { step: number }, action: Action) {
      switch (action.type) {
        case "next":
          return { step: state.step + 1 };
        case "reset":
          return { step: 0 };
        case "goto":
          return { step: action.step };
      }
    }
    
    const [state, dispatch] = useReducer(reducer, { step: 0 });
    ```

  - 优点：

    - 更新逻辑集中 → 组件代码更简洁；
    - reducer 可单独测试，不依赖 React 环境；
    - 和 Redux 思路一致，将来需要全局化时迁移成本低。

- **避免过度抽象**

  - 如果只是一个计数器或布尔状态，用 `useReducer` 反而会增加心智负担。

**扩展/对比**

- **与 Redux 的关系**：思想相同，都是通过 action 描述状态变化，reducer 控制迁移；差别是 `useReducer` 作用域在组件内部，而 Redux 作用域是全局。

- **Immer 配合**：

  - 传统写法：手动复制对象/数组，样板多；

  - 用 `produce` 包装 reducer，可以直接写“可变”风格：

    ```ts
    import { produce } from "immer";
    
    const reducer = produce((draft, action) => {
      switch (action.type) {
        case "add":
          draft.items.push(action.payload);
          break;
        case "remove":
          draft.items = draft.items.filter(i => i.id !== action.id);
      }
    });
    ```

### 15) 虚拟列表与分片渲染 🔥

**一句话回答**
 虚拟列表：只渲染视口内的元素，降低 DOM 数量；分片渲染：把一次性大渲染任务切成小批，利用调度避免阻塞主线程。

**要点**

- **虚拟列表**：`react-window`、`react-virtualized` 等库成熟稳定；大幅减少 DOM 节点数。
- **分片渲染**：`requestIdleCallback` / `scheduler` / React 18 的 `startTransition`，把渲染切成小批执行。
- **用户体验**：结合骨架屏/占位符，提升首屏感知性能。

**详细回答**

- **虚拟列表**

  - 原理：只渲染当前滚动区域内的元素，前后用占位容器撑起滚动条高度。
  - 优点：列表再大，DOM 数量始终可控（几十/上百）。
  - 典型场景：海量数据表格、长列表（聊天消息流、Feed 流）。
  - 实现要点：
    - **固定高度**：直接用索引 × itemHeight 计算偏移量，简单高效；
    - **可变高度**：需动态测量（`ResizeObserver` / 缓存高度 / 二分查找滚动位置），复杂度更高。
  - 库选择：
    - `react-window`：轻量、现代；
    - `react-virtualized`：功能强大（表格、Grid 支持），但包体大。

- **分片渲染**

  - 原理：一次性渲染数千个节点会阻塞主线程 → 把任务拆分为多批，每帧或空闲时渲染部分。

  - 技术手段：

    - 浏览器原生：`requestIdleCallback` / `requestAnimationFrame`；
    - React 调度：`startTransition`（标记低优先级渲染）、`scheduler` 包。

  - 典型场景：大表格、树形结构、图形渲染。

  - 示例：

    ```tsx
    // React 18
    startTransition(() => {
      setBigData(newData);
    });
    ```

    用户交互（高优先级）会先响应，低优先级渲染稍后执行，提升流畅度。

**扩展/对比**

- **虚拟列表 vs 分片渲染**
  - 虚拟列表：减少 **渲染数量** → 控制 DOM 总量；
  - 分片渲染：分摊 **渲染耗时** → 控制单次渲染时长；
  - 实际项目常常结合使用（比如：虚拟化 + 分批加载数据）。
- **面试考点**：
  - 手写简易虚拟滚动实现（核心：容器高度 + 偏移量计算）。
  - 说明如何用 `requestIdleCallback` 或 `startTransition` 把几千条数据分帧渲染。
- **Vue 对比**
  - Vue 没有类似 React Concurrent Mode 的分片渲染 API；
  - 通常靠第三方库（如 `vue-virtual-scroll-list`）或手动用 `nextTick`、定时器拆分渲染任务。

------

### 16) React Profiler 与 Web Vitals 🔥

**一句话回答**
 React Profiler 用于分析组件渲染性能瓶颈；Web Vitals 用于度量真实用户体验（LCP/FID/CLS/INP/TTFB）。

**要点**

- **Profiler API**：`<Profiler id="xxx" onRender={callback}>`，可嵌入应用代码；
- **React DevTools**：Profiler 面板可直观查看渲染次数、耗时、commit 阶段；
- **Web Vitals**：Google 提出的用户体验核心指标，涵盖加载、交互、视觉稳定性。

**详细回答**

- **React Profiler**

  - 用途：找出哪些组件 **频繁 re-render** 或渲染时间过长。

  - API：

    ```tsx
    <Profiler id="SearchBox" onRender={(id, phase, actualDuration) => {
      console.log(id, phase, actualDuration);
    }}>
      <SearchBox />
    </Profiler>
    ```

    - `id`：标识组件；
    - `phase`：`"mount"` 或 `"update"`；
    - `actualDuration`：本次渲染耗时。

  - 工具：React DevTools → Profiler 面板，可查看 flamegraph（火焰图）、ranked（按耗时排序）。

  - 典型应用：定位过度渲染、发现没必要的子组件更新。

- **Web Vitals**

  - Google 标准化的用户体验指标，侧重真实用户感受（RUM, Real User Monitoring）。

  - 核心指标：

    - **LCP (Largest Contentful Paint)**：最大内容绘制 → 页面主内容渲染速度；
    - **FID (First Input Delay)**：首次输入延迟 → 用户交互响应速度（React 18 更推荐 INP）；
    - **INP (Interaction to Next Paint)**：交互到下一次绘制 → 更全面的交互延迟指标；
    - **CLS (Cumulative Layout Shift)**：累积布局偏移 → 页面视觉稳定性；
    - **TTFB (Time to First Byte)**：首字节时间 → 服务端响应速度。

  - 采集方式：

    - `web-vitals` npm 包；
    - 或 Performance API + Analytics 上报。

  - 示例：

    ```ts
    import { getLCP, getFID, getCLS } from 'web-vitals';
    getLCP(console.log);
    getFID(console.log);
    getCLS(console.log);
    ```

**扩展/对比**

- **定位 React 性能问题思路**：
  1. React DevTools Profiler：确认渲染热点（过度渲染？渲染耗时过长？）。
  2. Web Vitals：确认真实用户体验是否达标（LCP < 2.5s, CLS < 0.1, INP < 200ms）。
  3. 代码级优化：
     - `React.memo` / `useMemo` / `useCallback` 稳定引用；
     - 组件拆分、虚拟列表、分片渲染；
     - SSR/CSR 混合渲染策略。
- **对比**
  - Profiler：更像“显微镜”，分析 React 内部渲染性能；
  - Web Vitals：是“体检报告”，从用户体验角度衡量整体性能。

### 17) ErrorBoundary 与 Suspense 🔥

**一句话回答**
 ErrorBoundary 用于捕获渲染树中的错误并显示兜底 UI；Suspense 用于处理异步渲染/懒加载，展示 fallback。

**要点**

- **ErrorBoundary**：通过类组件 `componentDidCatch` / `getDerivedStateFromError` 捕获渲染、生命周期错误。
- **Suspense**：当子组件抛出 Promise 时挂起渲染，展示 `fallback`。
- 二者可组合：错误 → 错误兜底；加载中 → Loading UI。

**详细回答**

- **ErrorBoundary**

  - 典型实现：

    ```tsx
    class ErrorBoundary extends React.Component {
      state = { hasError: false };
      // 当子组件抛出错误时，React 会调用它，并允许你通过返回一个对象来 更新 state，以便渲染备用 UI。
      // 渲染阶段、根据错误更新 state、更改UI、渲染 fallback UI
      static getDerivedStateFromError() {
        return { hasError: true };
      }
      // 触发时机在 getDerivedStateFromError 之后
      // commit 阶段、错误信息副作用处理、不能更改UI、打印/上报错误日志
      componentDidCatch(error, info) {
        console.error("Caught by ErrorBoundary:", error, info);
      }
      render() {
        if (this.state.hasError) return <h1>Something went wrong.</h1>;
        return this.props.children;
      }
    }
    ```

  - 能捕获：渲染阶段、生命周期方法、构造函数中的错误。

  - **不能捕获**：

    - 异步回调中的错误（需 try/catch）；
    - 事件处理器中的错误（需显式捕获）；
    - 服务端渲染（SSR）阶段的错误。

- **Suspense**

  - 当子组件返回 **Promise** 时，React 会“挂起”当前渲染并显示 `fallback`。

  - 常见用途：

    - **懒加载组件**：

      ```tsx
      const LazyComp = React.lazy(() => import("./Comp"));
      <Suspense fallback={<Spinner />}>
        <LazyComp />
      </Suspense>
      ```

    - **数据请求**（React 18+ 实验 / React 19 `use`）：

      ```tsx
      async function getUser() { return await fetch("/api/user").then(r => r.json()); }
      function Profile() {
        const user = use(getUser());
        return <div>{user.name}</div>;
      }
      ```

- **二者组合**

  - 常见模式：
    - Suspense → 显示 **Loading**；
    - ErrorBoundary → 显示 **错误兜底**；
    - 两者一起封装成 `Boundary` 组件，统一加载/错误处理。

**扩展/对比**

- **Vue 3**：提供 `<Suspense>`，支持异步 setup 的 fallback，但错误边界机制弱于 React，需要手动全局错误处理。

- **工程实践**：

  - 抽象一个 `<Boundary>` 组件，内部嵌套 ErrorBoundary + Suspense，减少重复代码：

    ```tsx
    <Boundary fallback={<Loading />} error={<ErrorUI />}>
      <SomeAsyncComp />
    </Boundary>
    ```

  - 便于统一管理“加载中/失败”UI，提升健壮性与一致性。

------

### 18) Portal 与焦点管理 🔥

**一句话回答**
 Portal 让组件渲染到 DOM 树外部（如 `body`），但逻辑上仍在 React 树内；常用于弹窗/悬浮层，需要额外管理焦点与可访问性。

**要点**

- `createPortal(children, container)` → 渲染到指定 DOM 节点。
- 常见容器：`document.body`（避免 z-index、overflow 限制）。
- 需处理问题：**焦点陷阱**、键盘导航（Esc 关闭）、ARIA 属性保证可访问性。

**详细回答**

- **使用场景**：
  - Modal / Dialog：需要覆盖页面内容，避免被父容器裁切。
  - Tooltip / Dropdown：需要跳出层级，悬浮在目标元素附近。
  - Toast / Notification：固定在页面某个区域，不随组件树位置限制。
- **问题点**：
  - 默认 Tab 顺序可能跳出弹窗，导致用户“走丢”；
  - 屏幕阅读器可能无法识别当前上下文；
  - 背景内容依然可聚焦，影响可访问性。
- **解决方式**：
  - **焦点陷阱（Focus Trap）**：进入 Modal 时自动聚焦第一个可交互元素，Tab 键在 Modal 内循环。
  - **关闭逻辑**：支持 `Esc` 键关闭。
  - **可访问性 (A11y)**：
    - `role="dialog"`
    - `aria-modal="true"`
    - 给标题加 `aria-labelledby`，内容加 `aria-describedby`。
  - 工具库：`react-aria`、`focus-trap-react`、`radix-ui` 提供成熟实现。

```tsx
import { createPortal } from "react-dom";

function Modal({ children, onClose }: { children: React.ReactNode; onClose: () => void }) {
  return createPortal(
    <div role="dialog" aria-modal="true" className="modal">
      <button onClick={onClose} aria-label="Close">×</button>
      {children}
    </div>,
    document.body
  );
}
```

**扩展/对比**

- **为什么 Modal 要用 Portal？**
   👉 避免受父容器 `overflow:hidden` 或 `z-index` 影响，保证浮层正确显示。
- **工程实践**：
  - 统一封装 `Modal`、`Tooltip` 等组件，内置 Portal + 焦点管理逻辑；
  - 配合 A11y 规范（`aria-*` 属性），保证键盘无障碍和屏幕阅读器可用性。

------

### 19) Context 性能问题与 selector 优化 🔥

**一句话回答**
 Context 更新会导致所有消费组件重渲染；可通过拆分 Context、使用 selector 或外部状态库来优化。

**理解**

- **`createContext`** 定义的时候，本质上没变，不管是配合 `useContext` 还是 `useContextSelector`，Context 的 Provider 都是同一个。
- 区别在于 **消费端**：
  - `useContext`：订阅的是 **整个 context 值对象**，只要 Provider 传入的 value 对象引用发生变化 → 所有消费者都会 re-render。
  - `useContextSelector`：只订阅你选择的那部分字段，只有当该字段变化时组件才会 re-render。

 **`useContextSelector`（精细订阅）**

```tsx
import React from "react";
import { createContext, useContextSelector } from "use-context-selector";

// 1. 创建 Context
interface Store {
  count: number;
  text: string;
}
const StoreContext = createContext<Store>({ count: 0, text: "" });

// 2. Provider
function StoreProvider({ children }: { children: React.ReactNode }) {
  const store = { count: 1, text: "hello" };
  return <StoreContext.Provider value={store}>{children}</StoreContext.Provider>;
}

// 3. Consumer（精细订阅）
function Text() {
  const text = useContextSelector(StoreContext, (v) => v.text);
  return <p>{text}</p>;
  // ✅ 只有 text 变化时才会 re-render
}
```

**用 `useContext`（全量订阅）**

```tsx
import React, { createContext, useContext } from "react";

// 1. 创建 Context
interface Store {
  count: number;
  text: string;
}
const StoreContext = createContext<Store>({ count: 0, text: "" });

// 2. Provider
function StoreProvider({ children }: { children: React.ReactNode }) {
  const store = { count: 1, text: "hello" };
  return <StoreContext.Provider value={store}>{children}</StoreContext.Provider>;
}

// 3. Consumer（全量订阅）
function Text() {
  const store = useContext(StoreContext);
  return <p>{store.text}</p>;
  // ❌ 即使只用 text，只要 count 变了，这个组件也会 re-render
}
```

**要点**

- Context 的更新机制 = **全量广播**，所有消费者都会 re-render。
- 优化方式：
  - 拆分多个 Context，减少无关组件被动更新；
  - 使用 `useContextSelector` / Zustand / Recoil 实现精细订阅；
  - 避免频繁变动的值放在全局 Context 中。

**详细回答**

- **性能陷阱**：
  - 如果把一个“大对象 store”放进 Context，每次任意属性变动都会导致所有 `useContext` 消费者重渲染。
  - 即使某些子组件只用其中一个字段，也会随整对象变化被动更新。
- **优化手段**：
  - **拆分 Context**：将不同职责的状态拆开，例如：
    - ThemeContext（主题）
    - AuthContext（用户信息）
    - ConfigContext（配置项）
       → 这样变化不会互相影响。
  - **精细订阅**：
    - React 18 提供的实验 API `useContextSelector`（`use-context-selector` 库）可以只订阅需要的字段；
    - 或使用 **Zustand**、**Recoil** 这类状态库，天然支持 selector，避免全量刷新。
  - **状态下沉**：只在需要的组件树下创建 Provider，缩小更新范围。

```tsx
// use-context-selector 示例
import { createContext, useContextSelector } from "use-context-selector";

const StoreContext = createContext<{ count: number; text: string }>({ count: 0, text: "" });

function Counter() {
  const count = useContextSelector(StoreContext, (v) => v.count);
  return <div>{count}</div>; // 只会在 count 变化时更新
}
```

**扩展/对比**

- **Context vs Redux**
  - Context：轻量，适合配置/低频更新，不带优化机制。
  - Redux：有 **selector + memoized** 机制，配合 DevTools，适合复杂全局状态管理。
- **实战建议**：
  - 配置类（Theme、Locale） → 用 Context；
  - 高频变动的数据（计数器、表单值、大型数据流） → 用 Zustand/Recoil 等专门状态库。

------

### 20) `useSyncExternalStore` 🔥

**一句话回答**
 React 18 推荐的订阅外部 store API，保证并发模式下快照一致，避免 Context 广播和“撕裂（tearing）”。

**要点**

- `useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)`
- **subscribe**：注册/取消订阅 → 数据变更时触发。
- **getSnapshot**：同步读取当前最新值（未变更时必须返回同一引用）。
- 保证并发渲染下快照一致 → 不会出现 tearing。
- Redux、Zustand 等库已基于此实现。

------

**详细回答**

- 🔴 旧写法：`useEffect + useState` 手动订阅

  ```tsx
  let count = 0;
  const listeners = new Set<() => void>();
  function inc() {
    count++;
    listeners.forEach(fn => fn());
  }
  function subscribe(fn: () => void) {
    listeners.add(fn);
    return () => listeners.delete(fn);
  }
  
  function Counter() {
    const [val, setVal] = React.useState(count);
  
    React.useEffect(() => {
      return subscribe(() => setVal(count));
    }, []);
  
    return <button onClick={inc}>{val}</button>;
  }
  ```

  👉 问题：在 **并发渲染** 下，某些树可能读到旧值，某些树读到新值 → **撕裂（tearing）**。

- 🟢 新写法：`useSyncExternalStore`

  ```tsx
  let count = 0;
  const listeners = new Set<() => void>();
  function inc() {
    count++;
    listeners.forEach(fn => fn());
  }
  function subscribe(fn: () => void) {
    listeners.add(fn);
    return () => listeners.delete(fn);
  }
  function getSnapshot() {
    return count;
  }
  
  function Counter() {
    const val = React.useSyncExternalStore(subscribe, getSnapshot);
    return <button onClick={inc}>{val}</button>;
  }
  ```

  👉 好处：在 **渲染阶段** 就读取快照，整棵 UI 树都能看到同一个值，彻底消灭 tearing。

------

**扩展/对比**

- React 18 以前：只能手写订阅，可能有竞态。
- React 18+：官方统一入口，保证快照和订阅时机对齐。
- Context：适合低频配置；频繁更新的全局数据（store / WebSocket / 浏览器状态）更适合 `useSyncExternalStore`。
- 面试亮点：解释 tearing 与并发渲染的关系，并写出前后对比。

---

### 21) 水合与 mismatch 问题 🔥

**一句话回答**
 水合：SSR 输出的 HTML 在客户端被 React “接管”；
Mismatch（不一致）：服务端 HTML 与客户端渲染结果不一致，导致报错或闪烁。

**要点**

- **SSR**：提升首屏性能，输出静态 HTML。
- **Hydration**：React 在客户端为已有 DOM 绑定事件，并接管后续更新。
- **Mismatch**：HTML 与 React 计算出的树不一致。

**详细回答**

- **水合过程**：
  1. 服务器返回 HTML；
  2. 客户端 React 对比已有 DOM；
  3. 仅绑定事件，不重新创建 DOM。
- **Mismatch 场景**：
  - 随机数 / `Date.now()` / `Math.random()` 在 SSR 与 CSR 不一致；
  - 依赖 `window`/`document` 的逻辑在服务端缺失；
  - 条件渲染导致两边结构不同。
- **后果**：
  - React 尝试修复 DOM；
  - 可能 UI 闪烁或直接报 “hydration failed”。

**扩展/对比**

- 避免 mismatch：
  - 保证 SSR 与 CSR 渲染逻辑一致；
  - 依赖客户端环境的逻辑放在 `useEffect`；
  - 动态值（时间戳、随机数）SSR 时固定或延迟到 CSR 生成。

---

### 22) StrictMode 的作用与坑 🔥

**一句话回答**
 StrictMode 在开发环境下启用“额外检查模式”，帮助发现潜在问题：副作用双执行、不安全 API 警告。

**要点**

- 检测不安全生命周期方法（类组件）。
- 模拟 **卸载+重装** effect，检查副作用是否正确清理。
- 检测过时 API（如 `findDOMNode`、旧 context API）。
- **生产环境不会启用这些检查**。

**详细回答**

- **Effect 双执行**：
   React 在 DEV 下故意执行：mount → unmount → mount，一次“排练”，确保副作用逻辑**幂等**、清理函数能正确释放资源（如事件监听、订阅）。

  ```tsx
  useEffect(() => {
    const id = setInterval(() => console.log("tick"), 1000);
    return () => clearInterval(id);
  }, []);
  ```

  如果忘记清理 → DEV 环境会打印两次，帮助你发现 bug。

- **并发模式准备**：
   React 未来更多并发渲染特性需要组件“可重入”，StrictMode 提前暴露不确定性代码。

- **常见坑**：

  - 网络请求、日志上报被执行两次 → 需要 **AbortController** 或幂等逻辑。
  - 不理解机制时误以为是 React bug，其实只是 DEV 检查。

**扩展/对比**

- 面试高频追问：“为什么我的 useEffect 执行两次？”
   👉 回答：**StrictMode 特意模拟卸载+重装，不是 bug，生产环境只会执行一次。**

---

### 23) TS 类型设计：Props 与状态 🔥

**一句话回答**
 Props 用接口/泛型定义清晰契约；状态类型最好推导，但必要时显式标注，避免 `any`。

**要点**

- **Props**：接口/类型别名定义清晰 API；泛型支持灵活复用。
- **状态**：`useState` 通常自动推导；需要多态时显式标注。
- 善用工具类型：`Pick` / `Omit` / `Partial` / `ReturnType`。
- 避免冗余定义，复用已有类型。

**详细回答**

```ts
// Props 定义
interface ButtonProps {
  type?: "primary" | "secondary";
  onClick?: () => void;
}
function Button({ type = "primary", onClick }: ButtonProps) {
  return <button onClick={onClick}>{type}</button>;
}

// 状态定义
const [count, setCount] = useState(0);           // 推导为 number
const [user, setUser] = useState<User | null>(null); // 显式标注，避免 any
```

- **复杂场景：泛型组件**

```ts
interface ListProps<T> {
  items: T[];
  render: (item: T) => React.ReactNode;
}
function List<T>({ items, render }: ListProps<T>) {
  return <ul>{items.map(render)}</ul>;
}
```

调用时：

```tsx
<List items={[1, 2, 3]} render={(n) => <li>{n}</li>} />;
```

- **工具类型实战**
  - `Pick<User, "id" | "name">`：只要部分字段（如用户预览）。
  - `Omit<ButtonProps, "onClick">`：去掉某些字段（如禁用点击事件）。
  - `Partial<User>`：将所有字段设为可选（如更新表单/patch 接口）。
  - `ReturnType<typeof fetchUser>`：获取函数返回类型，避免重复定义。

**扩展/对比**

- **Props 设计**：API 清晰、语义明确，能帮助 IDE 补全和防错。
- **状态设计**：保持最小、正确的类型范围，避免 `any` 和过度泛型化。
- 面试常问：“如何设计可扩展的 Props 类型？”
   👉 答：通过 **泛型 + 工具类型**，减少重复，增强复用性。

---

### 24) React 测试与可测性 🔥

**一句话回答**
 测试应关注“用户行为”而非组件实现，推荐 **React Testing Library (RTL) + Jest**；良好可测性源自解耦与状态下沉。

**要点**

- **单元测试**：逻辑函数/工具方法（Jest）。
- **组件测试**：RTL 模拟用户操作，避免依赖内部实现。
- **E2E 测试**：Playwright / Cypress，验证端到端流程。
- **可测性原则**：单一数据源、清晰边界、避免隐式依赖。

**详细回答**

- **RTL 哲学**：测试应像用户使用一样 → 通过 `screen.getByText`、`userEvent.click`、`userEvent.type` 验证输出和行为，而不是检查组件的私有 state 或内部方法。

```tsx
// Button.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import Button from "./Button";

test("按钮点击触发回调", async () => {
  const onClick = vi.fn();
  render(<Button onClick={onClick}>点我</Button>);
  await userEvent.click(screen.getByText("点我"));
  expect(onClick).toHaveBeenCalled();
});
```

- **可测性设计**：
  - **状态下沉**：让父组件控制状态，子组件只关注展示 → 更易 mock/测试。
  - **解耦副作用**：API 请求抽离到 service 层，组件只消费 props → 单测时能轻松 mock。
  - **职责单一**：一个组件只处理一个逻辑点，降低测试复杂度。

**扩展/对比**

- **RTL vs Enzyme**：
  - RTL → 行为驱动，模拟用户 → 社区推荐主流。
  - Enzyme → 测内部细节（state/instance），但这种测试 brittle（脆弱），官方已逐渐淘汰。
- **E2E**：结合 Playwright / Cypress 检查真实环境 → 登录/表单提交/页面跳转。

---

### 25) Next.js：RSC、Actions、缓存策略 🔥

**一句话回答**
 RSC（React Server Components）让渲染逻辑前移服务端；Actions 让表单/事件直接调用服务端函数；缓存策略决定数据新鲜度与性能。

**要点**

- **RSC**：组件在服务端执行 → 前端 bundle 更小。
- **Actions**：`<form action={fn}>` 直连服务端 → 少写 API 层。
- **缓存策略**：内置 `fetch` 缓存 + ISR。

**详细回答**

- **RSC（React Server Components）**
  - 服务端获取数据（如 DB 查询、API 调用），返回“序列化的 UI 描述”。
  - 客户端只需 hydrate 必要交互组件。
  - 优势：减少客户端包体，安全（避免把秘钥传到浏览器）。
- **Actions（Server Actions）**
  - React 19 & Next.js 支持 `<form action={serverFn}>`，直接调用服务端函数。
  - 省去前端写 `fetch('/api')`，逻辑内聚。
  - 可搭配乐观更新、pending 状态（`useFormStatus`）。
- **缓存策略（Next.js fetch 内置）**
  - `force-cache`（默认）：结果缓存，不会重新请求。
  - `no-store`：每次都请求，实时性高。
  - `revalidate=60`：ISR（增量静态再生成），定期刷新缓存。
  - 可结合 `cache: "force-cache"` + `revalidateTag` / `revalidatePath` 做精细刷新。

**扩展/对比**

- **性能保障手段**：
  - RSC：减少传输与客户端解析成本。
  - Streaming：逐块传输，先展示骨架，再补充真实内容。
  - 缓存分层：CDN 缓存 + Next.js fetch 缓存 + DB 缓存，层层优化。

---

### 26) React 合成事件 vs 原生事件 🔥

**一句话回答**
 合成事件是 React 封装的跨浏览器事件系统，委托到 root 容器；和原生事件在冒泡、解绑与执行顺序上存在差异。

**要点**

- React 17 起，事件委托目标从 `document` → **root 容器**（更好支持多 React 应用共存）。
- **合成事件对象**：`SyntheticEvent`，有统一 API（如 `stopPropagation`、`preventDefault`）。
- **互不影响**：合成事件的 `stopPropagation` 不会阻止原生事件，反之亦然。
- **例外**：**React 并不是所有事件都合成**，如 `scroll`、`media`、`load/error` 这类事件，仍是原生绑定

**详细回答**

- **合成事件系统**：
  - React 内部会把事件绑定在根容器上，通过事件冒泡统一捕获，再分发给具体组件 → 这叫“事件委托”。
  - 好处：跨浏览器兼容、减少真实事件监听器数量、统一调度（可结合优先级和批量更新）。
- **回收机制**：
  - `SyntheticEvent` 对象会在回调后重用（为减少 GC 压力），如果需要异步访问，应调用 `event.persist()`。
- **冒泡差异**：
  - 在 React 事件中 `stopPropagation()` → 只阻止合成事件冒泡。
  - 如果有原生事件监听器，它仍然会执行。
  - 反之，原生事件的 `stopPropagation()` 也不会阻止合成事件的分发。

```tsx
function App() {
  useEffect(() => {
    document.body.addEventListener("click", () => {
      console.log("原生事件");
    });
  }, []);
  return (
    <div onClick={() => console.log("React 合成事件")}>
      点我
    </div>
  );
}
```

点击时会触发 **“React 合成事件”** + **“原生事件”**，即使你在 React 的事件里写了 `e.stopPropagation()`。

**扩展/对比**

- **为什么 React 要自己实现？**
   👉 统一兼容 API（浏览器事件模型差异大）、减少监听器开销、为并发更新和优先级调度打基础。
- **工程实践**：
  - 全局事件（如 `resize`、`scroll`、`visibilitychange`）更适合直接用原生事件。
  - 组件内交互则优先用 React 合成事件，方便管理与解绑。

------

### 27) React 中的动画方案对比 🔥

**一句话回答**
 React 常见动画方案有 **CSS Transition**、**React Transition Group** 和 **Framer Motion**；前两者轻量简单，后者功能全面，适合复杂交互。

**要点**

- **CSS Transition**：声明式，性能好，适合简单场景。
- **React Transition Group**：解决组件挂载/卸载的过渡问题。
- **Framer Motion**：手势、物理动画、复杂交互首选。

**详细回答**

- **CSS Transition/Animation**
  - 在 CSS 里写 `transition: opacity 0.3s`，组件渲染时触发动画。
  - 优势：性能极佳（GPU 合成层优化），实现简单。
  - 局限：受限于 CSS 表达能力，难以实现复杂交互。
- **React Transition Group**
  - 提供 `<Transition>` / `<CSSTransition>` / `<TransitionGroup>`。
  - 用来处理 **组件挂载与卸载时的过渡**，如模态框淡入淡出、列表元素插入/移除动画。
  - 优势：和 React 生命周期契合，轻量。
- **Framer Motion**
  - 声明式 API，如 `<motion.div animate={{ x: 100 }}>`。
  - 内置物理动画（spring）、手势（drag、hover、tap）、layout 动画（自动计算位移过渡）。
  - 适合需要流畅交互的复杂场景。

**扩展/对比**

- **性能优化**：推荐只在 `transform` / `opacity` 上做动画，不要频繁修改 `width`、`height`、`top` 等，会触发布局与重绘。
- **Vue vs React**：Vue 内置 `<transition>`，开箱即用；React 更加灵活，但需依赖外部库。
- **选择建议**：
  - 简单 hover/渐隐 → CSS。
  - 组件进出场动画 → React Transition Group。
  - 高级交互（拖拽、复杂过渡、物理动画） → Framer Motion。

---

### 28) React 中的可访问性实践 🔥

**一句话回答**
 通过**语义化标签**、**ARIA 属性**、**焦点管理**和**键盘支持**，保证所有用户（包括使用屏幕阅读器、键盘导航的人群）都能操作 UI。

**要点**

- **语义化标签**：优先用 `<button>`、`<nav>`、`<header>`，而非 `<div>`+事件。
- **ARIA 属性**：`role="dialog"`, `aria-modal="true"`, `aria-label="关闭"。`
- **焦点管理**：打开 Modal 自动聚焦，关闭后焦点返回触发按钮。
- **键盘支持**：监听 `Enter` / `Space` / `Esc`，保证键盘可操作。

**详细回答**

- **React 默认不会处理可访问性**，需要开发者主动补充：

  - `<button>` vs `<div onClick>`：前者天然支持键盘与 ARIA，后者需要额外 `role` 与 `tabIndex`。

  - Modal 示例：

    ```tsx
    <div role="dialog" aria-modal="true" aria-labelledby="title">
      <h2 id="title">提示</h2>
      <button aria-label="关闭">×</button>
    </div>
    ```

- **焦点陷阱（Focus Trap）**：

  - 打开弹窗时聚焦到第一个输入框；Tab 循环在 Modal 内。
  - 可用 `focus-trap` 或 `react-aria` 实现。

- **工具链支持**：

  - `eslint-plugin-jsx-a11y` → 静态检查。
  - Axe / Lighthouse → 运行时检测。

**扩展/对比**

- **面试高频**：“React 和 Vue 在 A11y 上区别？”
   👉 差别不大，本质在于**开发规范**与**自动化检查**。
- **实践价值**：大厂对 WCAG（Web Content Accessibility Guidelines）合规有硬性要求，可访问性不只是用户体验，更是法律合规。

---

### 29) React 错误监控与埋点 🔥

**一句话回答**
 错误监控依赖 **ErrorBoundary + 全局错误捕获 + 上报平台**；埋点结合 **手动埋点** 与 **无埋点**，记录用户行为与性能指标。

**要点**

- **错误监控**
  - React 层：ErrorBoundary 捕获渲染错误。
  - 浏览器层：`window.onerror` / `unhandledrejection` 捕获全局错误。
- **性能监控**
  - Performance API：LCP / FID / CLS / INP。
- **埋点方式**
  - 手动埋点：关键交互（点击、提交）。
  - 无埋点：全局代理（如监听路由、DOM 事件）。

**详细回答**

- **错误监控链路**

  - React 组件：

    ```tsx
    class ErrorBoundary extends React.Component {
      state = { hasError: false };
      static getDerivedStateFromError() {
        return { hasError: true };
      }
      componentDidCatch(error, info) {
        // 上报错误
        reportError(error, info);
      }
      render() {
        return this.state.hasError ? <h1>出错了</h1> : this.props.children;
      }
    }
    ```

  - 全局异常：

    ```ts
    window.onerror = (msg, src, line, col, err) => {
      reportError(err || msg);
    };
    window.onunhandledrejection = (e) => {
      reportError(e.reason);
    };
    ```

  - SourceMap：定位真实代码位置，避免线上报错难追踪。

- **性能监控**

  - 使用 Performance API：

    ```ts
    new PerformanceObserver((entryList) => {
      for (const entry of entryList.getEntries()) {
        if (entry.name === "largest-contentful-paint") {
          reportMetric("LCP", entry.startTime);
        }
      }
    }).observe({ type: "largest-contentful-paint", buffered: true });
    ```

- **埋点设计**

  - **手动埋点**：关键路径（如支付按钮）加 `useTrackEvent("pay_click")`。

  - **无埋点**：框架层自动代理 `click`、`route change`，统一收集用户行为。

  - **React Hook 封装**：

    ```ts
    function useTrackEvent(event: string) {
      return useCallback(() => sendToServer({ event, time: Date.now() }), [event]);
    }
    ```

**扩展/对比**

- 常见 SaaS：Sentry、阿里 ARMS、字节 OneAPM。
- 面试追问：“如何定位线上白屏？”
   👉 ErrorBoundary 兜底 → 日志上报 → SourceMap 反解 → 配合性能埋点查瓶颈。

---

### 30) React 中的 XSS 风险 🔥

**一句话回答**
 React JSX 默认插值是安全的，真正的风险在于 **`dangerouslySetInnerHTML`**，需额外净化或配合 CSP。

**要点**

- **安全默认**：`<div>{userInput}</div>` → React 会自动转义。
- **危险 API**：`dangerouslySetInnerHTML={{ __html: ... }}` → 需要 DOMPurify 等库清理。
- **防御措施**：输入校验 + 输出转义 + CSP（Content-Security-Policy）。

**详细回答**

- **XSS 攻击链路**：

  1. 攻击者在评论区输入恶意 `<script>alert(1)</script>`。
  2. 如果直接通过 `dangerouslySetInnerHTML` 渲染，就会执行。
  3. 结果是页面被篡改、窃取 Cookie、注入钓鱼页面。

- **React 的保护**：

  - JSX 中的插值（`{value}`）会**自动 HTML 转义**，防止脚本执行。
  - 但如果你用 **`dangerouslySetInnerHTML`**，React 不再帮你转义，需要自己保证内容安全。

- **推荐做法**：

  ```tsx
  import DOMPurify from "dompurify";
  
  function RichText({ html }: { html: string }) {
    const safe = DOMPurify.sanitize(html);
    return <div dangerouslySetInnerHTML={{ __html: safe }} />;
  }
  ```

  - 使用 DOMPurify 白名单净化。

  - 配合 **CSP** 限制脚本执行来源，例如：

    ```
    Content-Security-Policy: script-src 'self' https://trusted.cdn.com
    ```

**扩展/对比**

- **为什么叫 dangerously？**
   👉 React 团队刻意提醒开发者：这是高风险 API，只在处理富文本（Markdown/文章编辑器）时使用。
- **Vue** 也有类似风险（`v-html`）。最佳实践是一样的：只在受控场景下使用 + 过滤净化。

---

### 31) React 组件测试策略 🔥

**一句话回答**
 逻辑函数用单元测试，组件用 React Testing Library 测用户行为，端到端测试用 Cypress/Playwright。

**要点**

- **RTL**：模拟用户 → 点击、输入、看到文本。
- **Jest**：Mock + 断言函数调用、状态。
- **E2E**：Cypress/Playwright → 在真实浏览器跑。

**详细回答**

- **单元测试**（Jest）：

  - 校验函数、状态管理逻辑。
  - 使用 `vitest`/`jest` 来跑。

- **组件测试**（RTL）：

  ```tsx
  render(<LoginForm />);
  fireEvent.change(screen.getByLabelText("用户名"), { target: { value: "jack" } });
  fireEvent.click(screen.getByText("提交"));
  expect(screen.getByText("登录成功")).toBeInTheDocument();
  ```

  - 强调测试 **用户行为** 而不是 `.state` 或 `.props`。

- **API Mock**：

  - 推荐 **msw (Mock Service Worker)** → 在浏览器/Node 里拦截 `fetch`。
  - 避免 mock 掉 React 内部逻辑。

- **E2E 测试**：

  - Cypress → 简单上手、UI 可视化。
  - Playwright → 支持多浏览器、并发、截图比对。

**扩展/对比**

- **Enzyme**：侧重内部实现，依赖 React 内部 API → 已逐渐过时。
- 面试高频问题：“复杂表单如何测？” 👉 校验逻辑抽到纯函数单测，组件只测输入输出行为。

---

### 32) 组件库设计经验 🔥

**一句话回答**
 组件库设计核心是：**API 简洁、易扩展、支持主题化与 A11y，避免过度封装**。

**要点**

- **API 设计**：props 命名清晰、合理默认值、支持泛型。
- **样式**：CSS Variables / Tailwind / CSS-in-JS，支持主题切换。
- **无障碍**：默认内置 `aria-*` 属性，键盘可达。

**详细回答**

- **Button**：
  - 支持 `size`（sm/md/lg）、`variant`（primary/secondary）。
  - 支持 `as` prop → 灵活渲染 `a`/`button`/`div`。
  - 透传 `className`/`style` → 用户可自定义。
- **Input**：
  - 使用 `forwardRef` → 可与表单库结合（如 React Hook Form）。
  - 支持 error 状态、描述文本、`aria-describedby`。
  - 提供键盘导航、聚焦状态。
- **类型支持**：
  - 所有组件都提供 TypeScript 类型声明。
  - 公共 Props 用 **泛型 + 工具类型**，减少重复。
- **样式系统**：
  - 主题化支持（暗黑模式/品牌色）。
  - 样式隔离，避免全局污染。

**扩展/对比**

- 面试常问：“如何避免组件库被锁死？”
   👉 提供 **`className` / `style` / `slot`** 扩展点，而不是写死逻辑。
- 对比：Ant Design 强约束（企业级统一风格），Chakra UI 更灵活（theme-first）。

