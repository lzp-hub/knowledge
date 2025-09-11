## 前言
而在开发 React 时，特别是使用 Next.js 时，就不得不考虑 Tailwind CSS 这种方案了。

使用 Tailwind CSS 就像在使用 Bootstrap，写出对应类名就能快速创建样式。如果你对CSS很有感觉，那么掌握 Tailwind CSS 只要 1 小时。

**适用范围：**

虽然在 React 中结合得比较紧密，但是也同样适用于其他的主流框架，例如：Vite、Nuxt、Angular，甚至是 Laravel、Ruby on Rails……

文档在此：[tailwindcss.com/docs/instal…](https://link.juejin.cn?target=https%3A%2F%2Ftailwindcss.com%2Fdocs%2Finstallation%2Fframework-guides)

参考：  


## 前言
基础篇：

+ [听说你还不会 Tailwind CSS（基础·上篇）](https://juejin.cn/post/7363534953651257353)
+ [听说你还不会 Tailwind CSS（基础·中篇）](https://juejin.cn/post/7363928559922724901)
+ [听说你还不会 Tailwind CSS（基础·下篇）](https://juejin.cn/post/7364409181053059098)

进阶篇：

+ [听说你还不会 Tailwind CSS（进阶·上篇）](https://juejin.cn/post/7365802529923399689)
+ [听说你还不会 Tailwind CSS（进阶·下篇）](https://juejin.cn/post/7371649020123152384)

响应式篇：

+ [听说你还不会 Tailwind CSS（响应式篇）](https://juejin.cn/post/7389089457734189093)

## 宽高
### 宽度高度
1. 预定义数值类（`w-数值`、`h-数值`）

通过 `w-数值` 和 `h-数值` 就能分别设定宽度和高度，例如：

```tsx
<div className="w-20 h-20 bg-blue-500">width and height</div>
```

`w-1` 的 1 表示 0.25 rem，即 4 px，以此类推，`w-20` 表示 5 rem，即 `width: 80px;`。同理，`h-20` 表示 `height: 80px;` 。

`bg-blue-500` 很好理解，就是 `background-color` 为蓝色，其中 500 表示某种饱和度的蓝色：数值越大颜色越深。

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840719662-93b1b0bc-2584-43fd-823d-498accd9a0b0.jpeg)

2. 手动书写任意值（`w-[]`、`h-[]`）

```tsx
<div className="w-[80px] h-20 bg-blue-500">width and height</div>
<div className="w-[5rem] h-20 bg-blue-500">width and height</div>
<div className="w-[5em] h-20 bg-blue-500">width and height</div>
```

想要更加直观可控，以 width 为例，`w-20` 实际上等价于：`w-[80px]` 、`w-[5rem]`、`w-[5em]`。

1. 百分比（`w-分子/分母`、`h-分子/分母`）

```tsx
<div className="w-1/2 h-20 bg-blue-300">w-1/2</div>
<div className="w-1/3 h-20 bg-blue-300">w-1/3</div>
<div className="w-1/4 h-20 bg-blue-300">w-1/4</div>
```

百分比的有效范围为：1/2, 1/3, 2/3, 1/4, 2/4, …, 11/12，整数不在此范围，例如 2/2, 3/3 等。

+ w-1/2 👉 `width: 50%;`
+ w-1/3 👉 `width: 33.333333%;`
+ w-1/4 👉 `width: 25%;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840719404-7aad280a-b611-4635-b338-508f01b211d8.jpeg)

2. `w-full`、`w-screen`

一般来说，占满宽度有两种情况。一个是占据容器的 100% 宽度，一个是占据设备屏幕的 100% 宽度。（另外还有 `w-svw`、`w-lvw`、`w-dvw`）

```tsx
<div className="w-[500px] border">
  <div className="w-full h-20 bg-blue-100">w-full</div>
  <div className="w-screen h-20 bg-blue-100">w-screen</div>
</div>
```

+ w-full 👉 `width: 100%;`
+ w-screen 👉 `width: 100vw;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840719511-a8db2a1b-c679-417b-9591-e433627e2dde.jpeg)

3. `h-svh`、`h-lvh`、`h-dvh`

在高度方面也是类似，也有 `h-full` 和 `h-screen`。由于设备的不同，考虑到手机 h5 之类的情况，使用以下更为灵活的发高度值：

h-svh 👉 `height: 100svh;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840719504-1ee6b88b-c058-4e2e-a7d1-51f36304627d.jpeg)

h-lvh 👉 `height: 100lvh;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840719540-c3a7ef2b-bbbc-47ce-987a-e614a889e6ac.jpeg)

h-dvh 👉 `height: 100dvh;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840720500-dbd263f4-d25a-45ed-8de3-84346536417a.jpeg)

在实际使用中，用起来第三种 h-dvh 会更加灵活。

### 最小/大宽度高度
1. `min-w-[]`、`max-w-[]`
2. `min-h-[]`、`max-h-[]`

这些一般用于响应式设计或防止溢出。下面是一个按钮文本防止宽度溢出的设计：

```tsx
<div className="inline-block min-w-20 h-10 leading-10 bg-green-200 text-center">
  btn
</div>
<br />
<div className="inline-block min-w-20 h-10 leading-10 bg-green-400 text-center">
  button
</div>
<br />
<div className="inline-block min-w-20 h-10 leading-10 bg-green-600 text-center">
  my long text button
</div>
```

+ min-w-20 👉 `min-width: 5rem; /* 80px */`
+ inline-block 👉 `display: inline-block;`
+ leading-10 👉 `line-height: 2.5rem; /* 40px */`
+ text-center 👉 `text-align: center;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840720350-a58c52b7-8af7-45c3-88b9-dd8fb8e7cf70.jpeg)

### size
`size-*` 可以快速创建出一个正方形。注意，低版本的 Tailwind CSS 可能没有这个特性。

```tsx
<div className="w-20 h-20 bg-blue-500">width and height</div>
```

等价于：

```tsx
<div className="size-20 bg-blue-500">width and height</div>
```

## 边距
外边距、内边距、空间间隔。

### margin
```tsx
<div className="mr-2 inline bg-red-200">inline1</div>
<div className="mr-2 inline bg-red-300">inline2</div>
<div className="mr-2 inline bg-red-400">inline3</div>
```

以 `mr-*` 为例，表示右侧的外边距。

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840720423-5f8a75eb-67f6-4eb9-bc17-9b2a738c9876.jpeg)

其他方位：

+ mt-* 👉 `margin-top: _;`
+ mb-* 👉 `margin-bottom: _;`
+ ml-* 👉 `margin-left: _;`
+ mx-* 👉 `margin-left: _; margin-right: _;`
+ my-* 👉 `margin-top: _; margin-bottom: _;`

内容区块水平方向居中：

```tsx
<main className="w-[1280px] mx-auto h-screen bg-sky-200">
  ...
</main>
```

mx-auto 👉 `margin: 0 auto;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840720606-a273dd85-a49b-439d-b52d-0963635a5947.jpeg)

### padding
```tsx
<p className="py-5 px-10 bg-yellow-300">#p1</p>
<p className="py-5 px-10 bg-yellow-400">#p2</p>
<p className="py-5 px-10 bg-yellow-500">#p3</p>
```

padding 部分和 margin 类似。

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840720906-09a3e411-85b4-4e0c-96db-e5d8e48767ef.jpeg)

### space
将 `space-x-*` 或 `space-y-*` 写在容器上，用来控制子元素之间的间距。

1. 水平方向排列

```tsx
<div className="space-x-4">
  <div className="inline-block bg-slate-400">01</div>
  <div className="inline-block bg-slate-500">02</div>
  <div className="inline-block bg-slate-600">03</div>
</div>
```

2. 垂直方向排列

```tsx
<div className="space-y-4">
  <div className="bg-emerald-300">01</div>
  <div className="bg-emerald-400">02</div>
  <div className="bg-emerald-500">03</div>
</div>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840720845-a9dc94b9-18b7-4d50-bf93-a949810e42e3.jpeg)

## 边框
线宽、线类型、弧度。

### 线宽 + 颜色
```html
<div className="border border-sky-500 size-10"></div>
<div className="border-2 border-sky-500 size-10"></div>
<div className="border-4 border-sky-500 size-10"></div>
```

通过 `border-*` 设定线宽，颜色的设置也很简单：`border-颜色-数值`。

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840720912-e928747e-9cbf-4f02-a42e-46701b54f046.jpeg)

