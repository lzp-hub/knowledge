# HTML 面试题

## 目录

- [HTML5 新特性](#html5-新特性)
- [语义化标签](#语义化标签)
- [可访问性 (A11y)](#可访问性-a11y)
- [SEO 优化](#seo-优化)
- [性能优化](#性能优化)
- [现代 Web 标准](#现代-web-标准)

---

## HTML5 新特性

### 1. HTML5 相比 HTML4 有哪些重大改进？

**核心改进：**

- **语义化标签**：`<header>`、`<nav>`、`<main>`、`<section>`、`<article>`、`<aside>`、`<footer>`
- **多媒体支持**：`<video>`、`<audio>`、`<canvas>`、`<svg>`
- **表单增强**：新的 input 类型、验证属性、`<datalist>`、`<output>`
- **存储 API**：`localStorage`、`sessionStorage`、`IndexedDB`
- **Web API**：`Geolocation`、`Web Workers`、`WebSocket`、`File API`
- **离线应用**：`Application Cache`、`Service Workers`

**示例：**

```html
<!-- 语义化结构 -->
<header>
  <nav>
    <ul>
      <li><a href="#home">首页</a></li>
      <li><a href="#about">关于</a></li>
    </ul>
  </nav>
</header>

<main>
  <article>
    <h1>文章标题</h1>
    <p>文章内容...</p>
  </article>
</main>

<!-- 多媒体 -->
<video controls>
  <source src="video.mp4" type="video/mp4" />
  <source src="video.webm" type="video/webm" />
  您的浏览器不支持视频播放
</video>

<!-- 增强表单 -->
<form>
  <input type="email" required placeholder="请输入邮箱" />
  <input type="date" name="birthday" />
  <input type="range" min="0" max="100" value="50" />
  <datalist id="browsers">
    <option value="Chrome"></option>
    <option value="Firefox"></option>
  </datalist>

  <input list="browsers" name="browser" />
</form>
```

### 2. 什么是 Web Components？如何使用？

**Web Components 是一套标准，允许创建可重用的自定义元素：**

```html
<!-- 定义自定义元素 -->
<script>
  class MyButton extends HTMLElement {
    constructor() {
      super();
      this.attachShadow({ mode: "open" });
      this.shadowRoot.innerHTML = `
      <style>
        button {
          background: #007bff;
          color: white;
          border: none;
          padding: 10px 20px;
          border-radius: 4px;
          cursor: pointer;
        }
      </style>
      <button><slot></slot></button>
    `;
    }
  }

  customElements.define("my-button", MyButton);
</script>

<!-- 使用自定义元素 -->
<my-button>点击我</my-button>
```

**核心特性：**

- **Custom Elements**：创建自定义 HTML 标签
- **Shadow DOM**：封装样式和结构
- **HTML Templates**：定义可复用的模板
- **ES Modules**：模块化导入导出

---

## 语义化标签

### 3. 为什么要使用语义化标签？有哪些好处？

**语义化标签的优势：**

1. **SEO 友好**：搜索引擎更容易理解页面结构
2. **可访问性**：屏幕阅读器能更好地导航
3. **代码可读性**：开发者更容易理解代码意图
4. **维护性**：结构清晰，便于维护

**语义化标签使用示例：**

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>语义化页面</title>
  </head>
  <body>
    <header>
      <h1>网站标题</h1>
      <nav aria-label="主导航">
        <ul>
          <li><a href="#home">首页</a></li>
          <li><a href="#about">关于</a></li>
          <li><a href="#contact">联系</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <section aria-labelledby="news-heading">
        <h2 id="news-heading">最新新闻</h2>
        <article>
          <header>
            <h3>新闻标题</h3>
            <time datetime="2024-01-15">2024年1月15日</time>
          </header>
          <p>新闻内容...</p>
          <footer>
            <p>作者：张三</p>
          </footer>
        </article>
      </section>

      <aside>
        <h2>相关链接</h2>
        <nav aria-label="相关链接">
          <ul>
            <li><a href="#">链接1</a></li>
            <li><a href="#">链接2</a></li>
          </ul>
        </nav>
      </aside>
    </main>

    <footer>
      <p>&copy; 2024 我的网站</p>
    </footer>
  </body>
</html>
```

### 4. `<section>` 和 `<article>` 的区别？

**区别对比：**

| 标签        | 用途               | 独立性     | 示例               |
| ----------- | ------------------ | ---------- | ------------------ |
| `<section>` | 文档中的主题性分组 | 依赖上下文 | 章节、标签页内容   |
| `<article>` | 独立完整的内容单元 | 可独立分发 | 博客文章、新闻条目 |

**使用示例：**

```html
<article>
  <header>
    <h1>博客文章标题</h1>
    <p>发布时间：<time datetime="2024-01-15">2024年1月15日</time></p>
  </header>

  <section>
    <h2>第一章</h2>
    <p>第一章内容...</p>
  </section>

  <section>
    <h2>第二章</h2>
    <p>第二章内容...</p>
  </section>

  <footer>
    <p>作者：张三 | 标签：<a href="#">技术</a></p>
  </footer>
</article>
```

---

## 可访问性 (A11y)

### 5. 如何提高网页的可访问性？

**核心可访问性实践：**

1. **语义化标签**：使用正确的 HTML 标签
2. **ARIA 属性**：提供额外的语义信息
3. **键盘导航**：确保所有功能可通过键盘访问
4. **颜色对比度**：确保文字和背景有足够对比度
5. **替代文本**：为图片提供 alt 属性

**可访问性示例：**

```html
<!-- 表单可访问性 -->
<form>
  <fieldset>
    <legend>用户信息</legend>

    <div>
      <label for="username">用户名：</label>
      <input
        type="text"
        id="username"
        name="username"
        required
        aria-describedby="username-help"
        aria-invalid="false"
      />
      <div id="username-help">请输入3-20个字符的用户名</div>
    </div>

    <div>
      <label for="email">邮箱：</label>
      <input
        type="email"
        id="email"
        name="email"
        required
        aria-describedby="email-error"
      />
      <div id="email-error" role="alert" aria-live="polite"></div>
    </div>
  </fieldset>

  <button type="submit">提交</button>
</form>

<!-- 导航可访问性 -->
<nav aria-label="主导航">
  <ul>
    <li><a href="#home" aria-current="page">首页</a></li>
    <li><a href="#about">关于</a></li>
    <li>
      <button aria-expanded="false" aria-controls="submenu">
        产品 <span aria-hidden="true">▼</span>
      </button>
      <ul id="submenu" hidden>
        <li><a href="#product1">产品1</a></li>
        <li><a href="#product2">产品2</a></li>
      </ul>
    </li>
  </ul>
</nav>

<!-- 图片可访问性 -->
<img
  src="chart.png"
  alt="2024年销售数据图表，显示Q1-Q4季度销售额分别为100万、150万、200万、180万"
  role="img"
/>

<!-- 复杂图片使用figure -->
<figure>
  <img src="diagram.png" alt="系统架构图" />
  <figcaption>图1：系统整体架构设计</figcaption>
</figure>
```

### 6. ARIA 属性的主要作用和使用场景？

**ARIA (Accessible Rich Internet Applications) 核心属性：**

```html
<!-- 角色定义 -->
<div role="button" tabindex="0" aria-pressed="false">自定义按钮</div>
<div role="alert" aria-live="assertive">重要提示信息</div>
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">确认对话框</h2>
  <p>您确定要删除这个项目吗？</p>
</div>

<!-- 状态和属性 -->
<button aria-expanded="false" aria-controls="menu" aria-haspopup="true">
  菜单
</button>
<ul id="menu" hidden>
  <li><a href="#">选项1</a></li>
  <li><a href="#">选项2</a></li>
</ul>

<!-- 关系定义 -->
<div>
  <label for="password">密码：</label>
  <input
    type="password"
    id="password"
    aria-describedby="password-requirements"
    aria-invalid="false"
  />
  <div id="password-requirements">
    密码必须包含至少8个字符，包括大小写字母和数字
  </div>
</div>

<!-- 实时区域 -->
<div aria-live="polite" aria-atomic="true">
  <!-- 动态更新的内容 -->
</div>
```

---

## SEO 优化

### 7. HTML 层面的 SEO 优化策略？

**核心 SEO 优化：**

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <!-- 基础元信息 -->
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <!-- SEO 核心标签 -->
    <title>页面标题 - 网站名称</title>
    <meta name="description" content="页面描述，150-160字符，包含关键词" />
    <meta name="keywords" content="关键词1,关键词2,关键词3" />

    <!-- Open Graph (社交媒体分享) -->
    <meta property="og:title" content="页面标题" />
    <meta property="og:description" content="页面描述" />
    <meta property="og:image" content="https://example.com/image.jpg" />
    <meta property="og:url" content="https://example.com/page" />
    <meta property="og:type" content="website" />

    <!-- Twitter Card -->
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:title" content="页面标题" />
    <meta name="twitter:description" content="页面描述" />
    <meta name="twitter:image" content="https://example.com/image.jpg" />

    <!-- 结构化数据 -->
    <script type="application/ld+json">
      {
        "@context": "https://schema.org",
        "@type": "Article",
        "headline": "文章标题",
        "author": {
          "@type": "Person",
          "name": "作者姓名"
        },
        "datePublished": "2024-01-15",
        "image": "https://example.com/image.jpg"
      }
    </script>

    <!-- 规范链接 -->
    <link rel="canonical" href="https://example.com/page" />

    <!-- 多语言支持 -->
    <link rel="alternate" hreflang="en" href="https://example.com/en/page" />
    <link rel="alternate" hreflang="zh-CN" href="https://example.com/zh/page" />
  </head>
  <body>
    <!-- 语义化结构 -->
    <header>
      <h1>主标题</h1>
      <nav>
        <ul>
          <li><a href="/">首页</a></li>
          <li><a href="/about">关于我们</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <article>
        <h2>文章标题</h2>
        <p>文章内容...</p>
      </article>
    </main>
  </body>
</html>
```

### 8. 如何优化图片的 SEO？

**图片 SEO 优化策略：**

```html
<!-- 基础图片优化 -->
<img
  src="product.jpg"
  alt="高质量无线耳机，支持降噪功能，续航30小时"
  title="无线耳机产品图"
  width="400"
  height="300"
  loading="lazy"
/>

<!-- 响应式图片 -->
<picture>
  <source media="(min-width: 800px)" srcset="large.jpg" />
  <source media="(min-width: 400px)" srcset="medium.jpg" />
  <img src="small.jpg" alt="产品图片" loading="lazy" />
</picture>

<!-- 现代图片格式 -->
<picture>
  <source srcset="image.avif" type="image/avif" />
  <source srcset="image.webp" type="image/webp" />
  <img src="image.jpg" alt="产品图片" loading="lazy" />
</picture>

<!-- 图片结构化数据 -->
<script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "ImageObject",
    "url": "https://example.com/image.jpg",
    "caption": "产品展示图",
    "contentUrl": "https://example.com/image.jpg",
    "width": 800,
    "height": 600
  }
</script>
```

---

## 性能优化

### 9. HTML 层面的性能优化策略？

**核心性能优化：**

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <!-- 关键资源预加载 -->
    <link rel="preload" href="critical.css" as="style" />
    <link rel="preload" href="hero-image.jpg" as="image" />
    <link
      rel="preload"
      href="font.woff2"
      as="font"
      type="font/woff2"
      crossorigin
    />

    <!-- DNS 预解析 -->
    <link rel="dns-prefetch" href="//fonts.googleapis.com" />
    <link rel="dns-prefetch" href="//cdn.example.com" />

    <!-- 预连接 -->
    <link rel="preconnect" href="https://api.example.com" />

    <!-- 关键 CSS 内联 -->
    <style>
      /* 关键 CSS 内联 */
      .hero {
        background: #f0f0f0;
      }
      .header {
        position: fixed;
        top: 0;
      }
    </style>

    <!-- 非关键 CSS 异步加载 -->
    <link
      rel="preload"
      href="non-critical.css"
      as="style"
      onload="this.onload=null;this.rel='stylesheet'"
    />
    <noscript><link rel="stylesheet" href="non-critical.css" /></noscript>

    <title>页面标题</title>
  </head>
  <body>
    <!-- 图片懒加载 -->
    <img
      src="placeholder.jpg"
      data-src="real-image.jpg"
      alt="描述"
      loading="lazy"
      class="lazy-image"
    />

    <!-- 视频优化 -->
    <video preload="metadata" poster="video-poster.jpg" controls>
      <source src="video.mp4" type="video/mp4" />
      <source src="video.webm" type="video/webm" />
    </video>

    <!-- 脚本优化 -->
    <script>
      // 关键 JavaScript 内联
      document.addEventListener("DOMContentLoaded", function () {
        // 初始化关键功能
      });
    </script>

    <!-- 非关键脚本延迟加载 -->
    <script src="non-critical.js" defer></script>

    <!-- 异步加载第三方脚本 -->
    <script>
      window.addEventListener("load", function () {
        var script = document.createElement("script");
        script.src = "analytics.js";
        script.async = true;
        document.head.appendChild(script);
      });
    </script>
  </body>
</html>
```

### 10. 如何实现图片懒加载？

**现代懒加载实现：**

```html
<!-- 原生懒加载 -->
<img
  src="placeholder.jpg"
  data-src="real-image.jpg"
  alt="描述"
  loading="lazy"
  class="lazy-image"
/>

<!-- 自定义懒加载实现 -->
<img
  src="placeholder.jpg"
  data-src="real-image.jpg"
  alt="描述"
  class="lazy-image"
/>

<script>
  class LazyLoader {
    constructor() {
      this.images = document.querySelectorAll(".lazy-image");
      this.observer = this.createObserver();
      this.init();
    }

    createObserver() {
      return new IntersectionObserver(
        (entries) => {
          entries.forEach((entry) => {
            if (entry.isIntersecting) {
              this.loadImage(entry.target);
              this.observer.unobserve(entry.target);
            }
          });
        },
        {
          rootMargin: "50px 0px",
          threshold: 0.01,
        }
      );
    }

    loadImage(img) {
      const src = img.dataset.src;
      if (src) {
        img.src = src;
        img.classList.add("loaded");
        img.removeAttribute("data-src");
      }
    }

    init() {
      this.images.forEach((img) => {
        this.observer.observe(img);
      });
    }
  }

  // 初始化懒加载
  document.addEventListener("DOMContentLoaded", () => {
    new LazyLoader();
  });
</script>

<style>
  .lazy-image {
    opacity: 0;
    transition: opacity 0.3s;
  }

  .lazy-image.loaded {
    opacity: 1;
  }
</style>
```

---

## 现代 Web 标准

### 11. 什么是 PWA？如何在 HTML 中实现？

**PWA (Progressive Web App) 核心特性：**

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <!-- PWA 元信息 -->
    <meta name="theme-color" content="#000000" />
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />
    <meta name="apple-mobile-web-app-title" content="我的应用" />

    <!-- 应用图标 -->
    <link rel="icon" type="image/png" sizes="32x32" href="icon-32x32.png" />
    <link rel="icon" type="image/png" sizes="16x16" href="icon-16x16.png" />
    <link rel="apple-touch-icon" href="apple-touch-icon.png" />

    <!-- Web App Manifest -->
    <link rel="manifest" href="manifest.json" />

    <title>PWA 应用</title>
  </head>
  <body>
    <div id="app">
      <h1>我的 PWA 应用</h1>
      <button id="install-btn" style="display: none;">安装应用</button>
    </div>

    <script>
      // 注册 Service Worker
      if ("serviceWorker" in navigator) {
        window.addEventListener("load", () => {
          navigator.serviceWorker
            .register("/sw.js")
            .then((registration) => {
              console.log("SW registered: ", registration);
            })
            .catch((registrationError) => {
              console.log("SW registration failed: ", registrationError);
            });
        });
      }

      // PWA 安装提示
      let deferredPrompt;
      const installBtn = document.getElementById("install-btn");

      window.addEventListener("beforeinstallprompt", (e) => {
        e.preventDefault();
        deferredPrompt = e;
        installBtn.style.display = "block";
      });

      installBtn.addEventListener("click", () => {
        if (deferredPrompt) {
          deferredPrompt.prompt();
          deferredPrompt.userChoice.then((choiceResult) => {
            if (choiceResult.outcome === "accepted") {
              console.log("用户接受了安装");
            }
            deferredPrompt = null;
            installBtn.style.display = "none";
          });
        }
      });
    </script>
  </body>
</html>
```

**manifest.json 配置：**

```json
{
  "name": "我的 PWA 应用",
  "short_name": "PWA App",
  "description": "一个渐进式 Web 应用",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#000000",
  "orientation": "portrait",
  "icons": [
    {
      "src": "icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

### 12. 现代 HTML 表单验证和用户体验优化？

**现代表单设计：**

```html
<form id="contact-form" novalidate>
  <fieldset>
    <legend>联系信息</legend>

    <!-- 实时验证 -->
    <div class="form-group">
      <label for="name">姓名 *</label>
      <input
        type="text"
        id="name"
        name="name"
        required
        minlength="2"
        maxlength="50"
        pattern="[a-zA-Z\u4e00-\u9fa5\s]+"
        autocomplete="name"
        aria-describedby="name-error name-help"
      />
      <div id="name-help" class="help-text">请输入真实姓名</div>
      <div
        id="name-error"
        class="error-text"
        role="alert"
        aria-live="polite"
      ></div>
    </div>

    <!-- 邮箱验证 -->
    <div class="form-group">
      <label for="email">邮箱 *</label>
      <input
        type="email"
        id="email"
        name="email"
        required
        autocomplete="email"
        aria-describedby="email-error"
      />
      <div
        id="email-error"
        class="error-text"
        role="alert"
        aria-live="polite"
      ></div>
    </div>

    <!-- 密码强度 -->
    <div class="form-group">
      <label for="password">密码 *</label>
      <input
        type="password"
        id="password"
        name="password"
        required
        minlength="8"
        pattern="^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d@$!%*?&]{8,}$"
        autocomplete="new-password"
        aria-describedby="password-error password-strength"
      />
      <div id="password-strength" class="strength-meter">
        <div class="strength-bar"></div>
      </div>
      <div
        id="password-error"
        class="error-text"
        role="alert"
        aria-live="polite"
      ></div>
    </div>

    <!-- 文件上传 -->
    <div class="form-group">
      <label for="avatar">头像</label>
      <input
        type="file"
        id="avatar"
        name="avatar"
        accept="image/*"
        aria-describedby="avatar-help"
      />
      <div id="avatar-help" class="help-text">
        支持 JPG、PNG 格式，大小不超过 2MB
      </div>
    </div>

    <!-- 自定义选择器 -->
    <div class="form-group">
      <label for="country">国家</label>
      <select id="country" name="country" autocomplete="country">
        <option value="">请选择国家</option>
        <option value="CN">中国</option>
        <option value="US">美国</option>
        <option value="JP">日本</option>
      </select>
    </div>

    <!-- 自定义复选框 -->
    <div class="form-group">
      <label class="checkbox-label">
        <input type="checkbox" name="agree" required />
        <span class="checkmark"></span>
        我同意 <a href="/terms" target="_blank">服务条款</a> 和
        <a href="/privacy" target="_blank">隐私政策</a>
      </label>
    </div>
  </fieldset>

  <div class="form-actions">
    <button type="submit" class="btn-primary">提交</button>
    <button type="reset" class="btn-secondary">重置</button>
  </div>
</form>

<script>
  class FormValidator {
    constructor(form) {
      this.form = form;
      this.init();
    }

    init() {
      this.form.addEventListener("submit", this.handleSubmit.bind(this));
      this.form.addEventListener("input", this.handleInput.bind(this));
      this.form.addEventListener("blur", this.handleBlur.bind(this), true);
    }

    handleSubmit(e) {
      e.preventDefault();

      if (this.validateForm()) {
        this.submitForm();
      }
    }

    handleInput(e) {
      const field = e.target;
      this.validateField(field);
    }

    handleBlur(e) {
      const field = e.target;
      if (field.tagName === "INPUT" || field.tagName === "SELECT") {
        this.validateField(field);
      }
    }

    validateForm() {
      const fields = this.form.querySelectorAll("input, select, textarea");
      let isValid = true;

      fields.forEach((field) => {
        if (!this.validateField(field)) {
          isValid = false;
        }
      });

      return isValid;
    }

    validateField(field) {
      const value = field.value.trim();
      const fieldName = field.name;
      const errorElement = document.getElementById(`${fieldName}-error`);

      // 清除之前的错误
      this.clearError(field);

      // 必填验证
      if (field.hasAttribute("required") && !value) {
        this.showError(field, "此字段为必填项");
        return false;
      }

      // 邮箱验证
      if (field.type === "email" && value) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!emailRegex.test(value)) {
          this.showError(field, "请输入有效的邮箱地址");
          return false;
        }
      }

      // 密码强度验证
      if (field.type === "password" && value) {
        this.checkPasswordStrength(field, value);
      }

      // 文件大小验证
      if (field.type === "file" && field.files.length > 0) {
        const file = field.files[0];
        if (file.size > 2 * 1024 * 1024) {
          // 2MB
          this.showError(field, "文件大小不能超过 2MB");
          return false;
        }
      }

      return true;
    }

    showError(field, message) {
      const errorElement = document.getElementById(`${field.name}-error`);
      if (errorElement) {
        errorElement.textContent = message;
        errorElement.style.display = "block";
      }

      field.setAttribute("aria-invalid", "true");
      field.classList.add("error");
    }

    clearError(field) {
      const errorElement = document.getElementById(`${field.name}-error`);
      if (errorElement) {
        errorElement.textContent = "";
        errorElement.style.display = "none";
      }

      field.removeAttribute("aria-invalid");
      field.classList.remove("error");
    }

    checkPasswordStrength(field, password) {
      const strengthBar = document.querySelector(".strength-bar");
      let strength = 0;

      if (password.length >= 8) strength++;
      if (/[a-z]/.test(password)) strength++;
      if (/[A-Z]/.test(password)) strength++;
      if (/\d/.test(password)) strength++;
      if (/[^a-zA-Z\d]/.test(password)) strength++;

      const strengthLevels = ["很弱", "弱", "一般", "强", "很强"];
      const colors = ["#ff4444", "#ff8800", "#ffaa00", "#88cc00", "#00aa00"];

      strengthBar.style.width = `${(strength / 5) * 100}%`;
      strengthBar.style.backgroundColor = colors[strength - 1] || "#ccc";

      if (strength < 3) {
        this.showError(
          field,
          `密码强度：${strengthLevels[strength - 1] || "很弱"}`
        );
      }
    }

    async submitForm() {
      const formData = new FormData(this.form);

      try {
        const response = await fetch("/api/contact", {
          method: "POST",
          body: formData,
        });

        if (response.ok) {
          this.showSuccess("表单提交成功！");
          this.form.reset();
        } else {
          throw new Error("提交失败");
        }
      } catch (error) {
        this.showError(null, "提交失败，请稍后重试");
      }
    }

    showSuccess(message) {
      // 显示成功消息
      const successDiv = document.createElement("div");
      successDiv.className = "success-message";
      successDiv.textContent = message;
      this.form.appendChild(successDiv);

      setTimeout(() => {
        successDiv.remove();
      }, 3000);
    }
  }

  // 初始化表单验证
  document.addEventListener("DOMContentLoaded", () => {
    const form = document.getElementById("contact-form");
    new FormValidator(form);
  });
</script>
```

---

## 总结

现代 HTML 开发需要关注：

1. **语义化**：使用正确的标签表达内容含义
2. **可访问性**：确保所有用户都能访问内容
3. **性能**：优化加载速度和用户体验
4. **SEO**：提高搜索引擎可见性
5. **现代标准**：拥抱 Web Components、PWA 等新技术
6. **用户体验**：提供流畅的交互和反馈

这些技能对于 4 年前端开发工程师来说都是必备的，能够帮助构建高质量、用户友好的现代 Web 应用。
