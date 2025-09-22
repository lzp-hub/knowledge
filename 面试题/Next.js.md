# 📘 Next.js 高频面试题 · 全展开版（2025 中国前端专用）

## 一、基础与核心概念

### 1. 什么是 Next.js？相比 CRA 的优势是什么？ 🔥

**一句话回答**
Next.js 是基于 React 的全栈框架，支持 SSR/SSG/ISR，内置路由与性能优化，比 CRA 更适合生产环境。

**要点**

- 基于 React。
- 支持多种渲染模式（SSR / SSG / ISR / CSR）。
- 内置路由、API、性能优化（图片/字体/代码分割）。
- CRA 仅支持 CSR。

**详细回答**
CRA (create-react-app) 只支持 **CSR**，首屏慢、不利 SEO。
Next.js 在 React 基础上扩展了：

- **渲染模式多样**：按需选择 SSR/SSG/ISR。
- **路由系统**：基于文件系统，支持动态/嵌套路由。
- **API Routes / Route Handlers**：内置后端接口能力。
- **优化**：自动代码分割、图片/字体优化、流式渲染。
- **SEO**：构建时即可产出 HTML，利于搜索引擎。

**扩展/对比**

- **Next.js vs CRA**：有 SEO/性能要求 → Next.js；仅内网后台 → CRA 够用。
- **Next.js vs Vite+React**：Vite 开发体验更快，但 Next.js 在生产级 SSR/ISR、BFF、边缘计算方面更成熟。
- **团队协作**：Next.js 约定优于配置，规范化更强，交付更快。

---

### 2. CSR、SSR、SSG、ISR 的区别与适用场景 🔥

**一句话回答**
CSR 全在前端渲染，SSR 服务端实时渲染，SSG 构建时静态化，ISR 在静态基础上定期更新。

**要点**

- CSR：首屏慢，交互快。
- SSR：首屏快，SEO 好，但服务器压力大。
- SSG：性能最佳，适合纯静态内容。
- ISR：静态生成 + 定时刷新，兼顾动态性。

**详细回答**

- **CSR**：适合后台管理系统，用户体验优先，SEO 不敏感。
- **SSR**：每次请求服务端渲染，适合需要实时数据和强 SEO 的页面（如新闻）。
- **SSG**：构建时产出 HTML，适合博客、文档、营销落地页。
- **ISR**：静态化页面可在后台再生（`revalidate`），适合电商、内容站点。

**扩展/对比**

- 面试官常问：“电商商品详情页选哪种？” 👉 **ISR**（可缓存，数据有时效）。
- **成本对比**：CSR 几乎无服务端压力；SSR 成本最高；SSG 成本最低；ISR 折中。

---

### 3. Next.js 中的目录结构（pages/ vs app/） 🔥

**一句话回答**
`pages/` 是旧版路由系统，`app/` 是新 App Router，支持 React Server Components 和 Server Actions。

**要点**

- pages/：基于文件系统，数据获取依赖 `getServerSideProps`。
- app/：引入 RSC、`layout.tsx`、并行/拦截路由。
- app/ 默认使用 React 18 特性。

**详细回答**