如果想要设定某一方向的边框：`border-*-数值`

+ border-t-数值 👉 `border-top-width: _;`
+ border-r-数值 👉 `border-right-width: _;`
+ border-b-数值 👉 `border-bottom-width: _;`
+ border-l-数值 👉 `border-left-width: _;`
+ border-x-数值 👉 `border-left-width: _; border-right-width: _;`
+ border-y-数值 👉 `border-top-width: _; border-right-width: _;`

注意 ⚠️：如果不加数值（例如：`border-t`），表示特定方向上的线宽为 1px。`border-0` 就是 `border-width: 0px;`。

### 线类型
border-solid 👉 `border-style: solid;`

border-dashed 👉 `border-style: dashed;`

border-dotted 👉 `border-style: dotted;`

border-double 👉 `border-style: double;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840721038-e59b701d-6c06-481c-bc55-4122bf135549.jpeg)

### 弧度
rounded 👉 `border-radius: 0.25rem; /* 4px */`

rounded-md 👉 `border-radius: 0.375rem; /* 6px */`

rounded-lg 👉 `border-radius: 0.5rem; /* 8px */`

rounded-full 👉 `border-start-start-radius: 9999px; border-end-start-radius: 9999px;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840721295-79a5ee37-2799-44a7-aa06-17bc31e5aea3.jpeg)

## 文本
字体（大小、对齐方式、加粗……）

### 字体大小
和前述章节类似，有关字体大小的相关使用也可以通过 `text-数值`、`text-[]` 的方式进行：

```html
<p className="text-sm">
  Lorem ipsum dolor sit amet, consectetur adipisicing elit. Unde officia.
</p>
<p className="text-base">
  Lorem ipsum dolor sit amet, consectetur adipisicing elit. Unde officia.
</p>
<p className="text-md">
  Lorem ipsum dolor sit amet, consectetur adipisicing elit. Unde officia.
</p>
<p className="text-[16px]">
  Lorem ipsum dolor sit amet, consectetur adipisicing elit. Unde officia.
</p>
<p className="text-lg">
  Lorem ipsum dolor sit amet, consectetur adipisicing elit. Unde officia.
</p>
<p className="text-xl">
  Lorem ipsum dolor sit amet, consectetur adipisicing elit. Unde officia.
</p>
```

其中，`text-base`、`text-md`、`text-[16px]` 都是一样的，取浏览器的字体默认值 16px。

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840721330-b45fa1cc-983d-4152-98b8-cf56ca35b49a.jpeg)

### 文本对齐方式
```html
<p className="text-left">
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Exercitationem
  adipisci minima dolore sit quas officiis velit sint! Dicta ad ea
  quaerat, sapiente provident et nihil cupiditate est, vero quo soluta.
</p>
<p className="text-center">
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Exercitationem
  adipisci minima dolore sit quas officiis velit sint! Dicta ad ea
  quaerat, sapiente provident et nihil cupiditate est, vero quo soluta.
</p>
<p className="text-right">
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Exercitationem
  adipisci minima dolore sit quas officiis velit sint! Dicta ad ea
  quaerat, sapiente provident et nihil cupiditate est, vero quo soluta.
</p>
<p className="text-justify">
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Exercitationem
  adipisci minima dolore sit quas officiis velit sint! Dicta ad ea
  quaerat, sapiente provident et nihil cupiditate est, vero quo soluta.
</p>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840721481-9d24da73-755f-47d9-8922-da307d754e34.jpeg)

### 字体斜体与加粗
```html
<p className="italic">Lorem ipsum! -- italic</p>
<p className="font-thin">Lorem ipsum! -- font-weight: 100;</p>
<p className="font-light">Lorem ipsum! -- font-weight: 300;</p>
<p className="font-normal">Lorem ipsum! -- font-weight: 400;</p>
<p className="font-bold">Lorem ipsum! -- font-weight: 700;</p>
<p className="font-black">Lorem ipsum! -- font-weight: 900;</p>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840721407-a1a58e66-bcda-43b7-a098-066200ec447a.jpeg)

## 颜色
颜色在之前的案例中都有接触，主要为：字体颜色、边框颜色、背景颜色、背景渐变色图像。

```html
<p className="text-red-500">Lorem ipsum! -- 文本颜色</p>
<p className="border-2 border-sky-500">Lorem ipsum! -- 边框颜色</p>
<p className="bg-orange-500">Lorem ipsum! -- 背景颜色</p>
<p className="bg-orange-500/75">Lorem ipsum! -- 背景颜色（75% 透明度）</p>
<p className="bg-orange-500/50">Lorem ipsum! -- 背景颜色（50% 透明度）</p>
<div className="bg-gradient-to-r from-purple-500 to-pink-500">
  向右渐变（purple-500 👉 pink-500）
</div>
<div className="bg-gradient-to-l from-transparent to-sky-500">
  向左渐变（sky-500 👈 transparent）
</div>
<div className="bg-gradient-to-r from-indigo-500 via-purple-500 to-pink-500">
  向右渐变（indigo-500 👉 purple-500 👉 pink-500）
</div>
```

字体、边框、背景、不同透明度的背景颜色都很容易理解。需要提一点的是渐变色，这里需要用 `from-颜色A`、`via-颜色B`、`to-颜色C` 来表示从颜色 A 经过 B，最后过渡到 C 的颜色变化。

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840721475-c31f8848-e163-483a-b3a1-abb190c01589.jpeg)

## 伪类
伪类是选择器的一种，它用于选择处于特定状态的元素，比如当它们是这一类型的第一个元素时，或者是当鼠标指针悬浮在元素上面的时候。

### :hover
鼠标悬浮在元素上面时出现的效果。

```html
<button className="border-2 bg-black text-white hover:bg-white hover:text-black">
  button
</button>
```

只要在想要实现 hover 效果的类名前添加 `hover:` 就可以了，例如上面悬浮时的按钮背景颜色被置为白色（`hover:bg-white`）、文字颜色置为黑色（`hover:text-black`）。

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840961698-0302a5a1-5d2e-4a6b-8dd3-704c71ded4c5.jpeg)

### :focus
按钮、输入框等表单控件聚焦时出现的效果。

```html
<input
  type="text"
  className="px-2 outline focus:outline-2 focus:outline-sky-500"
  placeholder="请聚焦这里..."
/>
```

+ outline 👉 `outline-style: solid;`
+ focus:outline-2, focus:outline-sky-500 👉

```html
input:focus {
  outline: 2px solid #0ea5e9;
}
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840961745-9977dc93-7278-404e-9b8f-9a9d0dc37f02.jpeg)

### :active
鼠标按住时就打开了 active 激活状态。

```html
<button className="bg-sky-500 active:bg-pink-500">button</button>
```

active:bg-pink-500 👉

```html
button:active {
  background-color: #EC4899;
}
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840961880-f534d2ad-57c2-4952-861c-392f2e8c8e66.jpeg)

## 伪元素
伪元素与伪类相似，不过表现得是像是往标记文本中加入全新的 HTML 元素一样，而不是向现有的元素上应用类。伪元素开头为双冒号`::`。例如：`::before`、`::after` 等等。

### ::before, ::after
```html
<label className="block">
  <span className="before:content-['⭐️'] before:mr-2 after:content-['*'] after:ml-0.5 after:text-red-500 block text-sm font-medium text-slate-700">
    Email
  </span>
  <input
    type="email"
    name="email"
    className="mt-1 px-3 py-2 bg-white border placeholder-slate-400 rounded-md"
    placeholder="请输入你的邮箱..."
  />
</label>
```

+ block 👉 `display: block;`
+ before:content-['⭐️'], before:mr-2 👉

```html
span::before {
  content: '⭐️';
  margin-right: 8px;
}
```

