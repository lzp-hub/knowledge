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
BFC 是一个独立的渲染区域，内部元素的布局不会影响外部，常用于清除浮动和避免 `margin` 重叠。

---

**要点**

- **触发条件：**
  - `float` 不为 `none`
  - `position: absolute | fixed`
  - `overflow: hidden | auto | scroll`
  - `display: inline-block | flex | grid | flow-root`
- **核心特性：**
  1. 内部元素独立布局，不影响外部
  2. 计算高度时包含浮动元素
  3. 相邻 BFC 的 `margin` 不会重叠
- **应用场景：**
  - 清除浮动导致的父级高度塌陷
  - 避免 `margin` 上下重叠
  - 阻止文字环绕浮动元素

---

**详细回答**

BFC（Block Formatting Context，块级格式化上下文）是 W3C CSS2.1 中的概念。一个元素触发 BFC 后，它的布局规则变得相对独立，外部环境不会影响它，反之亦然。

BFC 的三个常见应用：

1. **清除浮动**：父容器触发 BFC 后，能自动包含子元素的浮动高度。
2. **避免 margin 折叠**：相邻元素的上下 margin 在同一 BFC 内会折叠；不同 BFC 之间不会折叠。
3. **阻止文字环绕**：浮动元素通常会影响文字流，通过 BFC 可以避免。

---

**案例**

1. **清除浮动**

```html
<style>
  .container {
    overflow: hidden; /* 触发 BFC */
    border: 1px solid #333;
  }
  .left {
    float: left;
    width: 100px;
    height: 100px;
    background: lightblue;
  }
</style>

<div class="container">
  <div class="left"></div>
</div>
```

👉 如果不给 `.container` 触发 BFC，高度会塌陷。

---

1. **避免 margin 重叠**

```html
<style>
  .wrapper {
    background: #eee;
  }
  .wrapper.bfc {
    overflow: hidden;
  } /* 触发 BFC，阻止父子折叠 */
  .child {
    margin-top: 20px;
    height: 50px;
    background: #9fe;
  }
</style>

<div class="wrapper">
  <div class="child"></div>
  <!-- 20px 会与父的上边缘折叠到容器外 -->
</div>

<div class="wrapper bfc">
  <div class="child"></div>
  <!-- 20px 体现在容器内部 -->
</div>
```

👉 上：无 BFC，`child` 的 `margin-top:20px` 会“顶出容器”；下：父触发 BFC 后，20px 留在容器内。

---

1. **阻止文字环绕浮动**

```html
<style>
  .float {
    float: left;
    width: 100px;
    height: 100px;
    background: lightblue;
  }
  .text {
    overflow: hidden;
    background: lightyellow;
  } /* 触发 BFC */
</style>

<div class="float"></div>
<div class="text">
  如果不触发 BFC，这段文字会环绕浮动元素。 触发 BFC
  后，文字会避开浮动，排布在下方。
</div>
```

---

**扩展/对比**

- **如何清除浮动？**
  - 传统：`<div style="clear:both"></div>`
  - 现代：父元素触发 BFC（推荐 `display: flow-root`，语义清晰）
- **BFC vs IFC（行内格式化上下文）**
  - BFC：处理块级元素的布局规则
  - IFC：处理行内元素（文字、行内块）的布局规则
- **现代推荐写法：**

```css
.parent {
  display: flow-root; /* 专门用于触发 BFC，清晰且不破坏布局 */
}
```

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
z-index 只在**同一层叠上下文**内比较；一旦元素创建了**新的层叠上下文**，其中的 z-index 就被“隔离”，无法跨上下文比较。

---

**要点**

- **层叠上下文常见触发条件：**
  - `position: relative|absolute|sticky` 且 `z-index` 非 `auto`
  - `position: fixed`（无论 z-index 是否设置）
  - `opacity < 1`、`transform` 非 `none`、`filter` 非 `none`、`perspective` 非 `none`
  - `isolation: isolate`、`contain: paint`、`will-change: transform/opacity/filter`
  - **Flex/Grid 子项**：`z-index` 非 `auto` 的 **flex item / grid item** 也会建立层叠上下文
- **z-index 失效的高频原因：**
  - 不在同一层叠上下文（被上层上下文“隔离”）
  - 元素不是可参与层叠的类型（例如非定位元素；注意 flex/grid 例外）
  - 被祖先的叠层顺序/背景裁剪影响（负 z-index 等）

---

**详细回答**

