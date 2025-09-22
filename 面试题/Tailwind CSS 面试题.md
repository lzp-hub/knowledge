# 🎨 Tailwind CSS 面试题 · 高频考点（3–5 年工程师）

## 1) 什么是 Tailwind CSS？为什么选择它？ 🔥

**一句话回答**
Tailwind 是一个原子化 CSS 框架，用类名直接组合 UI，避免写重复样式。

**要点**

- Utility-first（原子类）；
- JIT 按需生成，未用类不打包；
- 主题/配置灵活；
- 开发效率高。

**详细回答**
Tailwind 提供原子类（如 `p-4`, `bg-blue-500`），无需重复写 CSS。
通过 `tailwind.config.js` 可扩展 tokens（颜色、间距、断点）。
JIT 模式极大减少最终 CSS 体积。

**扩展/对比**

- **vs CSS Modules**：Tailwind 更快，但类名语义性差。
- **vs Styled-Components**：Tailwind 无运行时，但缺少动态样式能力。

---

## 2) Tailwind 如何实现主题定制与响应式？ 🔥

**一句话回答**
通过配置文件扩展 theme tokens + 响应式断点前缀（sm/md/lg/xl）。

**要点**

- `theme.extend` 配置；
- `screens` 定义断点；
- 类名前缀控制响应式。

**详细回答**

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: { brand: "#1DA1F2" },
      screens: { xs: "480px" },
    },
  },
};
```

```html
<div class="bg-brand sm:bg-green-500 lg:bg-red-500">demo</div>
```

**扩展/对比**

- 面试官常问：“如何做暗黑模式？” 👉 `dark:` 前缀 或 `darkMode: "class" | "media"`。

---

## 3) Tailwind 的优缺点？ 🔥

**一句话回答**
优点：快速、灵活、可维护；缺点：类名臃肿、语义弱。

**要点**

- 优点：开发快、按需编译、生态丰富；
- 缺点：HTML 可读性差，需要团队规范。

**详细回答**

- 适合快速开发和配合设计系统。
- 可用 `@apply` 或 UI 库（shadcn/ui）减少模板臃肿。

**扩展/对比**

- 大厂常问：“如何在团队中推行 Tailwind？” 👉 设计 tokens + 封装组件库。

---

## 4) Tailwind 如何支持暗黑模式？ 🔥

**一句话回答**
通过 `dark:` 前缀或配置 `darkMode: "class" | "media"`。

**要点**

- `media` 跟随系统；
- `class` 手动切换；
- `dark:` 类名前缀控制。

**详细回答**

```js
// tailwind.config.js
module.exports = { darkMode: "class" };
```

```html
<html class="dark">
  <div class="bg-white dark:bg-black">...</div>
</html>
```

**扩展/对比**

- 面试追问：“如何实现暗黑模式按钮？” 👉 切换 `html.classList.toggle("dark")`。

---

## 5) 如何避免 Tailwind 的 className 冗长？ 🔥

**一句话回答**
使用 `@apply`、`clsx`、`cva` 等工具来管理和封装类。

**要点**

- `@apply` 提取公共样式；
- `clsx` 条件组合；
- `cva` 定义变体模式。

**详细回答**

```css
/* global.css */
.btn {
  @apply px-4 py-2 rounded bg-blue-500 text-white;
}
```

```tsx
import clsx from "clsx";
<button className={clsx("btn", active && "bg-green-500")} />;
```

**扩展/对比**

- 推荐团队封装 Button/Input 等基础组件，避免重复拼类名。

---

## 6) Tailwind 的性能优化 🔥

**一句话回答**
通过 JIT + Purge，只生成用到的类，控制最终 CSS 体积。

**要点**

- JIT 模式；
- purge（旧版）；
- safelist 白名单。

**详细回答**

- JIT 动态编译：KB 级 CSS。
- 动态类需 safelist：

```js
safelist: [/^bg-(red|green|blue)-(500|600)$/];
```

**扩展/对比**

- 与 AntD 相比，Tailwind 产物更轻，适合移动端。

---