+ after:content-['*'], after:ml-0.5, after:text-red-500 👉

```html
span::after {
  content: '*';
  margin-left: 2px;
  color: #ef4444;
}
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840961719-c1d8801c-54d7-47a8-9637-59931b045da5.jpeg)

### placeholder
```html
<input
  className="placeholder:italic placeholder:text-yellow-200 text-white bg-neutral-700 border rounded-md py-2 px-3"
  placeholder="请输入搜索内容..."
  type="text"
  name="search"
/>
```

+ placeholder:italic 👉 设置字体为斜体
+ placeholder:text-yellow-200 👉 设置颜色

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840961638-7030cd27-9f80-4156-8601-58afeea63082.jpeg)

### file input
```html
<input
  type="file"
  className="block w-full text-sm text-neutral-700
              file:mr-4 file:py-2 file:px-4
              file:rounded-full file:border-0
              file:text-sm file:font-semibold
              file:bg-cyan-50 file:text-cyan-700
              hover:file:bg-cyan-100
            "
/>
```

+ file:mr-4 file:py-2 file:px-4 👉 设置边距
+ file:rounded-full file:border-0 👉 设置边框
+ file:text-sm file:font-semibold 👉 设置字体
+ file:bg-cyan-50 file:text-cyan-700 👉 设置颜色
+ hover:file:bg-cyan-100 👉 设置悬浮效果

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840962370-f389d123-f4dd-4347-bfb2-5ea307d44773.jpeg)

### selection
```html
<div className="selection:bg-amber-300 selection:text-amber-900">
  <p>
    Lorem ipsum dolor, sit amet consectetur adipisicing elit. Magni fugit
    distinctio accusamus? Aliquid minus eaque, nostrum id perferendis
    autem labore architecto quasi quidem, neque recusandae voluptates quam
    nisi. Dolor, repellendus.
  </p>
</div>
```

+ selection:bg-amber-300 👉 设置选中时的背景颜色
+ selection:text-amber-900 👉 设置选中时的字体颜色

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840962576-b24d1750-8849-463a-b36d-801dcee80193.jpeg)

## flex
网页的骨架非常重要，而骨架的设计来源是布局。首先是 flex 布局。

### **两栏布局**
```html
<div className="flex h-screen">
  <div className="bg-red-200 w-48">left</div>
  <div className="bg-blue-200 flex-1">right</div>
</div>
```

+ flex 👉 `display: flex;`
+ flex-1 👉 `flex: 1 1 0%;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840962532-0f2778cf-d622-472c-a3b4-ed02da74cdce.jpeg)

### **水平垂直方向居中**
```html
<div className="flex justify-center items-center h-screen">
  <div className="bg-red-200 w-48 h-48">center</div>
</div>
```

+ flex 👉 `display: flex;`
+ justify-center 👉 `justify-content: center;`
+ items-center 👉 `align-items: center;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840962672-19a67754-9852-4a0f-aa2e-bda22419c948.jpeg)

### **两端对齐导航栏**
```html
<nav className="flex justify-between">
  <div className="cursor-pointer text-center bg-red-500 flex-1">Home</div>
  <div className="cursor-pointer text-center bg-green-500 flex-1">
    About
  </div>
  <div className="cursor-pointer text-center bg-blue-500 flex-1">
    Contact
  </div>
</nav>
```

+ flex 👉 `display: flex;`
+ flex-1 👉 `flex: 1 1 0%;`
+ cursor-pointer 👉 `cursor: pointer;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840962485-7c5e3e64-16a3-488f-957a-4a5e6c0efccc.jpeg)

### **垂直方向菜单栏**
```html
<ul className="flex flex-col h-screen">
  <li className="border mb-2 py-4 px-2">#item1</li>
  <li className="border mb-2 py-4 px-2">#item2</li>
  <li className="border mb-2 py-4 px-2">#item3</li>
  <li className="border mb-2 py-4 px-2">#item4</li>
  <li className="border mb-2 py-4 px-2">#item5</li>
  <li className="border mb-2 py-4 px-2">#item6</li>
</ul>
```

+ flex 👉 `display: flex;`
+ flex-col 👉 `flex-direction: column;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840962907-64b4743c-50d6-4fd6-b525-ba7d846afdc6.jpeg)

## grid
### **两栏布局**
```html
<div className="grid grid-cols-2 h-screen">
  <div className="bg-red-200">left</div>
  <div className="bg-blue-200">right</div>
</div>
```

+ grid 👉 `display: grid;`
+ grid-cols-2 👉 `grid-template-columns: repeat(2, minmax(0, 1fr));`

### **水平垂直方向居中**
```html
<div className="grid place-items-center h-screen">
  <div className="bg-red-200 w-48 h-48">center</div>
</div>
```

+ grid 👉 `display: grid;`
+ place-items-center 👉 `place-items: center;`

### **导航栏**
```html
<nav className="grid grid-cols-3">
  <div className="cursor-pointer text-center bg-red-500">Home</div>
  <div className="cursor-pointer text-center bg-green-500">About</div>
  <div className="cursor-pointer text-center bg-blue-500">Contact</div>
</nav>
```

+ grid 👉 `display: grid;`
+ grid-cols-3 👉 `grid-template-columns: repeat(3, minmax(0, 1fr));`

### **图片画廊**
```html
<div className="grid grid-cols-3 gap-4">
  <img src="/next.svg" alt="image 1" className="w-full object-scale-down h-64" />
  <img
    src="/vercel.svg"
    alt="image 2"
    className="w-full object-contain h-64"
  />
  <img src="/next.svg" alt="image 3" className="w-full object-cover h-64" />
  <img
    src="/vercel.svg"
    alt="image 3"
    className="w-full object-fill h-64"
  />
</div>
```

+ grid 👉 `display: grid;`
+ grid-cols-3 👉 `grid-template-columns: repeat(3, minmax(0, 1fr));`
+ gap-4 👉 `gap: 1rem; /* 16px */`
+ object-scale-down: `object-fit: scale-down;`
+ object-contain: `object-fit: contain;`
+ object-cover: `object-fit: cover;`
+ object-fill: `object-fit: fill;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840963018-fa7b148d-dce6-4aaa-8b17-f783124a4903.jpeg)

## 定位
默认的定位 position 为 static，另外除了它和 relative 定位都会脱离文档流，也就是像气球那样依次飘起来。

### **relative + absolute**
```html
<div className="relative h-40 border-2 bg-sky-200">
  <p>父容器</p>
  <div className="absolute top-10 left-10 size-48 bg-red-300">
    子元素A
  </div>
  <div className="absolute top-16 left-16 size-48 bg-green-300">
    子元素B
  </div>
  <div className="absolute top-24 left-24 size-48 bg-blue-300">
    子元素C
  </div>
</div>
```

+ relative 👉 `position: relative;`
+ absolute 👉 `position: absolute;`
+ top-10 👉 `top: 2.5rem; /* 40px */`
+ left-10 👉 `left: 2.5rem; /* 40px */`

还有另外两个方向：

+ bottom-* 👉 `bottom: _;`
+ right-* 👉 `right: _;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840963008-529e4b70-3148-4522-9061-dab8dd34cd71.jpeg)

### **z-index**
默认情况下，定位元素的 z-index 都是逐级递增的，就像上面那样，C > B > A。想要子元素 B 的 z 坐标最高，只要加上 `z-*` 即可：

```html
<div className="absolute top-16 left-16 size-48 bg-green-300  gap-4">
  子元素B
</div>
```

+ z-10 👉 `z-index: 10;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840963117-9ee59125-c437-4e5a-adba-f1c50bf8d361.jpeg)

### **fixed**
固定定位，将添加按钮固定在右下方是移动端中的常用功能：

```html
<button className="fixed bottom-10 right-10 size-10 text-white text-xl bg-purple-500 rounded-full">
  +
</button>
```

+ fixed 👉 `position: fixed;`
+ bottom-10 👉 `bottom: 2.5rem; /* 40px */`
+ right-10 👉 `right: 2.5rem; /* 40px */`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840963320-69725a22-e9dc-48b6-8680-bf2ac597f659.jpeg)