- **层叠上下文（Stacking Context）**是浏览器的层级分组单位：同一上下文内按 **叠放次序** 绘制；不同上下文之间，**整体作为一个层**参与更外层上下文的比较。
- **关键结论：\*\*子元素的 `z-index` \*\*永远无法\*\*跨越自己最近的层叠上下文去压过外面的元素；想“盖住”外部元素，要么把两者放进\*\*同一层叠上下文**，要么提升**父级（创建上下文的那个元素）**的层级。
- **常见坑：**父元素一旦有 `transform` / `opacity: .999` 等，就创建了新上下文；子元素 `z-index: 9999` 依然被困在里面，压不住外界的兄弟元素。

---

**案例**

1. **“我的弹窗 z-index:9999 也压不住头部”**

```html
<header class="header">HEADER</header>
<main class="page">
  <div class="modal">MODAL</div>
</main>
.header { position: fixed; z-index: 10; } /*
这句无意间创建了新层叠上下文，困住了 .modal */ .page { transform: translateZ(0);
} /* 以为很大就能盖住一切——错，因为被 .page 的上下文隔离 */ .modal { position:
fixed; z-index: 9999; }
```

**现象：** `.modal` 盖不住 `.header`。
**原因：** `.page` 有 `transform`，新上下文把 `.modal` 困住；`.header` 在外层上下文更上面。
**修复：**

- 移除 `.page` 的 `transform`/`opacity` 等触发；或
- 将 `.modal` 移到 `body`（同外层上下文）；或
- 提升 `.page` 自身在外层的层级：给 `.page { position: relative; z-index: 11; }`。

---

1. **“z-index 不生效”其实是没参与层叠**

```html
<div class="card">
  <span class="badge">NEW</span>
</div>
/* ❌ 非定位普通元素（且不是 flex/grid 子项）设置 z-index 无效 */ .badge {
z-index: 10; } /* ✅ 让它参与层叠比较 */ .card { position: relative; } .badge {
position: absolute; top: 0; right: 0; z-index: 10; }
```

> 备注：在 **flex/grid 布局**里，子项即使不设 `position`，只要 `z-index` 非 `auto`，也会创建上下文并可相互遮挡排序。

---

1. **负 z-index 的“消失术”**

```html
<div class="wrap">
  <div class="bg"></div>
  <div class="content">TEXT</div>
</div>
.wrap { position: relative; background: #fff; } .bg { position: absolute; inset:
0; background: #ace; z-index: -1; } .content{ position: relative; z-index: 0; }
```

**现象：** `.bg` 可能被父级背景“盖住”或被裁剪，看似“消失”。
**原因：** 负 z-index 仍受**父级所在上下文**约束，不能跑到父背景下面去。
**建议：\*\*少用负 z-index；改为让 `.content`/其他层\*\*正向提层**更稳妥。

---

1. **Flex 子项的 z-index 排序**

```html
<div class="row">
  <div class="item a">A</div>
  <div class="item b">B</div>
</div>
.row { display: flex; } .item { padding: 20px; } .a { background: #def; z-index:
1; } /* 建立自身上下文并在兄弟上方 */ .b { background: #fed; z-index: 0; }
```

**要点：**flex/grid 子项的 `z-index` 可直接决定兄弟间遮挡，无需 `position`。

---

**扩展/对比**

- **叠放次序（同一上下文内简化记忆）：**

  1. 背景与边框
  2. `z-index` 为负的元素
  3. 常规文档流（含 `z-index:auto` 的定位元素）
  4. 浮动
  5. `z-index >= 0` 的定位元素 / 建立上下文的元素（数值越大越上）

  > 实际更复杂，但记住“负的最底、正的越大越上”即可。

- **常见排障 Checklist：**

  - 是否被**祖先的层叠上下文**困住（`transform/opacity/filter` 等）？
  - 两个元素是否处于**同一上下文**进行比较？
  - 元素是否**参与层叠**（定位 or flex/grid 子项 or 触发上下文）？
  - 是否错误使用**负 z-index**？
  - 需要时可用 `isolation: isolate` **人为切割**上下文，或把浮层挂到 `body`。

- **工程实践：**

  - 建立统一的 z-index 设计体系（如：`--z-dropdown: 1000; --z-modal: 2000; --z-toast: 3000`）。
  - 弹窗/悬浮层统一 **Portal 到 body**，避免被页面局部 `transform`/`opacity` 限制。
  - 审查样式中是否“顺手加了 transform/opacity”导致层叠异常。
  - DevTools → Elements/Layers 查看 **Stacking Context** 与绘制层，定位问题更快。

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

### 9. 圣杯布局 / 双飞翼布局 🔥

**一句话回答**
圣杯布局和双飞翼布局都是传统的三栏布局方案：左右两栏固定宽度，中间栏自适应填充。

---

**要点**

- **圣杯布局**：利用 `float` 和 `padding` 实现，中间内容区域通过 `padding` 留出空间，避免被左右栏覆盖。
- **双飞翼布局**：在中间内容外面再套一层容器，通过 `margin` 留出空间，解决中间内容被遮挡的问题。

