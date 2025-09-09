## CSS相关经验及面试题

## 说说你对盒子模型的理解

当对一个文档进行布局（layout）的时候，浏览器的渲染引擎会根据标准之一的 CSS 基础框盒模型（CSS basic box model），将所有元素表示为一个个矩形的盒子（box）

一个盒子由四个部分组成：`content`、`padding`、`border`、`margin`

> + `content`，即实际内容，显示文本和图像
> + `boreder`，即边框，围绕元素内容的内边距的一条或多条线，由粗细、样式、颜色三部分组成
> + `padding`，即内边距，清除内容周围的区域，内边距是透明的，取值不能为负，受盒子的`background`属性影
> + `margin`，即外边距，在元素外创建额外的空白，空白通常指不能放其他元素的区域

在`CSS`中，盒子模型可以分成：

- **W3C 标准盒子模型**

  ![](https://static.vue-js.com/c0e1d2e0-8f9b-11eb-85f6-6fac77c0c9b3.png)

  从上图可以看到：

  - 盒子总宽度 = width + padding + border + margin;
  - 盒子总高度 = height + padding + border + margin

  也就是，**`width/height` 只是content，不包含 `padding` 和 `border `值**

  ```css
  <style>
  .box {
  		width: 200px;
  		height: 100px;
      padding: 20px;
  }
  </style>
  <div class=\"box\">
  	盒子模型
  </div>
  ```

  所以，设置`width`为200px，但由于存在`padding`，但实际上盒子的宽度有240px

- **IE 怪异盒子模型**

  ![](https://static.vue-js.com/cfbb3ef0-8f9b-11eb-ab90-d9ae814b240d.png)

  从上图可以看到：

  - 盒子总宽度 = width + margin;
  - 盒子总高度 = height + margin;

  也就是，**`width/height` 包含了`content`、 `padding `和 `border `值**

### Box-sizing

CSS 中的 box-sizing 属性定义了引擎应该如何计算一个元素的总宽度和总高度

> - content-box 默认值，元素的 width/height 不包含padding，border，与标准盒子模型表现一致
> - border-box 元素的 width/height 包含 padding，border，与怪异盒子模型表现一致
> - inherit 指定 box-sizing 属性的值，应该从父元素继承

```html
<style>
.box {
		width: 200px;
		height: 100px;
    padding: 20px;
    box-sizing: border-box;
}
</style>
<div class=\"box\">
	盒子模型
</div>
```

这时候，就可以发现盒子的所占据的宽度为200px

### 块级盒子

元素的**display**属性设置为**block、list-item**或者**table**的时候，该元素就称为**块级元素（block-level element）**，每个块级元素至少会生成一个**块级盒子（block-level box），每个块级盒子都会参与块格式化上下文的创建。**

### 行内盒子

元素的**display**属性设置为**inline、inline-block**或者**inline-table**的元素就称为**行内级元素（inline-level element）**，**行内级盒子（inline-level box）由行内级元素生成，参与行内格式化上下文创建的行内级盒子称为行内盒子（inline box）**

### 匿名块盒子

某些情况下，在进行视觉格式化的时候，需要添加一些増补性的盒子，这些盒子不能用CSS选择符选中，所以称为**匿名盒子（anonymous box）**，CSS选择器不能作用于匿名盒子，所以它不能被样式表赋予样式，因此所有可继承的CSS属性值都为inherit，而所有不可继承的CSS属性值都为initial。以下例子可以产生匿名块盒子：

```css
<div>文本文本文本文本<p>p标签包含的内容</p>文本文本文本文本</div>
```

## 文档流

`CSS` 页面布局技术允许我们拾取网页中的元素，并且控制它们相对正常布局流（普通流）、周边元素、父容器或者主视口/窗口的位置。技术布局从宏观上来说是受定位方案影响，定位方案包括**普通流**（Normal Flow，也叫常规流，正常布局流），**浮动**（Float），**定位技术**（Position）。

### 普通流

浏览器默认的 `HTML` 布局方式，此时浏览器不对页面做任何布局控制。元素按照其在 HTML 中的先后位置至上而下布局，在这个过程中，行内元素水平排列，直到当行被占满然后换行。块级元素则会被渲染为完整的一个新行。

当 **`position` 为 `static` 或 `relative`，并且 `float` 为 `none` 时会触发普通流**，普通流有以下特性：

+ 在普通流的块格式化上下文(BFC)中，盒子会**垂直依次排列**
+ 在普通流的行内格式化上下文(IFC)中，盒子会**水平依次排列**
+ 对于静态定位，即position为static时，每个盒子会**根据普通流所计算出的确切位置来定位**
+ 对于相对定位，即position为relative时，每个盒子还会**根据属性top、bottom、left和right在原本所在的位置上发生指定大小的偏移，不管这个盒子偏移距离多远，原来的位置都不会被其他元素占用**

### 浮动

- 浮动定位中，盒称为浮动盒（Floating Box）
- 浮动到当前所在行的**开始位置或者结尾位置**
- **普通流中的其他内容会“流”到浮动盒子的边缘处，除非元素通过clear清除前面的浮动。**

**clear属性** 确保当前元素的左右两侧不会有浮动元素。clear只对**元素本身**的布局起作用。 取值：left、right、both

#### 高度塌陷

父元素没有设置高度，子元素添加浮动脱离文档流时，父元素会出现高度塌陷

1. **子元素结尾处使用空元素，如`<div class="clear"></div> (.clear{clear:both})`** 原理：添加一对空的DIV标签，利用css的clear:both属性清除浮动，让父级DIV能够获取高度。 优点：浏览器支持好 缺点：多出了很多空的DIV标签，如果页面中浮动模块多的话，就会出现很多的空置DIV了，这样感觉视乎不是太令人满意。（不推荐使用）

2. **给父级div定义 高度** 原理：给父级DIV定义固定高度（height），能解决父级DIV 无法获取高度得问题。 优点：代码简洁 缺点：**高度被固定**死了，是适合内容固定不变的模块。（不推荐使用）

3. **让父级div 也一并浮起来** 这样做可以初步解决当前的浮动问题。但是也让父级浮动起来了，又会产生新的浮动问题。 不推荐使用

4. **父级div定义 display:table** 原理：将div属性强制变成表格 优点：不解 缺点：**会产生新的未知问题**。（不推荐使用）

5. **父级设置 overflow：hidden、auto；** 原理：这个方法的关键在于触发了**BFC**。 优点：代码简单，不存在结构和语义化问题 缺点：无法显示需要溢出的元素或者定位超出边界不显示（亦不太推荐使用）

6. **父级div定义 伪元素:after 和 zoom**

   > 原理：IE8以上和非IE浏览器才支持`:after`，原理和方法2有点类似，`zoom`(IE转有属性)可解决ie6,ie7浮动问题 优点：结构和语义化完全正确,代码量也适中，可重复利用率（建议定义公共类） 缺点：代码不是非常简洁（极力推荐使用）
   >
   > ```css
   > .clearfix:after{
   >       content:'';
   >       display:block;
   >       height:0;
   >       clear:both;
   >       visibility: hidden;
   >      overflow:hidden;
   > }
   > .clearfix {zoom:1;} /* IE6、7，可以激活BFC */
   > ```

------

### 绝对定位

如果元素的属性 `position` 为 `absolute` 或 `fixed`，它就是一个绝对定位元素。

在绝对定位布局中，元素会整体脱离普通流，因此绝对定位元素不会对其兄弟元素造成影响，而元素具体的位置由绝对定位的坐标决定。

它的定位相对于它的包含块，相关CSS属性：`top`、`bottom`、`left`、`right`；

对于 `position: absolute`，元素定位将相对于上级元素中最近的一个`relative、fixed、absolute`，如果没有则相对于body；

### position 几个属性的作用

- **static:默认位置。** 在一般情况下，我们不需要特别的去声明它，但有时候遇到继承的情况，我们不愿意见到元素所继承的属性影响本身，从而可以用position:static取消继承，即还原元素定位的**默认值**。设置为 static 的元素，它始终会处于页面流给予的位置(**static 元素会忽略任何 top、 bottom、left 或 right 声明**)。一般不常用。

- **relative:相对定位。** 相对定位是相对于元素默认的位置的定位，它偏移的 top，right，bottom，left 的值都以它原来的位置为基准偏移，而不管其他元素会怎么 样。注意 **relative 移动后的元素在原来的位置仍占据空间**。

- **absolute:绝对定位。** 设置为 absolute 的元素，如果它的 父容器设置了 position 属性，并且 position 的属性值为 absolute 或者 relative，那么就会依据父容器进行偏移。如果其父容器没有设置 position 属性，那么偏移是以 **body** 为依据。注意设置 absolute 属性的元素在**标准流中不占位置**。

- **fixed:固定定位。** 位置被设置为 fixed 的元素，可定位于相对于**浏览器窗口**的指定坐标。不论窗口滚动与否，元素都会留在那个位置。它始终是以 body 为依据的。 注意设置 fixed 属性的元素在**标准流中不占位置**。

- **sticky粘性定位。**在屏幕范围（viewport）时该元素的位置并不受到定位影响（设置是top、left等属性无效），当该元素的位置将要移出偏移范围时，定位又会变成fixed，根据设置的left、top等属性成固定位置的效果。

  sticky属性有以下几个特点：

  > - 该元素并不脱离文档流，仍然保留元素原本在文档流中的位置。
  > - 当元素在容器中被滚动超过指定的偏移值时，元素在容器内固定在指定位置。亦即如果你设置了top: 50px，那么在sticky元素到达距离相对定位的元素顶部50px的位置时固定，不再向上移动。

### 块格式化上下文（BFC）

https://juejin.cn/post/6844903902047895559

块格式化上下文（Block Formatting Context，BFC）,**是一个独立的渲染区域**，拥有一套渲染规则来约束这个区域中元素的布局，**该区域中元素的布局不会受到外界的影响**。

### BFC 的创建

display属性值为 **block list-item table**的元素,才会产生BFC,即块级元素

触发条件：

- **根元素（`<html>`）**
- **浮动元素（`float` 不为 `none`）**
- **绝对定位元素（`position` 为 `absolute` 或 `fixed`）**
- 表格的标题和单元格（`display` 为 `table-caption`，`table-cell`）
- 匿名表格单元格元素（`display` 为 `table` 或 `inline-table`）
- **行内块元素（`display` 为 `inline-block`）**
- **overflow的值不为 `visible` 的元素**
- **弹性元素（`display` 为 `flex` 或 `inline-flex` 的元素的直接子元素）**
- 网格元素（`display` 为 `grid` 或 `inline-grid` 的元素的直接子元素）
- **display值为flow-root的元素**

### BFC的布局规则(特性)

1. 在BFC中，盒子从顶端沿着垂直方向一个接一个地排列
2.  两个相邻盒子之间的垂直间隙是由他们的margin值所决定的，**在同一个BFC中，两个相邻**的块级盒子的垂直外边距会产生重叠。
3. 在BFC中，每一个盒子的左外边缘（margin-left）会**触碰**到容器的左边缘（border-left）。对于从右到左的格式来说，则是右外边缘触碰到容器的右边缘。即使存在浮动也是一样的。
4. BFC区域**不会和float的盒子重叠**。
5. 计算BFC区域高度的时候，所有元素包括**float元素都要参与计算高度**
1. BFC在页面上是一个隔离的独立容器，容器里面的子元素不会影响到外面的元素

### BFC的应用

+ 防止外边距margin折叠

  > ![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/29/16c3ca61e93ec40f~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.image)
  >
  > 属于同一个BFC的两个相邻盒子的margin会发生重叠，具体表现为**同正取最大的，同负取绝对值最大的，一正一负，相加**。
  >
  > + 可以单一个方向margin值，如都向下margin；或者一个使用margin,一个使用padding
  > + 也可以给元素增加个父即，在父级上创建不属于同一个BFC,这样就不会发生margin重叠。
  >
  > ```html
  > <style>
  > .box1 {
  >  width: 100px;
  >  height: 100px;
  >  background-color: khaki;
  >  margin: 20px;
  > }
  > .box2 {
  >  width: 200px;
  >  height: 200px;
  >  background-color: powderblue;
  >  margin: 20px;
  > }
  > .box2-parent {
  >  display: flow-root;/* 添加父元素创建不同的BFC，就不会发生margin重叠 */
  > }
  > </style>
  > <!-- <div class="box1"></div>
  > <div class="box2"></div> -->
  > <div class="box1"></div>
  > <div class="box2-parent">
  > 	<div class="box2"></div>
  > </div>
  > ```

+ 自适应多栏布局，两栏，三栏布局

  > 利用 `特性③` 和 `特性④`，中间栏创建 `BFC`，左右栏宽度固定后浮动。由于盒子的 margin box 的左边和包含块 border box 的左边相接触，同时浮动盒的区域不会和 `BFC` 重叠，所以中间栏的宽度会自适应
  >
  > ```html
  >  		<style>
  >       .box1 {
  >         width: 100px;
  >         height: 100px;
  >         background-color: khaki;
  >         float: left;
  >       }
  >       .box2 {
  >         display: flow-root; /* 添加这句解决 BFC */
  >         height: 100px;
  >         background-color: powderblue;
  >       }
  >     </style>
  >     
  >     <div class="box1"></div>
  >     <div class="box2"></div>
  > ```

+ 清除元素内部浮动，高度坍塌

  > 利用 `特性⑤`，`BFC` 内部的浮动元素也会参与高度计算，可以清除 `BFC` 内部的浮动
  >
  > ```html
  >     <style>
  >         .box {
  >             margin: 100px;
  >             width: 100px;
  >             height: 100px;
  >             background: red;
  >             float: left;
  >         }
  >         .container {
  >             background: #000;
  >             display: flow-root; /* 生成BFC，解决高度坍塌 */
  >         }
  >     </style>
  >     <div class="container">
  >         <div class="box"></div>
  >         <div class="box"></div>
  >     </div>
  > ```

------

###  em/px/rem/vh/vw 这些单位有什么区别？

**px**：绝对单位，页面按精确像素展示。

**em**：相对单位，相对于当前对象内文本的字体尺寸基准点为父节点字体的大小(font-size)。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸（`1em = 16px`）任意浏览器的默认字体高都是 16px

**rem**：相对单位，可理解为`root em`, 总是相对根节点`html`的字体大小来计算。例如 ：html {font-size: 62.5%;  }

**vh、vw**：主要用于页面视口大小布局，在页面布局上更加方便简单

> - 在桌面端，指的是浏览器的可视区域
> - 移动端指的就是布局视口

------

### 移动端响应式不布局的方式

1. **媒体查询 + rem** 

   ```css
   html,
   body {
       height: 100%;
   }
   @media screen and (max-width:320px) {
       html {
           font-size: 12px;
       }
   }
   @media screen and (min-width:321px) and (max-width:480px) {
       html {
           font-size: 14px;
       }
   }
   @media screen and (min-width:481px) and (max-width:720px) {
       html {
           font-size: 16px;
       }
   }
   @media screen and (min-width:721px) and (max-width:768px) {
       html {
           font-size: 18px;
       }
   }
   @media screen and (min-width:769px) and (max-width:1024px) {
       html {
           font-size: 20px;
       }
   }
   @media screen and (min-width:1025px) {
       html {
           font-size: 22px;
       }
   }
   ```

2. 阿里巴巴提供的开源插件 **flexble.js**

   > 1. 使用flexble.js，将默认的meta （name="viewport"）标签删除
   >
   > 2. 引入flexble.js文件到当前的html文件中（尽量的放在head）
   >
   > 3. 在flexble.js中,修改设计图的refreshRem函数尺寸数据640 === 6.4 || 750 === 7.5
   >
   >    ```js
   >         function refreshRem(){
   >             var width = docEl.getBoundingClientRect().width;
   >             console.log(width)
   >             if (width / dpr > 768) {
   >                 width = 768 * dpr;
   >             }
   >             var rem = width / 6.4;//根据设计图尺寸修改  750 >>> 7.5
   >             docEl.style.fontSize = rem + 'px';
   >             flexible.rem = win.rem = rem;
   >         }
   >    ```

3. flexble.js,配合webpack **px2rem-loader**

   1. 依赖安装 px2rem-loader

   ```js
   npm i px2rem-loader -D
   // 可以使⽤⼿淘的lib-flexible库，⻚⾯渲染时动态计算根元素的 font-size 值
   
   npm i lib-flexible -S
   // 在在html模版上通过<script>lib-flexible代码</script>引用,在没有配置资源内联的情况下，只能通过复制代码，而不能通过src引入,
   ```

   2. loader使用

   ```js
   module.exports = {
       ......
       module: {
           rules: [
               {
                   test: /.less$/,
                   use: [
                       'style-loader',
                       'css-loader',
                       'less-loader',
                        // px ⾃动转换成 rem
                       {
                           loader: 'px2rem-loader',
                           options: {
                               remUnit: 75,// rem相对于px的转化单位，1rem 转换为 75px   
                               remPrecision: 8 // px转化为rem时，其小数点的位数
                           }
                       }
                   ]
               },
           ]
       },
   };
   ```


4. **vw + rem** 

   >  **计算出100px占多少个vw，再将其设置为html的font-size**，之后使用rem来书写
   >
   >  (100px /  640px) * 100vw = 15.625vw。除部分特小字体外，全部使用rem为单位。
   >
   > ```css
   > html {
   >         font-size: 15.625vw; /* 此时1rem  == 100px */
   >     }
   > ```

5. **vw + sass里面function**，通过function来计算px占多少vw，直接用px来书写

   ```css
   @function vw ($px) {
     	/* 如果效果图是750，就除以750; */
       @return ($px / 640) * 100vw;
   		}
           
   @function vh ($px) {
       @return ($px / 1136) * 100vh;
   }
   /* @import "@/assets/sass/vw.scss";
   height: vw(100); */
   
   ```

###  CSS中，有哪些方式可以隐藏页面元素？有什么区别?

- display:none

  > 将元素设置为`display:none`后，元素在页面上将**彻底消失**元素本身占有的空间就会被其他元素占有。
  >
  > 消失后，自身绑定的事件不会触发，也不会有过渡效果
  >
  > 特点：元素不可见，**不占据空间，会重排和重绘，无法响应点击事件**

- visibility:hidden

  > 从页面上仅仅是隐藏该元素，DOM结果均会存在，只是当时在一个不可见的状态，给人的效果是隐藏了，所以他自身的事件不会触发
  >
  > 特点：元素不可见，**占据页面空间，不重排会重绘，无法响应点击事件**

- opacity:0

  > `opacity`属性表示元素的**透明度**，将元素的透明度设置为0后，在我们用户眼中，元素也是隐藏的
  >
  > 不会引发重排，一般情况下也会引发重绘
  >
  > 由于其仍然是存在于页面上的，所以他自身的的事件仍然是可以触发的，但被他遮挡的元素是不能触发其事件的
  >
  > 需要注意的是：其子元素不能设置opacity来达到显示的效果
  >
  > 特点：改变元素透明度，元素不可见，**占据页面空间，不重排可能会重绘，可以响应点击事件**

- 设置height、width模型属性为0

  > 将元素的`margin`，`border`，`padding`，`height`和`width`等影响元素盒模型的属性设置成0，如果元素内有子元素或内容，还应该设置其`overflow:hidden`来隐藏其子元素
  >
  > 特点：元素不可见，**不占据页面空间，会重排和重绘，无法响应点击事件**


------

### 怎么理解回流跟重绘？什么场景下会触发？

在`HTML`中，每个元素都可以理解成一个盒子，在浏览器解析过程中，会涉及到回流与重绘：

- 回流：布局引擎会根据各种样式计算每个盒子在页面上的**大小与位置**，计算这些值的过程称为回流或重排

  > 对 `DOM` 的修改引发了 `DOM `几何尺寸的变化（比如修改元素的宽、高或隐藏元素等）时

- 重绘：当计算好盒模型的位置、大小及其他属性后，浏览器根据每个盒子**特性进行绘制**

  >  重绘是改变不影响元素在网页中的位置的元素样式时 例如`color`或`background-color`或border-color或visibility，却并未影响其几何属性时

​      具体的浏览器解析渲染机制如下所示：

![](https://static.vue-js.com/2b56a950-9cdc-11eb-ab90-d9ae814b240d.png)

- 解析HTML，生成DOM树，解析CSS，生成CSSOM树(css规则树)
- 将DOM树和CSSOM树结合，生成渲染树(Render Tree)
- Layout(回流):根据生成的渲染树，进行回流(Layout)，**得到节点的几何信息（位置，大小）**
- Painting(重绘):根据渲染树以及回流得到的几何信息，**得到节点的绝对像素**
- Display:将像素发送给GPU，展示在页面上

### 回流触发时机

回流这一阶段主要是计算节点的**位置和几何**信息，那么当页面布局和几何信息发生变化的时候，就需要回流，如下面情况：

- **添加或删除**可见的DOM元素
- 元素的**位置**发生变化
- 元素的**大小**发生变化（包括外边距、内边框、边框大小、高度和宽度等，即盒子尺寸）
- 内容发生变化，比如文本变化或图片被另一个不同尺寸的图片所替代
- 页面一开始渲染的时候（这避免不了）
- 浏览器的**窗口尺寸**变化（因为回流是根据视口的大小来计算元素的位置和大小的）

还有一些容易被忽略的操作：获取一些特定属性的值

> offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、clientTop、clientLeft、clientWidth、clientHeight

这些属性有一个共性，就是**需要通过即时计算得到**。因此浏览器为了获取这些值，也会进行回流

除此还包括`getComputedStyle `方法，原理是一样的

**触发回流一定会触发重绘**

### 重绘触发时机

- 颜色相关的修改
- 文本方向的修改
- 阴影的修改
- visibility，

### 浏览器优化机制

由于每次回流（重排）都会造成额外的计算消耗，因此大多数浏览器都会通过**队列化修改并批量执行**来优化重排过程。浏览器会将修改操作放入到队列里，直到过了一段时间或者操作达到了一个阈值，才清空队列

当你获取布局信息的操作的时候，会强制队列刷新，包括前面讲到的`offsetTop`等方法都会返回最新的数据。因此浏览器不得不清空队列，触发回流重绘来返回正确的值

### 如何减少

我们了解了如何触发回流和重绘的场景，下面给出避免回流的经验：

- 如果想设定元素的样式，通过改变元素的 `class` 类名 (尽可能在 DOM 树的最里层)

  ```html
  <style>
      .basic_style {
          width: 100px;
          height: 200px;
          border: 10px solid red;
          color: red;
      }
  </style>
  <script>
      const container = document.getElementById('container')
      container.classList.add('basic_style')
  </script>
  ```

- 我们还可以通过通过设置元素属性`display: none`，将其从页面上去掉，然后再进行后续操作，这些后续操作也不会触发回流与重绘，这个过程称为离线操作

  ```js
  let container = document.getElementById('container')
  container.style.display = 'none'
  container.style.width = '100px'
  container.style.height = '200px'
  container.style.border = '10px solid red'
  container.style.color = 'red'
  ...（省略了许多类似的后续操作）
  container.style.display = 'block'	
  ```

- 对于那些复杂的动画，对其设置 `position: fixed/absolute`，尽可能地使元素脱离文档流，从而减少对其他元素的影响

- 每次循环都需要获取多次`offset`属性，比较糟糕，可以使用变量的形式缓存起来，待计算完毕再提交给浏览器发出重计算请求

  ```js
  // 缓存offsetLeft与offsetTop的值
  const el = document.getElementById('el') 
  let offLeft = el.offsetLeft, offTop = el.offsetTop
  
  // 在JS层面进行计算
  for(let i=0;i<10;i++) {
    offLeft += 10
    offTop  += 10
  }
  
  // 一次性将计算结果应用到DOM上
  el.style.left = offLeft + "px"
  el.style.top = offTop  + "px"
  ```

- 使用css3硬件加速，可以让**`transform`、`opacity`、`filters`**这些动画不会引起回流重绘

- 避免设置多项内联样式

- 避免使用 `table` 布局，`table` 中每个元素的大小以及内容的改动，都会导致整个 `table` 的重新计算

- 避免使用 CSS 的 `JavaScript` 表达式

### CSS 引入的方式有哪些? link 和@import 的区别是?

有四种：内联(元素上的style属性)、内嵌(style标签)、外链(link)、导入(@import) 

link和@import的区别：

1. 从属关系：link是html的标签，不仅可以加载 CSS 文件，还可以定义 RSS、rel 连接属性等；而@import是css的语法，只有导入样式表的作用。

2. 加载顺序：页面被加载时，link会和html同时被加载；而@import引入的 CSS 将在页面加载完毕后被加载。

3. 兼容性：@import是 CSS2.1 才有的语法，所以只能在 IE5以上 才能识别；而link是 HTML 标签，所以不存在兼容性问题。

4. DOM：javascript可以控制dom去改变link标签引入的样式，而@import的样式不是dom可以控制的。

5. link方式的样式权重高于@import的权重。

------

### 两个inline-block元素靠近为什么有间隙?

当两个行内块元素（display:inline-block）在同一行并排时,它们之间会存在着间距。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fe6aabcb944a448e97e85bfae861fdab~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

```html
  <style>
    .child{
      display: inline-block;
      width: 100px;
      height: 100px;
      background: pink;
    }
  </style>
  <div class="parent">
    <div class="child">child1</div>
    <div class="child">child2</div>
  </div>
```

**原因**

元素被当成行内元素排版的时候，**元素之间的`空白符（空格、回车换行等）`**都会被浏览器处理，根据white-space的处理方式（默认是normal，合并多余空白），**原来HTML代码中的回车换行被转成一个空白符，在字体不为0的情况下，空白符占据一定宽度，所以inline-block的元素之间就出现了空隙**。这些元素之间的间距会随着字体的大小而变化，当行内元素font-size:16px时，间距为8px。

**解决方法**

1. 解决元素之间的空白符

   ```html
   <!-- 将子元素前一个标签结束符和后一个标签开始符写在同一行 -->
     <div class="child">child1
     </div><div class="child">child2
   </div>
   <!-- 或者将所有子元素写在同一行 -->
     <div class="child">child1</div><div class="child">child2</div>
   ```

2. 为inline-block元素添加样式float:left

   > float布局会有高度塌陷问题，不推荐

3. 一般解：为父元素中设置font-size: 0，在子元素上重置正确的font-size

   ```html
   <div class="parent" style="font-size: 0px">
     <div class="child" style="font-size: 16px">child1</div>
     <div class="child" style="font-size: 16px">child2</div>
   </div>
   ```

4. **最优解**：为父元素设置`**word-spacing**`为负值，例如-1em

   > 仅作用于**空格字符**，即只用来增加空格的间隙宽度

   ```css
   .parent{
     word-spacing:-1em; 
   }
   ```

------

### 消除图片底部间隙的方法

1. 图片块状化 - 无基线对齐：`img { display: block; }`
2. 图片底线对齐：`img { vertical-align: bottom; }`
3. 行高足够小 - 基线位置上移：`.box { line-height: 0; }`

------

### 两栏布局5

左侧固定 + 右侧自适应布局

```html
<div class="outer">
  <div class="left">左侧</div>
  <div class="right">右侧</div>
</div>
```

1. 利用浮动，左边元素宽度固定 ，设置向左浮动。将**右边元素的 `margin-left`** 设为固定宽度 。

   ```css
   .outer {
     height: 100px; 
   }  													
   .left {
     float: left;
     width: 200px; 
     height: 100%;
     background: lightcoral;
   }
   /* 因为右边元素的 `width` 默认为 `auto` ，所以会自动撑满父元素。 */
   .right {
     margin-left: 200px; /* */
     height: 100%;
     background: lightseagreen;
   }
   ```

2. 利用浮动，左边元素宽度固定 ，设置向左浮动。右侧元素设置 `overflow: auto;` 这样**右边就触发了 `BFC`** ，`BFC` 的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠。

   ```css
   .outer {
     height: 100px; 
   }  													
   .left {
     float: left;
     width: 200px; 
     height: 100%;
     background: lightcoral;
   }
   .right {
     overflow: auto; /* BFC 的区域不会与浮动元素发生重叠 */
     height: 100%;
     background: lightseagreen;
   }
   ```

3. 利用 `flex` 布局，父级元素设为弹性盒，左边元素固定宽度，右边的元素设置 `flex: 1` 。

   ```css
   .outer {
     display:flex;
     height: 100px; 
   }  													
   .left {
     width: 200px; 
     height: 100%;
     background: lightcoral;
   }
   .right {
    	flex: 1; /* */
     height: 100%;
     background: lightseagreen;
   }
   ```

4. 利用绝对定位，父级元素设为相对定位。**左边元素 `absolute` 定位**（脱离文档流了），宽度固定。右边元素的 `margin-left` 的值设为左边元素的宽度值。

   ```css
   .outer {
     position: relative;
     height: 100px;
   }
   .left {
     position: absolute;
     width: 200px;
     height: 100%;
     background: lightcoral;
   }
   .right {
     margin-left: 200px;
     height: 100%;
     background: lightseagreen;
   }
   ```

5. 利用绝对定位，父级元素设为相对定位。左边元素宽度固定，**右边元素 `absolute` 定位**， `left` 为宽度大小，其余方向定位为 `0` 。

   ```css
   .outer {
     position: relative;
     height: 100px;
   }
   .left {
     width: 200px;
     height: 100%;
     background: lightcoral;
   }
   .right {
     position: absolute;
     left: 200px;
     top: 0;
     right: 0;
     bottom: 0;
     height: 100%;
     background: lightseagreen;
   }
   ```

### 三栏布局2

1. 两边使用 float，中间使用 margin，**中间元素的在最后面**

   ```html
       <style>
           .wrapper div {
               height: 200px;
           }
   
           .left {
               width: 200px;
               float: left;
               background: gray;
           }
           .right {
               width: 150px;
               float: right;
               background: gray;
           }
           .main {
               margin-left: 200px;
               margin-right: 150px;
               background: cyan;
           }
         </style>
          
           <div class="wrapper">
           <div class="left">左侧</div>
           <div class="right">右侧</div>
           <div class="main">中间</div>
       </div>
   ```

2.  两边使用 float，中间栏创建 BFC，**中间元素的在最后面**

   ```html
       <style>
           .wrapper div {
               height: 200px;
           }
           .left {
               width: 200px;
               float: left;
               background: gray;
           }
           .right {
               width: 150px;
               float: right;
               background: gray;
           }
           .main {
      	/* 由于 盒子的 margin-left 和包含块 border-left 相接触 */
     	/* 同时 BFC 区域不会和浮动盒重叠，所以宽度会自适应，main在浮动之后的元素 */
     					overflow: auto;
               background: cyan;
           }
         </style>
          
           <div class="wrapper">
             <div class="left">左侧</div>
             <div class="right">右侧</div>
             <div class="main">中间</div>
       	</div>
   ```

### margin负值问题

**负值，X轴往左，Y轴往上**

+ **margin-top** 为负值时，**元素自身会向上移动**相应距离，同时会影响下方的元素会向上移动；
+ margin-botom为负值时，元素自身不会位移，但是会减少自身供css读取的高度，从而影响下方的元素会向上移动。
+ **margin-left** 为负值时，**元素自身会向左移动**，同时会影响其它元素；
+ margin-right 为负值时，元素自身不会位移，但是会减少自身供css读取的宽度，从而影响右侧的元素会向左移动

1. #### 实现圣杯布局和双飞翼布局（经典三分栏布局）

   圣杯布局和双飞翼布局的目的：

   - 三栏布局，中间一栏最先加载和渲染（**内容最重要，这就是为什么还需要了解这种布局的原因**）。
   - 两侧内容固定，中间内容随着宽度自适应。
   - 一般用于 PC 网页。

   圣杯布局和双飞翼布局的技术总结：

   - 使用 `float` 布局。
   - 两侧使用 `margin` 负值，以便和中间内容横向重叠。
   - 防止中间内容被两侧覆盖，圣杯布局用 `padding` ，双飞翼布局用 `margin` 

### 圣杯

```html
    <div>头部</div>
    <div id="container" class="clearfix">
      <div class="center">我是中间我是中间间我是中间我是中间我是中间我是中间我是中间</div>
      <div class="left">我是左边</div>
      <div class="right">我是右边</div>
    </div>
    <div>尾部</div>
```

```css
    <style>
      #container {
        padding-left: 200px;
        padding-right: 150px;
        overflow: auto;
      }
      #container >div {
        float: left;
      }
      .center {
        width: 100%;
        background-color: lightcoral;
      }
      .left {
        width: 200px;
        position: relative;
        left: -200px;
        margin-left: -100%;
        background-color: lightcyan;
      }
      .right {
        width: 150px;
        margin-right: -150px;
        background-color: lightgreen;
      }
      .clearfix:after {
        content: "";
        display: table;
        clear: both;
      }
    </style>
```

### 双飞翼布局

```html
<div id="main" class="float">
  <div id="main-wrap">main</div>
</div>
<div id="left" class="float">left</div>
<div id="right" class="float">right</div>
```

```css
.float {
  float: left;
}
#main {
  width: 100%;
  height: 200px;
  background-color: lightpink;
}
#main-wrap {
  margin: 0 190px 0 190px;
}
#left {
  width: 190px;
  height: 200px;
  background-color: lightsalmon;
  margin-left: -100%;
}
#right {
  width: 190px;
  height: 200px;
  background-color: lightskyblue;
  margin-left: -190px;
}
```

tips：上述代码中 `margin-left: -100%` 相对的是父元素的 `content` 宽度，即不包含 `paddig` 、 `border` 的宽度。

其实以上问题需要掌握 **margin 负值问题** 即可很好理解

------

### 行内元素居中布局

水平居中

- 行内元素可设置：**text-align: center**
- **flex** 布局设置父元素：**display: flex** ; **justify-content: center**

垂直居中

- 单行文本父元素确认高度：**height === line-height**
- 多行文本父元素确认高度：**disaply: table-cell**; **vertical-align: middle**

### 行内块元素居中布局

**line-height + vertical-align + text-align**

```html
<style> 
  .demo1 {
    width: 500px;
    height: 200px;
    border: 1px solid #333;
    /* 关键代码 */
    text-align: center;
    line-height: 200px;
  }
  img {
      vertical-align: middle;
  }
</style>

<div class="demo1">
  <img src="https://dss2.bdstatic.com/6Ot1bjeh1BF3odCf/it/u=1765461753,2420530801&fm=218&app=92&f=PNG?w=121&h=75&s=A7D53F6EC4F07F803ADE5F7D0300D07C">
</div>
 
```

### 块级元素居中布局

定宽水平居中: **margin: 0 auto**

### 水平垂直居中

#### **定宽高**

1. 利用绝对定位，设置 `left: 50%` 和 `top: 50%` 现将子元素左上角移到父元素中心位置，然后再通过 `margin-left` 和 `margin-top` 以子元素自己的一半宽高进行负值赋值。该方法**盒子必须定宽高**。

   > **绝对定位和负magin值**

   ```css
   .father {
     position: relative;
   }
   .son {
     position: absolute;
     left: 50%;
     top: 50%;
     width: 200px;
     height: 200px;
     margin-left: -100px;
     margin-top: -100px;
   }
   ```

2. 利用绝对定位，子元素所有方向都为 `0` ，将 `margin` 设置为 `auto` ，由于宽高固定，对应方向实现平分，该方法**盒子必须定宽高**。

   > **绝对定位 + left/right/bottom/top + margin：auto**

   ```css
   .father {
     position: relative;
   }
   .son {
     position: absolute;
     top: 0;
     left: 0;
     right: 0;
     bottom: 0px;
     margin: auto;
     height: 100px;
     width: 100px;
   }
   ```

#### **不定宽高**

1. 利用绝对定位，设置 `left: 50%` 和 `top: 50%` 现将子元素左上角移到父元素中心位置，然后再通过 `translate` 来调整子元素的中心点到父元素的中心。该方法可以**不定宽高**。

   > **绝对定位 + transform**

   ```css
   .father {
     position: relative;
   }
   .son {
     position: absolute;
     left: 50%;
     top: 50%;
     transform: translate(-50%, -50%);
   }
   ```

2. 利用 `flex` ，最经典最方便的一种了，不用解释，定不定宽高无所谓的。

   ```css
   .father {
     display: flex;
     justify-content: center;
     align-items: center;
   }
   ```

3. flex变异布局，弹性盒的子元素只有一个，子元素利用 margin: auto , 实现居中。

   ```css
   .father {
     display: flex;
   }
   .son {
    	 margin: auto;
   }
   ```

### flex: 1，代表的含义？

+ `flex-grow: 1` ：该属性默认为 `0` ，如果存在剩余空间，元素也不放大。设置为 `1` 代表会放大。
+ `flex-shrink: 1` ：该属性默认为 `1` ，如果空间不足，元素缩小。0缩小，1缩小。
+ `flex-basis: 0%` ：该属性定义在分配多余空间之前，元素占据的主轴空间。浏览器就是根据这个属性来**计算是否有多余空间**的。默认值为 `auto` ，即项目本身大小。**设置为 `0%` 之后，因为有 `flex-grow` 和 `flex-shrink` 的设置会自动放大或缩小。**在做两栏布局时，如果右边的自适应元素 `flex-basis` 设为 `auto` 的话，其本身大小将会是 `0` 。

------

### 弹性盒实现自适应文本益出

```html
方式1:文本益处样式写在弹性盒的孙子上
<div style={{display:"flex"}}>
  <div style={{flexGrow:1,width: 0}}>
    <span>这里实现文本溢处，注明max-width或者width</span>
  </div>
</div>
方式2:文本益处写在弹性盒的儿子上
<div style={{display:"flex"}}>
  <div style={{flexGrow:1,width: 0}}>
  	这里实现文本溢处，注明max-width或者width
  </div>
</div>
```

### 文本溢出

* 单行文本溢出

  > width:容器的宽度
  >
  > + overflow：hidden；		(超出隐藏)内容会被修剪，并且其它内容是不可见得
  > + text-overflow:ellipsis ;	显示省略号...
  > + white-space:naowap；	（设置如何处理元素内的空白）文本不换行
  >
  > ```css
  > overflow: hidden;           
  > text-overflow: ellipsis;      
  > white-space: nowrap;     
  > ```
  >

* 多行文本	

  > width：
  >
  > + overflow:hidden;		(超出隐藏)内容会被修剪，并且其它内容是不可见得
  > + text-overflow:ellipsis;		显示省略号...
  > + display:-webkit-box;		将正常的容器变成弹性容器
  > + -webkit-box-orient:vertical;	设置伸缩盒子的子元素排列方式：从上到下垂直排列
  > + -webkit-line-clamp:3;	显示几行文本
  >
  > ```css
  > overflow: hidden;           
  > text-overflow: ellipsis;
  > 
  > display:-webkit-box;         
  > -webkit-box-orient:vertical; 
  > -webkit-line-clamp:3;       
  > ```
  >
  > 注意：由于上面的三个属性都是 CSS3 的属性，没有浏览器可以兼容，所以要在前面加一个`-webkit-` 来兼容一部分浏览器。要给容器添加height属性，可能会造成高度不够

+ 图片的滤镜，可置灰，就像下线的头像一样

```css
filter: grayscale(100%);
```

+ 规定段落中的文本**保留空白**符序列，但是正常地进行换行。

```css
white-space: pre-wrap;
```

+ 允许在单词内换行,不然**长单词**不会进行换行

```css
word-break: break-all;
```

## !important问题

超越`!important`：**max-width会覆盖width**，而且这种覆盖是超级覆盖，比`!important`的权重还要高

超越最大：**min-width覆盖max-width**，此规则发生在min-width和max-width冲突的时候，如下：

> min-width >>> max-width >>> width !important 

```css
.container{
    min-width:1400px;
    max-width:1200px;
}
```

------

## 轻松实现hover图片变成另外一张图片

```css
img:hover{
    content: url(laugh-tear.png);
}
复制代码
```

content 改变的仅仅是视觉呈现，当我们鼠标右键或其他形式保存这张图片时，所保存的还是原来 src 对应的图片。这种方法还可以用在官网标志上。

由于使用 conetnt 生成图片无法设置图片的尺寸，要想在移动端使用该技术，建议使用SVG图片。

## content换行符与打点loading效果

```html
正在加载中<dot>...</dot>
```

```css
dot {
    display: inline-block; 
    height: 1em;
    line-height: 1;
    text-align: left;
    vertical-align: -.25em;
    overflow: hidden;
}

dot::before {
    display: block;
    content: '...\A..\A.';
    white-space: pre-wrap;
    animation: dot 2s infinite step-start both;
}
@keyframes dot {
    33% { transform: translateY(-2em); }
    66% { transform: translateY(-1em); }
}
```

------

## CSS 怎么画一个大小为父元素宽度一半的正方形？

padding-top 百分比定义基于父元素宽度的百分比上内边距！！是基于父元素宽度

```html
    <style>
      .outer {
        width: 400px;
        height: 600px;
        background: red;
      }

      .inner {
        width: 50%;
        /* padding-top 百分比定义基于父元素宽度的百分比上内边距！！是基于父元素宽度 */
        padding-bottom: 50%; 
        background: blue;
      }
    </style>
  </head>
  <body>
    <div class="outer">
      <div class="inner"></div>
    </div>
```