- **pages/**：数据获取通过 `getServerSideProps` / `getStaticProps` / `getStaticPaths`。
- **app/**：支持 RSC，默认组件在服务端执行；支持 `generateMetadata`、`loading.tsx`、`error.tsx`、Server Actions。

**扩展/对比**

- 新项目推荐 App Router；旧项目仍大量使用 pages。
- 面试常追问：“为什么迁移到 App Router？” 👉 性能更好，减少客户端 JS，数据获取简化。

---

### 4. 什么是 App Router？为什么迁移？ 🔥

**一句话回答**
App Router 基于 React Server Components，提供更强的数据获取和性能优化，是 Next.js 的未来方向。

**要点**

- 默认支持 RSC。
- 多层 Layout + 并行/拦截路由。
- 数据获取简化，内置缓存策略。
- SEO 更友好。

**详细回答**
App Router 提供：

- **Server Components**：减少客户端 JS 体积。
- **Layout & Nested Routing**：天然支持多层布局与嵌套。
- **Server Actions**：可直接在服务端定义并调用函数。
- **流式渲染**：结合 Suspense 提升用户体验。

**扩展/对比**

- 面试官问：“App Router 比 pages 好在哪？” 👉 更好的性能、内置缓存、SSR/SEO 支持更强。
- **学习成本**：App Router 要熟悉 RSC、`use client`、缓存策略。

---

### 5. React Server Components (RSC) 的作用 🔥

**一句话回答**
RSC 允许组件在服务端运行，减少客户端 JS 体积，提高性能。

**要点**

- 默认组件即为 RSC。
- 客户端组件需显式 `"use client"`。
- 不能使用浏览器 API（如 window、document）。

**详细回答**

- **RSC 在服务端执行**，输出序列化的 UI 描述，客户端只需合成。
- 客户端组件负责事件、状态和交互，服务端组件负责数据获取和拼接 UI。
- 降低了客户端 bundle 大小，减少水合压力。

**扩展/对比**

- RSC vs SSR：SSR 生成 HTML，RSC 只生成虚拟树，最终由客户端拼合。
- 面试官可能问：“什么时候必须用 client component？” 👉 依赖 `useState`、`useEffect`、事件绑定、浏览器 API 时。

---

## 二、数据获取

### 6. `getServerSideProps` vs `getStaticProps` vs `getStaticPaths` 🔥

**一句话回答**
`getServerSideProps` 每次请求运行，`getStaticProps` 构建时运行，`getStaticPaths` 用于生成动态路由。

**要点**

- getServerSideProps：实时数据，SSR。
- getStaticProps：构建时执行，SSG。
- getStaticPaths：配合 SSG 动态路由。

**详细回答**

- `getServerSideProps`：每次请求触发，适合实时数据。
- `getStaticProps`：构建时执行，返回的 props 会静态写入 HTML。
- `getStaticPaths`：在构建时预生成所有动态路由页面。

**扩展/对比**

- 商品详情页：`getStaticProps + revalidate`（ISR）。
- 面试常问：“为什么不用 getInitialProps？” 👉 会阻塞优化，官方已弃用。

---

### 7. App Router 中的数据获取方式 🔥

**一句话回答**
App Router 推荐在 Server Components 里直接用 `fetch`，结合 `cache` 和 `revalidate` 控制缓存。

**要点**

- `fetch` 默认有缓存。
- `cache: 'no-store'` 强制实时请求。
- `next: { revalidate: 60 }` 支持 ISR。

**详细回答**

```tsx
export default async function Page() {
  const data = await fetch("https://api.example.com", {
    cache: "no-store",
    next: { revalidate: 60 },
  }).then((res) => res.json());

  return <div>{data.title}</div>;
}
```

- 不再需要 `getServerSideProps`，直接在组件里获取数据。
- 结合 Suspense，可流式渲染内容。

**扩展/对比**

- App Router 数据获取更简洁，但要理解缓存策略。
- CSR 获取数据仍用 SWR/React Query。

---

### 8. 什么是 Incremental Static Regeneration (ISR)？ 🔥

**一句话回答**
ISR 允许静态页面在构建后按需、定期重新生成。

**要点**

- 静态 + 动态结合。
- `revalidate` 设置过期时间。
- SEO 友好。

**详细回答**

- 初次请求返回旧页面。
- 触发后台再生新页面。
- 下次请求拿到新版本。

**扩展/对比**

- 新闻、电商常用 ISR，避免频繁构建。
- 面试官常问：“ISR 和 SSG 区别？” 👉 ISR 可定期刷新，SSG 一成不变。

---

# 三、路由与导航

### 9. 动态路由与嵌套路由 🔥

**一句话回答**
Next.js 动态路由通过 `[param]`，嵌套路由通过文件夹层级实现。

**要点**

- **pages/**：`pages/[id].tsx` 实现动态路由。
- **app/**：`app/blog/[slug]/page.tsx`，天然支持嵌套。
- 支持 catch-all：`[...slug].tsx`。

**详细回答**

- **动态路由**：如 `pages/posts/[id].tsx` → `/posts/1`。
- **嵌套路由**：目录层级即路由层级，`app/dashboard/settings/page.tsx` → `/dashboard/settings`。
- **catch-all**：`pages/docs/[...slug].tsx` → `/docs/a/b/c`。

**扩展/对比**

- App Router 更灵活，支持 **平行路由**（parallel routes）、**拦截路由**（intercepting routes）。
- 面试官常问：“如何生成所有动态路由页面？” 👉 `getStaticPaths`（pages）或 `generateStaticParams`（app）。

---

### 10. `useRouter` Hook 与导航 🔥

**一句话回答**
`useRouter` 提供路由对象，支持编程式导航和参数获取。

**要点**

- pages Router 用 `next/router`。
- `router.push` / `router.replace`。
- `router.query` 获取动态参数。

**详细回答**

```tsx
import { useRouter } from "next/router";

const router = useRouter();
console.log(router.query.id); // 获取动态路由参数
router.push("/home"); // 编程式跳转
```

**扩展/对比**

- App Router 用 `usePathname` 和 `useSearchParams` 替代。
- 面试官可能问：“如何获取 query 参数？” 👉 pages 用 `router.query`，app 用 `useSearchParams()`。

---

### 11. App Router 的 `usePathname`、`useSearchParams` 🔥

**一句话回答**
在 App Router 中，`usePathname` 获取路径，`useSearchParams` 获取 URL 参数。

**要点**

- `usePathname`：返回字符串路径。
- `useSearchParams`：类 `URLSearchParams`。
- 支持响应式更新。

**详细回答**

```tsx
"use client";
import { usePathname, useSearchParams } from "next/navigation";

const pathname = usePathname(); // "/blog/1"
const searchParams = useSearchParams();
const id = searchParams.get("id");
```

**扩展/对比**

- 替代了 `useRouter().query`。
- 面试官可能问：“在 App Router 如何实现搜索参数监听？” 👉 用 `useSearchParams`，配合 React 状态管理。

---

### 12. Middleware 的作用与应用场景 🔥

**一句话回答**
Middleware 在请求到达页面前运行，可用于鉴权、重定向、安全校验。

**要点**

- 基于 Edge Runtime。
- 修改 Request/Response。
- 应用场景：登录校验、A/B 测试、国际化路由。

**详细回答**

```ts
// middleware.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(req: NextRequest) {
  if (!req.cookies.get("token")) {
    return NextResponse.redirect(new URL("/login", req.url));
  }
}
```

- Middleware 在**边缘节点**运行，性能好，延迟低。
- 适合轻量逻辑，不适合复杂计算。

**扩展/对比**

- Middleware vs API Routes：Middleware 在**请求前**运行，API Routes 是**完整服务端接口**。
- 面试官常问：“权限校验放哪层合适？” 👉 登录拦截可放 Middleware，高级权限逻辑放 API/后端。

---

### 13. 路由跳转方式对比 🔥

**一句话回答**
Next.js 支持 `<Link>`、`useRouter` 编程式导航，以及服务端 `redirect`。

**要点**

- `<Link>`：推荐，带预取。
- `router.push`：事件驱动跳转。
- `redirect`：服务端跳转。

**详细回答**

- `<Link>` 自动做 **prefetch**，提高跳转性能。
- `router.push` 用于按钮点击跳转。
- App Router 的 `redirect()` 可在服务端逻辑中执行跳转。

**扩展/对比**

- CSR 场景：推荐 `<Link>`。
- SSR 场景：推荐 `redirect`。
- 面试官常问：“SSR 鉴权失败如何跳转？” 👉 `redirect('/login')`。

---

# 四、样式与资源

### 14. Next.js 支持的样式方式 🔥

**一句话回答**
Next.js 支持 CSS Modules、全局 CSS、CSS-in-JS、Tailwind 等多种方案。

**要点**

- **全局 CSS**：`app/globals.css` 或 `pages/_app.tsx` 引入。
- **CSS Modules**：默认支持，避免命名冲突。
- **CSS-in-JS**：styled-components / emotion 需配置 babel 插件。
- **Tailwind CSS**：官方文档推荐，适合中大型项目。

**详细回答**

- CSS Modules 自动生成哈希 className，本地作用域隔离。
- 全局 CSS 适合基础样式、reset、variables。
- styled-components 需要在 `next.config.js` 配置 `compiler.styledComponents = true`。
- Tailwind 在 Next.js 项目中可通过官方插件快速接入。

**扩展/对比**

- 面试官常问：“你们团队用什么样式方案？” 👉 大厂常答：**Tailwind + CSS Modules**（通用）、**styled-components**（高度定制）。
- 对比：CSS Modules → 最原生，Tailwind → 效率高，CSS-in-JS → 灵活但可能影响性能。

---

### 15. `next/image` 与 `next/font` 的作用 🔥

**一句话回答**
`next/image` 优化图片加载，`next/font` 优化字体加载。

**要点**

- `next/image`：懒加载、自动压缩、响应式。
- `next/font`：Google Fonts 本地化、可变字体支持。
- 均减少 CLS、提高性能评分。

**详细回答**

```tsx
import Image from "next/image";

<Image src="/logo.png" width={200} height={200} alt="logo" />;

// 字体优化
import { Inter } from "next/font/google";
const inter = Inter({ subsets: ["latin"] });
```

- `next/image` 默认启用懒加载和自动优化，支持 blur 占位符。
- `next/font` 自动生成 font-face，避免 FOUT/FOIT。

**扩展/对比**

- 面试官常问：“为什么不用 <img>？” 👉 `next/image` 提供优化管道 + CDN 加速。
- 字体优化比直接引第三方 link 更安全（避免被劫持）。

---

### 16. 静态资源的存放与引用 🔥

**一句话回答**
Next.js 静态资源放在 `public/`，通过 `/路径` 直接访问。

**要点**

- `public/logo.png` → `/logo.png`。
- 无需 import，直接引用。
- 不会被打包进 JS。

**详细回答**

- 静态资源适合 favicon、robots.txt、图片、视频等。
- 优点：CDN 可直接缓存，减少 JS bundle。

**扩展/对比**

- 静态资源 vs `import`：静态资源不会增加 JS 体积，import 会被打包。
- 面试官常问：“public 与 src/assets 区别？” 👉 public 不会进打包，src/assets 会。

---

# 五、API 与服务端能力

### 17. Next.js 中的 API Routes 🔥

**一句话回答**
API Routes 在 `pages/api/*` 下，提供内置后端接口能力。

**要点**

- 运行在 Node.js 环境。
- 支持 RESTful（GET/POST 等）。
- 常用于 BFF、小型接口。

**详细回答**

```ts
// pages/api/hello.ts
export default function handler(req, res) {
  res.status(200).json({ msg: "Hello API" });
}
```

- 适合处理简单请求（表单提交、代理、认证）。
- 不适合高负载业务（需独立后端）。

**扩展/对比**

- 面试官可能问：“API Routes 能替代后端吗？” 👉 小型项目可，复杂业务最好 BFF/独立服务。

---

### 18. App Router 中的 Route Handlers 🔥

**一句话回答**
Route Handlers 在 `app/api/*/route.ts`，是 App Router 的 API 路由。

**要点**

- 基于 Web 标准 Request/Response API。
- 支持 GET/POST/PUT/DELETE。
- 可运行在 Edge Runtime。

**详细回答**

```ts
// app/api/hello/route.ts
import { NextResponse } from "next/server";

