## 一、基础与选择器

### 1. CSS 选择器优先级规则 🔥

**一句话回答**
优先级从低到高：元素 < 类/伪类 < ID < 内联 < !important。

**要点**

- 权重计算：ID > 类 > 元素。
- 内联样式最高，除非有 !important。
- 继承样式优先级最低。

**详细回答**
CSS 优先级按以下规则：

1. !important > 内联（style） > ID > 类/伪类/属性选择器 > 标签选择器。
2. 相同权重时，后出现的规则覆盖前面的。
3. 继承的样式优先级最低。

**扩展/对比**

- 建议避免滥用 !important。
- 工程实践推荐 **BEM 命名规范** 保持清晰层次。

---

### 2. 伪类与伪元素的区别 🔥

**一句话回答**
伪类用于选择特定状态，伪元素用于创建虚拟元素。

**要点**

- 伪类（\:hover）：状态。
- 伪元素（::after）：虚拟节点。

**详细回答**

- 伪类如 `:hover`、`:focus` 表示元素在特定状态下的样式。
- 伪元素如 `::before`、`::after` 创建额外的虚拟 DOM 节点。

**扩展/对比**

- CSS3 规定伪元素用 `::`，伪类用 `:`。
- 伪元素常用于绘制装饰性内容（清除浮动等）。

---

### 3. BFC（块级格式化上下文） 🔥

**一句话回答**
BFC 是独立的渲染区域，内部元素布局不会影响外部。

**要点**

- 触发方式：overflow、float、absolute/fixed、display\:inline-block/flex/grid。
- 应用场景：清除浮动、避免 margin 重叠。

**详细回答**
BFC 的特点：

1. 内部元素不会影响外部布局。
2. 计算高度时包含浮动元素。
3. 相邻 BFC 的 margin 不会重叠。

**扩展/对比**

- 常见面试题：“如何清除浮动？” 👉 给父元素加 `overflow:hidden` 或 `display:flow-root` 触发 BFC。

---

### 4. inline、block、inline-block 的区别

**一句话回答**
inline 不独占一行，block 独占一行，inline-block 既能设置宽高又不换行。

**要点**

- inline：宽高无效。
- block：独占一行，可设置宽高。
- inline-block：不换行 + 可设置宽高。

**详细回答**

- `span` 默认 inline；`div` 默认 block；`img` 默认 inline-block。

**扩展/对比**

- 工程中常用 inline-block 实现多列布局。

---

### 5. position 的属性值区别

**一句话回答**
static 默认，relative 相对自身偏移，absolute 相对最近定位祖先，fixed 相对视口，sticky 结合 relative 和 fixed。

**要点**

- absolute：脱离文档流，相对最近的非 static 元素定位。
- fixed：相对视口，不随滚动。
- sticky：在一定范围内相对，超出吸顶。

**详细回答**

- sticky 常用于吸顶导航。
- absolute 常与 relative 父元素配合。

**扩展/对比**

- sticky 兼容性问题在早期浏览器中较多。

---

### 6. z-index 与层叠上下文 🔥

**一句话回答**
z-index 只在同一层叠上下文中比较，新的层叠上下文会隔离。

**要点**

- 层叠上下文触发条件：position+z-index、opacity<1、transform、filter 等。
- z-index 无效多因层叠上下文不同。

**详细回答**

- 父元素创建层叠上下文后，子元素的 z-index 无法影响父级外部元素。
- 常见 bug：元素被莫名遮挡，其实是层叠上下文问题。

**扩展/对比**

- 工程中常见解决方案：检查是否误用 `transform`。

---

# 二、布局与排版

### 7. Flexbox 常见属性与典型布局实现 🔥

**一句话回答**
Flex 是一维布局，常用于水平/垂直居中、等分布局。

**要点**

- 容器属性：`display:flex`、`justify-content`、`align-items`、`flex-wrap`。
- 子项属性：`flex:1`、`align-self`、`order`。

**详细回答**
Flex 常见写法：

- 水平居中：`justify-content:center`。
- 垂直居中：`align-items:center`。
- 水平+垂直居中：`display:flex; justify-content:center; align-items:center;`。

**扩展/对比**

