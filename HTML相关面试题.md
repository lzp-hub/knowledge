# HTML相关面试题

## DOCTYPE 标签有什么用？

DOCTYPE声明于文档最前面，告诉浏览器以何种方式来渲染页面。

HTML5中的声明方式如下：

```html
<!DOCTYPE html>
```

------

## 常用的 meta 元素有哪些？

`<meta>` 元素标签是提供有关HTML文档的元数据，元数据不会显示在页面上，但是能够被机器识别。

总而言之, meta标签是用来让机器识别的，同时它对SEO起着重要的作用。

**charset**

指定了html文档的编码格式，常用的是utf-8(Unicode的字符编码)，还有ISO-8859-1(拉丁字母的字符编码)......

```html
<meta charset="utf-8">
```

**name & content**

指定元数据的名称(这部分对SEO非常有用)

- author——定义了页面的作者

```html
<meta name="author" content="Tony">
```

- keywords——为搜索引擎提供关键字

```html
<meta name="keywords" content="HTML, CSS, JavaScript">
```

- description——对网页整体的描述

```html
<meta name="description" content="My tutorials on HTML, CSS and JavaScript">
```

- viewport——对页面视图相关进行定义

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

- theme-color——定义主题颜色

```html
<meta name="theme-color" content="#222">
```

**http-equiv & content**

为内容属性的信息/值提供HTTP头

- refresh——每30s刷新一次文档

```html
<meta http-equiv="refresh" content="30">
```

- X-UA-Compatible——告知浏览器以何种版本渲染界面。下面的例子有限使用IE最新版本

```html
<meta http-equiv="X-UA-Compatible" content="ie=edge">
```

- Cache-Control——请求和响应遵循的缓存机制，可以声明缓存的内容，修改过期时间，可多次声明

+ no-transform——不得对资源进行转换或转变。 no-siteapp——禁止百度进行转码

```html
<meta http-equiv="Cache-Control" content="no-transform">
<meta http-equiv="Cache-Control" content="no-siteapp">
```

------

## mete标签中的viewport 有什么用？

viewport 是用户网页的可视区域。手机浏览器是把页面放在一个虚拟的"窗口"（viewport）中，通常这个虚拟的"窗口"（viewport）比屏幕宽，这样就不用把每个网页挤到很小的窗口中，用户可以通过平移和缩放来看网页的不同部分。

一个常用的针对移动网页优化过的页面的 viewport meta 标签大致如下：

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0,minimum-scale=1.0, user-scalable=no">
```

+ width=device-width——将页面宽度设置为跟随屏幕宽度变化而变化
+ initial-scale=1.0——设置浏览器首次加载页面时的初始缩放比例(0.0-10.0正数)
+ maximum-scale=1.0——允许用户缩放的最大比例 (0.0-10.0正数)，必须大于等于minimum-scale
+ minimum-scale=1.0——允许用户缩放的最小比例 (0.0-10.0正数)，必须小于等于maximum-scale
+ user-scalable=no——是否允许用户手动缩放(yes或者no)

------

## script 标签中，为什么建议放在 body 标签的底部，async 和 defer 两个属性有什么用途和区别？

在 HTML 中会遇到以下三类 script：

```js
<script src='xxx'></script>
<script src='xxx' async></script>
<script src='xxx' defer></script>
```

script标签用于加载脚本与执行脚本，直接使用script脚本时，html会按照顺序来加载并执行脚本，在脚本加载并执行的过程中，会**阻塞后续的DOM渲染**。如果将script标签放在头部，在文件很大的情况下将导致首屏加载时间延长，影响用户体验。

**建议将script标签放在body的底部，或者通过defer、async属性将js文件转为异步加载（注意对内联脚本是无效的）**

**defer**

脚本并行加载，等待HTML解析完成之后（不会影响后续DOM的渲染），按照加载顺序执行脚本，执行时机**DOMContentLoaded事件派发之前**

**async**

脚本并行加载，加载完成之后立即执行，执行时机不确定，仍有可能阻塞HTML解析，执行时机在**load事件派发之前**

------

## style标签写在body后与body前有什么区别？

页面加载自上而下 当然是先加载样式。**style标签应放在body前**

写在**body标签后**由于浏览器以逐行方式对HTML文档进行解析，当解析到写在尾部的样式表（外联或写在style标签）会导致**浏览器停止之前的渲染，等待加载且解析样式表完成之后重新渲染**，在windows的IE下可能会出现FOUC现象（即样式失效**导致的页面闪烁**问题）

------

## 能否使用自闭合script标签引入脚本文件?

**不能**。自闭合标签来自于XML语法，而不是HTML语法。

根据现在的HTML语法，只有不需要结束标签的void element（如img之类的），或者是外部元素（如svg）可以使用自闭合。script标签显然不在此列。

```js
// 正确写法
<script src="..."></script>

