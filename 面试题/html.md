## 一、语义化与结构

### 1. 什么是 HTML 语义化？为什么重要？ 🔥

**一句话回答**
语义化是用合适的标签表达内容的意义，让代码更易读、更利于 SEO 和可访问性。

**要点**

- 可读性：对人/机器都友好。
- SEO：搜索引擎更易理解结构。
- 无障碍：读屏器更好支持。

**详细回答**
HTML 语义化就是选择正确的标签表达结构和意义，例如标题用 `<h1>`\~`<h6>`，段落用 `<p>`，导航用 `<nav>`。这样不仅让开发者读得懂，搜索引擎也能更好地分析，辅助设备也能更好解析。

**扩展/对比**

- `<div>` 没有语义，语义化标签更有利于 SEO。
- React/Vue 中也要注意语义化，而不仅是 `<div>` 套娃。

---

### 2. 常见语义化标签及使用场景

**一句话回答**
HTML5 提供了 header, main, section, article, aside, footer 等语义标签，用于描述页面结构。

**要点**

- header：页眉/标题。
- nav：导航。
- main：主要内容。
- article：独立文章。
- aside：侧边栏。
- footer：页脚。

**详细回答**
这些标签取代了过去全用 `<div>` 的写法，让页面结构更清晰，利于 SEO 和可访问性。

**扩展/对比**

- 搜索引擎会识别 `<article>` 内文本权重。
- React JSX 里也能直接使用这些标签。

---

### 3. `<div>` 与 `<section>` 的区别

**一句话回答**
`<div>` 是纯容器，无语义；`<section>` 表示文档中独立的语义化区块。

**要点**

- div：布局容器。
- section：语义化区块。

**详细回答**
`<section>` 适合表示文章的章节、主题内容块；`<div>` 则仅用于无语义的分组。

**扩展/对比**

- 面试官常问：什么时候用 div？👉 当没有明确语义时才用。

---

## 二、渲染与性能

### 4. 浏览器输入 URL 到页面渲染完成的过程 🔥

**一句话回答**
网络请求 → 解析 HTML 构建 DOM → 构建 CSSOM → Render Tree → Layout → Paint → Composite。

**要点**

- HTML → DOM。
- CSS → CSSOM。
- DOM + CSSOM → Render Tree。
- Layout：计算位置。
- Paint：绘制像素。
- Composite：合成层。

**详细回答**
浏览器拿到 HTML 后解析成 DOM，同时下载/解析 CSS 构建 CSSOM。二者合并成 Render Tree，计算布局(Layout)，再绘制(Paint)，最后 GPU 合成(Composite)。

**扩展/对比**

- JS 会阻塞 DOM 构建，CSS 会阻塞 Render Tree。
- 优化点：懒加载、减少阻塞资源、使用 defer/async。

---

### 5. 回流（Reflow）与重绘（Repaint） 🔥

**一句话回答**
回流是重新计算布局，重绘是样式改变但不影响布局。

**要点**

- 回流比重绘开销大。
- 改变几何属性触发回流。
- 改变颜色触发重绘。

**详细回答**

- 回流：DOM 结构/尺寸/布局变化触发，需重新计算位置。
- 重绘：外观变化但不改变几何（如背景色）。

**扩展/对比**

- 优化：合并多次 DOM 操作，使用 `classList` 批量修改，使用 `requestAnimationFrame`。

---

### 6. `<script>` 的 async 与 defer 🔥

**一句话回答**
async 并行下载，下载完立即执行；defer 并行下载，等 DOM 解析完再按顺序执行。

**要点**

- async：执行顺序不保证。
- defer：保持加载顺序。

**详细回答**

- async 适合独立脚本（如统计）。
- defer 适合依赖 DOM 的脚本。

**扩展/对比**

- 默认 `<script>` 会阻塞 HTML 解析。
- 面试官常考对比 async vs defer。

---

### 7. 为什么 CSS 在 head，JS 在 body 底部？

**一句话回答**
CSS 放在 head 保证先渲染，JS 放底部避免阻塞 DOM 解析。

**要点**

- CSS 阻塞渲染。
- JS 阻塞 DOM 解析。