---

**详细回答**

1. **圣杯布局**

   - 结构：`header + content + footer`，`content` 内部再包含 `left + center + right` 三栏。
   - 思路：中间区域先写在 DOM 前面，用 `padding` 给左右栏留位置；左右栏用 `float:left/right` 定位，然后用 `margin` 负值把它们“拉回”到中间区域的两侧。
   - 特点：经典、巧妙，但写法相对复杂。

   ```html
   <div class="container">
     <div class="center">center</div>
     <div class="left">left</div>
     <div class="right">right</div>
   </div>
   ```

   ```css
   .container {
     padding: 0 200px; /* 给左右栏留出空间 */
   }
   .center {
     float: left;
     width: 100%;
   }
   .left {
     float: left;
     width: 200px;
     margin-left: -100%;
   }
   .right {
     float: left;
     width: 200px;
     margin-left: -200px;
   }
   ```

---

1. **双飞翼布局**

   - 结构：与圣杯类似，但中间内容再套一层 `.inner` 容器。
   - 思路：中间栏设置 `width:100%` 并 `float:left`，内部 `.inner` 再用 `margin` 左右留出空间，避免被浮动的左右栏遮挡。
   - 特点：结构更直观，解决了圣杯布局“内容可能被覆盖”的问题。

   ```html
   <div class="container">
     <div class="center">
       <div class="inner">center</div>
     </div>
     <div class="left">left</div>
     <div class="right">right</div>
   </div>
   ```

   ```css
   .center {
     float: left;
     width: 100%;
   }
   .inner {
     margin: 0 200px;
   }
   .left {
     float: left;
     width: 200px;
     margin-left: -100%;
   }
   .right {
     float: left;
     width: 200px;
     margin-left: -200px;
   }
   ```

---

**扩展/对比**

- 圣杯 vs 双飞翼：
  - 圣杯用 **padding** 留空，双飞翼用 **额外容器 + margin**。
  - 双飞翼更直观，不会出现中间内容被覆盖的问题。
- **现代开发更推荐 Flex/Grid：**

```css
.container {
  display: flex;
}
.left {
  width: 200px;
}
.right {
  width: 200px;
}
.center {
  flex: 1;
}
```

---

### 10. 多列布局方式对比 🔥

**一句话回答**
多列布局可以用 `float`、`flex`、`grid`、`position` 等方式实现，现代开发首选 **Flex** 和 **Grid**。

---

**要点**

- **float**：传统方案，依赖浮动，需要清除浮动。
- **flex**：简洁，适合一维横向或纵向布局。
- **grid**：强大，适合二维复杂网格。
- **position**：用绝对定位实现，但不推荐常规多列布局。

---

**详细回答**

1. **float 布局**

   - 原理：元素脱离标准文档流，浮动到左侧或右侧。

   - 缺点：需要手动清除浮动（`clearfix`），不适合复杂场景。

   - 示例：

     ```css
     .col {
       float: left;
       width: 33.33%;
     }
     .clearfix::after {
       content: "";
       display: block;
       clear: both;
     }
     ```

2. **flex 布局**

   - 原理：父容器设 `display:flex`，子元素按主轴排列。

   - 优点：语义简洁、响应式友好，支持伸缩。

   - 示例：

     ```css
     .container {
       display: flex;
     }
     .col {
       flex: 1;
     }
     ```

3. **grid 布局**

   - 原理：二维网格，父容器设 `display:grid`，定义行列。

   - 优点：适合复杂排版、响应式布局。

   - 示例：

     ```css
     .container {
       display: grid;
       grid-template-columns: 200px 1fr 200px;
     }
     ```

4. **position 布局**

   - 原理：用 `position:absolute` 定位到指定位置。

   - 缺点：脱离文档流，不利于自适应，维护成本高。

   - 示例：

     ```css
     .left {
       position: absolute;
       left: 0;
       width: 200px;
     }
     .right {
       position: absolute;
       right: 0;
       width: 200px;
     }
     .center {
       margin: 0 200px;
     }
     ```

---

**扩展/对比**

- **float**：历史遗留，现代很少用了。
- **flex**：适合一维（行/列），比如三栏布局。
- **grid**：适合二维网格，比如“头部+侧边栏+内容区+底部”。
- **position**：偶尔用于特殊 UI（悬浮层、固定按钮）。

---

### 11. 水平/垂直居中元素的多种实现方法 🔥

**一句话回答**
常见方式有：Flex、Grid、absolute+transform、line-height、table-cell，现代开发首选 Flex/Grid。

---

**要点**