- Flex 适合一维布局；二维布局推荐 Grid。

---

### 8. Grid 布局的核心概念与应用场景 🔥

**一句话回答**
Grid 是二维布局，适合复杂的页面网格。

**要点**

- 容器：`display:grid`，定义行列。
- 属性：`grid-template-rows/columns`、`grid-gap`、`grid-area`。

**详细回答**
Grid 能在两个维度上同时控制，适合面向整体布局的 UI：

```css
.container {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  grid-template-rows: auto 1fr auto;
}
```

**扩展/对比**

- Grid + Flex 联合使用：Grid 管大布局，Flex 管局部细节。

---

### 9. 圣杯布局 / 双飞翼布局

**一句话回答**
圣杯和双飞翼都是三栏布局方案，左右固定，中间自适应。

**要点**

- 圣杯：padding + float。
- 双飞翼：额外中间容器。

**详细回答**

- 圣杯布局利用 `float:left/right` 实现左右固定，中间 padding 留白。
- 双飞翼布局在中间多包一层容器解决内容被遮挡问题。

**扩展/对比**

- 现代开发中更推荐 Flex 或 Grid 简化实现。

---

### 10. 多列布局方式对比

**一句话回答**
可用 float、flex、grid、position 实现多列布局，Flex/Grid 是现代首选。

**要点**

- float：老方案，需要清除浮动。
- flex：简洁，适合一维布局。
- grid：强大，适合复杂网格。
- position：不推荐常规布局。

**详细回答**

- 过去多用 float，现在基本用 flex/grid。
- Grid 在响应式中优势明显。

**扩展/对比**

- 面试官常考：用三种方式实现三列布局。

---

### 11. 水平/垂直居中元素的多种实现方法 🔥

**一句话回答**
常见方式：flex、grid、absolute+transform、line-height。

**要点**

- flex：`justify-content:center; align-items:center;`。
- absolute：`position:absolute; top:50%; left:50%; transform:translate(-50%,-50%)`。
- grid：`place-items:center`。

**详细回答**

- 不同方式适合不同场景。
- 绝对定位适合已知宽高。

**扩展/对比**

- 面试官常考：“写 5 种方法实现居中”。

---

# 三、盒模型与渲染

### 12. 标准盒模型 vs 怪异盒模型 🔥

**一句话回答**
标准模型下 width=content，怪异模型下 width=content+padding+border。

**要点**

- 标准：默认模式。
- 怪异：由 DOCTYPE 决定。
- box-sizing 可切换。

**详细回答**

```css
/* 标准 */
box-sizing: content-box;
/* 怪异 */
box-sizing: border-box;
```

border-box 模式下更好算宽度。

**扩展/对比**

- 工程推荐统一使用 `border-box`。

---

### 13. margin 重叠（collapse） 🔥

**一句话回答**
垂直方向上相邻 margin 会发生重叠，取最大值。

**要点**

- 父子、兄弟之间可能发生。
- 水平方向不会重叠。

**详细回答**
例：父元素与第一个子元素的 margin-top 会合并。

**扩展/对比**

- 解决方式：父元素加 `padding`、`border` 或触发 BFC。

---

### 14. CSS 中的包含块（containing block）

**一句话回答**
包含块是元素计算定位和百分比大小的参考区域。

**要点**

- absolute：最近的定位祖先。
- fixed：视口。
- relative：父元素内容区。

**详细回答**
包含块决定了元素的定位上下文和尺寸计算方式。

**扩展/对比**

- 面试官可能结合 position 考察。

---

### 15. 回流（Reflow）与重绘（Repaint）在 CSS 层面的触发

**一句话回答**
回流是布局计算，重绘是样式更新。

**要点**

- 回流更昂贵。
- 常见触发：改变尺寸、添加/删除节点。

**详细回答**

- 改变元素宽高/字体大小会触发回流。
- 改变颜色/背景只触发重绘。

**扩展/对比**

- 优化：减少逐帧操作，合并 DOM 改动。

---

# 四、动画与过渡

### 16. CSS transition 与 animation 的区别 🔥

**一句话回答**
transition 用于状态过渡，需要触发条件；animation 可独立运行并支持关键帧。