**详细回答**
浏览器遇到 JS 会暂停 DOM 解析执行脚本，如果 CSS 未加载完，JS 可能访问不到正确的样式信息，所以 CSS 应在 head，JS 放底部或用 defer/async。

**扩展/对比**

- 现代项目打包工具会自动优化资源加载顺序。

---

### 8. DOCTYPE 的作用 & HTML5 vs HTML4

**一句话回答**
DOCTYPE 用于声明文档类型和标准模式，HTML5 简化为 `<!DOCTYPE html>`。

**要点**

- 触发标准模式 vs 怪异模式。
- HTML4：DTD（过渡型/严格型）。
- HTML5：统一简化声明。

**详细回答**

- 浏览器解析 HTML 时，会根据 DOCTYPE 进入标准模式或怪异模式。
- 怪异模式下，盒模型和部分兼容性处理不同。
- HTML5 不再依赖 DTD，只需 `<!DOCTYPE html>` 即可。

**扩展/对比**

- 面试官常问：DOCTYPE 写不写有区别？👉 写，避免浏览器进入怪异模式。

---

### 9. 浏览器容错机制（错误标签处理）

**一句话回答**
浏览器遇到错误标签会自动修复 DOM 树，以保证页面能渲染。

**要点**

- 容错性强。
- 错误标签会被忽略或重排。

**详细回答**

- 比如 `<table>` 内写了 `<div>`，浏览器会自动调整到 table 外。
- 未闭合标签浏览器会自动闭合。

**扩展/对比**

- 这就是为什么错误写法也能“正常显示”，但并不推荐。
- 面试官可能考“浏览器宽容性 vs 标准写法”。

---

# 三、表单与可访问性

### 10. 受控 vs 非受控组件（HTML 原生）

**一句话回答**
受控：值由 JS 控制；非受控：值由 DOM 自行管理。

**要点**

- 受控：`value + oninput`。
- 非受控：`defaultValue`。

**详细回答**
受控组件能更好地统一管理表单数据，便于验证和联动。非受控更简单，数据需要手动获取。

**扩展/对比**

- React 里也有同样概念，受控更常见。

---

### 11. `<label>` 与 `<input>` 的关系

**一句话回答**
label 与 input 绑定后，点击 label 会聚焦 input。

**要点**

- for 属性。
- label 包裹 input。

**详细回答**
label 提升可访问性，让用户更容易操作表单。

**扩展/对比**

- 对无障碍支持非常重要。

---

### 12. HTML5 新增表单属性

**一句话回答**
HTML5 为表单增加了 autocomplete、required、pattern、placeholder 等属性，增强交互与校验。

**要点**

- required：必填。
- pattern：正则校验。
- autocomplete：自动填充。
- input type：email、url、number。

**详细回答**
HTML5 表单大幅增强：

- `<input type="email">` 自动校验邮箱格式。
- `required` 属性能阻止提交。
- `pattern` 提供正则校验。

**扩展/对比**

- React 项目里常用受控校验代替，但 HTML5 属性能做兜底校验。

---

### 13. 如何提升页面可访问性（A11y） 🔥

**一句话回答**
通过语义化标签、aria 属性、tabIndex、label 关联提升无障碍体验。

**要点**

- 使用语义化标签：`<nav>`、`<main>`、`<header>`、`<footer>`。
- ARIA 属性：`aria-label`、`aria-hidden`、`role`。
- 键盘可达性：`tabIndex`、`Enter`/`Space` 触发事件。
- 表单可用性：`<label for="id">` 与 `<input id="id">` 绑定。

**详细回答**

- **语义化结构**：使用正确的 HTML 标签帮助读屏器识别页面区域，例如 `<main>` 告诉用户“这是主要内容”。
- **ARIA 属性**：对非语义元素（如自定义按钮 `<div role="button">`）补充信息，提升可读性。
  - `aria-label="关闭弹窗"` 给图标按钮增加描述。
  - `aria-hidden="true"` 隐藏对读屏器无意义的装饰性元素。
