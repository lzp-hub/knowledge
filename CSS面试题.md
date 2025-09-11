# CSS 面试题

## 目录

- [现代 CSS 特性](#现代-css-特性)
- [布局系统](#布局系统)
- [动画与过渡](#动画与过渡)
- [性能优化](#性能优化)
- [响应式设计](#响应式设计)
- [CSS 工程化](#css-工程化)

---

## 现代 CSS 特性

### 1. CSS Grid 和 Flexbox 的区别和使用场景？

**核心区别对比：**

| 特性       | CSS Grid           | Flexbox             |
| ---------- | ------------------ | ------------------- |
| 维度       | 二维布局（行+列）  | 一维布局（行或列）  |
| 容器控制   | 直接控制子元素位置 | 通过主轴/交叉轴控制 |
| 适用场景   | 复杂页面布局       | 组件内部布局        |
| 浏览器支持 | 较新               | 更广泛              |

**Grid 布局示例：**

```css
/* 网格容器 */
.grid-container {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "header header header header header header header header header header header header"
    "sidebar main main main main main main main main main main main"
    "footer footer footer footer footer footer footer footer footer footer footer footer";
  gap: 20px;
  min-height: 100vh;
}

.header {
  grid-area: header;
}
.sidebar {
  grid-area: sidebar;
}
.main {
  grid-area: main;
}
.footer {
  grid-area: footer;
}

/* 响应式网格 */
.responsive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
}

/* 复杂网格布局 */
.complex-grid {
  display: grid;
  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: 100px 1fr 100px;
  grid-template-areas:
    "nav nav nav"
    "sidebar content aside"
    "footer footer footer";
}

/* 网格线命名 */
.named-grid {
  display: grid;
  grid-template-columns: [sidebar-start] 200px [sidebar-end content-start] 1fr [content-end];
  grid-template-rows: [header-start] 80px [header-end main-start] 1fr [main-end];
}

/* 子元素定位 */
.grid-item {
  grid-column: content-start / content-end;
  grid-row: main-start / main-end;
}
```

**Flexbox 布局示例：**

```css
/* 基础 Flexbox */
.flex-container {
  display: flex;
  flex-direction: row; /* row | column | row-reverse | column-reverse */
  justify-content: center; /* 主轴对齐 */
  align-items: center; /* 交叉轴对齐 */
  flex-wrap: wrap; /* 换行 */
  gap: 20px;
}

/* 子元素控制 */
.flex-item {
  flex: 1; /* flex-grow flex-shrink flex-basis */
  flex-basis: 200px;
  align-self: stretch; /* 单独控制交叉轴对齐 */
}

/* 常见 Flexbox 模式 */
/* 1. 居中布局 */
.center-layout {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

/* 2. 等分布局 */
.equal-layout {
  display: flex;
}

.equal-layout > * {
  flex: 1;
}

/* 3. 固定+自适应 */
.fixed-flexible {
  display: flex;
}

.fixed-flexible .fixed {
  flex: 0 0 200px; /* 固定宽度 */
}

.fixed-flexible .flexible {
  flex: 1; /* 自适应 */
}

/* 4. 圣杯布局 */
.holy-grail {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.holy-grail .content {
  display: flex;
  flex: 1;
}

.holy-grail .main {
  flex: 1;
}

.holy-grail .sidebar {
  flex: 0 0 200px;
}
```

### 2. CSS 自定义属性（CSS Variables）的使用？

**CSS 变量基础：**

```css
:root {
  /* 全局变量 */
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --success-color: #28a745;
  --danger-color: #dc3545;
  --warning-color: #ffc107;
  --info-color: #17a2b8;

  /* 字体变量 */
  --font-family-base: "Inter", -apple-system, BlinkMacSystemFont, sans-serif;
  --font-size-base: 16px;
  --font-size-lg: 18px;
  --font-size-sm: 14px;

  /* 间距变量 */
  --spacing-xs: 4px;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  --spacing-xl: 32px;

  /* 阴影变量 */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);

  /* 边框圆角 */
  --border-radius-sm: 4px;
  --border-radius-md: 8px;
  --border-radius-lg: 12px;

  /* 过渡时间 */
  --transition-fast: 0.15s ease;
  --transition-normal: 0.3s ease;
  --transition-slow: 0.5s ease;
}

/* 组件级变量 */
.card {
  --card-bg: #ffffff;
  --card-border: #e9ecef;
  --card-shadow: var(--shadow-md);

  background: var(--card-bg);
  border: 1px solid var(--card-border);
  box-shadow: var(--card-shadow);
  border-radius: var(--border-radius-md);
  padding: var(--spacing-lg);
}

/* 主题切换 */
[data-theme="dark"] {
  --primary-color: #0d6efd;
  --card-bg: #212529;
  --card-border: #495057;
  --text-color: #ffffff;
}

/* 响应式变量 */
@media (max-width: 768px) {
  :root {
    --font-size-base: 14px;
    --spacing-md: 12px;
    --spacing-lg: 16px;
  }
}

/* 动态变量 */
.dynamic-component {
  --component-width: 200px;
  --component-height: 100px;

  width: var(--component-width);
  height: var(--component-height);
  transition: var(--transition-normal);
}

.dynamic-component:hover {
  --component-width: 250px;
  --component-height: 120px;
}
```

**JavaScript 操作 CSS 变量：**

```javascript
// 获取变量值
const root = document.documentElement;
const primaryColor = getComputedStyle(root).getPropertyValue("--primary-color");

// 设置变量值
root.style.setProperty("--primary-color", "#ff0000");

// 动态主题切换
function setTheme(theme) {
  const themes = {
    light: {
      "--bg-color": "#ffffff",
      "--text-color": "#000000",
      "--primary-color": "#007bff",
    },
    dark: {
      "--bg-color": "#000000",
      "--text-color": "#ffffff",
      "--primary-color": "#0d6efd",
    },
  };

  const themeVars = themes[theme];
  Object.entries(themeVars).forEach(([property, value]) => {
    root.style.setProperty(property, value);
  });
}

// 响应式变量
function updateResponsiveVars() {
  const width = window.innerWidth;
  const fontSize = width < 768 ? "14px" : "16px";
  root.style.setProperty("--font-size-base", fontSize);
}

window.addEventListener("resize", updateResponsiveVars);
```

### 3. CSS Container Queries 的使用？

**Container Queries 基础：**

```css
/* 定义容器上下文 */
.card-container {
  container-type: inline-size;
  container-name: card;
}

/* 基于容器宽度的样式 */
@container card (min-width: 300px) {
  .card {
    display: flex;
    flex-direction: row;
  }

  .card-image {
    width: 120px;
    height: 120px;
  }

  .card-content {
    flex: 1;
    padding-left: 16px;
  }
}

@container card (max-width: 299px) {
  .card {
    display: flex;
    flex-direction: column;
  }

  .card-image {
    width: 100%;
    height: 200px;
  }

  .card-content {
    padding-top: 16px;
  }
}

/* 复杂容器查询 */
.product-grid {
  container-type: inline-size;
  container-name: product-grid;
}

@container product-grid (min-width: 600px) {
  .product-card {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }
}

@container product-grid (min-width: 900px) {
  .product-card {
    grid-template-columns: 1fr 1fr 1fr;
  }
}

/* 容器查询与媒体查询结合 */
@media (min-width: 768px) {
  .responsive-container {
    container-type: inline-size;
  }

  @container (min-width: 400px) {
    .component {
      font-size: 18px;
    }
  }
}
```

---

## 布局系统

### 4. 现代 CSS 布局技术对比？

**布局技术选择指南：**

```css
/* 1. Flexbox - 一维布局 */
.flex-layout {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

/* 2. Grid - 二维布局 */
.grid-layout {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
}

/* 3. Subgrid - 嵌套网格对齐 */
.parent-grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 20px;
}

.child-grid {
  display: grid;
  grid-template-columns: subgrid;
  grid-column: 1 / -1;
}

/* 4. CSS Logical Properties - 国际化友好 */
.logical-layout {
  margin-inline-start: 20px; /* 左边距（LTR）或右边距（RTL） */
  margin-inline-end: 20px; /* 右边距（LTR）或左边距（RTL） */
  margin-block-start: 10px; /* 上边距 */
  margin-block-end: 10px; /* 下边距 */

  border-inline-start: 2px solid #000;
  padding-inline: 16px;
  padding-block: 8px;
}

/* 5. CSS Masonry - 瀑布流布局 */
.masonry-layout {
  display: masonry;
  masonry-auto-flow: next;
  masonry-columns: 3;
}

/* 6. CSS Anchor Positioning - 锚点定位 */
.anchor-container {
  position: relative;
}

.anchor-element {
  position: absolute;
  top: anchor(bottom);
  left: anchor(center);
  transform: translateX(-50%);
}
```

### 5. 如何实现复杂的响应式布局？

**现代响应式布局策略：**

```css
/* 1. 容器查询 + 媒体查询 */
.responsive-component {
  container-type: inline-size;
}

@container (min-width: 300px) {
  .component {
    display: flex;
    flex-direction: row;
  }
}

@media (max-width: 768px) {
  .responsive-component {
    container-type: normal;
  }
}

/* 2. CSS Grid 响应式 */
.responsive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: clamp(16px, 4vw, 32px);
}

/* 3. 流体排版 */
.fluid-typography {
  font-size: clamp(16px, 4vw, 24px);
  line-height: clamp(1.4, 1.2 + 0.5vw, 1.6);
}

/* 4. 响应式间距 */
.responsive-spacing {
  padding: clamp(16px, 5vw, 48px);
  margin: clamp(8px, 2vw, 24px);
}

/* 5. 复杂响应式布局 */
.complex-responsive {
  display: grid;
  grid-template-areas:
    "header"
    "nav"
    "main"
    "sidebar"
    "footer";
  grid-template-columns: 1fr;
  gap: 20px;
}

@media (min-width: 768px) {
  .complex-responsive {
    grid-template-areas:
      "header header"
      "nav main"
      "sidebar main"
      "footer footer";
    grid-template-columns: 200px 1fr;
  }
}

@media (min-width: 1024px) {
  .complex-responsive {
    grid-template-areas:
      "header header header"
      "nav main sidebar"
      "footer footer footer";
    grid-template-columns: 200px 1fr 250px;
  }
}

.header {
  grid-area: header;
}
.nav {
  grid-area: nav;
}
.main {
  grid-area: main;
}
.sidebar {
  grid-area: sidebar;
}
.footer {
  grid-area: footer;
}
```

---

## 动画与过渡

### 6. CSS 动画性能优化策略？

**高性能动画实现：**

```css
/* 1. 使用 transform 和 opacity */
.optimized-animation {
  transform: translateX(0);
  opacity: 1;
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.optimized-animation:hover {
  transform: translateX(10px);
  opacity: 0.8;
}

/* 2. 避免触发重排的属性 */
.bad-animation {
  /* 避免这些属性 */
  transition: width 0.3s ease; /* 触发重排 */
  transition: height 0.3s ease; /* 触发重排 */
  transition: margin 0.3s ease; /* 触发重排 */
}

.good-animation {
  /* 使用这些属性 */
  transition: transform 0.3s ease; /* 只触发重绘 */
  transition: opacity 0.3s ease; /* 只触发重绘 */
}

/* 3. 使用 will-change 提示浏览器 */
.animation-element {
  will-change: transform, opacity;
}

.animation-element.animation-complete {
  will-change: auto; /* 动画结束后移除 */
}

/* 4. 硬件加速 */
.hardware-accelerated {
  transform: translateZ(0); /* 强制硬件加速 */
  backface-visibility: hidden;
  perspective: 1000px;
}

/* 5. 关键帧动画优化 */
@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

.slide-in {
  animation: slideIn 0.5s ease-out;
}

/* 6. 动画性能监控 */
.performance-monitor {
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

/* 7. 减少动画复杂度 */
.complex-animation {
  /* 避免复杂的 box-shadow 动画 */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s ease;
}

.complex-animation:hover {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}
```

### 7. 现代 CSS 动画技术？

**高级动画技术：**

```css
/* 1. CSS 自定义属性动画 */
@property --progress {
  syntax: "<number>";
  initial-value: 0;
  inherits: false;
}

.progress-bar {
  --progress: 0;
  background: linear-gradient(
    90deg,
    #007bff var(--progress),
    #e9ecef var(--progress)
  );
  transition: --progress 1s ease;
}

.progress-bar.animate {
  --progress: 75%;
}

/* 2. 视差滚动效果 */
.parallax-container {
  height: 100vh;
  overflow-x: hidden;
  overflow-y: auto;
  perspective: 1px;
}

.parallax-element {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  transform: translateZ(-1px) scale(2);
}

/* 3. 滚动驱动动画 */
.scroll-driven {
  animation: slideIn linear;
  animation-timeline: scroll();
  animation-range: entry 0% entry 100%;
}

@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

/* 4. 3D 变换动画 */
.card-3d {
  transform-style: preserve-3d;
  transition: transform 0.6s;
}

.card-3d:hover {
  transform: rotateY(180deg);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}

.card-back {
  transform: rotateY(180deg);
}

/* 5. 弹性动画 */
.elastic-animation {
  animation: elastic 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

@keyframes elastic {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}

/* 6. 粒子效果 */
.particle {
  position: absolute;
  width: 4px;
  height: 4px;
  background: #007bff;
  border-radius: 50%;
  animation: particle-float 3s infinite ease-in-out;
}

@keyframes particle-float {
  0%,
  100% {
    transform: translateY(0) rotate(0deg);
    opacity: 1;
  }
  50% {
    transform: translateY(-20px) rotate(180deg);
    opacity: 0.5;
  }
}
```

---

## 性能优化

### 8. CSS 性能优化最佳实践？

**核心优化策略：**

```css
/* 1. 选择器优化 */
/* 避免深层嵌套 */
.bad-selector .very .deep .nested .selector {
  color: red;
}

/* 使用类选择器 */
.good-selector {
  color: red;
}

/* 2. 减少重排重绘 */
.optimized-element {
  /* 批量修改样式 */
  transform: translateX(0) translateY(0) scale(1);
  opacity: 1;
  transition: transform 0.3s ease, opacity 0.3s ease;
}

/* 3. 使用 CSS 变量减少重复 */
:root {
  --primary-color: #007bff;
  --border-radius: 4px;
  --spacing: 16px;
}

.button {
  background: var(--primary-color);
  border-radius: var(--border-radius);
  padding: var(--spacing);
}

/* 4. 关键 CSS 内联 */
.critical-css {
  /* 首屏关键样式 */
  display: block;
  width: 100%;
  height: 100vh;
}

/* 5. 懒加载非关键 CSS */
.non-critical {
  /* 非关键样式延迟加载 */
}

/* 6. 使用 contain 属性 */
.contained-element {
  contain: layout style paint;
}

/* 7. 优化字体加载 */
@font-face {
  font-family: "CustomFont";
  src: url("font.woff2") format("woff2");
  font-display: swap; /* 字体交换策略 */
}

/* 8. 图片优化 */
.optimized-image {
  image-rendering: -webkit-optimize-contrast;
  image-rendering: crisp-edges;
}

/* 9. 减少 CSS 文件大小 */
/* 使用工具压缩和优化 */
.compressed-css {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  vertical-align: baseline;
}
```

### 9. 现代 CSS 加载策略？

**CSS 加载优化：**

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- 1. 关键 CSS 内联 -->
    <style>
      /* 首屏关键样式 */
      .header {
        position: fixed;
        top: 0;
        width: 100%;
      }
      .hero {
        height: 100vh;
        background: #f0f0f0;
      }
    </style>

    <!-- 2. 预加载关键 CSS -->
    <link
      rel="preload"
      href="critical.css"
      as="style"
      onload="this.onload=null;this.rel='stylesheet'"
    />
    <noscript><link rel="stylesheet" href="critical.css" /></noscript>

    <!-- 3. 非关键 CSS 延迟加载 -->
    <link
      rel="preload"
      href="non-critical.css"
      as="style"
      onload="this.onload=null;this.rel='stylesheet'"
    />
    <noscript><link rel="stylesheet" href="non-critical.css" /></noscript>

    <!-- 4. 字体优化 -->
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap"
      rel="stylesheet"
    />
  </head>
  <body>
    <!-- 内容 -->

    <!-- 5. 动态加载 CSS -->
    <script>
      // 条件加载 CSS
      if (window.innerWidth > 768) {
        const link = document.createElement("link");
        link.rel = "stylesheet";
        link.href = "desktop.css";
        document.head.appendChild(link);
      }

      // 用户交互后加载
      document.addEventListener(
        "click",
        function () {
          if (!document.querySelector('link[href="interactive.css"]')) {
            const link = document.createElement("link");
            link.rel = "stylesheet";
            link.href = "interactive.css";
            document.head.appendChild(link);
          }
        },
        { once: true }
      );
    </script>
  </body>
</html>
```

**CSS 模块化加载：**

```css
/* 1. CSS 模块化 */
/* components/button.css */
.button {
  display: inline-block;
  padding: 12px 24px;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

/* 2. 主题系统 */
/* themes/light.css */
:root {
  --bg-color: #ffffff;
  --text-color: #000000;
  --primary-color: #007bff;
}

/* themes/dark.css */
[data-theme="dark"] {
  --bg-color: #000000;
  --text-color: #ffffff;
  --primary-color: #0d6efd;
}

/* 3. 响应式 CSS */
/* responsive/mobile.css */
@media (max-width: 768px) {
  .container {
    padding: 16px;
  }
}

/* responsive/desktop.css */
@media (min-width: 769px) {
  .container {
    padding: 32px;
  }
}
```

---

## 响应式设计

### 10. 现代响应式设计策略？

**响应式设计最佳实践：**

```css
/* 1. 移动优先设计 */
.mobile-first {
  /* 移动端样式 */
  font-size: 14px;
  padding: 16px;
}

@media (min-width: 768px) {
  .mobile-first {
    /* 平板样式 */
    font-size: 16px;
    padding: 24px;
  }
}

@media (min-width: 1024px) {
  .mobile-first {
    /* 桌面样式 */
    font-size: 18px;
    padding: 32px;
  }
}

/* 2. 流体网格 */
.fluid-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: clamp(16px, 4vw, 32px);
}

/* 3. 弹性图片 */
.responsive-image {
  max-width: 100%;
  height: auto;
  object-fit: cover;
}

/* 4. 响应式排版 */
.responsive-typography {
  font-size: clamp(16px, 4vw, 24px);
  line-height: clamp(1.4, 1.2 + 0.5vw, 1.6);
}

/* 5. 断点系统 */
:root {
  --breakpoint-sm: 576px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 992px;
  --breakpoint-xl: 1200px;
}

/* 6. 容器查询响应式 */
.responsive-component {
  container-type: inline-size;
}

@container (min-width: 300px) {
  .component {
    display: flex;
    flex-direction: row;
  }
}

@container (max-width: 299px) {
  .component {
    display: flex;
    flex-direction: column;
  }
}

/* 7. 响应式间距 */
.responsive-spacing {
  padding: clamp(16px, 5vw, 48px);
  margin: clamp(8px, 2vw, 24px);
}

/* 8. 响应式导航 */
.nav-mobile {
  display: block;
}

.nav-desktop {
  display: none;
}

@media (min-width: 768px) {
  .nav-mobile {
    display: none;
  }

  .nav-desktop {
    display: flex;
  }
}
```

---

## CSS 工程化

### 11. CSS 架构和命名规范？

**BEM 命名规范：**

```css
/* Block - 独立的功能组件 */
.button {
}
.card {
}
.menu {
}

/* Element - 块的组成部分 */
.button__text {
}
.card__title {
}
.card__content {
}
.menu__item {
}

/* Modifier - 块或元素的状态 */
.button--primary {
}
.button--large {
}
.card--featured {
}
.menu__item--active {
}

/* 实际应用示例 */
.hero {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 80px 20px;
  text-align: center;
}

.hero__title {
  font-size: 3rem;
  color: white;
  margin-bottom: 20px;
}

.hero__subtitle {
  font-size: 1.2rem;
  color: rgba(255, 255, 255, 0.8);
  margin-bottom: 30px;
}

.hero__button {
  display: inline-block;
  padding: 12px 24px;
  background: white;
  color: #667eea;
  text-decoration: none;
  border-radius: 25px;
  font-weight: 600;
}

.hero__button--outline {
  background: transparent;
  color: white;
  border: 2px solid white;
}
```

**CSS 架构组织：**

```css
/* 1. 基础样式 (base) */
/* reset.css */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* typography.css */
body {
  font-family: "Inter", sans-serif;
  line-height: 1.6;
  color: #333;
}

/* 2. 布局样式 (layout) */
/* grid.css */
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.row {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -10px;
}

.col {
  flex: 1;
  padding: 0 10px;
}

/* 3. 组件样式 (components) */
/* button.css */
.btn {
  display: inline-block;
  padding: 12px 24px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.3s ease;
}

.btn--primary {
  background: #007bff;
  color: white;
}

.btn--secondary {
  background: #6c757d;
  color: white;
}

/* 4. 工具样式 (utilities) */
/* utilities.css */
.text-center {
  text-align: center;
}
.text-left {
  text-align: left;
}
.text-right {
  text-align: right;
}

.mb-1 {
  margin-bottom: 8px;
}
.mb-2 {
  margin-bottom: 16px;
}
.mb-3 {
  margin-bottom: 24px;
}

.d-none {
  display: none;
}
.d-block {
  display: block;
}
.d-flex {
  display: flex;
}

/* 5. 主题样式 (themes) */
/* theme.css */
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --success-color: #28a745;
  --danger-color: #dc3545;
  --warning-color: #ffc107;
  --info-color: #17a2b8;
}

[data-theme="dark"] {
  --primary-color: #0d6efd;
  --secondary-color: #6c757d;
  --success-color: #198754;
  --danger-color: #dc3545;
  --warning-color: #ffc107;
  --info-color: #0dcaf0;
}
```

### 12. CSS-in-JS 和现代 CSS 解决方案？

**CSS-in-JS 示例：**

```javascript
// styled-components
import styled from "styled-components";

const Button = styled.button`
  background: ${(props) => (props.primary ? "#007bff" : "#6c757d")};
  color: white;
  padding: 12px 24px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: all 0.3s ease;

  &:hover {
    background: ${(props) => (props.primary ? "#0056b3" : "#545b62")};
  }

  &:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }
`;

// emotion
import { css } from "@emotion/react";

const buttonStyles = css`
  background: #007bff;
  color: white;
  padding: 12px 24px;
  border: none;
  border-radius: 4px;
  cursor: pointer;

  &:hover {
    background: #0056b3;
  }
`;

// CSS Modules
import styles from "./Button.module.css";

function Button({ children, variant = "primary" }) {
  return (
    <button className={`${styles.button} ${styles[variant]}`}>
      {children}
    </button>
  );
}
```

**现代 CSS 解决方案：**

```css
/* 1. CSS 自定义属性 + 计算 */
.dynamic-component {
  --base-size: 16px;
  --scale-factor: 1.2;
  --final-size: calc(var(--base-size) * var(--scale-factor));

  font-size: var(--final-size);
}

/* 2. CSS 逻辑属性 */
.logical-layout {
  margin-inline-start: 20px;
  margin-inline-end: 20px;
  padding-block: 16px;
  border-inline-start: 2px solid #000;
}

/* 3. CSS 容器查询 */
.container {
  container-type: inline-size;
}

@container (min-width: 300px) {
  .content {
    display: flex;
    flex-direction: row;
  }
}

/* 4. CSS 子网格 */
.parent-grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
}

.child-grid {
  display: grid;
  grid-template-columns: subgrid;
  grid-column: 1 / -1;
}

/* 5. CSS 锚点定位 */
.anchor-container {
  position: relative;
}

.anchor-element {
  position: absolute;
  top: anchor(bottom);
  left: anchor(center);
  transform: translateX(-50%);
}
```

---

## 总结

现代 CSS 开发需要掌握：

1. **布局技术**：Flexbox、Grid、Subgrid 的正确使用
2. **响应式设计**：移动优先、容器查询、流体设计
3. **性能优化**：选择器优化、动画性能、加载策略
4. **现代特性**：CSS 变量、逻辑属性、容器查询
5. **工程化**：架构设计、命名规范、模块化
6. **可访问性**：语义化、对比度、键盘导航

这些技能对于 4 年前端开发工程师来说都是必备的，能够帮助构建高质量、高性能的现代 Web 应用。
