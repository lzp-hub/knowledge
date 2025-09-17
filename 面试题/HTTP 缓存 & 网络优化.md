## 一、HTTP 缓存机制

### 1. 强缓存与协商缓存的区别 🔥

**一句话回答**
 强缓存直接使用本地缓存，协商缓存需向服务器确认资源是否更新。

------

**要点**

- **强缓存**：`Cache-Control / Expires`。
- **协商缓存**：`ETag / Last-Modified`。
- **强缓存不发请求**，协商缓存发请求但可能返回 `304 Not Modified`。

------

**详细回答**

- **强缓存**

  - 浏览器在缓存有效期内直接使用本地副本，不会发请求。

  - 响应状态码：`200 (from disk cache)` 或 `200 (from memory cache)`。

  - 示例：

    ```http
    Cache-Control: max-age=3600
    Expires: Wed, 21 Oct 2025 07:28:00 GMT
    ```

- **协商缓存**

  - 浏览器会请求服务器，带上条件头：

    - `If-None-Match`（配合 `ETag`）。
    - `If-Modified-Since`（配合 `Last-Modified`）。

  - 资源未变更时返回 `304`，否则返回新资源。

  - 示例：

    ```http
    ETag: "abc123"
    Last-Modified: Wed, 21 Oct 2025 07:28:00 GMT
    ```

------

**扩展/对比**

- 为什么优先用 `Cache-Control` 而不是 `Expires`？
   👉 `Expires` 依赖客户端时间，可能因本地时钟错误而失效；`Cache-Control` 精确到秒级，更现代。
- `ETag` vs `Last-Modified`：
  - `ETag` 更精确（字节级），但计算和存储成本更高。
  - `Last-Modified` 精度较低（秒级），且内容不变但时间更新也会触发更新。

------

**优化案例**

1. **静态资源（JS/CSS/图片）**

   - 使用强缓存 + 文件指纹（hash 命名）。

   - 示例：`app.abc123.js`。

   - 服务端配置：

     ```http
     Cache-Control: max-age=31536000, immutable
     ```

2. **HTML 页面**

   - HTML 更新频繁，避免长缓存。

   - 常见做法：

     ```http
     Cache-Control: no-cache
     ETag: "xyz789"
     ```

   - 浏览器每次请求都会询问服务端，但未变更时只返回 `304`。

3. **混合策略（推荐）**

   - 静态资源走强缓存（长缓存 + hash）。
   - 动态资源走协商缓存（`ETag/Last-Modified`）。
   - 既保证性能，又能保证内容实时更新。

---

### 2. Cache-Control 常见指令 🔥

**一句话回答**
Cache-Control 通过响应头精细控制缓存行为。

**要点**

- `max-age`：缓存秒数。
- `no-cache`：缓存但需校验。
- `no-store`：完全不缓存。
- `public/private`：是否可被共享缓存代理。

**详细回答**

```http
Cache-Control: public, max-age=31536000
```

表示资源可被所有缓存（包括 CDN）存储 1 年。

**扩展/对比**

- 面试官可能问：“ETag 和 Cache-Control 能同时用吗？” 👉 可以，强缓存过期后进入协商缓存。

---

### 3. ETag 与 Last-Modified 的区别

**一句话回答**
ETag 基于文件内容生成标识，Last-Modified 基于文件修改时间。

**要点**

- ETag：更精确，支持秒级甚至字节级。
- Last-Modified：可能因频繁写入导致不准确。

**详细回答**

- ETag 是文件的 hash 或唯一标识。
- Last-Modified 精度是秒，且某些文件系统可能频繁更新时间戳导致误判。

**扩展/对比**

- 面试官可能问：“为什么需要 ETag？” 👉 解决 Last-Modified 精度不足问题。

---

### 4. 浏览器缓存分类 🔥

**一句话回答**
浏览器缓存分为强缓存、协商缓存、Service Worker 缓存、本地缓存。

**要点**

- Memory Cache：存放于内存，刷新后清空。
- Disk Cache：持久化到磁盘。
- Service Worker：自定义缓存策略。
- 本地存储：localStorage / IndexedDB。

**详细回答**
浏览器先查内存缓存，再查磁盘缓存。
Service Worker 可拦截请求并实现离线缓存。

**扩展/对比**

- 面试官可能问：“F5 和 Ctrl+F5 刷新区别？” 👉 F5 用缓存，Ctrl+F5 强制请求服务器。

---

## 二、预加载与预取

### 5. preload、prefetch、dns-prefetch 的区别 🔥

**一句话回答**
preload 是关键资源提前加载，prefetch 是可能用到的资源提前获取，dns-prefetch 是提前解析域名。

**要点**

- preload：当前页面关键资源。
- prefetch：未来页面可能需要。
- dns-prefetch：提前做 DNS 查询。