- **键盘无障碍**：所有交互元素（按钮、链接、表单）应能通过 `Tab` 导航，避免 `onclick` 只绑定鼠标事件。
- **表单增强**：确保输入框有 `<label>` 绑定或 `aria-label`，帮助屏幕阅读器播报。

**扩展/对比**

- **常见问题**：面试官可能会问
  - “`aria-hidden` 有什么作用？” 👉 它会让元素对读屏器不可见，但视觉上依然存在。
  - “为什么要用语义化标签而不是一堆 `<div>`？” 👉 因为 `<div>` 对读屏器无意义，而 `<nav>`、`<main>` 能提供结构信息。

**优化案例**

- **按钮可访问性**

  ```html
  <!-- 错误：div 没有语义 -->
  <div onclick="submitForm()">提交</div>

  <!-- 正确：button 自带语义和键盘支持 -->
  <button type="submit">提交</button>
  ```

- **图标按钮描述**

  ```html
  <!-- 错误：屏幕阅读器只会播报“按钮” -->
  <button><i class="icon-close"></i></button>

  <!-- 正确：aria-label 增加可读性 -->
  <button aria-label="关闭弹窗">
    <i class="icon-close" aria-hidden="true"></i>
  </button>
  ```

- **表单关联**

  ```html
  <!-- 推荐 label+for 绑定 -->
  <label for="email">邮箱</label>
  <input id="email" type="email" required />
  ```

**补充**

A11y（ Accessibility 这个单词比较长，中间有 11 个字母）指的是让网页或应用能够被 **所有人** 使用，包括：

- 使用读屏器的视障用户
- 不能用鼠标、依赖键盘或语音操作的用户
- 有色盲、色弱的人群
- 使用不同设备（手机、平板、屏幕阅读器等）的用户

ARIA 属性是 **标准化的 HTML 扩展属性**，用来提升 **无障碍（Accessibility, A11y）**

- 所有 ARIA 属性都以 `aria-` 前缀开头，例如：
  - `aria-label`（为元素提供无障碍名称）
  - `aria-hidden`（告诉读屏器忽略元素）
  - `aria-expanded`（表示折叠/展开状态）
  - `aria-checked`（表示勾选状态）

---

# 四、多媒体与新特性

### 14. `<canvas>` 与 `<svg>` 区别 🔥

**一句话回答**
canvas 是像素绘制，svg 是矢量图形。

**要点**

- canvas：位图，适合高频绘制。
- svg：矢量，适合图标/图表。

**详细回答**
canvas 基于像素，性能好但放大失真；svg 基于 XML，节点可操作，放大不失真，但复杂图形性能差。

**扩展/对比**

- 常见面试题：画 1000 个点用哪个？👉 canvas。
- 图标用 svg 更合适。

---

### 15. 图片优化（picture / srcset / lazy） 🔥

**一句话回答**
`<picture>` / `srcset` 用于响应式图片，`loading="lazy"` 可以延迟加载，减少带宽和首屏压力。

---

**要点**

- **srcset**：同一图片资源的多版本，浏览器根据屏幕分辨率 / 设备宽度选择合适的。
- **sizes**：配合 srcset 告诉浏览器“在不同视口下应该用多大尺寸的图片”。
- **picture**：可根据媒体条件（屏幕宽度、格式支持度）选择不同资源。
- **lazy loading**：`loading="lazy"` 延迟加载非首屏图片，减少阻塞。
- **现代优化**：WebP/AVIF 替代 JPEG/PNG，进一步减少体积。

---

**详细回答**

1. **srcset + sizes 响应式加载**

```html
<img
  src="default.jpg"
  srcset="small.jpg 480w, medium.jpg 800w, large.jpg 1200w"
  sizes="(max-width: 600px) 480px, (max-width: 1000px) 800px, 1200px"
  alt="demo"
/>
```

浏览器会根据设备宽度，自动选择合适的资源，避免小屏加载大图。

---

1. **picture 更灵活的场景**

```html
<picture>
  <!-- 优先使用 AVIF 格式 -->
  <source type="image/avif" srcset="image.avif" />
  <!-- 退化到 WebP -->
  <source type="image/webp" srcset="image.webp" />
  <!-- 再退化到 JPEG -->
  <img src="image.jpg" alt="demo" loading="lazy" />
</picture>
```

