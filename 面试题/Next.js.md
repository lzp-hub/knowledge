# 📘 Next.js 高频面试题 · 全展开版（2025 中国前端专用）

## 一、基础与核心概念

### 1. 什么是 Next.js？相比 CRA 的优势是什么？ 🔥

**一句话回答**
Next.js 是基于 React 的全栈框架，支持 SSR/SSG/ISR，内置路由与优化，比 CRA 更适合生产环境。

**要点**

- 基于 React。
- 支持 SSR / SSG / ISR。
- 内置路由、API、性能优化。
- CRA 仅 CSR。

**详细回答**
CRA (create-react-app) 只支持 CSR（客户端渲染），首屏慢且不利 SEO。
Next.js 在 React 基础上扩展了：

- **渲染模式多样**：支持 CSR / SSR / SSG / ISR。
- **路由**：基于文件系统自动生成。
- **API Routes**：内置后端能力。
- **优化**：代码分割、图片优化、字体优化。

**扩展/对比**

- 面试官常问：“你为什么用 Next.js？” 👉 回答可结合实际：SEO 需求、性能优化、SSR 支持。

---

### 2. CSR、SSR、SSG、ISR 的区别与适用场景 🔥

**一句话回答**
CSR 全靠前端渲染，SSR 服务端实时渲染，SSG 构建时静态化，ISR 静态生成+定期更新。

**要点**

- CSR：首屏慢，交互快。
- SSR：首屏快，SEO 好，服务器压力大。
- SSG：性能最佳，适合静态内容。
- ISR：静态生成 + 定时刷新。

**详细回答**

- CSR：纯前端渲染，适合内部系统。
- SSR：每次请求都服务端渲染，适合新闻类页面。
- SSG：构建时生成静态 HTML，适合博客/文档。
- ISR：基于 revalidate 定期更新，适合电商/内容网站。

**扩展/对比**

- 面试官常问：“如果是商品详情页你会选什么？” 👉 ISR（兼顾 SEO 和动态性）。

---

### 3. Next.js 中的目录结构（pages/ vs app/）区别

**一句话回答**
pages/ 是旧路由系统，app/ 是新 App Router，支持 RSC 和 Server Actions。

**要点**

- pages/：基于文件系统，支持 getServerSideProps。
- app/：支持 Server Components、并发特性。
- app/ 默认基于 React 18 特性。

**详细回答**