// 错误写法
<script src="..."/>
```

------

##  什么是 HTML 语义化？

- 让搜索引擎更容易读懂，有助于爬虫抓取更多的有效信息，爬虫依赖于标签来确定上下文和各个关键字的权重（SEO）。
- 在没有 CSS 样式下，页面也能呈现出很好地内容结构、代码结构(nav、header、footer、aside、section、article)
- 增加代码可读性，有利于团队的开发、维护

------

## 行内元素有哪些？块级元素有哪些？ 空(void)元素有那些？

CSS规范规定，每个元素都有display属性，确定该元素的类型，每个元素都有默认的display值，如div的display默认值为“block”，则为“块级”元素；span默认display属性值为“inline”，是“行内”元素。

- 常用的块状元素有：

  ```html
  <div>、<p>、<h1>...<h6>、<ol>、<ul>、<dl>、<table>、<address>、<blockquote> 、<form>
  ```

- 常用的内联元素有：

  ```html
  <a>、<span>、<br>、<i>、<em>、<strong>、<label>、<q>、<var>、<cite>、<code>
  ```

- 常用的内联块状元素有

  ```html
  <img>、<input>
  ```

- 知名的空元素：

  ```html
  <br/> <hr/> <img/> <input/> <link/> <meta/>
  ```


------

## 如何禁用a标签跳转页面或定位链接?

```html
<a href="" >href为空会当前页面刷新</a>
<a href="#">href为#会当前页面刷新</a>
```

1. 在html中，href属性，设置为javascript:空或者void(0)。如：

```html
<a href="javascript:void(0);" >点此无反应</a>
<a href="javascript:;" >点此无反应</a>
```

2. 在js文件中阻止默认点击事件：

```js
Event.preventDefault()
```

3. 在css文件中处理点击，不响应任何鼠标事件：

```css
pointer-events: none;
```

------

## iframe是什么？有哪些优缺点？

iframe 可以在一个网站里面嵌入另一个网站的内容。

+ iframe的优点

  > - iframe**能够原封不动的把嵌入的网页**展现出来。
  > - 如果有多个网页引用iframe，那么你只需要修改iframe的内容，就可以实现调用的每一个页面内容的更改，方便快捷。
  > - 网页如果为了统一风格，头部和版本都是一样的，就可以写成一个页面，用iframe来嵌套，可以增加代码的可重用。
  > - 如果遇到加载缓慢的**第三方内容如图标和广告**，这些问题可以由iframe来解决。

+ iframe的缺点

  > - 会产生很多页面，不容易管理。
  > - iframe框架结构有时会让人感到迷惑，如果框架个数多的话，可能会出现上下、左右**滚动条**，会分散访问者的注意力，用户体验度差。 代码复杂，无法被一些搜索引擎索引到，这一点很关键，现在的搜索引擎爬虫还不能很好的处理iframe中的内容，所以**使用iframe会不利于搜索引擎优化**。
  > - 很多的移动设备（PDA 手机）无法完全显示框架，**设备兼容性差**。
  > - iframe 里面的样式、脚本资源，会增加请求次数，对于大型网站是不可取的。
  > - iframe 会阻碍页面的 onload 事件，给用户页面加载很慢的感觉。

  **iframe 已经渐渐的退出了前端开发的舞台。**

------

## HTML 标签中的 src 和 href 有什么区别

- href 是超文本引用，它是指向资源的位置，建立与目标文件的联系；
- src 目的是把资源下载到页面中；

浏览器解析 **href 不会阻塞对文档的处理**（这就是官方建议使用 link 引入而不是 @ import 的原因），**src 会阻塞对文档的处理**。

------

## 页面导入样式时，使用link和@import有什么区别？

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <link rel="stylesheet" rev="stylesheet" href="myCss.css" type="text/css" > 
        <style type="text/css" >   
            @import url("./myCss.css");   
        </style> 
    </head>
</html>
```

1. 从属关系：link是html的标签，不仅可以加载 CSS 文件，还可以定义 RSS、rel 连接属性等；而@import是css的语法，只有导入样式表的作用。

2. 加载顺序：页面被加载时，link会和html同时被加载；而@import引入的 CSS 将在页面加载完毕后被加载。

3. 兼容性：@import是 CSS2.1 才有的语法，所以只能在 IE5以上 才能识别；而link是 HTML 标签，所以不存在兼容性问题。

4. DOM：javascript可以控制dom去改变link标签引入的样式，而@import的样式不是dom可以控制的。

5. link方式的样式权重高于@import的权重。

------

## 什么是HTML5，以及和HTML的区别是什么？

HTML5是HTML的新标准，其主要目标是无需任何额外的插件如Flash、Silverlight等，就可以传输所有内容。它囊括了动画、视频、丰富的图形用户界面等。

