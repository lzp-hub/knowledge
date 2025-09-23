### `generateStaticParams`（静态生成动态路径）

**作用**：为动态路由（如 `app/blog/[slug]/page.tsx`）在构建期产出所有可预渲染的 `params`。
 **你的用法：**

```ts
export async function generateStaticParams() {
  const list = await getCollectionItems<IBlogField>(currentCollectionId);
  return list.map((item) => ({ slug: item.slug }));
}
```

**要点**

- 返回形如 `{ slug: string }[]` 的数组；字段名必须和目录参数名一致（这里是 `[slug]`）。
- **大列表**谨慎：构建时间 & 产物体积会膨胀。
- 可搭配：
  - `export const dynamicParams = false;`（只允许这些静态路径，其他 404）
  - ISR：在页面或 `fetch` 上用 `revalidate` 让静态页定期刷新。

------

### `generateMetadata`（按路由生成 SEO 元数据）

**作用**：在服务端为当前路由生成 `<title> / <meta> / Open Graph` 等。
 **你的用法（建议小改）：**

```ts
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const slug = params.slug; // ← 不需要 await，params 不是 Promise
  return generateMetadataByPath(
    `/${COLLECTIONS_MAP[currentCollectionId].slug}/[slug]`,
    slug
  );
}
```

**要点**

- `params` 是**同步对象**；不要 `await params`。
- 常用于根据 `slug` 拉取文案/图片后返回 `Metadata`。
- 若全局通用可在 `layout.tsx` 里 `export const metadata = { ... }`；**动态**就用 `generateMetadata`。

------

### `robots.ts`（动态 robots.txt）

**作用**：生成 `robots.txt`。
 **典型用法：**

```ts
// app/robots.ts
import type { MetadataRoute } from 'next';

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [{ userAgent: '*', allow: '/' }],
    sitemap: 'https://example.com/sitemap.xml',
  };
}
```

**要点**

- 返回 `MetadataRoute.Robots`；可按环境/路径动态控制抓取策略。

------

### `sitemap.ts`（动态 sitemap.xml）

**作用**：生成站点地图，利于 SEO。
 **典型用法：**

```ts
// app/sitemap.ts
import type { MetadataRoute } from 'next';

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const items = await getAllPublishedSlugs();
  return [
    { url: 'https://example.com/', lastModified: new Date() },
    ...items.map((slug) => ({
      url: `https://example.com/blog/${slug}`,
      lastModified: new Date(),
    })),
  ];
}
```

**要点**

- 可动态读取数据库/接口，返回所有 URL。

------

### `metadata`（静态元数据）

**作用**：在 `layout.tsx` 或 `page.tsx` 直接导出**静态**元信息。
 **示例：**

```ts
// app/blog/layout.tsx
export const metadata = {
  title: 'Blog',
  description: 'Latest posts',
};
```

**要点**

- 静态可写 `metadata`，需要按 `params` 变化就用 `generateMetadata`。

------

### `not-found.tsx`（路由级 404 视图）

**作用**：定义当前路由分段的 404 UI。
 **示例：**

```tsx
// app/blog/[slug]/not-found.tsx
export default function NotFound() {
  return <div>文章不存在或已下线</div>;
}
```

**要点**

- 在服务端逻辑中调用 `notFound()`（来自 `next/navigation`）即可跳转到此页面。
- 可与 `dynamicParams=false` 搭配，让未列入 `generateStaticParams` 的路径直接 404。

------

### `middleware.ts`（请求前置处理，Edge 运行）

**作用**：在**到达路由前**做重写/重定向/鉴权等。
 **示例：**

```ts
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(req: NextRequest) {
  const isLoggedIn = Boolean(req.cookies.get('token'));
  if (!isLoggedIn && req.nextUrl.pathname.startsWith('/admin')) {
    const url = new URL('/login', req.url);
    url.searchParams.set('from', req.nextUrl.pathname);
    return NextResponse.redirect(url);
  }
  return NextResponse.next();
}