### **sticky**
粘性定位，一般把导航栏贴在顶端：

```html
<ul className="h-40 border-2 overflow-auto">
  <nav className="sticky top-0 bg-teal-500">navigation</nav>
  <li>#item1</li>
  <li>#item2</li>
  <li>#item3</li>
  <li>#item4</li>
  <li>#item5</li>
  <li>#item6</li>
  <li>#item7</li>
  <li>#item8</li>
  <li>#item9</li>
  <li>#item10</li>
</ul>
```

+ overflow-auto 👉 `overflow: auto;`
+ sticky 👉 `position: sticky;`
+ top-0 👉 `top: 0;`

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736840963395-8ade7fde-a1fb-44e2-8503-6eb931c04595.jpeg)

## 形变
通过 transform 设置，元素可以进行平移、旋转、缩放、倾斜。

### 平移
通过 `translate-[x/y]-数值` 就能实现 x 轴或 y 轴的平移。

```html
<div className="translate-x-10 translate-y-10 size-20 bg-amber-500">translate</div>
```

+ translate-x-10 👉 `transform: translateX(2.5rem);`
+ translate-y-10 👉 `transform: translateY(2.5rem);`

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841232651-17f890a0-8df0-4bdb-8aab-55f844eac81b.webp)

如果想要实现相对于自身的百分比平移：

+ translate-x-1/2 👉 `transform: translateX(50%);`
+ translate-x-full 👉 `transform: translateX(100%);`
+ translate-y-1/3 👉 `transform: translateY(33.333333%);`
+ translate-y-full 👉 `transform: translateY(100%);`

### **结合子绝父相实现元素水平垂直居中**
```html
<div className="relative h-40 border-2">
  <div
    className="absolute top-1/2 left-1/2 translate-x-[-50%] translate-y-[-50%] size-20 bg-amber-500"
  ></div>
</div>
```

+ top-1/2 👉 `top: 50%;`
+ left-1/2 👉 `left: 50%;`
+ translate-x-[-50%], translate-y-[-50%] 👉 `transform: translate(-50%, -50%);`

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841232641-4284dab1-7991-44cb-8811-ef1c21685372.webp)

对于负值，`translate-x-[-50%]` 可以方便地写为：`-translate-x-[50%]` 或 `-translate-x-1/2`。因此上述代码等价于：

```html
<div className="relative h-40 border-2">
  <div
    className="absolute top-1/2 left-1/2 -translate-x-[50%] -translate-y-1/2 size-20 bg-amber-500"
  ></div>
</div>
```

### **旋转**
通过 `rotate-数值` 来旋转元素。

```html
<div className="rotate-45 size-20 bg-lime-500">rotate</div>
```

+ rotate-45 👉 `transform: rotate(45deg);`

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841232906-3a723912-0441-466e-ba30-2aaba7b07359.webp)

### **缩放**
通过 `scale-数值` 来缩放元素，例如：50 表示 0.5，75 表示 0.75，以此类推。也可以通过 `scale-[x/y]-数值` 对 x 轴或 y 轴来单独设置缩放比例。

```html
<div className="scale-50 size-20 bg-rose-300">scale 0.5</div>
<div className="scale-75 size-20 bg-rose-300">scale 0.75</div>
<div className="scale-100 size-20 bg-rose-300">scale 1</div>
<div className="scale-125 size-20 bg-rose-300">scale 1.25</div>
```

+ scale-50 👉 `transform: scale(.5);`
+ scale-75 👉 `transform: scale(.75);`
+ scale-100 👉 `transform: scale(1);`
+ scale-125 👉 `transform: scale(1.25);`

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841233021-63a4607c-b9b6-4b2b-82d8-e3bd54dd4825.webp)

### **倾斜**
通过 `skew-[x/y]-数值` 来倾斜元素。

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841232770-a14c3377-5483-4002-aa7f-69c5dd4b2424.webp)

## 过渡效果
使用过渡效果可以让元素移动等更加灵动。

### 常规使用
```html
<div
  className="transition size-20 -translate-x-[80%]  bg-sky-300 cursor-pointer hover:translate-x-0 hover:bg-sky-500"
></div>
```

通过给元素添加 transition 类名就相当于添加了以下声明：

```html
transition-property: color, background-color, border-color,
  text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter,
  backdrop-filter;
transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
transition-duration: 150ms;
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841233373-8c959aa0-3d57-404a-a816-e03b9804e53e.webp)

### 持续时间
持续时间是指这段过渡效果需要多长时间播放完。通过 `duration-数值` 就能设置，单位为 ms：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841233343-c5ffae1d-38d5-4df3-854e-0ac1542685a8.webp)

### 时间函数（速率函数）
通过设置 `ease-*` 来实现不同的时间函数：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841233439-2ac1aace-0738-40b2-b733-625948c77a60.webp)

+ ease-in 👉  以低速率开始，然后逐渐加快。
+ ease-out 👉  以高速率开始，然后逐渐减慢。
+ ease-in 👉  以低速率开始，然后逐渐加快，在结束时在此减慢。

### 延迟时间
延迟时间是指等多长时间再触发效果。通过 `delay-数值` 来实现：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841233450-b5be18c9-a273-4492-a179-cdf815e152ea.webp)

## 动画
在 Tailwind CSS 中，提供了可供直接使用的动画。

### animate-spin
```html
<div className="animate-spin bg-orange-500 size-10 rounded-full">...</div>
```

+ animate-spin 👉

```html
css

 代码解读
复制代码animation: spin 1s linear infinite;

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841233517-7502acb5-f5a5-44a5-9bd8-9707bf6ea960.webp)

### animate-ping
```html
<div
  className="animate-ping bg-teal-500 size-10 rounded-full text-center leading-[40px]"
>
  💗
</div>
```

+ animate-ping 👉

```html
animation: ping 1s cubic-bezier(0, 0, 0.2, 1) infinite;

@keyframes ping {
  75%,
  100% {
    transform: scale(2);
    opacity: 0;
  }
}
```

+ leading-[40px] 👉 `line-height: 40px;`

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841234006-39600202-7c98-4d11-a62b-e115421c79f0.webp)

### animate-pulse
```html
<div className="animate-pulse bg-gray-500 w-48 h-10 text-center leading-[40px]">
  Loading...
</div>
```

+ animate-pulse 👉

```html
css

 代码解读
复制代码animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;

@keyframes pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841234030-d956e47a-b17e-4102-9a9d-4f1d86816fd4.webp)

### animate-bounce
```html
<div
  className="animate-bounce bg-black/50 size-10 rounded-full text-center leading-[40px]"
>
  👇
</div>
```

+ animate-bounce 👉

```html
animation: bounce 1s infinite;

@keyframes bounce {
  0%,
  100% {
    transform: translateY(-25%);
    animation-timing-function: cubic-bezier(0.8, 0, 1, 1);
  }
  50% {
    transform: translateY(0);
    animation-timing-function: cubic-bezier(0, 0, 0.2, 1);
  }
}
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841234045-4db645c9-b41c-4c0b-9dd3-ea1fc6a46c14.webp)



## 什么是可以复用的？
在开始之前，首先要明确一个问题：什么可以复用？

### `.container`
在网页开发中，`.container` 是一个出现频率极高的容器类，它用来做什么呢？给网页框定一个容器范围，例如：网页的展示区域宽度范围为 1280px - 1920px：

```html
<div className="h-screen bg-sky-200 min-w-[1280px] max-w-[1920px] mx-auto"></div>
```

当浏览器窗口宽度小于 1280px 时会出现横向滚动条以确保能够显示 1280px 宽度的页面内容，当浏览器窗口大于 1920px 时，最多显示 1920px 的页面宽度。

又或者只是把内容展示在 1280px 的范围中：

```html
<div className="h-screen bg-sky-200 w-[1280px] mx-auto"></div>
```

在以往的开发中，控制宽度的样式声明会放在一起，然后封装在 `.container` 这个 CSS 类中，以后者为例，CSS 会是这样：