export async function GET(req: Request) {
  return NextResponse.json({ msg: "hello" });
}
```

- 更接近 Web Fetch 标准，统一开发体验。
- 支持 edge functions 部署，更低延迟。

**扩展/对比**

- pages/api vs app/api：pages 是 Node 风格，app 是 Web 标准。
- 面试官可能问：“如何决定运行环境？” 👉 `export const runtime = "edge"` / "nodejs"。

---

### 19. Middleware 与 Edge Functions 的区别 🔥

**一句话回答**
Middleware 在请求前拦截，Edge Functions 在边缘运行完整函数逻辑。

**要点**

- Middleware：轻量拦截，修改 req/res。
- Edge Functions：可作为完整 API 接口。
- 都运行在 Edge Runtime（V8 沙箱）。

**详细回答**

- Middleware：适合鉴权、重定向、日志、国际化。
- Edge Functions：适合延迟敏感的 API（A/B 测试、推荐系统）。

**扩展/对比**

- Middleware vs API Routes：Middleware 无返回 UI/API 逻辑，仅拦截。
- 面试官可能问：“什么时候用 Edge Function？” 👉 动态内容在全球分发时。

---

### 20. Next.js 中的鉴权实现 🔥

**一句话回答**
常见方案：JWT、本地 session、NextAuth.js，结合 Middleware 做路由保护。

**要点**

- JWT：放 HttpOnly Cookie。
- Middleware：拦截未授权请求。
- NextAuth.js：官方推荐插件。

**详细回答**

```ts
// middleware.ts
import { NextResponse } from "next/server";