- **Flex**：`justify-content:center; align-items:center;`
- **Absolute + transform**：`position:absolute; top:50%; left:50%; transform:translate(-50%,-50%)`
- **Grid**：`display:grid; place-items:center;`
- **line-height**：文本单行居中，用 `line-height` 等于容器高度。
- **table-cell**：`display:table-cell; vertical-align:middle; text-align:center;`

---

**详细回答**

1. **Flex 居中（最推荐，现代浏览器支持好）**

   ```css
   .parent {
     display: flex;
     justify-content: center;
     align-items: center;
     height: 300px;
   }
   .child {
     width: 100px;
     height: 100px;
     background: red;
   }
   ```

2. **Grid 居中（更简洁，二维居中一行解决）**

   ```css
   .parent {
     display: grid;
     place-items: center;
     height: 300px;
   }
   .child {
     width: 100px;
     height: 100px;
     background: red;
   }
   ```

3. **Absolute + transform（经典兼容写法）**

   ```css
   .parent {
     position: relative;
     height: 300px;
   }
   .child {
     position: absolute;
     top: 50%;
     left: 50%;
     transform: translate(-50%, -50%);
     width: 100px;
     height: 100px;
     background: red;
   }
   ```

4. **line-height（适合纯文本单行垂直居中）**

   ```css
   .parent {
     height: 100px;
     line-height: 100px;
     text-align: center;
   }
   .child {
     display: inline-block;
     vertical-align: middle;
   }
   ```

5. **table-cell（老方法，兼容 IE 时代常用）**

   ```css
   .parent {
     display: table;
     width: 300px;
     height: 300px;
   }
   .child {
     display: table-cell;
     vertical-align: middle;
     text-align: center;
   }
   ```

---

**扩展/对比**

- **现代开发**：Flex/Grid 简洁、响应式更友好，是首选。
- **兼容性场景**：Absolute+transform 兼容性好，适合老系统。
- **文本居中**：line-height 最快，但局限于单行文本。
- **历史方案**：table-cell 过时，但了解能帮你面试回答更全面。

---

# 三、盒模型与渲染

### 12. 标准盒模型 vs 怪异盒模型 🔥

**一句话回答**
标准盒模型下 `width = content`，怪异盒模型下 `width = content + padding + border`。

---

**要点**

- **标准模型（content-box）**：CSS 默认模式，`width` 只包含内容区，不包含 `padding` 和 `border`。
- **怪异模型（border-box）**：早期浏览器的旧实现，`width` 会把 `padding` 和 `border` 算进去。
- **切换方式**：通过 `box-sizing` 属性显式指定。

**详细回答**

```css
/* 标准 */
box-sizing: content-box;
/* 怪异 */
box-sizing: border-box;
```

border-box 模式下更好算宽度。

**扩展/对比**

- **推荐写法（工程实践）**

  ```
  *, *::before, *::after {
    box-sizing: border-box;
  }
  ```

  → 全局统一使用 `border-box`，宽度计算更直观（总宽度就是写的 width）。

- **面试常见追问**

  - “为什么现在推荐用 `border-box`？” 👉 因为更符合设计稿直观宽度，尤其在复杂布局时更好算。
  - “什么时候还会遇到怪异模型？” 👉 当 HTML 没有声明 `<!DOCTYPE html>` 时，浏览器会回退到 quirks mode。

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
`transition` 用于状态过渡，需要触发条件；`animation` 可独立运行并支持关键帧。

**要点**

- `transition`：两态切换，需触发事件。
- `animation`：基于关键帧，自动执行，可循环。

**详细回答**

- **transition**

  - 通常用在 hover、focus、active 等状态切换。
  - 语法简洁，但不能自动循环。

  ```css
  .btn {
    transition: background 0.3s ease;
  }
  .btn:hover {
    background: red;
  }
  ```

- **animation**

  - 支持复杂关键帧，可以不依赖事件自动执行。
  - 支持 `infinite` 无限循环。

  ```css
  @keyframes loading {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
  .spinner {
    animation: loading 1s linear infinite;
  }
  ```

**扩展/对比**

- 面试常见考法：
  - “按钮 hover 用什么？” 👉 transition。
  - “loading 动画用什么？” 👉 animation。

---

### 17. transform 与 translate 的性能对比 🔥

**一句话回答**
`transform` 使用 GPU 加速，性能优于直接修改 `top/left`。

**要点**

- `transform`：不会触发布局回流，直接进入合成阶段。
- `top/left`：会触发布局（layout），性能差。

**详细回答**

- **使用 transform: translate()**

  ```css
  .box {
    transform: translateX(100px);
  }
  ```

  → 触发 GPU 合成层，避免 layout，性能最佳。

- **使用 top/left**

  ```css
  .box {
    position: relative;
    left: 100px;
  }
  ```

  → 触发布局（reflow）+ 重绘（repaint），对性能影响较大。

**扩展/对比**