```html
.container {
  width: 1280px;
  margin: 0 auto;
}
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841294484-aceeda73-1f79-4700-929d-247bc35eaa7c.jpeg)

### `.btn`
按钮样式也容易复用，假设有一个普通按钮如下：

```html
<button className="bg-black text-white min-w-[80px]">button</button>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841294470-f6fda0bc-2a2d-4be6-8699-7b938fb0638c.jpeg)

通常样式被封装在 `.btn` 中：

```html
.btn {
  min-width: 80px;
  color: white;
  background-color: black;
}
```

### `.center`
又或者是水平垂直居中，这在开发中就更常见了：

```html
<div
  className="w-[1280px] mx-auto my-2 h-48 border-2 flex justify-center items-center"
>
  <div className="bg-sky-500 size-20"></div>
</div>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841294576-9f035021-404f-4e84-afb4-741f32774eb8.jpeg)

`.center` 类的声明如下：

```html
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

那么这些复用的类在 Tailwind CSS 中应该如何处理呢？

## 自定义指令
Tailwind CSS 使用了一种专属的 at 规则（[at-rules](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FCSS%2FAt-rule)）的 CSS 语句用来定义 CSS 如何运行。

### `@tailwind`
在配置 Tailwind CSS 时，有一步非常重要的步骤就是在全局样式（例如：globals.css）中添加：

```html
@tailwind base;
@tailwind components;
@tailwind utilities;
```

`@tailwind` 指令用于将 Tailwind 中的 base、components、utilities 三个层级的样式插入到全局样式中。

+ base：这是最基础的层级，在这个层级上，Tailwind 提供了一些界定基础样式的规则。例如 margin、padding、color、font-size 等等。
+ components：在这个层级可以创建可复用的样式块，例如：按钮、卡片等。默认情况下是空的。
+ utilities：作为工具层级，包括了 Tailwind 的大部分功能，例如： layout、flex、grid、spacing（margin 和 padding）、colors、typography、borders 等等。

### `@layer` + `@apply`
@layer 这个指令告诉 Tailwind 想要把对应的样式放在上述哪一个层级（base、components、utilities）。在实际使用中需要配合 @apply**，**它将允许我们使用现有的 Tailwind CSS 类。

上一节提到的 `.btn` 属于 components（组件级别的复用样式），而 `.container`、`.center` 属于 utilities（更为底层的样式应用），在 globals.css 中添加以下代码：

```html
@layer components {
  .btn {
    @apply bg-black text-white min-w-[80px];
  }
}

@layer utilities {
  .container {
    @apply w-[1280px] mx-auto;
  }
  .center {
    @apply flex items-center justify-center;
  }
}
```

设置完成后，就可以直接使用了。

+ **.container**

```html
<div className="h-screen bg-sky-200 w-[1280px] mx-auto"></div>
```

等价于：

```html
<div className="h-screen bg-sky-200 container"></div>
```

+ .**btn**

```html
<button className="bg-black text-white min-w-[80px]">button</button>
```

等价于：

```html
<button className="btn">button</button>
```

+ **.center**

```html
<div
  className="w-[1280px] mx-auto my-2 h-48 border-2 flex justify-center items-center"
>
  <div className="bg-sky-500 size-20"></div>
</div>
```

等价于：

```html
<div className="w-[1280px] mx-auto my-2 h-48 border-2 center">
  <div className="bg-sky-500 size-20"></div>
</div>
```

## 自定义函数
Tailwind CSS 提供了两个实用的自定义函数——theme() 和 screen()，它们的作用是在我们自己的 CSS 样式中使用 Tailwind CSS 中的特定值。

### `theme()`
使用 `theme()` 函数获取 Tailwind 样式变量，下面是一个在 React 中的使用示例：

```html
/* my-style.module.css */
.content-area {
  height: calc(100vh - theme(spacing.12));
}
```

然后引入到组件中：

```html
import styles from './my-style.module.css';

export default function Page() {
  return (
    <div className={`container my-2 border-2 center ${styles['content-area']}`}>
      <div className="bg-sky-500 size-20"></div>
    </div>
  );
}
```

spacing 是一个空间刻度系统，在基础篇中使用的数值就是基于这套系统。上述的 spacing.12 表示的是数值为 12 时的空间距离：

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841294464-c4515f92-6c25-44b0-84aa-0f0e8808f796.jpeg)

也就是 3rem，等价于 48px。因此最后的效果如下：

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841294699-39c1b70d-f10d-4462-8286-328d1f41b5f4.jpeg)

### `screen()`
以往写媒体查询比较麻烦，需要定义各种条件而且容易搞混：

```html
@media screen and (min-width: 640px) {
  /* ... */
}
```

现在可以用 `screen()` 函数快速创建媒体查询，减少很多心智负担：

```html
@media screen(sm) {
  /* ... */
}
```

媒体查询相关内容将在《响应式篇》深入，感兴趣的话可以先关注~

## 自定义样式
有时候 Tailwind CSS 预定义的样式没有我们想要使用的默认值，只能自己写。

### 任意值
第一种方式是使用任意值，这在基础篇已经用过很多次了，就是通过 `name-[]` 的方式。

中括号中放入任意值，可以是**长度单位**：

```html
<div className="w-[200px] h-[200px] bg-sky-500"></div>
<div className="w-[15em] h-[20rem] bg-red-500"></div>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841294995-98f87f4e-921d-4d2f-9776-b75b89f294a5.jpeg)

颜色值：

```html
<p className="text-[#0c0c0c]">cool color</p>
```

文本内容：

```html
<p className='before:content-["✨"]'>star!</p>
```

甚至是 `theme()` 函数：

```html
<p className="w-[theme(spacing.96)]">
  Lorem ipsum dolor sit, amet consectetur adipisicing elit. Voluptate doloribus
  aliquam tempore ducimus iure at cupiditate harum dicta voluptas voluptatum,
  quidem quasi sunt. Quo quia molestias iure quod ducimus culpa.
</p>
```

或者是使用 `var()` 定义的变量：

```html
/* global.css */
:root {
  --my-color: #ff4200;
}
```

```html
<p className="text-[--my-color]">
  Lorem ipsum dolor sit amet consectetur adipisicing elit. Consectetur
  repellendus, animi, pariatur expedita omnis nihil assumenda cum ratione ipsam,
  eveniet sit tempora placeat voluptatum voluptas ea odio quisquam nisi!
  Repellendus!
</p>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841294996-074a8240-b6b8-4257-b8bf-3c31bd1d63ff.jpeg)

### `@layer` + `theme()`
在自定义指令 `@layer` 中已经掌握了如何创建自己的自定义组件、工具样式。再结合 `theme()`，可以更加方便地编写自己的样式代码。

例如有一个 card 样式：

```html
<div className="size-48 p-5 rounded-md bg-sky-300">my card</div>
```