HTML5是由万维网联盟（W3C）和Web Hypertext Application Technology Working Group 合作创建的HTML新版本。

```html
// HTML4.01
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//en" "http://www.w3.org/TR/html4/strict.dtd"> 
没有体现结构语义化的标签，通常都是这样来命名的<div id="header"></div>//这样表示网站的头部。

//HTML5
<!DOCTYPE HTML> 
 语义上却有很大的优势。提供了一些新的标签，比如：<header><article><footer>。
```

**`<!DOCTYPE HTML>`告诉浏览器以何种方式来渲染页面，没有则浏览器将无法识别html文件，因此html将无法正常工作**

------

## HTML5 有哪些新特性？

- 新增语义化标签：nav、header、footer、aside、section、article

- 音频、视频标签：audio、video

- 数据存储：localStorage、sessionStorage

- canvas（画布）、Geolocation（地理定位）、websocket（通信协议）

- input标签新增属性：placeholder、autocomplete、autofocus、required

- history API

  > go、forward、back、pushstate

------

## 简述 html 页面渲染过程?

整个渲染过程其实就是将URL对应的各种资源，通过浏览器渲染引擎的解析，输出可视化的图像。

1. 解析HTML，生成DOM树，解析CSS，生成CSSOM树(css规则树)

2. 将DOM树和CSSOM树结合，生成渲染树(Render Tree)

3. Layout(回流):根据生成的渲染树，进行回流(Layout)，**得到节点的几何信息（位置，大小）**

4. Painting(重绘):根据渲染树以及回流得到的几何信息，**得到节点的绝对像素**