- 工程实践：动画和位移统一用 `transform`，比如 `translate3d` 可以显式触发 GPU。

---

### 18. requestAnimationFrame vs CSS 动画 🔥

**一句话回答**
`requestAnimationFrame` 是 JS 驱动，CSS 动画由浏览器优化调度。

**要点**

- CSS 动画：声明式，浏览器可优化，性能好。
- rAF：命令式，灵活，可结合业务逻辑。

**详细回答**

- **CSS 动画**

  - 浏览器可在合成线程执行，不依赖 JS。
  - 适合简单、可预定义的过渡。

  ```css
  .fade-in {
    animation: fadeIn 1s ease-in-out;
  }
  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  ```

- **requestAnimationFrame**

  - 提供 JS 回调，每帧（通常 60FPS）执行一次。
  - 适合 canvas、游戏动画或复杂控制。

  ```js
  function move(el) {
    let pos = 0;
    function step() {
      pos += 2;
      el.style.transform = `translateX(${pos}px)`;
      if (pos < 300) requestAnimationFrame(step);
    }
    requestAnimationFrame(step);
  }
  ```

**扩展/对比**

- 简单交互：CSS 动画更优。
- 逻辑驱动动画（如进度条、canvas 游戏）：用 rAF。

---

### 19. will-change 的作用与风险 🔥

**一句话回答**
`will-change` 提示浏览器未来可能变化的属性，从而提前优化。

**要点**

- 常用于 `transform`、`opacity`。
- 提前创建合成层，减少首帧卡顿。
- 滥用会增加内存和 GPU 占用。

**详细回答**

```css
.card {
  will-change: transform, opacity;
}
```

- 告诉浏览器：此元素即将变化。
- 浏览器会提前分配合成层，加快动画开始时的渲染速度。
- **风险**：如果过多使用，GPU 显存会消耗过大，反而拖慢性能。

**扩展/对比**

- 推荐短期使用，比如在 hover 前设置，动画结束后移除：

  ```js
  el.addEventListener("mouseenter", () => {
    el.style.willChange = "transform";
  });
  el.addEventListener("mouseleave", () => {
    el.style.willChange = "auto";
  });
  ```

---

# 五、响应式与适配

### 20. 媒体查询（media query）的常见写法 🔥

**一句话回答**
媒体查询通过检测设备条件（宽度、分辨率、方向等）应用不同样式，常用于响应式布局。

**要点**

- **宽度相关**
  - `min-width`（大于等于）
  - `max-width`（小于等于）
- **方向相关**
  - `orientation: portrait`（竖屏）
  - `orientation: landscape`（横屏）
- **分辨率/像素密度**
  - `min-resolution` / `max-resolution`（dpi）
  - `min-device-pixel-ratio`（常见于 Retina 屏）

---

**代码示例**

```css
/* 小于等于 768px 的设备 */
@media (max-width: 768px) {
  body {
    font-size: 14px;
  }
}

/* 大于等于 1024px 的设备 */
@media (min-width: 1024px) {
  body {
    font-size: 18px;
  }
}

/* 横屏时 */
@media (orientation: landscape) {
  .video-player {
    width: 80%;
  }
}

/* 高分辨率屏幕（Retina） */
@media (min-device-pixel-ratio: 2), (min-resolution: 192dpi) {
  .logo {
    background-image: url(logo@2x.png);
  }
}
```

---

**详细回答**
媒体查询的核心作用是 **根据设备条件调整样式**，实现响应式布局。

- **小屏设备**（如手机）一般使用 `max-width` 针对窄屏优化，比如字体更小、布局堆叠。
- **大屏设备**（如桌面）则用 `min-width` 设置更复杂的布局，比如多栏显示。
- **屏幕方向**（横/竖屏）在平板/移动端适配场景常用。
- **高分辨率屏幕**（Retina）常用于加载 2x/3x 的图片资源，避免模糊。

---

**扩展/对比**

- **移动端优先**（Mobile First）

  - 默认写小屏样式，再用 `min-width` 覆盖大屏：

    ```css
    body {
      font-size: 14px;
    } /* 默认移动端 */
    @media (min-width: 768px) {
      body {
        font-size: 16px;
      } /* 平板 */
    }
    @media (min-width: 1200px) {
      body {
        font-size: 18px;
      } /* 桌面 */
    }
    ```

- **桌面端优先**

  - 默认写桌面样式，再用 `max-width` 覆盖小屏。

- **现代替代方案**

  - 除了媒体查询，现代布局常用 **Flex/Grid + 百分比/自适应单位**（vw、fr）减少依赖媒体查询。
  - **Container Query（容器查询）** 是新趋势，更细粒度，按容器宽度而非整个视口宽度来适配。

---

### 21. rem / em / px / vw / vh 的区别 🔥