![](https://cdn.nlark.com/yuque/0/2025/jpeg/8379512/1736841295002-c097d212-fdb7-46bd-860e-199e7e764791.jpeg)

使用 `@layer` + `@apply`：

```html
@layer components {
  .card {
    @apply size-48 rounded-md bg-sky-300;
  }
}
```

使用 `@layer` + `theme()`：

```html
@layer components {
  .card {
    width: theme(spacing.48);
    height: theme(spacing.48);
    padding: theme(spacing.5);
    border-radius: theme('borderRadius.md');
    background-color: theme('colors.sky.300');
  }
}
```

后者的写法和写原生就很像了，只需要掌握 Tailwind 的内置系统规则即可。之前提到 spacing 是一个刻度系统，同样还有颜色 colors、边框弧度 borderRadius 等等。

最后只需要愉快地写下这样一行代码就能搞定卡片样式：

```html
<div className="card">my card</div>
```

以上就是如何实现样式复用的全部内容，总的来说就是两个方案：

+ `@layer` + `@apply`
+ `@layer` + `theme()`

不过仍然有一些需要注意的地方，我们最好不要滥用这些方法，虽然可以方便书写，但是过多的封装会造成打包体积的增大，可以直接写 Tailwind 的地方就用预设值直接写，除非是在文中提到的那种十分常见的样式才有必要封装起来。



经过初始化后，在根目录下有一个 tailwind.config.ts 文件：

```html
ts

 代码解读
复制代码import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}",
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
      backgroundImage: {
        "gradient-radial": "radial-gradient(var(--tw-gradient-stops))",
        "gradient-conic":
          "conic-gradient(from 180deg at 50% 50%, var(--tw-gradient-stops))",
      },
    },
  },
  plugins: [],
};
export default config;
```

上面就是 Tailwind CSS 的配置文件，这篇文章主要讲解 `theme` 配置项，利用它来实现样式的扩展。

  


## content
`content` 配置项将会接收一个数组，表示应用 Tailwind CSS 的文件范围：

+ `"./pages/**/*.{js,ts,jsx,tsx,mdx}"` 👉 ./pages 目录下无限级别子目录中的所有以 js、ts、jsx、tsx、mdx 结尾的文件
+ `"./components/**/*.{js,ts,jsx,tsx,mdx}"` 👉 ./components 下无限级别子目录中的所有以 js、ts、jsx、tsx、mdx 结尾的文件
+ `"./app/**/*.{js,ts,jsx,tsx,mdx}"` 👉 ./app 下无限级别子目录中的所有以 js、ts、jsx、tsx、mdx 结尾的文件

其中，`**/*` 表示该目录下的无限级别子目录。

## theme
theme 在之前已经遇到过，使用 `theme()` 函数可以获取 Tailwind 默认的样式变量，比如：颜色搭配、字体、边框、响应式断点等等内容。

### 覆盖原样式
默认情况下，初始化时就会生成默认的配置内容，具体看[这里](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ftailwindlabs%2Ftailwindcss%2Fblob%2Fmaster%2Fstubs%2Fconfig.full.js)。

如果说要覆盖掉默认的样式，比如，覆盖掉默认颜色：

```html
import type { Config } from 'tailwindcss';

const config: Config = {
  // ...
  theme: {
    colors: {
      blue: '#1fb6ff',
      purple: '#7e5bef',
      pink: '#ff49db',
      orange: '#ff7849',
      green: '#13ce66',
      yellow: '#ffc82c',
      'gray-dark': '#273444',
      gray: '#8492a6',
      'gray-light': '#d3dce6',
    },
  },
};
export default config;
```

`colors` 用于定义默认颜色，因此想要使用的颜色应该写在那里。使用看看：

```html
<pre className="text-blue">blue: &apos;#1fb6ff&apos;</pre>
<pre className="text-purple">purple: &apos;#7e5bef&apos;</pre>
<pre className="text-pink">purple: &apos;#ff49db&apos;</pre>
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841452251-71dcb134-2e8a-4da0-a93b-a5a20cac9371.webp)

上面代码中的 `&apos;` 表示 HTML 中的 `'` 。

由于覆盖了 `colors` 对象的配置，原来的默认颜色就不起作用了，比如下面的 `red-200` 就不起作用：

```html
<p className="text-red-200">text-red-200 没有效果</p>
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841452522-d11fec91-a0cf-4043-af30-253ff0638b48.webp)

尽管 `colors` 的值被覆盖了，但是其他的配置不受影响，比如 `spacing`，它们继续继承默认值。

### 扩展原样式
更多的时候，我们希望使用 Tailwind 的便利，同时添加更多的选择。比如，想要追加一些新的颜色，那么就在 `extend` 对象中添加：

```html
import type { Config } from 'tailwindcss';

const config: Config = {
  // ...
  theme: {
    // ...
    extend: {
      colors: {
        'primary-dark': '#1f2937',
        'primary-light': '#f3f4f6',
        'secondary-dark': '#1f2937',
        'secondary-light': '#f3f4f6',
      },
    },
  },
};
export default config;
```

然后使用这些新增的颜色：

```html
<p className="text-primary-dark">primary-dark</p>
<p className="text-primary-light bg-primary-dark">primary-dark</p>
<p className="text-secondary-dark">primary-dark</p>
<p className="text-secondary-light bg-secondary-dark">primary-dark</p>
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841452500-91f12e84-375c-45f0-b38d-bad50270de22.webp)

### 可扩展的关键词
除了 `colors`，之前的文章中提到的 `spacing`，又或者是控制响应式 `screens`，也都可以被覆盖或扩展。下面是一张完整的样式声明配置及其对应关系的描述表格：

| 关键词 | 描述 |
| --- | --- |
| accentColor | Values for the accent-color property |
| animation | Values for the animation property |
| aria | Values for the aria property |
| aspectRatio | Values for the aspect-ratio property |
| backdropBlur | Values for the backdropBlur plugin |
| backdropBrightness | Values for the backdropBrightness plugin |
| backdropContrast | Values for the backdropContrast plugin |
| backdropGrayscale | Values for the backdropGrayscale plugin |
| backdropHueRotate | Values for the backdropHueRotate plugin |
| backdropInvert | Values for the backdropInvert plugin |
| backdropOpacity | Values for the backdropOpacity plugin |
| backdropSaturate | Values for the backdropSaturate plugin |
| backdropSepia | Values for the backdropSepia plugin |
| backgroundColor | Values for the background-color property |
| backgroundImage | Values for the background-image property |
| backgroundOpacity | Values for the background-opacity property |
| backgroundPosition | Values for the background-position property |
| backgroundSize | Values for the background-size property |
| blur | Values for the blur plugin |
| borderColor | Values for the border-color property |
| borderOpacity | Values for the borderOpacity plugin |
| borderRadius | Values for the border-radius property |
| borderSpacing | Values for the border-spacing property |
| borderWidth | Values for the borderWidth plugin |
| boxShadow | Values for the box-shadow property |
| boxShadowColor | Values for the boxShadowColor plugin |
| brightness | Values for the brightness plugin |
| caretColor | Values for the caret-color property |
| colors | Your project's color palette |
| columns | Values for the columns property |
| container | Configuration for the container plugin |
| content | Values for the content property |
| contrast | Values for the contrast plugin |
| cursor | Values for the cursor property |
| divideColor | Values for the divideColor plugin |
| divideOpacity | Values for the divideOpacity plugin |
| divideWidth | Values for the divideWidth plugin |
| dropShadow | Values for the dropShadow plugin |
| fill | Values for the fill plugin |
| flex | Values for the flex property |
| flexBasis | Values for the flex-basis property |
| flexGrow | Values for the flex-grow property |
| flexShrink | Values for the flex-shrink property |
| fontFamily | Values for the font-family property |
| fontSize | Values for the font-size property |
| fontWeight | Values for the font-weight property |
| gap | Values for the gap property |
| gradientColorStops | Values for the gradientColorStops plugin |
| gradientColorStopPositions | Values for the gradient-color-stop-positions property |
| grayscale | Values for the grayscale plugin |
| gridAutoColumns | Values for the grid-auto-columns property |
| gridAutoRows | Values for the grid-auto-rows property |
| gridColumn | Values for the grid-column property |
| gridColumnEnd | Values for the grid-column-end property |
| gridColumnStart | Values for the grid-column-start property |
| gridRow | Values for the grid-row property |
| gridRowEnd | Values for the grid-row-end property |
| gridRowStart | Values for the grid-row-start property |
| gridTemplateColumns | Values for the grid-template-columns property |
| gridTemplateRows | Values for the grid-template-rows property |
| height | Values for the height property |
| hueRotate | Values for the hueRotate plugin |
| inset | Values for the top, right, bottom, and left properties |
| invert | Values for the invert plugin |
| keyframes | Keyframe values used in the animation plugin |
| letterSpacing | Values for the letter-spacing property |
| lineHeight | Values for the line-height property |
| listStyleType | Values for the list-style-type property |
| listStyleImage | Values for the list-style-image property |
| margin | Values for the margin property |
| lineClamp | Values for the line-clamp property |
| maxHeight | Values for the max-height property |
| maxWidth | Values for the max-width property |
| minHeight | Values for the min-height property |
| minWidth | Values for the min-width property |
| objectPosition | Values for the object-position property |
| opacity | Values for the opacity property |
| order | Values for the order property |
| outlineColor | Values for the outline-color property |
| outlineOffset | Values for the outline-offset property |
| outlineWidth | Values for the outline-width property |
| padding | Values for the padding property |
| placeholderColor | Values for the placeholderColor plugin |
| placeholderOpacity | Values for the placeholderOpacity plugin |
| ringColor | Values for the ringColor plugin |
| ringOffsetColor | Values for the ringOffsetColor plugin |
| ringOffsetWidth | Values for the ringOffsetWidth plugin |
| ringOpacity | Values for the ringOpacity plugin |
| ringWidth | Values for the ringWidth plugin |
| rotate | Values for the rotate plugin |
| saturate | Values for the saturate plugin |
| scale | Values for the scale plugin |
| screens | Your project's responsive breakpoints |
| scrollMargin | Values for the scroll-margin property |
| scrollPadding | Values for the scroll-padding property |
| sepia | Values for the sepia plugin |
| skew | Values for the skew plugin |
| space | Values for the space plugin |
| spacing | Your project's spacing scale |
| stroke | Values for the stroke property |
| strokeWidth | Values for the stroke-width property |
| supports | Values for the supports property |
| data | Values for the data property |
| textColor | Values for the text-color property |
| textDecorationColor | Values for the text-decoration-color property |
| textDecorationThickness | Values for the text-decoration-thickness property |
| textIndent | Values for the text-indent property |
| textOpacity | Values for the textOpacity plugin |
| textUnderlineOffset | Values for the text-underline-offset property |
| transformOrigin | Values for the transform-origin property |
| transitionDelay | Values for the transition-delay property |
| transitionDuration | Values for the transition-duration property |
| transitionProperty | Values for the transition-property property |
| transitionTimingFunction | Values for the transition-timing-function property |
| translate | Values for the translate plugin |
| size | Values for the size property |
| width | Values for the width property |
| willChange | Values for the will-change property |
| zIndex | Values for the z-index property |