**要点**

- transition：两态切换，需触发。
- animation：关键帧动画，可循环自动执行。

**详细回答**

- transition 常用于 hover/focus 状态变化。
- animation 支持复杂关键帧控制 (`@keyframes`)，能自动执行，支持无限循环。

**扩展/对比**

- 面试官可能让你写出按钮 hover 变色的 transition 与 loading 动画的 animation。

---

### 17. transform 与 translate 的性能对比 🔥

**一句话回答**
transform 使用 GPU 加速，性能优于直接修改 top/left。

**要点**

- transform：避免回流，触发合成层。
- top/left：触发布局，性能差。

**详细回答**

- `transform: translate()` 改变的是合成层，不触发回流，只会重新合成。
- 修改 `top/left` 会导致 layout → paint → composite。

**扩展/对比**

- 工程实践：动画和位移推荐用 transform。

---

### 18. requestAnimationFrame vs CSS 动画

**一句话回答**
requestAnimationFrame 是 JS 驱动，CSS 动画由浏览器优化调度。

**要点**

- CSS 动画：浏览器优化，性能好。
- rAF：更灵活，可与逻辑结合。

**详细回答**

- CSS 动画在可能的情况下由浏览器合成线程执行。
- requestAnimationFrame 更适合复杂逻辑驱动的动画（如 canvas）。

**扩展/对比**

- 简单交互：CSS 动画。
- 复杂控制：rAF。

---

### 19. will-change 的作用与风险

**一句话回答**
will-change 提示浏览器未来可能变化的属性，从而提前优化。

**要点**

- 常用于 transform、opacity。
- 提前创建合成层。
- 滥用会增加内存消耗。

**详细回答**

- will-change 可以减少首帧卡顿，但会占用显存。
- 推荐短时间使用，动画结束后移除。

**扩展/对比**

- 面试官常问：如何优化 CSS 动画？👉 transform + will-change。

---

# 五、响应式与适配

### 20. 媒体查询（media query）的常见写法 🔥

**一句话回答**
媒体查询通过检测设备条件（宽度、分辨率等）应用不同样式。

**要点**

- min-width / max-width。
- orientation（横竖屏）。
- resolution / dpi。

**代码示例**

```css
@media (max-width: 768px) {
  body {
    font-size: 14px;
  }
}
```

**详细回答**
常用于响应式布局，让不同屏幕设备适配不同样式。

**扩展/对比**

- 移动端优先：先写小屏，再用 min-width 覆盖大屏。

---

### 21. rem / em / px / vw / vh 的区别 🔥

**一句话回答**
px 固定，em 相对父级，rem 相对根元素，vw/vh 相对视口。

**要点**

- px：绝对像素。
- em：相对父元素字体大小。
- rem：相对根元素字体大小。
- vw/vh：相对视口宽高。

**详细回答**

- rem 常用于响应式（动态设置 html 字体大小）。
- vw/vh 常用于全屏布局。

**扩展/对比**

- 面试官常考：移动端适配用什么？👉 rem/vw。

---

### 22. 移动端适配方案

**一句话回答**
常见方案：流式布局、媒体查询、flex、vw/vh、自适应字体。

**要点**

- flex：弹性适配。
- vw/vh：相对视口。
- rem：结合 JS 动态计算。

**详细回答**

- rem 方案：根据屏幕宽度动态设置 html font-size。
- vw 方案：CSS 直接基于视口宽度。

**扩展/对比**

- 大厂常用 vw/rem 方案结合媒体查询。

---

### 23. 响应式图片与 image-set

**一句话回答**
image-set 可根据分辨率加载不同清晰度的图片。

**要点**

- srcset：HTML 属性。
- image-set：CSS 背景。

**代码示例**

```css
.bg {
  background-image: image-set("small.png" 1x, "large.png" 2x);
}
```

**详细回答**
image-set 用于 CSS 背景，类似 srcset 的作用，提升高清屏体验。

**扩展/对比**

- 面试官常问：移动端如何适配 2x/3x 屏？👉 srcset/image-set。

---

# 六、样式与工程化

### 24. CSS Modules、CSS-in-JS、Tailwind CSS 的对比 🔥