**一句话回答**
`px` 固定，`em` 相对父级字体，`rem` 相对根元素字体，`vw/vh` 相对视口。

---

**要点**

- **px**：绝对像素，固定不随环境变化。
- **em**：相对父元素的 `font-size`。
- **rem**：相对根元素 (`html`) 的 `font-size`。
- **vw/vh**：相对视口宽度/高度（1vw = 视口宽度的 1%，1vh = 视口高度的 1%）。

---

**详细回答**

- **px（像素）**

  - 绝对单位，写死大小。
  - 缺点：不随设备缩放，响应式适配差。

  ```css
  .title {
    font-size: 16px;
  }
  ```

- **em**

  - 计算基准是**父元素的 font-size**。
  - 会层层继承，容易出现“复利”效果。

  ```css
  .parent {
    font-size: 16px;
  }
  .child {
    font-size: 2em;
  } /* = 32px */
  ```

- **rem**

  - 计算基准是**根元素 html 的 font-size**，常用于响应式布局。
  - 一般在移动端会用 JS 动态设置 html 的 font-size。

  ```css
  html {
    font-size: 16px;
  }
  .box {
    font-size: 1.5rem;
  } /* = 24px */
  ```

- **vw / vh**

  - **vw**：视口宽度百分比（100vw = 整个屏幕宽）。
  - **vh**：视口高度百分比（100vh = 整个屏幕高）。
  - 常用于全屏布局或自适应字体。

  ```css
  .full-screen {
    width: 100vw;
    height: 100vh;
  }
  ```

---

**扩展/对比**

- **移动端适配**
  - 常见方案一：`rem` + 动态设置 `html { font-size }`，保证不同屏幕宽度下等比缩放。
  - 常见方案二：`vw`，直接写成视口百分比，简单直观。
- **推荐实践**
  - PC 端：用 `px` + 少量 `rem`。
  - 移动端：用 `rem` 或 `vw`，避免 `px`。
  - 字体/间距：推荐 `rem`，因为只需改 root font-size 就能整体缩放。
  - 全屏组件：推荐 `vh/vw`，如 banner、背景。

---

### 22. 移动端适配方案 🔥

**一句话回答**
移动端常见适配方案包括流式布局、媒体查询、flex、rem、vw/vh，自适应字体，通常会多方案结合。

---

**要点**

- **流式布局**：百分比宽度，随屏幕变化。
- **媒体查询**：针对断点覆盖样式。
- **flex**：弹性盒子，常用于移动端。
- **rem 方案**：通过 JS 设置根字体大小，页面元素按 rem 缩放。
- **vw/vh 方案**：CSS 单位直接相对视口宽高。
- **自适应字体**：配合 rem 或 clamp() 实现。

---

**详细回答**

1. **rem 动态适配**

   - 根据屏幕宽度动态设置 `html` 的 `font-size`。

   - 示例：

     ```js
     // 根据屏幕宽度动态设置根字体大小
     (function () {
       function setRem() {
         document.documentElement.style.fontSize =
           document.documentElement.clientWidth / 10 + "px"; // 1rem = 屏幕宽度/10
       }
       window.addEventListener("resize", setRem);
       setRem();
     })();
     ```

     ```css
     .box {
       width: 2rem;
     } /* 宽度随屏幕缩放 */
     ```

2. **vw/vh 自适配**

   - 直接用视口单位：

     ```css
     .banner {
       width: 100vw;
       height: 50vh;
     }
     .title {
       font-size: 5vw;
     }
     ```

3. **媒体查询 + flex**

   - 常用于断点控制：

     ```css
     @media (max-width: 768px) {
       .menu {
         flex-direction: column;
       }
     }
     ```

---

**扩展/对比**

- **大厂常用方案**
  - 淘宝：rem 动态适配（lib-flexible）。
  - 饿了么 / 京东：vw/vh 单位适配，减少 JS 依赖。
- **面试官常问**：
  - “你们移动端适配是怎么做的？” 👉 rem/vw/vh + 媒体查询。
  - “为什么不用 px？” 👉 不随屏幕缩放，体验差。

---

### 23. 响应式图片与 image-set 🔥

**一句话回答**
响应式图片通过 `srcset` 或 `image-set` 根据设备分辨率或屏幕宽度加载合适的图片，提升清晰度和性能。

---

**要点**

- **srcset**：HTML 属性，适配不同屏幕宽度或 DPR（像素比）。
- **sizes**：配合 srcset 告诉浏览器在不同宽度下选哪个资源。
- **image-set**：CSS 版本，用于背景图。
- **解决痛点**：在 2x/3x 屏幕下防止模糊，同时避免加载过大资源。

---

**代码示例**