// 仅匹配部分路径
export const config = {
  matcher: ['/admin/:path*'],
};
```

**要点**

- 运行在 Edge，**不能**使用 Node 原生模块。
- 适合做 AB 实验、地域分流、权限校验、URL 规范化等。

------

## 常见坑 & 实践建议

- **`params` 不是 Promise**：`generateMetadata` 里别 `await params`。
- **体积控制**：`generateStaticParams` 不要一次性产出海量路径；必要时分页/分段或改用 ISR。
- **缓存与再验证**：结合 `fetch` 的 `cache` / `next: { revalidate }`，或在 Action/Route Handler 调用 `revalidatePath`/`revalidateTag`。
- **404 语义**：数据缺失场景使用 `notFound()`，而非返回空 UI。
- **middleware 最小化**：逻辑越轻越好，避免全站匹配导致开销。

需要的话，我可以根据你项目的 `COLLECTIONS_MAP` 和 `getCollectionItems`，给出一版**完整的 blog 路由段模板**（含 `page.tsx`、`generateStaticParams`、`generateMetadata`、`not-found.tsx`、`sitemap.ts`、`robots.ts`、`middleware.ts`）来直接套用。



📘 Next.js（App Router）高频面试题 · 进阶版

## 一、核心与路由体系

### 1) App Router 是什么？与旧 pages 有何本质区别？🔥

**一句话回答**
 App Router 以 **RSC（React Server Components）** 为内核，用文件约定解耦“路由、布局、状态页、SEO、数据与缓存”，具备并行/拦截/流式/Actions 等现代能力。

**要点**

- `app/` 目录：`layout/page/loading/error/not-found/template`
- 默认 RSC，交互组件需 `"use client"`
- 并行路由、拦截路由、流式 SSR、Server Actions
- 数据=组件内 `fetch` + **统一缓存**（`cache/revalidate`、标签、路径失效）

**详细回答**

- **心智切换**：从“页面=客户端组件+拉数据”→“页面=服务端拼装+按需上客户端交互”。RSC 让数据离渲染更近，减少客户端 JS 与水合压力。
- **工程解耦**：layout 抽壳，page 专注内容；状态页（loading/error/not-found）按路由段拆分，错误不再全局“拖死”。
- **数据统一**：不再分散在 GSSP/GSP；`fetch` + 段级配置（`dynamic/revalidate/fetchCache`）描述渲染与缓存行为。
- **体验升级**：Streaming 让“壳先出、数据渐进”；并行/拦截路由提升信息密度与导航体验；Actions 将“写入+失效+刷新”闭环在一处。

**扩展/对比**

- `pages/`：
  - 优点：生态成熟，上手低成本；
  - 局限：无 RSC、无并行/拦截、数据与布局/SEO耦合度高、全客户端包更大。
- 迁移判断：核心诉求是 **性能（降低 JS / 提升首屏）、内容站点（ISR/SEO）、复杂布局、提交变更闭环** 时，收益明显。

------

### 2) App Router 的文件约定与渲染生命周期 🔥

**一句话回答**
 `layout` 共享壳、`page` 内容、`loading`/`error`/`not-found` 管状态、`template` 强制重挂；结合 Suspense 实现端到端流式。

**要点**

- `layout` 可嵌套，负责结构/导航/主题
- `page` 渲染主体内容
- `loading` 对应 Suspense fallback（段级）
- `error` 捕获子树错误，提供 `reset()`
- `template` 每次导航重建子树（动画/状态复位）

**详细回答**

- **渲染顺序**：优先输出顶层 layout（壳），子段用 Suspense 包裹；`loading.tsx` 在该段数据未就绪时兜底，占位可细化到卡片级。
- **错误隔离**：`error.tsx` 只影响该段（类似 React Error Boundary，但天然与路由段绑定），避免整页白屏。
- **重建语义**：`template` 用于需要“重新挂载生命周期”的场景（如切 Tab 要重置动画/滚动/内部状态）。

**扩展/对比**

- `pages/` 靠 `_app/_document` 与全局 ErrorBoundary，颗粒粗、状态页手写成本高。
- 面试提示：说出**为什么 template 不是 layout**（layout 旨在保持稳定壳、可缓存；template 引导重挂载）。

------

### 3) 动态路由、`generateStaticParams` 与 Segment Config 🔥

**一句话回答**
 `[param]` 定义动态段，`generateStaticParams` 让动态页也能 SSG，Segment 导出变量控制静/动与 ISR。

**要点**

- 目录：`app/blog/[slug]/page.tsx`
- 预生：`export async function generateStaticParams()`
- 段配置：`export const dynamic` / `revalidate` / `fetchCache`
- 典型：目录页静态+详情页 ISR/动态混合

**详细回答**

- **`generateStaticParams`**：仅需返回参数列表（如所有 slug），Next 会在构建期对这些路径生成静态 HTML；未覆盖路径可用 `dynamicParams` 策略补充。
- **段级策略矩阵**：
  - `dynamic='force-static'`：强制静态（可结合 `revalidate` 做 ISR）
  - `dynamic='force-dynamic'`：强制每请求动态
  - `revalidate=60`：ISR 间隔 60 秒
  - `fetchCache='force-no-store'`：对应请求层禁缓存
- 与 `cookies()/headers()`：一旦读取请求态，默认转动态（除非明确隔离在小段）。

**扩展/对比**

- `pages/` 用 `getStaticPaths+getStaticProps`，心智更分散。
- 面试可举例：**电商详情** → `generateStaticParams` 预生热销 SKU，长尾走 ISR/动态；**新闻** → 目录静态、详情 ISR。

------

### 4) 并行路由（Parallel Routes）与拦截路由（Intercepting Routes）🔥

**一句话回答**
 并行路由用命名插槽 `@slot` 同时渲染多个子区域；拦截路由用 `(..)` 把子页面内容“借位”展示（如 Modal/Drawer）。

**要点**

- 并行：`app/@left`、`app/@right` + `layout` 注入 `<Left/><Right/>`
- 拦截：`(..)segment` / `(...)segment` 语法
- 典型：列表+详情并行、详情在上层以弹层打开且可直达链接

**详细回答**

- **并行**：不再“一个 page 填满屏”，而是同级多片段并发加载；结合 Suspense/Streaming，慢源不阻塞快源。
- **拦截**：保证 “/items/[id]” 既能在新页面打开，也能在 “/items” 上层以弹层形式展示（复用同一路由组件，状态与可分享性兼得）。

**扩展/对比**

- 传统做法要么写两个页面，要么手工同步 URL 与 UI 状态，维护成本极高。
- 面试建议：清晰阐述两者差异与组合使用场景（例如“邮件三栏布局 + 详情弹层”）。

------

## 二、数据获取与缓存

### 5) App Router 的 `fetch` 缓存层级与策略 🔥

**一句话回答**
 `fetch` 默认参与 **请求缓存** 与 **数据缓存**，再叠加 **路由缓存**；通过 `cache`、`revalidate`、`tags`、`revalidatePath/Tag` 做精准“取/存/失效”。

**要点**

- `cache: 'force-cache' | 'no-store'` 控制**请求层**
- `next: { revalidate, tags }` 控制**数据层**（ISR + 标签）
- 路由层：页面片段缓存（`router.refresh()` 可刷新）
- 失效手段：`revalidatePath('/p')`、`revalidateTag('posts')`

**详细回答**

- **请求缓存**：相同 URL+init 的 `fetch` 结果可直接复用（编译期+运行期综合）。
- **数据缓存**：带 `revalidate` 的响应会在服务器数据层缓存并按期再生；`tags` 让你跨页面/模块聚类失效。
- **路由缓存**：在客户端保留最近访问过的页面片段，路由返回秒开；与并行路由一起体验更佳。

**扩展/对比**

- `pages/` 时代缓存策略需要自建层；App Router 把“缓存”设计为 **默认存在的能力**，重点变成“声明行为”。
- 面试点睛：能说清 **三层缓存** 与 **四个关键原语**（`cache/revalidate/tags/revalidate*`）。

------

### 6) 静态渲染 vs 动态渲染（Dynamic Rendering）🔥

**一句话回答**
 默认尽可能静态；读取请求态/`no-store`/强制动态时，转为动态；**以 Suspense 为边界**实现“片段动态、壳静态”。

**要点**

- 触发动态：`cookies()/headers()`、`no-store`、`force-dynamic`
- 目标：把动态范围**收窄**到最小岛
- 结合 PPR：静态壳 + 动态岛

**详细回答**

- **产出策略**：静态可被 CDN 穿透缓存，TTFB/LCP 更稳定；动态需要服务端参与，每次请求都计算。
- **拆分技巧**：将“用户态/实时库存/个性化”包在 Suspense 里，其他部分保持静态或 ISR，做到“体验好 + 成本可控”。

**扩展/对比**

- 常见反模式：全页 `no-store` 导致吞吐大跌。
- 面试表达：先给“默认静态”的原则，再讲“如何最小化动态区域”的工程拆分。

------

### 7) `revalidatePath` / `revalidateTag` 的使用场景 🔥

**一句话回答**
 它们是 **命中式增量失效**：按路径或按内容域刷新缓存；常与 Server Actions/后台变更联动。

**要点**

- 内容更新后：`revalidateTag('posts')`（全站帖子列表/详情自动焕新）
- 定点页面：`revalidatePath('/blog')`
- 组合：写入成功 → 失效 → `router.refresh()` → 用户看到最新

**详细回答**

- `revalidateTag` 多用于**一类数据**（如某集合的文章）；`revalidatePath` 精确到**特定页面**。二者组合能覆盖 90% 的刷新需求。
- 与 ISR 协同：手动失效不必等待间隔；高峰流量更可控。

**扩展/对比**

- 传统：要么全量重建，要么线上强制回源；如今改为细粒度、低成本的“局部真相刷新”。

------

### 8) Draft Mode（草稿模式）🔥

**一句话回答**
 在不污染线上缓存的前提下，让编辑者会话获得**实时**内容，适合 CMS 预览。

**要点**

- 启用/关闭草稿的 API（Route Handler）
- 草稿态绕过缓存（等价于 `no-store`）
- 与权限/登录结合，仅对编辑者生效

**详细回答**

- 草稿态一般通过设置 Cookie 标记；页面内逻辑读取该态后选择绕过缓存或加特定请求头，确保编辑者能看到最新草稿。
- 默认用户仍命中缓存，不受影响。

**扩展/对比**

- 旧方案：单独“预发环境/预览域名”；App Router 直接内建“会话级预览”，更接近真实线上渲染链路。

------

## 三、Server Actions 与变更流

### 9) Server Actions 的价值与最佳实践 🔥

**一句话回答**
 在组件内定义服务端函数以处理“写操作”，**不暴露密钥**，并在同处编排缓存失效与 UI 刷新。

**要点**

- 表单直接 `action={myAction}`，或客户端 `useTransition` 调用
- 在 Action 内做：校验 → DB 写入 → `revalidate*` → 返回
- 与进度/错误/乐观 UI 协作

**详细回答**

- **安全**：私钥、DB 连接、第三方凭证完全在服务端；避免“先到客户端再转发”的风险。
- **编排**：在一个函数里完成业务写入与缓存策略，减少“写完再想起刷缓存”的漏网。
- **渐进增强**：非 JS 环境下提交也能回退为标准表单 POST。

**扩展/对比**

- 旧法：API Route + 客户端 fetch；现在：组件—Action—失效—刷新“一条龙”。
- 面试亮点：讲清 **安全边界** 与 **一致性闭环**（写→失效→refresh）。

------

### 10) 变更后的 UI 刷新与乐观更新 🔥

**一句话回答**
 用 `useTransition` 做非阻塞提交，`router.refresh()` 刷新段级快照；乐观 UI 需具备失败回滚策略。

**要点**

- `pending` 状态控制按钮禁用/进度条
- 成功后：`revalidate* + refresh`
- 失败：回滚本地状态、展示错误源（后端 message）

**详细回答**

- **体验**：`useTransition` 不阻塞输入与路由交互；结合 Toast/进度条反馈。
- **一致性**：避免只改本地状态却没刷新缓存导致“页面两套真相”。
- **回滚**：对乐观插入/删除设计可逆操作，或在失败时发起一次强制 `refresh` 求真相。

**扩展/对比**

- 传统场景需手写大量“前端状态同步 + 失效策略”；App 把“缓存即数据源”变成主通路。

------

## 四、Streaming、并发与用户体验

### 11) Streaming SSR 与 `loading.tsx`/Suspense 🔥

**一句话回答**
 利用 Suspense 将慢资源“包裹”成延后区域，让“页面壳 + 关键框架”先渲染，慢数据后续补齐。

**要点**

- `loading.tsx` = 该段 Suspense fallback
- 适合新闻、电商详情、仪表盘
- 与并行路由组合：快慢区块互不拖累

**详细回答**

- **指标改善**：TTFB/LCP 显著下降，“骨架屏可见”早于“数据可用”；用户感觉“更快能用”。
- **落地技巧**：按卡片/模块切边界，小处也能独立流式；首屏只保证最关键可见 + 交互。

**扩展/对比**

- `pages/` SSR 等所有数据齐全再出 HTML，慢源阻塞整页；Streaming 是 App 的默认思路之一。

------

### 12) `error.tsx` 与错误边界策略 🔥

**一句话回答**
 在路由段落粒度把错误“圈”住，提供 `reset()` 重试，既保护整体稳定又便于定位问题。

**要点**

- 服务端/数据错误进入 `error.tsx`
- 客户端副作用错误仍需客户端 ErrorBoundary
- 记录：在 Action/Handler 里落日志（含 traceId/用户上下文）

**详细回答**

- **用户体验**：局部错误页 + 重试按钮，而不是整页“红屏/空白”。
- **可观测性**：在 `error.tsx` 上报 Sentry/自建监控，附带 params/searchParams，定位到具体段与数据源。

**扩展/对比**

- `pages/` 多为全局 `_error` 或自定义 500，定位/止损粒度差；App 更贴近“问题就地处理”。

------

## 五、导航与参数

### 13) `next/navigation` 的路由 API（App 专属）🔥

**一句话回答**
 用 `useRouter/usePathname/useSearchParams` 管理导航和查询；服务端 `redirect/notFound` 让跳转在渲染阶段完成，更干净。

**要点**

- 客户端：`useRouter().push/replace/back/forward/refresh`
- 读取：`usePathname()`、`useSearchParams()`（类 `URLSearchParams`）
- 服务端：`redirect('/login')`、`notFound()`
- 刷新：`router.refresh()` 刷段级快照（与 Router Cache 协作）

**详细回答**

- **客户端导航**只在 `"use client"` 组件中可用，适合按钮/交互跳转；`refresh()` 会请求最新的 RSC Payload，**不做整页刷新**，仅更新当前段的“数据快照”。
- **服务端跳转**在 RSC/Route Handler 中直接 `redirect`，渲染短路，避免渲染中间态（对鉴权/条件路由尤佳）。
- **参数读取**推荐在 RSC 里用 `searchParams`（函数入参）而不是在客户端解析，减少客户端耦合。

**扩展/对比**

- `pages/`：使用 `next/router`，没有 `redirect/notFound` 这种“服务端短路”的一等能力。
- 面试亮点：阐明 **refresh 与 SPA 的差异**（刷新的是 RSC 数据片段，不是浏览器整页）。

------

### 14) `generateMetadata` 与 SEO 体系 🔥

**一句话回答**
 `metadata/generateMetadata` 把 SEO 信息纳入 RSC 生命周期，支持**动态元信息**与**结构化多文件约定**（icons、manifest、robots、sitemap）。

**要点**

- 静态：`export const metadata = {...}`
- 动态：`export async function generateMetadata({ params, searchParams })`
- 相关约定：`app/icon.tsx | app/robots.ts | app/sitemap.ts | app/manifest.ts`

**详细回答**

- **动态 SEO**：在 `generateMetadata` 里拿到 `params/searchParams` 后，服务端请求内容（如文章标题/描述/OG 图）并返回元信息，**首屏就输出完整 `<head>`**，利于爬虫抓取与社交卡片预览。
- **结构化约定**让 SEO 基建真正“工程化”：站点地图/Robots/Manifest 都走代码生成，减少手工维护出错。

**扩展/对比**

- `pages/` 多靠 `next/head` 手写，容易遗漏或动态不一致。
- 面试可加：多语言 SEO（`alternates`/`hreflang`）、canonical、OG & Twitter 卡片生成策略。

------

## 六、运行时与边缘能力

### 15) Route Handlers（`app/api/*/route.ts`）🔥

**一句话回答**
 基于 Fetch 标准的 API 端点，结合 `runtime = 'edge'|'nodejs'` 灵活选择执行环境，并且与 Actions/中间件天然协作。

**要点**

- 导出对应 HTTP 方法：`GET/POST/PUT/DELETE`
- 响应：`NextResponse.json()` 或构造 `Response`
- 环境：`export const runtime = 'edge' | 'nodejs'`
- 读取：`cookies() / headers()`，与页面一致

**详细回答**

- **一致的 Web API**（Request/Response）降低心智负担，便于复用同一中间层（如签名校验、追踪头等）。
- **Edge** 适合延迟敏感/就近路由的轻计算接口；**Node** 适合需要 Node 原生能力或本地二进制的重计算接口。

**扩展/对比**

- `pages/api` 只能 Node 风格；Route Handlers 扩展到 Edge 并靠近 RSC 生命周期。
- 面试亮点：讲清“**何时选 Edge**”（读多写少、轻逻辑、依赖最少）。

------

### 16) Middleware 的边缘拦截与常见场景 🔥

**一句话回答**
 在路由命中前进行“**就近拦截**”：鉴权、分流、A/B、i18n 重写，返回 `NextResponse.next/redirect/rewrite`。

**要点**

- 文件：`middleware.ts`（根级）
- 可读取：Cookies、地理位置、UA
- 常见：登录校验、地域/设备分流、灰度桶、国际化前缀重写

**详细回答**

- **性能**：运行在 Edge Runtime，避免回源到主服务；对“无状态判定”非常高效（例如未登录直接 302）。
- **重写与分流**：配合 Rewrite 把 `/` 重写到 `/zh` 或实验版本路径，**URL 不变**但内容不同，便于 A/B。

**扩展/对比**

- Middleware **不适合重计算/长耗时**；那就用 Route Handlers/后端。
- 与 Server Actions 对比：Middleware 在“读”场景更强，Actions 在“写”场景更强。

------

## 七、客户端与 RSC 边界

### 17) `"use client"` 的判定与拆分策略 🔥

**一句话回答**
 凡是需要状态/事件/副作用/浏览器 API 的组件，都必须 `"use client"`；其余尽量留在 RSC，以**减 JS 包**与**减水合**。

**要点**

- 客户端：`useState/useEffect`、事件绑定、`window/document`
- 服务器：数据获取、模板组织、权限控制
- 策略：**边界下沉**（交互粒度越小越好），Server → Client 单向数据传递

**详细回答**

- **边界组件**（Boundary Component）理念：页面/布局尽可能是 Server，交互碎片才是 Client；减少“客户端再发请求”。
- **可测性/性能**：Server 组件无副作用，更易测试与复用；客户端体积/水合时间显著降低。

**扩展/对比**

- `pages/` 几乎一切都是客户端渲染，包体随规模走高。
- 面试可加：如何定位“必须 client”的第三方依赖并把它“关在小黑屋”（见下一题）。

------

### 18) RSC 与第三方库的兼容策略 🔥

**一句话回答**
 只要库依赖浏览器环境或运行期副作用（Chart/Editor/Map/Canvas），就放客户端；必要时用 `dynamic(..., { ssr: false })` 兜底。

**要点**

- 交互/绘图类：客户端 + 动态导入
- 数据纯函数类：可在服务端执行（RSC）
- 降级：`dynamic(() => import('xxx'), { ssr: false })`

**详细回答**

- **类型分类**：
  1. **纯计算/格式化**：尽量放 RSC，避免进 bundle。
  2. **UI/可视化**：多数要 client，且用 `dynamic` 减少首屏阻塞（与 Suspense 配合做“懒首屏”）。
- **边界传参**：将数据在 RSC 准备好，通过 props 传给 Client 组件，**避免二次 fetch**。

**扩展/对比**

- 反模式：把整页都标 `"use client"` 只因为用了一个图表。
- 面试亮点：讲清“**最小化客户端岛**”的方法与收益（包体、INP/LCP）。

------

## 八、图片/字体/静态资源

### 19) `next/image` 与 RSC 的协作 🔥

**一句话回答**
 `<Image>` 带懒加载/自适应/格式协商与占位；在 RSC 中产出优化标记，真正加载在客户端完成，提升 LCP/CLS。

**要点**

- 必备：`alt`、尺寸（`width/height` 或 `fill + sizes`）
- 远程域名：`next.config.js` 里 `images.domains`
- 静态资源：优先 public/静态导入，受控于 Next 的优化管道

**详细回答**

- **CLS 防抖**：提供布局占位，避免首屏挤跳；
- **自适应**：按 DPR/视口返回合适尺寸与格式（webp/avif），减少传输体积。
- **缓存/CDN**：产物由 Next 代理优化，可结合平台 CDN 全局缓存。

**扩展/对比**

- 原生 `<img>` 缺少优化管道，需自行处理 srcset/sizes/loading。
- 面试可加：SSR + `<Image>` 的工作流与可能的坑（例如在 Edge 上的远程优化代理）。

------

### 20) `next/font` 的本地字体优化 🔥

**一句话回答**
 `next/font` 把 Google/自托管字体打包进产物，支持子集/可变字体，减少 FOUT/FOIT 与第三方依赖风险。

**要点**

- `import { Inter } from 'next/font/google'`
- 声明子集、字重、display；得到 className 直接用
- 自托管：`next/font/local` 避免外链

**详细回答**

- **性能/稳定性**：本地字体可被 CDN 缓存，**不受外网波动影响**；首屏更稳。
- **可变字体**：一个文件覆盖多字重，减少请求；结合 `font-display` 折中首屏闪烁。

**扩展/对比**

- 外链 `<link>` 方案容易被阻塞或劫持，且缓存可控性差。
- 面试可加：在中文场景如何做**子集裁剪**与**按需加载**（减少巨大字体包）。

------

## 九、性能与 PPR

### 21) 部分预渲染（PPR / Partial Prerendering）🔥

**一句话回答**
 同一页面里：**壳**静态可缓存、**岛**动态可个性化；以 Suspense 边界实现“静/动混合”的工程化落地。

**要点**

- 目标：在保持可缓存的同时承载个性化/实时块
- 手法：静态壳 + 动态岛（Suspense）
- 适合：登录后个性化仪表盘、详情页库存/价格块

**详细回答**

- **渲染拓扑**：先输出静态部分提升 LCP；动态岛在流式中补齐，**TTFB/LCP 与个性化体验兼得**。
- **缓存命中**：CDN 命中静态壳的概率高，服务器压力更稳定。

**扩展/对比**

- “全动态”会导致吞吐/成本难控；PPR 是更优的现实主义解。
- 面试亮点：讲出可观测指标改善（TTFB/LCP/INP）与可伸缩性。

------

### 22) Web Vitals 与 App Router 常见优化手段 🔥

**一句话回答**
 靠 **减少客户端 JS、Streaming、Image/Font 优化、细粒度缓存与失效** 改善 LCP/CLS/INP。

**要点**

- **JS 减量**：RSC 优先、最小化 `"use client"`、动态导入重组件
- **首屏**：Streaming + `loading.tsx`、`<Image>` 正确 sizes
- **缓存**：`revalidate`/tags + `router.refresh()`
- **交互**：避免重型同步任务，使用 `useTransition`/requestIdleCallback

**详细回答**

- **INP**：避免在交互路径上加载巨型依赖；把图表/编辑器延迟到可见时才加载。
- **LCP**：首屏关键图像用 `<Image>` 与正确尺寸；减少阻塞字体与大 CSS。
- **CLS**：预留高度、使用 `fill + sizes`、避免异步插入破坏布局。

**扩展/对比**

- `pages/` 的全客户端心智让 JS 层成为瓶颈；App 的 RSC 与 Streaming 能天然缓解。
- 面试可加：如何用 `next/script` 的 `strategy` 控制第三方脚本时机。

------

## 十、安全与鉴权

### 23) 鉴权：Middleware + Server Actions + Route Handlers 🔥

**一句话回答**
 **边缘粗鉴权**（Middleware）、**服务端细授权**（Handlers）、**写操作走 Actions**，令安全边界和一致性闭环可组合。

**要点**

- Cookie 存 Session/Token（HttpOnly、SameSite）
- Middleware：未登录 302、按角色重写
- Handlers：登录/刷新、细粒度资源校验
- Actions：写 → `revalidate*` → `refresh`（前后状态一致）

**详细回答**

- **会话策略**：把凭证放 HttpOnly，减少 XSS 风险；刷新 Token 走 Handler。
- **最小授权**：在 Handler 内做资源级别校验（如只能改自己的订单），返回最小范围数据。
- **变更闭环**：写后失效与刷新，避免“错层状态”。

**扩展/对比**

- 传统：前端拿 Token 到处飞；App 架构让**“写逻辑”天然在服务端**，安全与一致性更强。
- 面试加分：提 CSP、DOMPurify、同源策略与 CSRF Token（见你的安全篇）。

------

## 十一、部署与运维

### 24) 运行时选择：Edge vs Node.js 🔥

**一句话回答**
 **读多写少、延迟敏感**选 Edge；**依赖 Node 模块/本地库/长耗时**选 Node。可混合部署、路由粒度选择。

**要点**

- Edge：冷启动小、就近执行、API 受限
- Node：生态全、长任务稳定、冷启动略大
- 声明：`export const runtime = 'edge' | 'nodejs'`

**详细回答**

- **Edge 适合**：个性化内容首跳、地理/设备分流、签名校验、轻聚合。
- **Node 适合**：复杂 DB 事务、PDF/图像处理、依赖原生扩展（Sharp、Puppeteer）。
- **混合**：目录或路由粒度分别声明，达到**成本/性能最优解**。

**扩展/对比**

- 误区：把重活放 Edge 会踩 API 限制与超时。
- 面试亮点：给出实际场景拆分表（如“登录页 SSR Edge、后台报表 Node”）。

------

### 25) 常见“从 pages 迁到 app”踩坑清单 🔥

**一句话回答**
 **换心智**：以 **RSC + 缓存 + 段级状态** 为中心，先做等价迁移，再引入 Streaming/Actions/并行路由/PPR。

**要点**

- 数据：`getServerSideProps/getStaticProps` → 组件内 `fetch` + 段级 `revalidate/dynamic`
- 路由：`next/router` → `next/navigation`；`_app/_document` → 多层 `layout`
- 交互：最小化 `"use client"`，第三方重组件用 `dynamic`
- 刷新：写后 `revalidatePath/Tag` + `router.refresh()`
- SEO：`next/head` → `generateMetadata` + 约定文件

**详细回答**

- **策略**：
  1. **等价重构**（不引入新特性，先跑通）；
  2. 引入 **Streaming/并行/拦截** 重塑信息架构；
  3. 梳理 **缓存矩阵**（静/动、请求/数据/路由三层）；
  4. 将“写路径”改成 **Server Actions + 精准失效**。
- **排错心法**：动态被静态化？检查 `cookies()/headers()` 与段级 `dynamic`；首屏阻塞？检查 `"use client"` 泄露与大依赖未动态导入。

**扩展/对比**

- 迁移不是“一次性重写”，而是**分阶段收益递增**的工程。
- 面试亮点：分享你在真实项目中的“迁移分步与量化指标”（包体/LCP/INP/TTFB 改善）。

------