**一句话回答**
CSS Modules 提供局部作用域，CSS-in-JS 逻辑与样式结合，Tailwind 是原子化类名方案。

**要点**

- CSS Modules：基于构建工具，生成哈希类名。
- CSS-in-JS：动态样式、主题化。
- Tailwind：类名即样式，开发快但语义差。

**详细回答**

- CSS Modules 适合中大型项目，避免全局污染。
- CSS-in-JS 更灵活，适合需要运行时样式。
- Tailwind 提高开发效率，牺牲可读性。

**扩展/对比**

- 面试官常问：你项目里如何管理样式？👉 结合场景回答。

---

### 25. 预处理器 vs 后处理器（Sass/Less vs PostCSS）

**一句话回答**
Sass/Less 提供变量、嵌套，PostCSS 是插件平台，支持自动前缀等功能。

**要点**

- Sass/Less：增强语法。
- PostCSS：生态插件，自动化处理。

**详细回答**

- Sass/Less 是编译期工具。
- PostCSS 依赖插件，如 autoprefixer。

**扩展/对比**

- 现代工程往往结合使用。

---

### 26. 如何避免 CSS 全局污染？ 🔥

**一句话回答**
通过命名规范、作用域隔离和模块化避免全局污染。

**要点**

- BEM 命名。
- CSS Modules。
- Shadow DOM。

**详细回答**

- 全局污染来源于选择器过宽。
- BEM（Block\_\_Element--Modifier）约定俗成。
- Shadow DOM 隔离作用域。

**扩展/对比**

- 大型团队开发中这是常考点。

---

### 27. CSS 变量 vs Sass 变量 🔥

**一句话回答**
CSS 变量运行时生效，Sass 变量编译期替换。

**要点**

- CSS 变量：动态、继承、可切换主题。
- Sass 变量：编译后无法修改。

**详细回答**

- CSS 变量写法：`--color: red; var(--color)`。
- 支持在运行时通过 JS 修改。

**扩展/对比**

- 主题切换推荐 CSS 变量。

---

### 28. 主题切换实现方式

**一句话回答**
常见方式：CSS 变量、data-theme 属性、Tailwind 配置。

**要点**

- CSS 变量结合 JS 修改。
- data-theme 控制全局。
- Tailwind 的暗黑模式支持。

**详细回答**

```css
:root {
  --bg: #fff;
}
[data-theme="dark"] {
  --bg: #000;
}
```

**扩展/对比**

- React 项目中可用 Context + CSS variables。

---

# 七、性能与优化

### 29. 提升 CSS 渲染性能的常见手段 🔥

**一句话回答**
减少回流重绘，使用 GPU 加速，避免复杂选择器。

**要点**

- transform/opacity 替代 left/top。
- will-change 提示优化。
- 合理触发 BFC，减少浮动 bug。

**详细回答**

- 尽量合并 DOM 操作。
- 动画用 `transform: translate`。
- 避免使用层级过深的选择器。

**扩展/对比**

- 面试官常问：如何优化性能？👉 说到 reflow/repaint、合成层。

---

### 30. 首屏渲染优化（Critical CSS）

**一句话回答**
Critical CSS 指提取首屏关键样式内联，其他延迟加载。

**要点**

- 内联关键 CSS。
- 非关键样式延迟加载。
- 减少阻塞渲染。

**详细回答**

- 将首屏必须的 CSS 提取内联 `<style>`。
- 其他 CSS 用 `media` 或 `load` 事件加载。

**扩展/对比**

- Next.js/Vite 都有插件支持 Critical CSS。

---

### 31. 大型项目中 CSS 的组织方式 🔥

**一句话回答**
常见规范有 BEM、ITCSS、OOCSS，用于保持结构清晰和可维护性。

**要点**

- BEM：Block\_\_Element--Modifier。
- ITCSS：分层结构。
- OOCSS：面向对象 CSS。

**详细回答**

- BEM 适合团队协作，命名清晰。
- ITCSS 强调样式按层级组织（工具层、组件层、页面层）。

**扩展/对比**

- 面试官常问：你们团队如何组织 CSS？👉 给出实际规范经验。

---