export function middleware(req) {
  const token = req.cookies.get("token");
  if (!token) return NextResponse.redirect(new URL("/login", req.url));
}
```

- JWT 适合前后端分离。
- NextAuth.js 集成 OAuth、Credentials、Email 登录。
- Session 存在 Redis/DB，配合 `next-auth`。

**扩展/对比**

- 面试官可能问：“Token 存哪里更安全？” 👉 HttpOnly Cookie。
- localStorage 存 token 易被 XSS 窃取。

---

# 六、性能与优化

### 21. Next.js 内置性能优化 🔥

**一句话回答**
Next.js 自动做代码分割、懒加载、图片/字体优化、路由预取，提高 Lighthouse/Web Vitals 得分。

**要点**

- 自动 **代码分割**：每个页面独立 chunk。
- 图片优化：`next/image`。
- 字体优化：`next/font`。
- 预取：`<Link>` 默认预取下一页。

**详细回答**

- **代码分割**：避免一次性加载整个应用。
- **图片优化**：自带懒加载、响应式、WebP 压缩。
- **字体优化**：内置 Google Fonts 本地化。
- **预取**：在视口中 `<Link>` 元素会自动预取对应页面 bundle。

**扩展/对比**

- 面试官常问：“为什么 Next.js 性能比 CRA 好？” 👉 CRA 默认没有 SSR/ISR、代码分割不够精细、无图片/字体优化。
- Next.js 在**无配置**情况下即可达到高性能评分，适合生产环境。

---

### 22. 如何提升 Next.js 首屏性能？ 🔥

**一句话回答**
减少阻塞资源、优化渲染链路，利用 ISR、懒加载和 Critical CSS 提升首屏体验。

**要点**

- 懒加载：组件/图片/视频。
- Critical CSS：只加载首屏需要的样式。
- ISR：缓存页面，减少服务端压力。
- 资源拆分：动态 import。

**详细回答**

- **图片懒加载**：非首屏图片使用 `loading="lazy"`。
- **动态 import**：`import dynamic(() => import('../HeavyComp'))`。
- **Critical CSS**：Next.js 自动抽取，避免 render-blocking。
- **流式渲染（Streaming SSR）**：用户先看到 skeleton，再渐进更新内容。

**扩展/对比**

- 测量工具：**Lighthouse、Web Vitals、Next.js 自带分析器**。
- 面试官常问：“如何优化 LCP/CLS/FID？” 👉 通过图片优化、减少 JS、字体优化。

---

### 23. Next.js 中的缓存机制 🔥

**一句话回答**
Next.js 基于 RSC + fetch 内置缓存，可设置 `cache` 和 `revalidate` 精细化控制。

**要点**

- `cache: 'force-cache'`（默认）。
- `cache: 'no-store'`（禁用缓存）。
- `next: { revalidate: X }`（ISR）。

**详细回答**

```ts
const data = await fetch("https://api.example.com", {
  cache: "no-store", // 或 "force-cache"
  next: { revalidate: 60 },
});
```

- **SSR 页面**：`no-store`，保证实时。
- **ISR 页面**：`revalidate`，定时更新。
- **SSG 页面**：`force-cache`，构建时静态。

**扩展/对比**

- 面试官可能问：“怎么避免缓存过期时用户等白屏？” 👉 ISR 旧页面继续可用，新页面后台生成。
- App Router 比 pages Router 更统一，fetch 就能搞定，不用写一堆 GSP/GSSP。

---

# 七、SEO 优化

### 24. Next.js 如何提升 SEO？ 🔥

**一句话回答**
利用 SSR/SSG/ISR 输出 HTML，结合 `generateMetadata`、sitemap、robots.txt 提升可爬取性。

**要点**

- SSR/SSG：HTML 可直接被爬虫读取。
- `generateMetadata`：App Router 推荐。
- sitemap.xml、robots.txt。
- Open Graph（OG）信息：社交分享优化。

**详细回答**

```ts
// app/blog/[slug]/page.tsx
export async function generateMetadata({ params }) {
  return {
    title: `Blog ${params.slug}`,
    description: "SEO 优化示例",
    openGraph: { title: "Blog OG", images: ["/og.png"] },
  };
}
```

- **App Router**：推荐用 `generateMetadata`。
- **Pages Router**：用 `next/head`。
- **附加优化**：canonical 链接、防止重复内容。

**扩展/对比**

- 面试官常问：“Next.js 为什么比纯 React SEO 好？” 👉 React 默认 CSR（HTML 空壳），Next.js SSR/SSG/ISR 输出完整 HTML。
- Next.js 内置 sitemap/robots 生成插件，CRA 没有。

---

### 25. 如何处理多语言/国际化 (i18n) SEO？ 🔥

**一句话回答**
通过 Next.js i18n 配置 + `hreflang` 标签，实现多语言页面的 SEO 优化。

**要点**

- `next.config.js` → `i18n.locales`。
- 自动生成多语言路由。
- `hreflang` 标签帮助搜索引擎识别语言版本。

**详细回答**

- 配置 `next.config.js`：

```js
i18n: {
  locales: ["en", "zh", "fr"],
  defaultLocale: "en",
}
```

- 页面路由自动支持 `/zh/blog`、`/fr/blog`。
- SEO 需要在 `<head>` 加：

```html
<link rel="alternate" href="https://example.com/zh" hreflang="zh" />
<link rel="alternate" href="https://example.com/en" hreflang="en" />
```

**扩展/对比**

- 面试官可能问：“i18n 怎么兼顾 SEO？” 👉 配置 hreflang、生成 sitemap 多语言版本。

---

# 八、安全

### 26. Next.js 如何防御 XSS？ 🔥

**一句话回答**
利用 React 默认转义 + 禁用危险 API + CSP + DOMPurify 防御 XSS。

**要点**

- JSX 默认转义。
- 避免 `dangerouslySetInnerHTML`。
- CSP 限制脚本来源。
- DOMPurify 清洗富文本。

**详细回答**

- React 默认把 `<script>` 转义成文本。
- 必须渲染富文本时：

```tsx
import DOMPurify from "dompurify";