**详细回答**

```html
<link rel="preload" href="main.js" as="script" />
<link rel="prefetch" href="next.js" />
<link rel="dns-prefetch" href="//cdn.example.com" />
```

**扩展/对比**

- 面试官可能问：“preload 和 prefetch 的优先级不同吗？” 👉 preload 高优先级，prefetch 低优先级。

---

### 6. HTTP/2 的多路复用

**一句话回答**
HTTP/2 用单连接多路复用，解决了 HTTP/1.1 队头阻塞问题。

**要点**

- 单 TCP 连接。
- 并行传输。
- 头部压缩。

**详细回答**

- HTTP/1.1 每个请求需要独立连接，队头阻塞严重。
- HTTP/2 把请求拆分成帧，在单连接里交错发送。

**扩展/对比**

- 面试官可能问：“为什么有了 HTTP/2，还要用 CDN 域名拆分？” 👉 在部分老浏览器和 HTTP/1.1 下依旧需要。

---

### 7. HTTP/3 (QUIC) 的特点

**一句话回答**
HTTP/3 基于 QUIC（UDP），支持更快的连接建立和更优的多路复用。

**要点**

- 基于 UDP。
- 0-RTT 建连。
- 无队头阻塞。

**详细回答**

- QUIC 避免了 TCP 三次握手的延迟。
- 多路复用不会因为一个流阻塞影响其他流。

**扩展/对比**

- 面试官可能问：“为什么 QUIC 能解决队头阻塞？” 👉 因为基于 UDP，每个流独立。

---

## 三、性能优化与监控

### 8. 前端性能指标（Core Web Vitals） 🔥

**一句话回答**
 Core Web Vitals 是 Google 提出的网页体验核心指标，包括 **LCP（最大内容绘制）**、**FID（首次输入延迟）**、**CLS（累计布局偏移）**。

------

**要点**

- **LCP（Largest Contentful Paint）**：衡量页面主要内容渲染速度，目标 < 2.5s
- **FID（First Input Delay）**：衡量用户首次交互响应速度，目标 < 100ms
- **CLS（Cumulative Layout Shift）**：衡量页面布局稳定性，目标 < 0.1

------

**详细回答**

- **LCP**
  - 衡量用户视窗中最大可见内容（图片、视频、标题等）的渲染完成时间。
  - **常见问题**：图片过大未压缩、阻塞式 CSS/JS、Web 字体加载慢。
  - **优化方法**：
    - 使用图片懒加载（`loading="lazy"`）、WebP/AVIF 压缩。
    - 内联关键 CSS，推迟非关键资源加载。
    - 使用 CDN 加速静态资源。
- **FID**
  - 衡量用户首次与页面交互（点击、输入等）到事件处理程序被触发的延迟。
  - **常见问题**：主线程长任务阻塞、大体积 JS Bundle。
  - **优化方法**：
    - 代码分割（Code Splitting）、懒加载非关键脚本。
    - 将长任务切分为小任务，使用 `requestIdleCallback` 或 `setTimeout`。
    - 使用 Web Worker 将复杂计算移出主线程。
- **CLS**
  - 衡量页面元素在渲染过程中是否发生意外的视觉偏移。
  - **常见问题**：图片/广告/iframe 无尺寸预留，字体闪烁（FOIT/FOUT），动态插入 DOM 未固定布局。
  - **优化方法**：
    - 所有图片/视频指定 `width` 和 `height` 或使用 CSS `aspect-ratio`。
    - 为广告位、懒加载区域预留固定尺寸占位。
    - 使用 `font-display: swap` 减少字体加载抖动。
    - 避免在已有内容上方插入新节点，尽量追加在下方。

------

**扩展/对比**

- 面试官常问：
  - “如何优化 **CLS**？” 👉 预留图片/广告位大小，避免布局突变。
  - “FID 和 TBT 的区别？” 👉 FID 是真实用户交互延迟，TBT（Total Blocking Time）是实验室指标，用于反映主线程阻塞时间。
  - “LCP 和 FMP/FCP 的区别？” 👉 FCP（首次内容绘制）只是“有东西出现”，LCP 是“主要内容出现”，更接近真实体验。

---

### 9. 如何设计前端缓存策略 🔥

**一句话回答**
静态资源用强缓存 + hash，动态数据用协商缓存 + API 缓存策略。

**要点**

- 静态资源：文件名 hash，Cache-Control: max-age。
- 动态数据：ETag / revalidate。
- CDN + Service Worker。

**详细回答**

- JS/CSS 文件：`app.abc123.js`，可长时间缓存。
- HTML 文件：短缓存或不缓存，保证更新及时。
- API 数据：结合 React Query / SWR。

**扩展/对比**

- 面试官可能问：“你们项目怎么做缓存？” 👉 结合业务场景回答（CDN + React Query）。
