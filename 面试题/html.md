太好了 👍
我会把 **《HTML 高频面试题 · 全展开版（2025）》** 分成 **三段**完整贴出，你复制拼接即可保存为 `html-interview-2025.md`。

---

# 📘 HTML 高频面试题 · 全展开版（2025 中国前端专用）

> 总计：26 道高频题，覆盖语义化 / 渲染性能 / 表单可访问性 / 多媒体 / 存储通信 / 安全 / 移动端
> 每道题均采用 **一句话 → 要点 → 详细回答 → 扩展/对比** 四层结构

---

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

### 13. 如何提升页面可访问性（A11y）

**一句话回答**
通过语义化标签、aria 属性、tabIndex、label 关联提升无障碍体验。

**要点**

- aria-label / aria-hidden。
- tabIndex 控制键盘焦点。
- label 与 input 绑定。

**详细回答**

- 用 `<nav>`、`<main>` 帮助读屏器识别区域。
- 用 aria-\* 描述非语义元素。
- 保证交互元素能通过键盘访问。

**扩展/对比**

- 面试官常问：aria-hidden 有什么作用？👉 隐藏元素对读屏器不可见。

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
picture/srcset 提供响应式图片，lazy 属性可延迟加载。

**要点**

- srcset：不同分辨率/尺寸。
- picture：根据媒体条件选择资源。
- loading="lazy"：懒加载。

**代码示例**

```html
<picture>
  <source media="(max-width:600px)" srcset="small.jpg" />
  <img src="large.jpg" alt="demo" loading="lazy" />
</picture>
```

**详细回答**
响应式图片能减少带宽浪费并提升清晰度。

**扩展/对比**

- 面试官常问：如何优化图片加载？👉 响应式 + 懒加载。

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

# 五、存储与通信

### 17. localStorage / sessionStorage / cookie / IndexedDB 🔥

**一句话回答**
localStorage 持久化，sessionStorage 会话级，cookie 小且随请求发送，IndexedDB 适合结构化数据。

**要点**

- localStorage：5-10MB，长期。
- sessionStorage：窗口级，关闭即失效。
- cookie：4KB，随请求发送。
- IndexedDB：大规模存储。

**详细回答**
cookie 更适合鉴权；localStorage 适合缓存配置；IndexedDB 适合离线应用。

**扩展/对比**

- token 存储安全性问题常被问：httpOnly cookie 更安全。

---

### 18. `<iframe>` 的作用与安全问题 🔥

**一句话回答**
iframe 用于嵌套页面，但可能带来性能和安全问题。

**要点**

- sandbox 属性。
- 同源策略限制。
- 性能开销大。

**详细回答**
iframe 常用于广告、第三方内容。sandbox 可限制脚本执行、表单提交。

**扩展/对比**

- 跨域通信需 postMessage。

---

### 19. postMessage 的作用

**一句话回答**
postMessage 用于跨窗口/跨 iframe 安全通信。

**要点**

- window\.postMessage。
- 指定 origin。

**详细回答**
允许不同源的文档安全交换消息，常用于 iframe 或多窗口通信。

**扩展/对比**

- React/Vue 项目中可用于微前端通信。

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