<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(content) }} />;
```

- 配合 CSP，防止外部脚本执行。

**扩展/对比**

- 面试官可能问：“为什么 token 不放 localStorage？” 👉 localStorage 易被 XSS 窃取，应放 HttpOnly Cookie。
- Next.js 没有额外防护，但默认 RSC 降低客户端暴露面。

---

### 27. Next.js 如何防御 CSRF？ 🔥

**一句话回答**
通过 SameSite Cookie、CSRF Token、二次确认机制。

**要点**

- Cookie：`SameSite=Lax` 或 `Strict`。
- CSRF Token：表单/请求携带。
- 高危操作加二次确认。

**详细回答**

- 服务端生成 CSRF Token，前端表单/请求提交时附带。
- Cookie 设置：

```http
Set-Cookie: session=abc; HttpOnly; Secure; SameSite=Lax
```

- 登录态跨站请求不自动带 Cookie。

**扩展/对比**

- 面试官可能问：“SameSite=Strict 会有啥问题？” 👉 用户从外部站点跳转回来时可能丢登录态。
- 最佳实践：SameSite=Lax + CSRF Token。

---

### 28. 点击劫持防御 🔥

**一句话回答**
通过 `X-Frame-Options` 或 CSP 的 `frame-ancestors` 禁止页面被 iframe 嵌入。

**要点**

- `X-Frame-Options: DENY`。
- CSP：`frame-ancestors 'self'`。
- 前端可检测 `window.top !== window.self`。

**详细回答**

- **服务器头**：

```http
Content-Security-Policy: frame-ancestors 'self'
```

- **前端检测**：

```ts
if (window.top !== window.self) {
  document.body.innerHTML = "Blocked in iframe";
}
```

**扩展/对比**

- 面试官常问：“哪些网站仍允许被 iframe 嵌入？” 👉 第三方支付、嵌入式插件（需特定白名单）。

---

# 九、工程化 & 构建优化

### 29. Next.js 的构建与部署流程 🔥

**一句话回答**
Next.js 构建产物包含 `.next/` 静态资源和 serverless 函数，可部署到 Vercel、Node Server、Edge Functions。

**要点**

- 构建命令：`next build`。
- 产物：静态文件 + serverless/edge 代码。
- 部署方式：Vercel（默认）、Node.js、自托管、Docker、静态导出。

**详细回答**

- `next build` → 生成 `.next/` 目录，包含 `server/`、`static/`、`chunks/`。
- 生产启动：`next start`。
- 部署方式：

  - **Vercel**：一键部署，自动 ISR。
  - **Node.js Server**：通过 `next start` 自行部署。
  - **静态导出**：`next export`，仅支持纯 SSG。
  - **Docker**：常见于企业 CI/CD。

**扩展/对比**

- 面试官常问：“ISR 静态导出能用吗？” 👉 不能，必须用支持服务端的部署环境。
- 部署时要关注 CDN 缓存策略，避免 ISR 无效。

---

### 30. CI/CD 如何集成 Next.js？ 🔥

**一句话回答**
CI/CD 通过 GitHub Actions/GitLab CI 自动化执行构建、测试、部署。

**要点**

- Lint + Test → Build → Deploy。
- 常用命令：`npm run lint && npm run test && npm run build`。
- 部署到 Vercel/自建服务器。

**详细回答**

```yaml
# GitHub Actions 示例
name: Next.js CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: npm ci
      - run: npm run lint
      - run: npm run build
      - run: npm run test