- **pages/**：传统路由，数据获取依赖 getServerSideProps/getStaticProps。
- **app/**：引入 React Server Components，支持 server-only 逻辑、`generateMetadata`、`useServerAction`。

**扩展/对比**

- 2025 面试更倾向问 App Router，老项目还在用 pages。

---

### 4. 什么是 App Router？为什么迁移？ 🔥

**一句话回答**
App Router 基于 React Server Components，提供更强的数据获取和优化能力，是未来方向。

**要点**

- Server Components。
- 并发渲染。
- 数据获取简化。
- SEO 友好。

**详细回答**
App Router 提供：

- Server Components：减少客户端 JS 体积。
- Layout & Nested Routing：支持嵌套布局。
- Server Actions：直接调用后端函数。

**扩展/对比**

- 面试官常问：“为什么 App Router 比 pages 好？” 👉 更好的性能、内置数据获取、SEO。

---

### 5. React Server Components (RSC) 在 Next.js 中的作用 🔥

**一句话回答**
RSC 允许部分组件在服务端渲染，减少客户端 JS 体积，提高性能。

**要点**

- 服务端执行，不打包到客户端。
- 客户端仅接收渲染结果。
- 结合 Suspense 实现流式渲染。

**详细回答**

- RSC 让开发者写的组件可以是 **server-only** 或 **client-only**。
- 默认是 Server Component，只有加 `"use client"` 才会被打包到前端。
- 避免了很多不必要的客户端 bundle。

**扩展/对比**

- 面试官常问：“RSC 和 SSR 的区别？” 👉 RSC 在服务端执行逻辑，SSR 是输出 HTML，二者可结合。

---

# 二、数据获取

### 6. `getServerSideProps` vs `getStaticProps` vs `getStaticPaths` 🔥

**一句话回答**
`getServerSideProps` 每次请求运行，`getStaticProps` 构建时运行，`getStaticPaths` 用于生成动态静态路由。

**要点**

- getServerSideProps：请求时运行，SSR。
- getStaticProps：构建时运行，SSG。
- getStaticPaths：配合 SSG 动态路由。

**详细回答**

- `getServerSideProps`：实时获取数据，每次请求都调用。
- `getStaticProps`：构建时调用，生成静态 HTML。
- `getStaticPaths`：指定动态路径参数，生成对应页面。

**扩展/对比**

- 面试官常问：“电商商品详情页用哪个？” 👉 ISR (`getStaticProps + revalidate`)。

---

### 7. `getInitialProps` 为什么不推荐？

**一句话回答**
`getInitialProps` 会阻塞页面渲染，且无法利用静态优化，因此已被弃用。

**要点**

- 阻止自动静态优化。
- 页面性能差。
- App Router 不再支持。

**详细回答**
`getInitialProps` 在 pages 时代常用，但缺点：

- 每次请求都运行，无法缓存。
- 阻止静态化，影响性能。
- App Router 已用 async Server Components 取代。

**扩展/对比**

- 面试时若被问，应强调“推荐使用 `getServerSideProps` / `getStaticProps` 或 App Router 的数据获取”。

---

### 8. App Router 中的数据获取方式 🔥

**一句话回答**
App Router 推荐在 **Server Components** 里直接用 `fetch`，并结合缓存策略。

**要点**

- `fetch` 默认缓存。
- `cache: 'no-store'` 强制实时。
- `revalidate` 支持 ISR。

**详细回答**

```ts
export default async function Page() {
  const data = await fetch("https://api.example.com", {
    cache: "no-store",
  }).then((res) => res.json());
  return <div>{data.title}</div>;
}
```

App Router 不再需要 `getServerSideProps`，直接在组件里调用即可。

**扩展/对比**

- 面试官可能追问：CSR 数据获取怎么做？ 👉 `useEffect + fetch` 或 SWR/React Query。

---

### 9. 什么是 Incremental Static Regeneration (ISR)？ 🔥

**一句话回答**
ISR 允许静态页面在构建后按需、定期重新生成。

**要点**

- 静态 + 动态结合。
- `revalidate` 设置时间。
- SEO 友好。

**详细回答**
ISR 机制：

- 初次请求返回旧静态页面。
- 后台触发再生成。
- 下次请求会拿到最新版本。

**扩展/对比**

- 电商/新闻网站常用 ISR 保证内容更新。

---

### 10. Next.js 中的 revalidate 是如何工作的？

**一句话回答**
`revalidate` 设置页面缓存失效时间，到期后会后台生成新页面。

**要点**

- `revalidate: 60` 表示 60 秒后更新。
- 旧页面依然可用，新页面生成后替换。

**详细回答**

```ts
export async function getStaticProps() {
  return {
    props: { data },
    revalidate: 60,
  };
}
```

**扩展/对比**

- ISR = SSG + revalidate。
- App Router 中可直接在 `fetch` 设置 `next: { revalidate: 60 }`。

---

# 三、路由与导航

### 11. 动态路由与嵌套路由 🔥

**一句话回答**
Next.js 动态路由通过 `[param]`，嵌套路由通过文件夹层级实现。

**要点**

- pages：`pages/[id].tsx`。
- app router：`app/blog/[slug]/page.tsx`。

**详细回答**

- 动态路由：`/blog/[id]` → `/blog/1`。
- 嵌套路由：目录结构决定层级，自动生成路径。

**扩展/对比**

- 面试官常问：“如何生成所有动态路由？” 👉 `getStaticPaths`。

---

### 12. `useRouter` Hook 与导航

**一句话回答**
`useRouter` 提供路由对象，支持编程式导航和参数获取。

**要点**

- `router.push` / `router.replace`。
- `router.query` 获取参数。

**详细回答**

```ts
import { useRouter } from "next/router";
const router = useRouter();
router.push("/home");
```

**扩展/对比**

- App Router 替换为 `usePathname` / `useSearchParams`。

---

### 13. App Router 的 `usePathname`、`useSearchParams`

**一句话回答**
在 App Router 中，`usePathname` 获取路径，`useSearchParams` 获取 query 参数。

**要点**

- `usePathname`: 返回当前路径。
- `useSearchParams`: 类似 `URLSearchParams`。

**详细回答**

```ts
const pathname = usePathname();
const searchParams = useSearchParams();
const id = searchParams.get("id");
```

**扩展/对比**

- 替代了 `useRouter().query`。

---

### 14. Middleware 的作用与应用场景 🔥

**一句话回答**
Middleware 在请求到达页面前运行，可用于鉴权、重定向、日志。

**要点**

- 基于 Edge Runtime。
- 修改 request/response。
- 典型应用：登录校验。

**详细回答**

```ts
export function middleware(req) {
  if (!req.cookies.get("token")) {
    return NextResponse.redirect(new URL("/login", req.url));
  }
}
```

**扩展/对比**

- 面试官常问：“Middleware 和 API Route 区别？” 👉 Middleware 在边缘拦截，请求未到服务端逻辑。

---

### 15. 路由跳转方式对比

**一句话回答**
Next.js 支持 Link 组件、编程式导航（useRouter）、以及服务端重定向。

**要点**

- `<Link>`：推荐，预取优化。
- `router.push`：编程式。
- `redirect`：服务端。

**详细回答**

- `<Link>` 内置 prefetch，性能最佳。
- `router.push` 适合事件回调跳转。
- `redirect` 常用于鉴权。

**扩展/对比**

- 面试官可能追问：“CSR 与 SSR 的跳转方式有何不同？” 👉 CSR 用 Link，SSR 用 redirect。

---

# 四、样式与资源

### 16. Next.js 支持的样式方式 🔥

**一句话回答**
Next.js 支持 CSS Modules、全局 CSS、CSS-in-JS、Tailwind 等。

**要点**

- CSS Modules：局部作用域。
- 全局 CSS：`globals.css`。
- CSS-in-JS：styled-components / emotion。
- Tailwind：原子化 CSS。

**详细回答**

- CSS Modules 默认支持，避免命名冲突。
- Tailwind 常用在中大型项目。
- CSS-in-JS 需配置 babel 插件。

**扩展/对比**

- 面试官常问：“你们团队用什么样式方案？” 👉 根据经验回答。

---

### 17. `next/image` 与 `next/font` 的作用 🔥

**一句话回答**
`next/image` 提供图片优化，`next/font` 提供字体优化。

**要点**

- image：懒加载、响应式、自动压缩。
- font：Google Fonts 本地化、可变字体。

**详细回答**

```tsx
import Image from "next/image";
<Image src="/logo.png" width={200} height={200} alt="logo" />;
```

- `next/font` 支持本地和在线字体，避免闪烁。

**扩展/对比**

- 面试官常问：“next/image 比 <img> 好在哪？” 👉 懒加载+优化+CDN。

---

### 18. 静态资源的存放与引用

**一句话回答**
Next.js 静态资源放在 `public/` 目录，通过根路径访问。

**要点**

- `public/logo.png` → `/logo.png`。
- 不需要 import。

**详细回答**

- 适合存放 favicon、robots.txt、图片资源。

**扩展/对比**

- 如果是动态图片，推荐用 `next/image`。

---

# 五、API 与服务端能力

### 19. Next.js 中的 API Routes

**一句话回答**
API Routes 让 Next.js 在 pages 目录下直接提供后端接口。

**要点**

- `pages/api/*`。
- 返回 JSON。
- 内置 Node.js 环境。

**详细回答**

```ts
export default function handler(req, res) {
  res.status(200).json({ msg: "Hello API" });
}
```

适合小型后端接口、BFF 模式。

**扩展/对比**

- 生产环境复杂接口一般交给独立后端。

---

### 20. App Router 中的 Route Handlers 🔥

**一句话回答**
Route Handlers 是 App Router 里的 API 路由，用于构建后端逻辑。

**要点**

- `app/api/*/route.ts`。
- 支持 GET/POST/PUT/DELETE。
- 运行在 Edge 或 Node 环境。

**详细回答**

```ts
export async function GET(req: Request) {
  return NextResponse.json({ msg: "hello" });
}
```

**扩展/对比**

- 相比 pages/api，更贴近 Web 标准（Request/Response API）。

---

### 21. Middleware 与 Edge Functions 的区别

**一句话回答**
Middleware 在请求前运行拦截，Edge Functions 是运行在边缘的完整函数逻辑。

**要点**

- Middleware：轻量拦截。
- Edge Function：完整 API 逻辑。
- 都运行在 Edge Runtime。

**详细回答**

- Middleware 适合鉴权、重定向。
- Edge Function 适合 CDN 边缘计算，延迟更低。

**扩展/对比**

- 面试官常问：“Middleware 能不能替代 API Routes？” 👉 不行，只适合拦截处理。

---

### 22. Next.js 中的鉴权实现 🔥

**一句话回答**
常见方式：JWT、本地 session、NextAuth.js，结合 Middleware 做路由保护。

**要点**

- Middleware 校验 cookie/token。
- NextAuth.js 插件。
- JWT 存储在 HttpOnly cookie。

**详细回答**

```ts
export function middleware(req: NextRequest) {
  const token = req.cookies.get("token");
  if (!token) return NextResponse.redirect(new URL("/login", req.url));
}
```

**扩展/对比**

- 面试官可能问：“为什么不用 localStorage 存 token？” 👉 HttpOnly cookie 更安全。

---

# 六、性能与优化

### 23. Next.js 内置性能优化 🔥

**一句话回答**
Next.js 自动做代码分割、懒加载、图片/字体优化、路由预取。

**要点**

- 自动代码分割。
- 图片优化。
- `Link` 预取。

**详细回答**

- 每个页面打包单独 chunk。
- Link 默认预取下一页。

**扩展/对比**

- 面试官常问：“Next.js 为什么比 CRA 性能好？” 👉 内置优化。

---

### 24. 如何提升 Next.js 的首屏性能？ 🔥

**一句话回答**
通过减少阻塞资源、Critical CSS、懒加载、ISR 提升首屏性能。

**要点**

- 图片懒加载。
- 代码分割。
- Critical CSS 内联。
- ISR 缓存。

**详细回答**

- 优化 SSR：减少 API 请求链路。
- 优化 CSR：懒加载组件。

**扩展/对比**

- 面试官可能问：“如何衡量优化效果？” 👉 Lighthouse、Web Vitals。

---

### 25. Next.js 中的 SEO 优化 🔥

**一句话回答**
通过 SSR/SSG、`generateMetadata`、`next/head`、sitemap 提升 SEO。

**要点**

- SSR/SSG：可见 HTML。
- generateMetadata：App Router 推荐。
- sitemap.xml、robots.txt。

**详细回答**

```ts
export async function generateMetadata() {
  return { title: "My Page", description: "SEO text" };
}
```

**扩展/对比**

- 面试官常问：“Next.js 如何比纯 React SEO 好？” 👉 SSR/SSG + Metadata。a