- 好处：按格式优先级加载最佳图片（现代浏览器用 AVIF，老浏览器用 JPG）。
- 实用场景：电商商品图、门户 banner。

---

1. **懒加载**

```html
<img src="product.jpg" alt="商品" loading="lazy" />
```

- Chrome/Firefox/Safari 已原生支持。
- 对首屏外图片非常有效，减少初始流量。

---

**扩展/对比**

- 面试官可能会问：
  - **“如何优化电商网站的商品图片加载？”**
    👉 使用 `srcset + lazy`，首屏用低清晰度预览图（LQIP/Blur-up），滚动到可视区域时加载高清图。
  - **“CDN 图片优化能做什么？”**
    👉 动态裁剪、格式转换（自动返回 WebP/AVIF）、加缓存。

---

**优化案例**

1. **低清晰度占位图（LQIP/Blur-up）**
   - 在图片还没加载完之前先显示一张模糊小图，避免白屏抖动。
   - 常见于 Next.js 的 `<Image>` 组件 (`placeholder="blur"`)。
2. **骨架屏/渐进加载**
   - 给大图留出占位框架（防止 CLS），加载完成后替换。
3. **压缩与格式**
   - 使用 `imagemin` 或 CDN 压缩。
   - WebP/AVIF 体积小，清晰度高，推荐优先加载。
4. **缓存与 CDN**
   - 静态资源配置长缓存（如 `Cache-Control: max-age=31536000`）。
   - 图片分发走 CDN，降低延迟。

---

👉 总结：
**图片优化就是三个层面：**

- **加载策略**（响应式 + 懒加载 + 低清晰度占位）。
- **格式优化**（WebP/AVIF）。
- **传输优化**（CDN 缓存）。

---

### 16. Web Components 基础

**一句话回答**
Web Components 提供了封装与复用原生组件的标准，包括 Shadow DOM、Custom Elements 和 Template。

**要点**

- Custom Elements：自定义标签。
- Shadow DOM：样式隔离。
- Template：可复用结构。

**详细回答**

- Shadow DOM 保证组件内部样式不会影响外部。
- Custom Elements 定义新标签，如 `<user-card>`。
- Template 提供结构，可动态克隆。

**扩展/对比**

- React/Vue 是框架级解决方案，Web Components 是浏览器标准。
- 微前端场景下可能结合使用。

---

### 17. localStorage / sessionStorage / cookie / IndexedDB 🔥

**一句话回答**
localStorage 持久化，sessionStorage 会话级，cookie 小且随请求发送，IndexedDB 适合结构化数据。

**要点**

- **localStorage**：容量 5–10MB，长期存储，除非手动清理。
- **sessionStorage**：作用于当前标签页，会话结束即清空。
- **cookie**：容量约 4KB，随每次请求携带，常用于会话管理。
- **IndexedDB**：浏览器原生数据库，支持大规模结构化存储。

**详细回答**

- cookie 更适合鉴权（尤其是 httpOnly + Secure 的 session cookie）。
- localStorage 常用于保存用户偏好、前端缓存。
- sessionStorage 适合存临时状态，如表单填写进度。
- IndexedDB 能存储离线应用数据（如 PWA、文件缓存）。

**扩展/对比**

- 面试官常问：**“token 放 localStorage 还是 cookie？”**
  👉 httpOnly cookie 更安全，能防御 XSS 盗取。

---

### 18. `<iframe>` 的作用与安全问题 🔥

**一句话回答**
iframe 用于嵌套页面，但可能带来性能和安全风险。

**要点**

- **sandbox 属性**：限制脚本、表单、插件。
- **同源策略**：限制跨域访问。
- **性能问题**：加载慢、阻塞渲染。

**详细回答**

- iframe 常用于广告、第三方组件、支付页面。
- sandbox 可配置：`sandbox="allow-scripts allow-same-origin"` 等。
- iframe 跨域通信依赖 `postMessage`。

**扩展/对比**