## 预处理器的使用
在上一篇中有朋友评论：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841452438-4111b5d6-fc49-4f03-b21d-7004c5bcf0b1.webp)

### postcss-nesting
官方推荐的 `postcss-nesting` 插件可以满足嵌套的需求。首先安装依赖：

```html
bash

 代码解读
复制代码npm install -D postcss-nesting
```

然后放进 postcss 处理器配置中（postcss.config.mjs）：

```html
js

 代码解读
复制代码/** @type {import('postcss-load-config').Config} */
const config = {
  plugins: {
    'tailwindcss/nesting': 'postcss-nesting',
    tailwindcss: {},
  },
};

export default config;
```

现在使用一下子，在 global.css 中添加以下代码：

```html
css

 代码解读
复制代码/* ... */

@layer utilities {
  .container {
    @apply w-[1280px] mx-auto;
    span {
      @apply text-lg text-blue;
    }
  }
}
```

`.container` 用于控制容器内容居中，`span` 元素嵌套在其中。

```html
html

 代码解读
复制代码<div className="container bg-gray-light">
  <p>
    Lorem ipsum <span>dolor</span> sit amet, consectetur adipisicing elit.
    Sit accusamus incidunt, minima eligendi delectus sint facere cum,
    placeat dolorum rem debitis <span>doloribus</span> dolore nesciunt
    ratione laudantium <span>doloribusstop</span>enim error architecto
    odio!
  </p>
</div>
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841452493-a8d556e3-043d-4f9c-8cc6-029e27c89a4f.webp)

实测有效。

### 就是想用 Sass
笔者头铁，笔者就是想用 Sass！

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841453205-32add650-9ab8-469d-a613-09661be24759.webp)

也不是不行，不过需要知道一件事：预处理器（Sass 之类的）和 Tailwind CSS 是在不同的阶段处理的。预处理器首先处理其输入文件并生成 CSS，然后 Tailwind CSS 和 PostCSS 在预处理器生成的 CSS 上继续处理。

也就是说，不能把 Tailwind 的 `theme()` 函数的输出传给一个 Sass 的颜色函数，比如：

```html
css

 代码解读
复制代码.error {
  background-color: darken(theme('colors.red.500'), 10%);
}
.btn:hover {
  background-color: light(theme('colors.red.500'), 10%);
}
```

`darken($color, $amount)` 和 `lighten($color, $amount)` 就是 Sass 中的颜色函数。由于 Sass 在 Tailwind 之前运行，还未生成 CSS，因此 `theme()` 并不可用。

以 React 和 Sass 为例，有 Demo.module.scss 如下：

```html
css

 代码解读
复制代码.container {
  @apply w-[1280px] mx-auto;
  span {
    @apply text-lg text-blue;
  }
}

.title {
  // @apply text-4xl font-bold;
  font-size: 36px;
  line-height: 40px;
  font-weight: bold;
}

.success {
  background-color: theme('colors.green');
}
```

引入到组件中使用：

```html
typescript

 代码解读
复制代码import styles from './Demo.module.scss';

export default function Page() {
  return (
    <div>
      <div className={styles.container}>
        <h1 className={styles.title}>hello</h1>
        <p>
          Lorem ipsum <span>dolor</span> sit amet, consectetur adipisicing elit.
          Sit accusamus incidunt, minima eligendi delectus sint facere cum,
          placeat dolorum rem debitis <span>doloribus</span> dolore nesciunt
          ratione laudantium <span>doloribusstop</span>enim error architecto
          odio!
        </p>
        <p className={styles.success}>
          Lorem ipsum dolor, sit amet consectetur adipisicing elit. Corporis,
          maiores eos. Aliquam reiciendis, totam eos deserunt earum, quos velit
          quo magnam temporibus quaerat voluptate expedita nostrum eligendi
          aspernatur fuga harum!
        </p>
      </div>
    </div>
  );
}
```

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841453294-65fc2648-44e3-4d91-bcb5-e2ea3bae693d.webp)

## 媒体查询
在以往，我们使用 CSS3 进行开发时会通过媒体查询来实现。比如有代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      /* 样式代码 */
    </style>
  </head>
  <body>
    <div>
      <div id="app"></div>
    </div>
    <script>
      class App {
        constructor() {
          this.el = document.getElementById('app');
          this.init();
        }
        init() {
          this.render();
          this.watch();
        }
        render() {
          this.el.innerHTML = 'first render';
        }
        watch() {
          window.addEventListener('resize', () => {
            const width = window.innerWidth;
            this.el.innerHTML = `${width}px`;
          });
        }
      }
      const app = new App();
    </script>
  </body>
</html>
```

这是一个简单的 Demo，当用户调整浏览器宽度时就会更新宽度显示，而且在不同的宽度下背景颜色发生变化。

### `@media`
样式代码是这样的：

```html
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #fff;
  font-size: 48px;
}
@media screen and (min-width: 640px) {
  #app {
    background-color: red;
  }
}
@media screen and (min-width: 768px) {
  #app {
    background-color: orange;
  }
}
@media screen and (min-width: 1024px) {
  #app {
    background-color: yellow;
  }
}
@media screen and (min-width: 1280px) {
  #app {
    background-color: green;
  }
}
@media screen and (min-width: 1536px) {
  #app {
    background-color: blue;
  }
}
```

上面用到了 CSS3 的一个特性：`@media`，`screen` 表示设备类型为屏幕，通过 `and` 关键字来并列多个配置。`min-width` 表示最小宽度，如果按照一把直尺的刻度去理解，最小宽度就是一条线段的左侧较小值，往右，宽度大于这个值时就会应用对应的样式声明。

### 断点
上面的多个媒体查询定义决定了响应式的不同断点，这些断点被称为 **breakpoints**，这是每一个具备响应式系统的 UI 规范都要考虑的。上面的 5 个断点对应如下：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841510627-31e4c59e-1fd0-4aa3-926d-87c859f06b25.webp)

+ small：640px 起，横向模式显示状态的手机（640px 到下一个断点显示为红色）
+ medium：768px 起，平板（768px 到下一个断点显示为橙色）
+ large：1024px 起，电脑（1024px 到下一个断点显示为蛋心色）
+ x large：1280px 起，大型电脑（1280px 到下一个断点显示为绿色）
+ 2x large：1536px 起，更大型电脑（1536px 到下一个断点显示为蓝色）