- **HTML 响应式图片**：

  ```html
  <img
    src="small.jpg"
    srcset="small.jpg 1x, medium.jpg 2x, large.jpg 3x"
    alt="product"
  />
  ```

- **HTML 根据宽度选择**：

  ```html
  <img
    src="small.jpg"
    srcset="small.jpg 480w, medium.jpg 800w, large.jpg 1200w"
    sizes="(max-width: 600px) 480px, (max-width: 1000px) 800px, 1200px"
    alt="product"
  />
  ```

- **CSS 背景响应式 image-set**：

  ```css
  .banner {
    background-image: image-set(
      "banner@1x.png" 1x,
      "banner@2x.png" 2x,
      "banner@3x.png" 3x
    );
  }
  ```

---

**详细回答**

- **srcset + sizes**：浏览器会根据 DPR 或 viewport 宽度自动选择合适图片。
- **image-set**：作用和 srcset 类似，但用于 CSS 背景图，移动端常用于 banner、广告位。
- **优势**：只下载需要的资源，减少流量和加载时间，同时保证清晰度。

---

**扩展/对比**

- **常见面试题**：
  - “如何适配高清屏？” 👉 `srcset` / `image-set`。
  - “为什么不直接上 3x 大图？” 👉 浪费流量，低端机加载慢。
- **最佳实践**：
  - 小图标：SVG 更推荐。
  - 大图：用响应式图片。

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
通过命名规范、作用域隔离和模块化来避免全局污染。

---

**要点**

- **BEM 命名**：Block\_\_Element--Modifier，降低选择器冲突概率。
- **CSS Modules**：自动生成唯一 class，避免全局共享。
- **Shadow DOM**：原生隔离作用域，常用于 Web Components。
- **作用域约束**：合理使用命名空间或父级 class 限定。

---

**详细回答**

- **全局污染来源**：

  - 选择器过宽（如 `div {}`、`* {}`）。
  - 不同模块使用相同 class 名。
  - 第三方库引入的样式覆盖业务样式。

- **解决方案**：

  1. **BEM 规范**：

     ```css
     .card__title--highlight {
       color: red;
     }
     ```

  2. **CSS Modules**（React 示例）：

     ```jsx
     import styles from "./Button.module.css";
     <button className={styles.primary}>Click</button>;
     ```

  3. **Shadow DOM**：

     ```js
     const shadow = element.attachShadow({ mode: "open" });
     shadow.innerHTML = `<style>p{color:red}</style><p>Hello</p>`;
     ```

  4. **Tailwind 原子类**：通过工具类避免全局层叠。

---

**扩展/对比**

- **小团队**：BEM + 组件化就够。
- **大团队/复杂项目**：CSS Modules、Tailwind 更可靠。
- **Web Components 场景**：推荐 Shadow DOM，彻底隔离。

---

### 27. CSS 变量 vs Sass 变量 🔥

**一句话回答**
CSS 变量运行时生效，Sass 变量编译期替换。

---

**要点**

- **CSS 变量**：动态、可继承、可 JS 修改。
- **Sass 变量**：静态编译，运行时不可变。

---

**详细回答**

- **CSS 变量**：

  ```css
  :root {
    --primary: #007bff;
  }
  button {
    background: var(--primary);
  }
  ```

  ```js
  // JS 动态修改
  document.documentElement.style.setProperty("--primary", "red");
  ```

- **Sass 变量**：

  ```scss
  $primary: #007bff;
  button {
    background: $primary;
  }
  ```

  编译后：

  ```css
  button {
    background: #007bff;
  }
  ```

---

**扩展/对比**

- **主题切换/暗黑模式**：必须用 CSS 变量，因为能运行时切换。
- **简单样式复用**：Sass 变量即可。
- **工程实践**：Sass 提供 mixin、函数，更适合复杂样式逻辑；CSS 变量适合动态场景。

---

### 28. 主题切换实现方式 🔥

**一句话回答**
主题切换常见方式包括 CSS 变量、data-theme 属性控制、Tailwind 的暗黑模式。

---

**要点**

- **CSS 变量**：最灵活，支持运行时切换。
- **data-theme**：全局属性控制不同主题变量。
- **Tailwind**：内置暗黑模式支持（`dark:` 前缀）。

---

**详细回答**

- **CSS 变量方案**（推荐）：

  ```css
  :root {
    --bg: #fff;
    --text: #000;
  }
  [data-theme="dark"] {
    --bg: #000;
    --text: #fff;
  }
  body {
    background: var(--bg);
    color: var(--text);
  }
  ```

  ```js
  // JS 切换主题
  document.documentElement.setAttribute("data-theme", "dark");
  ```

- **Tailwind 暗黑模式**：

  ```js
  // tailwind.config.js
  module.exports = { darkMode: "class" };
  ```

  ```html
  <div class="bg-white dark:bg-black"></div>
  ```