- 现代微前端方案（qiankun、Module Federation）常用 iframe/postMessage 实现隔离与通信。

---

### 19. postMessage 的作用 🔥

**一句话回答**
postMessage 用于跨窗口/跨 iframe 安全通信。

**要点**

- `window.postMessage(message, targetOrigin)`。
- 必须指定 `origin`，避免被恶意站点接收。
- 监听：`window.addEventListener("message", handler)`。

**详细回答**
允许不同源的页面安全交换数据，绕过同源策略。
典型应用：

- 主页面与 iframe 交互（如支付页面、第三方登录）。
- 多标签页共享状态。
- 微前端框架内部通信。

**扩展/对比**

- 面试官可能问：**“如何实现跨域 iframe 通信？”**
  👉 答：postMessage。

---

# 六、安全

### 20. 什么是 XSS？如何防护？ 🔥

**一句话回答**
XSS 是恶意脚本注入攻击，防护手段是转义/净化/限制输入。

**要点**

- 转义输出。
- DOMPurify 净化。
- CSP 限制内联脚本。

**详细回答**
XSS 类型：反射型、存储型、DOM 型。防护措施：转义、净化、CSP。

**扩展/对比**

- React JSX 默认会转义插值。

---

### 21. 什么是 CSRF？如何防护？ 🔥

**一句话回答**
CSRF 是跨站请求伪造攻击，防护手段是 token 验证和 SameSite Cookie。

**要点**

- 验证 token。
- SameSite。
- Referer 检查。

**详细回答**
攻击者诱导用户点击恶意链接，伪造请求。常见防护：CSRF token、SameSite Cookie。

**扩展/对比**

- 大厂项目常结合双重验证：token + SameSite。

---

### 22. CSP（内容安全策略）

**一句话回答**
CSP 通过限制资源来源防止 XSS 注入。

**要点**

- 限制 script/img/style 来源。
- 仅白名单可执行。

**详细回答**
CSP 是浏览器安全策略，常配置在响应头 `Content-Security-Policy`。

**扩展/对比**

- 面试官常问：如何在前端层面防御 XSS？答：DOMPurify + CSP。

---

# 七、移动端与适配

### 23. `<meta viewport>` 🔥

**一句话回答**
viewport 控制页面在移动端的缩放与宽度适配。

**要点**

- width=device-width。
- initial-scale。

**代码示例**

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

**详细回答**
保证页面宽度等于设备宽度，避免缩放问题。

**扩展/对比**

- 不设置会导致移动端缩小显示。

---

### 24. 响应式图片 🔥

**一句话回答**
用 `<picture>` 和 `srcset` 根据设备和分辨率加载不同资源。

**要点**

- srcset：多分辨率。
- picture：条件加载。

**详细回答**
响应式图片能减少带宽浪费并提升清晰度。

**扩展/对比**

- React/Vue 项目可结合图片优化服务。

---

### 25. 移动端点击 300ms 延迟

**一句话回答**
300ms 延迟来自早期双击缩放逻辑，可用 viewport 或 FastClick 移除。

**要点**

- 原因：等待区分单击/双击。
- 解决：viewport 禁止缩放。

**详细回答**
现代浏览器支持 `meta viewport`，设置 `width=device-width` + 禁用缩放即可消除延迟。

**扩展/对比**

- 移动端框架已内置优化。

---

# 八、性能优化

### 26. 关键渲染路径（CRP）优化 & 预加载/预获取 🔥

**一句话回答**
关键渲染路径是浏览器从 HTML/CSS/JS 到首屏渲染的过程，优化方式包括减少阻塞资源和使用 preload/prefetch。

**要点**

- preload：当前页面关键资源提前加载。
- prefetch：未来页面资源提前加载。
- 减少阻塞：CSS 放 head，JS defer/async。

**详细回答**

- 优化首屏需减少关键资源大小和数量。
- `<link rel="preload">`：高优先级加载关键字体/脚本。
- `<link rel="prefetch">`：后台低优先级加载未来页面资源。

**扩展/对比**

- 面试官常问：preload 和 prefetch 区别？👉 preload 是现在用，prefetch 是未来用。

---