### mobile first
mobile first 是一个比较通用的概念——优先考虑移动设备，随着屏幕变大再去适应更大的设备宽度。这主要依靠上面所说的断点机制，下面来看在 Tailwind CSS 中如何使用响应式。

## Tailwind CSS 中的响应式系统
在 Tailwind CSS 中不需要像上面那样写复杂的媒体查询声明代码了，而是通过提前预设好的 CSS 类名来定义元素的样式。

### 断点
Tailwind CSS 的断点和 2.2 中提到的断点一样，与类名的对应关系如下：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841510196-ca5ceee4-93f5-479b-8a4f-8a4fac898f05.webp)

### 断点前缀:类名
通过 `断点前缀:类名` 的形式就能定义元素的响应式样式，下面是一个简单的使用案例：

```html
'use client';

import useWindowWidth from '@/hooks/useWindowWidth';

export default function Home() {
  const width = useWindowWidth();

  return (
    <main className="text-center sm:bg-red-300 md:bg-orange-300 lg:bg-yellow-300 xl:bg-green-300 2xl:bg-blue-300">
      <div>hello tailwind css</div>
      <div>current width: {width}px</div>
    </main>
  );
}
```

以 Next.js 中的 React 为例：

+ `sm:bg-red-300` 表示在 640px 以上时为红色
+ `md:bg-orange-300` 表示 768px 以上时为橙色
+ …

其他依次类推，`sm`、`md` 等都是断点前缀，冒号后面是想要定义的样式类名。

最终效果如下所示：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841510172-717a345d-46de-431c-99f2-55a0e977eb87.webp)

需要注意的是，没有加上断点前缀的类名默认是作用于所有设备大小的，比如上面代码中的 `text-center` ，可以看到在任何大小下都是文本居中对齐的。

### 定义范围
如果我只想在 `sm` 到 `lg` 的范围内应用背景颜色怎么办？显然只写 `sm:bg-red-300` 是不行的，因为它表示的是大于 640px 宽度都会应用到红色。

解决办法是使用 `*:max-*` （`*` 表示断点前缀）为它定义一段范围：

```html
<main className="text-center sm:max-lg:bg-red-300">
  <div>hello tailwind css</div>
  <div>current width: {width}px</div>
</main>
```

+ `sm:max-lg:bg-red-300` 表示在 `sm` 到 `lg` 这段范围内（640px～1024px）应用红色

## 自定义配置
同样在 tailwind.config.ts 中来配置各种自定义的规则，对于响应式的部分则在 `theme.screens` 中去定义。

### 覆盖默认值
默认情况下断点前缀的配置如下所示：

```html
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    screens: {
      'sm': '640px',
      // => @media (min-width: 640px) { ... }

      'md': '768px',
      // => @media (min-width: 768px) { ... }

      'lg': '1024px',
      // => @media (min-width: 1024px) { ... }

      'xl': '1280px',
      // => @media (min-width: 1280px) { ... }

      '2xl': '1536px',
      // => @media (min-width: 1536px) { ... }
    }
  }
}
```

覆盖默认值的方法很简单，直接在 `theme.screens` 中替换掉原来的值即可，例如：

```html
jsx

 代码解读
复制代码theme: {
  screens: {
    sm: '576px',
    // => @media (min-width: 576px) { ... }

    md: '960px',
    // => @media (min-width: 960px) { ... }

    lg: '1440px',
    // => @media (min-width: 1440px) { ... }
  },
},
```

注意：默认值有 `sm`、`md`、`lg`、`xl`、`2xl` 等等，因此上面只写了 `sm`、`md`、`lg` 三种，实际上能应用的也就只有这三种。因此，这不是覆盖原来的某个断点，而是直接覆盖了 `theme.screens` 对象的所有值。

### 覆盖原来值
如果只是想覆盖掉某一个断点，只需要在 `theme.extend.screens` 中修改，比如我想覆盖 `sm` 断点：

```html
jsx

 代码解读
复制代码extend: {
  screens: {
    sm: '576px',
    // => @media (min-width: 576px) { ... }
  },
}
```

这样，`sm` 的值就从 640px 变为 576px，因此，从 576px 开始，背景颜色就是红色了：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841510183-58607d0e-6451-4239-b3bf-bf5f455072d4.webp)

### 添加更大的断点
在 `theme.extend.screens` 中也可以添加更大范围的断点：

```html
extend: {
  screens: {
    '3xl': '1600px',
    // => @media (min-width: 1600px) { ... }
  },
}
```

给 `main` 添加 `3xl` 的断点：

```html
<main className="text-center sm:bg-red-300 md:bg-orange-300 lg:bg-yellow-300 xl:bg-green-300 2xl:bg-blue-300 3xl:bg-slate-300">
  <div>hello tailwind css</div>
  <div>current width: {width}px</div>
</main>
```

效果如下：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841510132-2f7d0424-8927-4e14-8bae-be8350bc19c1.webp)

### 添加更小的断点
虽然更大的断点可以通过 `theme.extend.screens` 来实现，但是更小的却不可以这么做，原因在于 `extend` 是向原来的内容进行追加，所以是在最后一位，而 tailwind 需要它们按照从小到大的顺序排序。

在 `tailwindcss` 模块中有一个 `defaultTheme` 对象，这里存放着默认断点值，只要将默认值和自定义的更小范围断点结合在一起就可以实现了：

```html
import type { Config } from 'tailwindcss';
import defaultTheme from 'tailwindcss/defaultTheme';

const config: Config = {
  // ...
  theme: {
    screens: {
      xs: '475px',
      ...defaultTheme.screens,
    },
  },
};
export default config;
```

之后就可以使用 `xs` 了：

```html
jsx

 代码解读
复制代码<main className="text-center xs:bg-purple-300 sm:bg-red-300 md:bg-orange-300 lg:bg-yellow-300 xl:bg-green-300 2xl:bg-blue-300 3xl:bg-slate-300">
  <div>hello tailwind css</div>
  <div>current width: {width}px</div>
</main>
```

+ `xs:bg-purple-300` 表示 475px 起背景颜色为紫色

效果如下：

![](https://cdn.nlark.com/yuque/0/2025/webp/8379512/1736841510698-9a1892f1-1ad2-4216-b232-c04a4603671a.webp)

### 定义断点范围
在 3.3 节通过 `*:max-*` 的方式实现了断点范围的控制，在配置文件中还提供了一种更加快捷的方案：

```html
theme: {
  screens: {
    sm: { min: '640px', max: '767px' },
    // => @media (min-width: 640px and max-width: 767px) { ... }

    md: { min: '768px', max: '1023px' },
    // => @media (min-width: 768px and max-width: 1023px) { ... }

    lg: { min: '1024px', max: '1279px' },
    // => @media (min-width: 1024px and max-width: 1279px) { ... }

    xl: { min: '1280px', max: '1535px' },
    // => @media (min-width: 1280px and max-width: 1535px) { ... }

    '2xl': { min: '1536px' },
    // => @media (min-width: 1536px) { ... }
  },
}
```

默认情况下的断点范围较大，比如 `sm` 表示 640px 起，这样一旦 `md` 等断点都没有去设置就会都应用 `sm` 的样式了。像上面这样的配置就控制了断点的范围使之更加符合响应式的本质。

有下面代码：

```html
tsx

 代码解读
复制代码<main className="text-center sm:bg-red-300">
  <div>hello tailwind css</div>
  <div>current width: {width}px</div>
</main>
```

只有在屏幕宽度处于 640px ～ 767px 的范围下，背景颜色才表现为红色。

## 5. 总结
这篇文章主要讲解了传统响应式和 TailwindCSS 中的响应式，有了 Tailwind 就不用再去写媒体查询了，这对于开发者来说是一个喜讯。在开发响应式方面最值得关注的就是断点，控制好断点也就控制好了响应式。除了上述提到的配置特性，还有自定义名称、自定义多个范围、自定义查询等等，这类特性其实并不是开发必需的，只属于灵活性的范畴，如果需要可以自行查阅文档。