---

**扩展/对比**

- **多主题（浅色/深色/高对比度）**：CSS 变量 + data-theme 最佳。
- **仅暗黑/亮色切换**：Tailwind 自带支持，简单直接。
- **面试延伸**：
  - “如何做暗黑模式？” 👉 CSS 变量。
  - “如何减少主题切换卡顿？” 👉 使用 CSS 变量而不是切换整套 CSS 文件。

---

# 七、性能与优化

---

### 29. 提升 CSS 渲染性能的常见手段 🔥

**一句话回答**
减少回流重绘，合理利用 GPU 加速，避免复杂选择器。

---

**要点**

- **避免频繁回流/重绘**：合并 DOM 操作，批量修改样式。
- **使用 transform/opacity 替代 left/top**：动画走合成层。
- **will-change 提示浏览器优化**：提前准备合成层。
- **减少复杂选择器**：尽量避免层级过深或通配符选择器。

---

**详细回答**

- **回流 (Reflow)**：影响布局计算，例如修改 `width`、`height`、`top`。
- **重绘 (Repaint)**：影响元素外观（如 `color`、`background`），但不改布局。
- **合成层优化**：`transform` 和 `opacity` 通常只触发合成，不会引起回流重绘。

**代码案例**

```css
/* ❌ 性能差：top 会触发回流 */
.box {
  position: absolute;
  top: 100px;
  transition: top 0.3s;
}

/* ✅ 推荐：transform GPU 加速 */
.box {
  transform: translateY(100px);
  transition: transform 0.3s;
}
```

---

**扩展/对比**

- **面试常考**：区别 reflow 与 repaint，如何减少性能消耗。
- **工程经验**：列表渲染时，避免频繁修改 DOM，推荐用 `requestAnimationFrame` 或虚拟滚动。

---

### 30. 首屏渲染优化（Critical CSS） 🔥

**一句话回答**
Critical CSS 指提取首屏关键样式内联，非关键样式延迟加载。

---

**要点**

- **关键 CSS 内联**：减少阻塞渲染。
- **非关键样式异步加载**：`media` 或 `preload`。
- **减少 render-blocking**：避免加载过多外部 CSS 阻塞渲染。

---

**详细回答**

- **做法**：
  1. 提取首屏关键 CSS，内联在 `<head>`。
  2. 其他 CSS 使用 `link` 标签延迟加载。
  3. 可结合构建工具自动化（Next.js、Vite 插件）。

**代码案例**

```html
<!-- 内联首屏关键 CSS -->
<style>
  body {
    font-family: sans-serif;
  }
  header {
    height: 60px;
    background: #fff;
  }
</style>

<!-- 延迟加载非关键 CSS -->
<link
  rel="preload"
  href="other.css"
  as="style"
  onload="this.rel='stylesheet'"
/>
```

---

**扩展/对比**

- **Next.js**：自动 Critical CSS 提取，内联在 HTML 中。
- **Vite/webpack**：可用插件 `critters` 实现。
- **面试常问**：如何优化首屏？👉 提到 Critical CSS + 懒加载资源。

---

### 31. 大型项目中 CSS 的组织方式 🔥

**一句话回答**
大型项目通常采用 BEM、ITCSS、OOCSS 等规范保持结构清晰和可维护性。

---

**要点**

- **BEM**：Block\_\_Element--Modifier，命名清晰，适合组件化。
- **ITCSS**：分层（工具层、基础层、组件层、页面层），强调架构。
- **OOCSS**：抽象样式，复用性强。
- **CSS Modules / Tailwind**：现代项目常用的模块化方案。

---

**详细回答**

- **BEM 例子**

  - **Block**：`card`，代表一个独立的卡片组件。
  - **Element**：`card__title`、`card__button`，只能存在于 `card` 内部。
  - **Modifier**：`card--highlight`、`card__button--primary`，用来表示不同状态/样式。

  ```css
  .card__title--highlight {
    color: red;
  }
  ```

- **ITCSS 层级**

  1. **Settings**（变量）
  2. **Tools**（函数、混入）
  3. **Generic**（reset）
  4. **Elements**（基础标签样式）
  5. **Objects**（布局容器）
  6. **Components**（UI 组件）
  7. **Utilities**（工具类）

- **OOCSS 思路**：

  - **结构**和**皮肤**分离，例如按钮结构和主题样式独立维护。

---

**扩展/对比**

- **小型项目**：BEM 就够了。
- **中大型团队**：ITCSS + CSS Modules 更好维护。
- **现代趋势**：Tailwind/原子化 CSS，减少命名负担，提高复用性。
- **面试常问**：你们团队如何组织 CSS？👉 可以结合 BEM + CSS Modules 实际经验作答。

---