```

- 企业内可结合 **Jenkins** 或 **GitLab CI**，再配合 Docker 镜像发布。

**扩展/对比**

- 面试官常问：“你们团队怎么保证上线质量？” 👉 CI 跑测试、lint，CD 自动回滚。
- Vercel 集成 CI/CD 最简单，Git push 即触发构建。

---

### 31. Next.js Monorepo 管理 🔥

**一句话回答**
Monorepo 管理多个应用/包，常用 pnpm workspace、Turborepo、Nx。

**要点**

- **pnpm workspace**：轻量，依赖去重。
- **Turborepo**：缓存 + 并行构建。
- **Nx**：插件丰富，适合大型企业。

**详细回答**

- Monorepo 优势：共享组件、依赖一致、统一版本管理。
- Next.js + pnpm workspace：多个应用共用依赖，`pnpm install` 节省磁盘。
- Turborepo：缓存构建结果，提高 CI 速度。

**扩展/对比**

- 面试官常问：“为什么选 pnpm？” 👉 硬链接节省磁盘 + Monorepo 支持强。
- 大厂（Google/Meta）常用 Monorepo，配合自动化构建工具。

---

### 32. 包管理器对比：npm / yarn / pnpm 🔥

**一句话回答**
npm 官方，yarn 强调一致性，pnpm 磁盘占用最小，Monorepo 最优。

**要点**

- **npm**：最通用，v5+ 有 lock 文件。
- **yarn**：更快，workspaces 友好。
- **pnpm**：内容寻址 + 硬链接，节省磁盘。

**详细回答**

- yarn v1 → 替代 npm 的性能瓶颈；yarn v2 (berry) 引入 Plug’n’Play。
- pnpm 通过全局内容寻址，多个项目可共享依赖，减少重复下载。
- Monorepo 场景，pnpm workspace 最轻量。

**扩展/对比**

- 面试官常问：“pnpm 和 yarn workspaces 哪个好？” 👉 pnpm 磁盘效率更高。
- CI/CD 场景推荐 `npm ci` / `pnpm install --frozen-lockfile` 保证一致性。

---

### 33. Node 版本管理：nvm 🔥

**一句话回答**
nvm 管理多个 Node.js 版本，保证团队/CI 环境一致。

**要点**

- `nvm use` 切换版本。
- `.nvmrc` 固定项目 Node 版本。
- 避免环境差异导致构建失败。

**详细回答**

- 老项目可能用 Node 14，新项目用 Node 18。
- 团队成员只需 `nvm use` 即可对齐版本。
- CI/CD 流水线可配置 Node 版本。

**扩展/对比**

- 面试官常问：“如何避免环境差异？” 👉 `.nvmrc` + CI 配置 Node 版本。
- Windows 用 `nvm-windows` 替代。

---

### 34. Git Flow / GitHub Flow / Trunk-based 🔥

**一句话回答**
三种分支模型分别适合不同团队规模：Git Flow（完整）、GitHub Flow（轻量）、Trunk-based（现代 DevOps）。

**要点**

- Git Flow：master、develop、feature、release、hotfix。
- GitHub Flow：main + feature + PR。
- Trunk-based：所有人直接 main，每天多次集成。

**详细回答**

- **Git Flow**：分支清晰，适合长周期项目。
- **GitHub Flow**：简洁，适合敏捷开发。
- **Trunk-based**：大厂常用，结合 Feature Flag。

**扩展/对比**

- 面试官常问：“你们团队怎么做多人协作？” 👉 小团队：GitHub Flow；大团队：Git Flow；大厂 DevOps：Trunk-based。
- Git Flow 稳健但复杂，Trunk-based 现代高效。

---