5. Display:将像素发送给GPU，展示在页面上

   以上5个步骤并不是一次性顺序完成的。如果DOM或者CSSOM被修改，以上过程会被重复执行。实际上，CSS和JavaScript往往会多次修改DOM或者CSSOM。

   ![](//i.loli.net/2021/07/04/kK39pXjgDdJ75Ux.png)

   

- 回流：布局引擎会根据各种样式计算每个盒子在页面上的**大小与位置**，计算这些值的过程称为回流或重排

  > 对 `DOM` 的修改引发了 `DOM `几何尺寸的变化（比如修改元素的宽、高或隐藏元素等）时会触发
  >
  > - **添加或删除**可见的DOM元素
  > - 元素的**位置**发生变化
  > - 元素的**尺寸**发生变化（包括外边距、内边框、边框大小、高度和宽度等，即盒子尺寸）
  > - 内容发生变化，比如文本变化或图片被另一个不同尺寸的图片所替代
  > - 页面一开始渲染的时候（这避免不了）
  > - 浏览器的**窗口尺寸**变化（因为回流是根据视口的大小来计算元素的位置和大小的）
  > - offsetTop等**需要通过即时计算得到**。因此浏览器为了获取这些值，也会进行回流

- 重绘：当计算好盒模型的位置、大小及其他属性后，浏览器根据每个盒子**特性进行绘制**

  >  重绘是改变不影响元素在网页中的位置的元素样式时 例如`color`或`background-color`或border-color或visibility，却并未影响其几何属性时
  >
  >  - 颜色的修改
  >  - 文本方向的修改
  >  - 阴影的修改
  >  - visibility，

------

## 浏览器如何优化渲染？

由于每次重排都会造成额外的计算消耗，因此大多数浏览器都会通过**队列化修改并批量执行**来优化重排过程。浏览器会将修改操作放入到队列里，直到过了一段时间或者操作达到了一个阈值，才清空队列

当你获取布局信息的操作的时候，会强制队列刷新，包括前面讲到的`offsetTop`等方法都会返回最新的数据。因此浏览器不得不清空队列，触发回流重绘来返回正确的值

**如何减少**

我们了解了如何触发回流和重绘的场景，下面给出**避免回流**的经验：

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

------

## CSSOM树和DOM树是同时解析的吗？

浏览器下载HTML解析页面生成DOM树，遇到CSS标签就开始解析CSS，这个过程不会阻塞，但是如果遇到了JS脚本，此时假如CSSOM还没有构建完，需要等待CSSOM构建完，再去执行JS脚本，然后再执行DOM解析，此时会阻塞

------

## 简单描述从输入网址到页面显示的过程？

1. 缓存解析阶段： 浏览器缓存，系统缓存，路由器缓存

2. DNS解析：网址转换为IP地址

3. 发起TCP连接：TCP提供一种可靠的传输，这个过程涉及到三次握手。

4. 发送HTTP请求：构建HTTP请求报文，并通过TCP协议，发送到服务器指定端口。

5. 服务器处理请求并返回HTTP报文，连接结束，四次挥手

6. 浏览器解析渲染页面

   > - 解析HTML形成DOM树
   > - 解析CSS形成CSSOM 树
   > - 合并DOM树和CSSOM树形成渲染树
   > - 浏览器开始渲染并绘制页

------

## img标签上title属性与alt属性的区别是什么？

- **alt**，是其特有属性，是图片内容的等价描述，用于**图片无法加载时显示**
- **title**，作用是提供建议性的信息，通常是鼠标滑动到元素上是显示，可以为链接添加描述性文字，来更加清楚的表达链接的目的。

------

##  说说 HTML、XML、XHTML 的区别

- HTML：超文本标记语言，是语法较为松散的、不严格的Web语言；
- XML：可扩展的标记语言，主要用于存储数据和结构，可扩展；
- XHTML：可扩展的超文本标记语言，基于XML，作用与HTML类似，但语法更严格

------

## 什么是渐进增强和优雅降级？

**渐进增强**（progressive enhancement）：主要是**针对低版本的浏览器进行页面重构**，保证基本的功能情况下，再针对高级浏览器进行效果，交互等方面的改进和追加功能，以达到更好的用户体验

**优雅降级** graceful degradation： **一开始就构建完整的功能**，然后再针对低版本的浏览器进行兼容。

**区别**

- 渐进增强则意味着往前看，是从一个非常基础的，能够起作用的版本开始的，并在此基础上不断扩充，以适应未来环境的需要

- 优雅降级（功能衰竭）意味着往回看，是从复杂的现状开始的，并试图减少用户体验的供给；

  

------

## 前端该如何选择图片的格式？

https://juejin.cn/post/6844904125918871560

------

## 什么是 DOM 和 BOM？

- DOM 指的是文档对象模型，它指的是把文档当做一个对象，这个对象主要定义了处理网页内容的方法和接口。
- BOM 指的是浏览器对象模型，它指的是把浏览器当做一个对象来对待，这个对象主要定义了与浏览器进行交互的方法和接口。BOM的核心是 **window**，而 window 对象具有双重角色，它既是通过 js 访问浏览器窗口的一个接口，又是一个 Global（全局）对象。这意味着在**网页中定义的任何对象，变量和函数，都能作为全局对象的一个属性或者方法存在**。**window 对象含有 location 对象、navigator 对象、screen 对象等子对象**，并且 DOM 的最根本的对象 document 对象也是 BOM 的 window 对象的子对象。

------

## 页面统计数据中，常用的 PV、UV 指标分别是什么？

**PV(页面访问量)**

即页面浏览量或点击量，用户每1次对网站中的每个网页访问均被记录1个PV。

用户对同一页面的多次访问，访问量累计，用以衡量网站用户访问的网页数量。

**UV(独立访客)**

是指通过互联网访问、浏览这个网页的自然人。访问您网站的一台电脑客户端为一个访客。

00:00-24:00内相同的客户端只被计算一次。

------

## 什么是 IconFont?

顾名思义，IconFont 就是**字体图标**。严格地说，就是一种字体，但是，它们不包含字母或数字，而是包含**符号和字形**。您可以使用 CSS 设置样式，就像设置常规文本一样，这使得 IconFont 成为 Web 开发时图标的热门选择。

**优点**

- 可以方便地将任何 CSS 效果应用于它们。
- 因为它们是**矢量图形**，所以它们是可伸缩的。这意味着我们可以在不降低质量的情况下伸缩它们。
- 我们只需要发送一个或少量 HTTP 请求来加载它们，而不是像图片，可能需要多个 HTTP 请求。
- 由于尺寸小，它们加载速度快。
- 它们在所有浏览器中都得到支持（甚至支持到 IE6）。

**不足**

- 不能用来显示复杂图像
- 通常只限于一种颜色，除非应用一些 CSS 技巧
- 字体图标通常是根据特定的网格设计的，例如 16x16, 32×32, 48×48等。如果由于某种原因将网格系统改为25×25，可能不会得到清晰的结果

------

## 如何控制 input 输入框的输入字数？

一般是通过 maxlength 属性进行限制：

```html
<input maxlength="5" />
```

另外还可以通过监听 `οninput` 事件，对输入值进行处理

------

## 如何禁止input展示输入的历史记录

输入input时会提示原来输入过的内容，还会出现下拉的**历史记录**，禁止这种情况只需在input中加入： **autocomplete=“off”**

```html
<input type="text"  autocomplete="off" />
```

autocomplete 属性是用来规定输入字段是否启用自动完成的功能。

------

## input上传文件可以同时选择多张吗？怎么设置？

可以，通过给input标签设置**multiple**属性。

```html
<input type="file" name="files" multiple/>
```

------

## 使用input标签上传图片时，怎样触发默认拍照功能？

**capture** 属性用于指定文件上传控件中媒体拍摄的方式。

```html
// user 前置摄像头
<input type="file" accept="image/*;" capture="user" />
// environment 后置摄像头
<input type='file' accept='image/*;' capture='environment'/>
```

------

##  label标签有什么用？

label标签来定义**表单控制间的关系**。当用户选择该标签时，浏览器会自动将焦点转到和标签相关的表单控件上。

+ 方式1. 通过for属性关联表单控件的**id**，如果在react中for则为**htmlFor**
+ 方式2. label标签包裹着表单控件

```html
<label for="Name">Number:</label>
<input type='text' id="Name"/>

<label>Date:<input type="text" name="B"/></label>
```

------

## title与h1的区别、b与strong的区别、i与em的区别？

+ **title**是网站标题，一个页面只能有一个。概括网站信息，可以直接告诉搜索引擎和用户这 个网站是关于什么主题和内容的，是显示在网页Tab栏里的

+ **h1**是文章主题，面对用户，更突出其视觉效果，指向页面主体信息，是显示在网页中的。如果title为空，搜索引擎会默认页面title为h1内的内容

  > h1是在没有外界干扰下除title以外第二个能强调页面主旨的标记，在一个页面中应该使用且**只使用一次h1**标记。

+ b (bold)是实体标签，用来给文字加粗,没有实际含义，常用来表达无强调或着中意味的粗体文字
+ **strong** 是逻辑标签，作用是**加强字符语气，可通过css添加样式**，搜索引擎更侧重strong标签
+ i (italic)是实体标签，用来使字符倾斜，没有实际含义，常用来表达无强调或着重意味的斜体。
+ **em**(emphasis)是逻辑标签，**强调文本内容，其默认格式是斜体，可以通过CSS添加样式**，搜索引擎更侧重em标签

------

## 说说你对以下几个页面生命周期事件的理解：DOMContentLoaded，load，beforeunload，unload

HTML页面的生命周期有以下三个重要事件：

**DOMContentLoaded**

事件在**DOM树构建完毕后**被触发，我们可以在这个阶段js可以访问所有DOM节点

- `async`和`defer`的脚本可能还没有执行完。
- 图片及其他资源文件可能还在下载中。
- Firefox, Chrome和Opera会在`DOMContentLoaded`执行时自动补全表单

```js
document.addEventListener("DOMContentLoaded", ()=>{});
```

**load**

事件在页面**所有资源被加载完毕后**触发（图像，样式表等）

附加资源已经加载完毕，可以在此事件触发时获得图像的大小

```html
<script>
  window.onload = function() {
    alert(`Image size: ${img.offsetWidth}x${img.offsetHeight}`);
  };
</script>

<img id="img" src="https://en.js.cx/clipart/train.gif?speed=1&cache=0">
```

**beforeunload**

如果用户**即将**离开页面或者关闭窗口时，`beforeunload`事件将会被触发以进行额外的确认。

浏览器将显示返回的字符串，举个例子：

```javascript
window.onbeforeunload = function() {
  return "有未保存的更改。现在离开?";
};
```

有些浏览器像Chrome和火狐会忽略返回的字符串取而代之显示浏览器自身的文本，这是为了安全考虑，来保证用户不受到错误信息的误导。

**unload**

用户离开页面的时候，`window`对象上的`unload`事件会被触发，我们可以做一些不存在延迟的事情，比如关闭弹出的窗口，可是我们无法阻止用户转移到另一个页面上。我们可以使用 **navigator.sendBeacon** 来发送网络请求

------

## 如何实现浏览器内多个标签页之间的通信？

**localStorage**

- localStorage是浏览器多个标签共用的存储空间，所以可以用来实现多标签之间的通信

  > sessionStorage是会话级的存储空间，每个标签页都是单独的

- 在一个标签页里面使用`localStorage.setItem(key,value)`添加；localStorage.removeItem(key,value);在另一个标签页里面监听`storage`事件。即可得到`localstorge`存储的值，实现不同标签页之间的通信。

```js
// 添加
localStorage.setItem(key,value) 
// 删除
localStorage.removeItem(key,value) 

// 添加监听 storage 的变化
window.onload = function () {
    window.addEventListener('storage', function (event) {
      // event 事件对象包含 domain newValue oldValue key
        console.log(event.key + '=' + event.newValue);
    })
}
```

**定时器setInterval+cookie**

将要传递的信息存储在cookie中，每隔一定时间读取cookie信息， 即可随时获取要传递的信息。

在A页面将需要传递的消息存储在cookie当中

由于Cookies是在同域可读的，在B页面设置`setInterval`,以一定的时间间隔去读取cookie的值。(不停地问cookie)

```javascript
// 设置 cookie
document.cookie = "name=" + name;

// 获取 cookie
function getCookie(key) {
  const _string = '{"';
  _string += document.cookie.replace(/;\s+/gim, '","').replace(/=/gim, '":"');
  _string += '"}'[key];

  return JSON.parse(_string);
}。
```

这样做确实可以实现我想要的功能，但是这样的方法相当浪费资源。虽然在这个性能过盛的时代，浪费不浪费也感觉不出来，但是这种实现方案，确实不够优雅。

**postMessage**

两个需要交互的tab页面具有依赖关系。

如 A页面中通过JavaScript的window.open打开B页面，或者B页面通过iframe嵌入至A页面，此种情形最简单，可以通过HTML5的 window.postMessage API完成通信，由于postMessage函数是绑定在 window 全局对象下，因此通信的页面中必须有一个页面（如A页面）可以获取另一个页面（如B页面）的window对象，这样才可以完成单向通信；B页面无需获取A页面的window对象，如果需要B页面对A页面的通信，只需要在B页面**侦听message事件，获取事件中传递的source对象**，该对象即为A页面window对象的引用：

```js
//B页面
window.addEventListner('message',(e)=>{
    let {data,source,origin} = e;
    source.postMessage('message echo','/');
});
```

postMessage的第一个参数为消息实体，它是一个结构化对象，即可以通过“JSON.stringify和JSON.parse”函数还原的对象；第二个参数为消息发送范围选择器，设置为“/”意味着只发送消息给同源的页面，设置为“*”则发送全部页面。

**WebSocket**通讯

WebSocket 是全双工（full-duplex）通信自然可以实现多个标签页之间的通信（服务器可以主动发数据给浏览器；浏览器也可以主动发数据给服务器）

**Broadcast Channel**

顾名思义，“广播频道”，官方文档里的解释为“用于同源不同页面之间完成通信的功能”，在其中某个页面发送的消息会被其他页面监听到。注意“同源”二字，该方法无法完成跨域的数据传输。

**SharedWorker**

html5浏览器的新特性,SharedWorker可以被多个window共同使用，但必须保证这些标签页都是同源的(相同的协议，主机和端口号)

------

## img的srcset属性的作⽤？

响应式页面中经常用到**根据屏幕密度设置不同的图片**。这时就用到了 img 标签的srcset属性。srcset属性用于设置不同屏幕密度下，img 会自动加载不同的图片。用法如下：

```js
<img src="image-128.png" srcset="image-256.png 2x" />
```

使用上面的代码，就能实现在屏幕密度为1x的情况下加载image-128.png, 屏幕密度为2x时加载image-256.png。

按照上面的实现，不同的屏幕密度都要设置图片地址，目前的屏幕密度有1x,2x,3x,4x四种，如果每一个图片都设置4张图片，加载就会很慢。所以就有了新的srcset标准。代码如下：

```html
<img src="image-128.png"
    srcset="image-128.png 128w, image-256.png 256w, image-512.png 512w"
    sizes="(max-width: 360px) 340px, 128px" />
```

其中srcset指定图片的地址和对应的图片质量。sizes用来设置图片的尺寸零界点。对于 srcset 中的 w 单位，可以理解成图片质量。如果可视区域小于这个质量的值，就可以使用。浏览器会自动选择一个最小的可用图片。

sizes语法如下：

```
sizes="[media query] [length], [media query] [length] ... "
```

sizes就是指默认显示128px, 如果视区宽度大于360px, 则显示340px。

------

## Canvas和SVG有什么区别？

**SVG** SVG可缩放矢量图形（Scalable Vector Graphics）是基于可扩展标记语言XML描述的2D图形的语言，SVG基于XML就意味着SVG DOM中的每个元素都是可用的，可以为某个元素附加Javascript事件处理器。在 SVG 中，每个被绘制的图形均被视为对象。如果 SVG 对象的属性发生变化，那么浏览器能够自动重现图形。

- **不依赖分辨率（矢量图）**
- **支持事件处理器**
- 最适合带有大型渲染区域的应用程序（比如谷歌地图）
- 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）
- 不适合游戏应用

**Canvas**：Canvas是画布，通过Javascript来绘制2D图形，是逐像素进行渲染的。其位置发生改变，就会重新进行绘制。

- **依赖分辨率（位图）**
- **不支持事件处理器**
- 弱的文本渲染能力
- **能够以 .png 或 .jpg 格式保存结果图像**
- 最适合**图像密集型的游戏**，其中的许多对象会被频繁重绘

------

## 浏览器乱码的原因是什么？如何解决？

**产生乱码的原因：** 文本字符编码过程与解码过程使用了不同的编码方式

> - 网页源代码是`gbk`的编码，而内容中的中文字是`utf-8`编码的，这样浏览器打开即会出现`html`乱码，反之也会出现乱码；
> - `html`网页编码是`gbk`，而程序从数据库中调出呈现是`utf-8`编码的内容也会造成编码乱码；
> - 浏览器不能自动检测网页编码，造成网页乱码。

- **解决办法：**使用**支持要展示字体的字符集编码**，并且**编解码使用同一种编码方式**

  > - 使用软件编辑HTML网页内容；
  > - 如果网页设置编码是`gbk`，而数据库储存数据编码格式是`UTF-8`，此时需要程序查询数据库数据显示数据前进程序转码；
  > - 如果浏览器浏览时候出现网页乱码，在浏览器中找到转换编码的菜单进行转换

##  HTML5 有哪些 drag 相关的 API ？

- dragstart：事件主体是被拖放元素，在开始拖放被拖放元素时触发。
- darg：事件主体是被拖放元素，在正在拖放被拖放元素时触发。
- dragend：事件主体是被拖放元素，在整个拖放操作结束时触发。
- dragenter：事件主体是目标元素，在被拖放元素进入某元素时触发。
- dragover：事件主体是目标元素，在被拖放在某元素内移动时触发。
- dragleave：事件主体是目标元素，在被拖放元素移出目标元素是触发。
- drop：事件主体是目标元素，在目标元素完全接受被拖放元素时触发。

------

## SEO的原理是什么？

搜索引擎是当今规模最大、最复杂的计算系统之一。搜索引擎排名大致上可以分为四个步骤。

**爬行和抓取**

搜索引擎派出一个能够在网上发现新网页并抓取文件的程序，这个程序通常被称为蜘蛛或机器人。

搜索引擎蜘蛛从数据库中已知的网页开始出发，就像正常用户的浏览器一样访问这些网页并抓取文件。

**并且搜索引擎蜘蛛会跟踪网页上的链接，访问更多网页，这个过程就叫爬行。**

**当通过链接发现有新的网址时，蜘蛛将把新网址记录入数据库等待抓取。**

**索引**

搜索引擎索引程序把蜘蛛抓取的**网页文件分解、分析**，并以巨大表格的形式存入数据库，这个过程就是索引。在索引数据库中，网页文字内容，关键词出现的位置、字体、颜色、加粗、斜体等相关信息都有相应记录。

**搜索词处理**

用户在搜索引擎界面输入关键词，单击“搜索”按钮后，搜索引擎程序即对输入的搜索词进行处理，如中文特有的分词处理，对关键词词序的分别，去除停止词，判断是否需要启动整合搜索，判断是否有拼写错误或错别字等情况。搜索词的处理必须十分快速。

**排序**

对搜索词进行处理后，搜索引擎排序程序开始工作，从索引数据库中找出所有包含搜索词的网页，并且根据排名计算法计算出哪些网页应该排在前面，然后按一定格式返回“搜索”页面。

**但是即使最好的搜素引擎在鉴别网页上也还无法与人相比，这就是为什么网站需要搜索引擎优化。没有 SEO 的帮助，搜索引擎常常并不能正确返回最相关、最权威、最有用的信息。**

------

## SEO是什么？

SEO（Search Engine Optimization），汉译为搜索引擎优化。

搜索引擎优化是一种利用搜索引擎的搜索规则来提高目前网站在有关搜索引擎内的自然排名的方式。

SEO是指为了从搜索引擎中获得更多的免费流量，从网站结构、内容建设方案、用户互动传播、页面等角度进行合理规划，使网站更适合搜索引擎的索引原则的行为。

------

## 如何实现SEO优化?

SEO主要分为内部和外部两个方向。

1. 内部优化

- META 标签优化：例如：TITLE，KEYWORDS，DESCRIPTION （TDK）等的优化
- 内部链接的优化，包括相关性链接（Tag 标签），锚文本链接，各导航链接，及图片链接
- 网站内容更新：每天保持站内的更新(主要是文章的更新等)
- 服务器端渲染（SSR）

2. 外部优化

- 外部链接类别：博客、论坛、B2B、新闻、分类信息、贴吧、知道、百科、相关信息网等尽量保持链接的多样性
- 外链运营：每天添加一定数量的外部链接，使关键词排名稳定提升。
- 外链选择：与一些和你网站相关性比较高,整体质量比较好的网站交换友情链接,巩固稳定关键词排名

## SPA应用怎么进行SEO？

SPA技术将产出html的逻辑从服务器转移到了客户端，在进入React, Vue等UI框架进行开发时，我们开发的页面更多的是在客户端进行脚本执行、数据请求和UI动态装载。

那么搜索引擎爬虫在抓取这样的页面的时，在未做任何优化的情况下，通常拿到的是类似下面的字符文本：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>title</title>
</head>
<body>
<div id="root"></div>
<script src="index.js"></script>
</body>
</html>
```

除了可以事先定义的title（可能title也不能事先确定），在SPA下很多内容需要通过ajax请求server拿到数据通过脚本执行产生。通常爬虫不会有类似浏览器的执行环境去产生这些内容。

那么如何让爬虫拿到的数据和用户通过浏览器看到的数据尽量是一致？

**SPA的优缺点**

优点：用户体验好，前后端代码分离，利于后期的维护

缺点：seo不好，首次加载时长比较久，导航需要自己去实现前进后退。

**SPA如何解决SEO的问题**

SSR 服务端渲染

优点：首屏加载快(因为服务器返回的网页已经包含数据, 所以之下载完JS/CSS就可以直接渲染)。每次请求返回的都是一个独立完成的网页, 更利于SEO。

缺点就是服务器压力会比较大，对网络要求比较大，

## 怎么实现“点击回到顶部”的功能？

1. 锚点

使用锚点链接是一种简单的返回顶部的功能实现。

该实现主要在页面顶部放置一个指定名称的锚点链接，然后在页面下方放置一个返回到该锚点的链接，用户点击该链接即可返回到该锚点所在的顶部位置。

```html
<body style="height:2000px;">
    <div id="topAnchor"></div>
    <a href="#topAnchor" style="position:fixed;right:0;bottom:0">回到顶部</a>
</body>
```

2. scrollTop

scrollTop属性表示**被隐藏在内容区域上方的像素数**。

**元素未滚动时，scrollTop的值为0**，如果元素被垂直滚动了，scrollTop的值大于0，且表示元素上方不可见内容的像素宽度

由于scrollTop是可写的，可以利用scrollTop来实现回到顶部的功能

```html
<body style="height:2000px;">
    <button id="test" style="position:fixed;right:0;bottom:0">回到顶部</button>
    <script>
      	const test = document.getElementById('test')
        test.onclick = function(){
            document.documentElement.scrollTop = 0;
        }
    </script>
</body>
```

3. scrollTo

scrollTo(x,y)方法滚动当前window中显示的文档，让文档中由坐标**x和y指定的点**位于显示区域的左上角

设置scrollTo(0,0)可以实现回到顶部的效果

```html
<body style="height:2000px;">
    <button id="test" style="position:fixed;right:0;bottom:0">回到顶部</button>
    <script>
				const test = document.getElementById('test')
        test.onclick = function(){
            scrollTo(0,0);
        }
    </script>
</body>
```

4. scrollBy()

scrollBy(x,y)方法滚动当前window中显示的文档，**x和y指定滚动的相对量**

只要把当前页面的滚动长度作为参数，逆向滚动，则可以实现回到顶部的效果

```html
<body style="height:2000px;">
    <button id="test" style="position:fixed;right:0;bottom:0">回到顶部</button>
    <script>
      	const test = document.getElementById('test')
        test.onclick = function(){
            var top = document.documentElement.scrollTop
            scrollBy(0,-top);
        }
    </script>
</body>
```

5. scrollIntoView()

Element.scrollIntoView方法滚动当前元素，进入浏览器的可见区域　

该方法可以接受一个布尔值作为参数。如果为true，表示元素的顶部与当前区域的可见部分的顶部对齐（前提是当前区域可滚动）；如果为false，表示元素的底部与当前区域的可见部分的尾部对齐（前提是当前区域可滚动）。如果没有提供该参数，默认为true

使用**该方法的原理与使用锚点的原理类似**，在页面最上方设置目标元素，当页面滚动时，目标元素被滚动到页面区域以外，点击回到顶部按钮，使目标元素重新回到原来位置，则达到预期效果

```html
<body style="height:2000px;">
    <div id="target"></div>
    <button id="test" style="position:fixed;right:0;bottom:0">回到顶部</button>
    <script>
    		const test = document.getElementById('test')
        test.onclick = function(){
            test.scrollIntoView();// test.scrollIntoView({ block: 'start', behavior: 'auto' })
        }
    </script>
</body>
```

## 宏任务与微任务有哪些？

+ 宏任务（macrotask）

  > - **setTimeout/setInterval**
  > - **ajax**
  > - setImmediate (Node 独有)
  > - requestAnimationFrame (浏览器独有)
  > - I/O
  > - UI rendering (浏览器独有)

+ 微任务（microtask）

  > - **process.nextTick** (Node 独有)
  > - **Promise**
  > - **Object.observe**
  > - **MutationObserver**



## 35 js和css是如何影响DOM树构建的？

## 第38题：说说对 web worker 的了解

## 第39题：HTML5的离线储存怎么使用，它的工作原理是什么

## 第40题：浏览器是如何对 HTML5 的离线储存资源进行管理和加载？

## 第45题：DNS 预解析是什么？怎么实现？

##  第47题：前端跨页面通信，你知道哪些方法？