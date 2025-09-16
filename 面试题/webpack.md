# webpack

## 谈谈你对 Webpack的认识？

WebPack是一个模块打包工具，可以使用 WebPack管理模块依赖，并编译输岀模块所需的静态文件。它能够很好地管理与打包Web开发中所用到的HTML、 JavaScript 、CSS以及各种静态文件（图片、字体等），让开发过程更加高效。对于不同类型的资源， WebPack有对应的模块加载器。Web Pack模块打包器会分析模块间的依赖关系，最后生成优化且合并后的静态资源。

两大最核心的特点：**一切皆模块**和**按需加载**

与其他构建工具相比，有如下优势：

- 智能解析：对 CommonJS 、 AMD 、ES6 的语法做了兼容
- 万物模块：对 js、css、图片等资源文件都支持打包
- 开箱即用：HRM、Tree-shaking等功能
- 代码分割：可以将代码切割成不同的 chunk，实现按需加载，降低了初始化时间
- 插件系统，具有强大的 Plugin 接口，具有更好的灵活性和扩展性
- 易于调试：支持 SourceUrls 和 SourceMaps
- 快速运行：webpack 使用异步 IO 并具有多级缓存，这使得 webpack 很快且在增量编译上更加快
- 生态环境好：社区更丰富，出现的问题更容易解决

## loader的作用，常用loader有哪些？

**Loader 作用是转换特定类型的文件**

webpack 默认支持处理 JS 与 JSON 文件，其他类型（`css`、`sass`、`png`等）都处理不了，这里必须借助 Loader 来对不同类型的文件的进行处理。

**loader的配置**

- 配置方式（推荐）：在 webpack.config.js文件中`module.rules`属性中指定 loader

  > + 对象属性`test` 为匹配的规则，一般情况为正则表达式;
  > + 对象属性`use`针对匹配到文件类型，使用什么加载(`loader`)和使用的参数（`options`），也是数组
  >
  > ```js
  > module.exports = {
  >   module: {
  >     rules: [
  >       {
  >         test: /\.css$/,
  >         use: [
  >           { loader: 'style-loader' },
  >           {
  >             loader: 'css-loader',
  >             options: {
  >               modules: true
  >             }
  >           },
  >           { loader: 'sass-loader' }
  >         ]
  >       }
  >     ]
  >   }
  > };
  > ```
  >
  
- 内联方式：在每个 import 语句中显式指定 loader

- CLI 方式：在 shell 命令中指定它们

**loader的特性**

- loade 支持链式调用，**调用方式是从右到左，从下到上**；
- loader 可以是同步的，也可以是异步的
- loader 运行在 Node.js 中，并且能够执行任何操作
- 除了常见的通过 `package.json` 的 `main` 来将一个 npm 模块导出为 loader，还可以在 module.rules 中使用 `loader` 字段直接引用一个模块
- 插件(plugin)可以为 loader 带来更多特性
- loader 能够产生额外的任意文件

**常用的 loader**

### babel-loader

- `babel-loader` 使用 Babel **将 ES6 语法转化为 ES5**
- `@babel/core` Babel 编译的核心包
- `@babel/preset-env` Babel 编译的预设，可以理解为 Babel 插件的超集

1. ```bash
   npm install babel-loader @babel/core @babel/preset-env -D
   ```

2. Babel 配置文件

   ```js
   // ./babelrc.js
   module.exports = {
     presets: [
       [
         "@babel/preset-env",
         { 
           useBuiltIns: "entry",
           corejs: "3.9.1", // 是 core-js 版本号
           targets: {
             chrome: "58",
             ie: "11",
           },
         },
       ],
     ],
   };
   ```

常见 Babel 预设还有：

- `@babel/preset-flow`
- `@babel/preset-react`
- `@babel/preset-typescript`

对于正在提案中，还未进入 ECMA 规范中的新特性，Babel 是无法进行处理的，必须要安装对应的插件，例如:**@ 装饰器写法**

- `@babel/plugin-proposal-decorators`
- `@babel/plugin-proposal-class-properties`

```js
// ./babelrc.js
module.exports = {
  presets: [
   ...
  ],
  plugins: [    
    ["@babel/plugin-proposal-decorators", { legacy: true }],
    ["@babel/plugin-proposal-class-properties", { loose: true }],
  ]
};
```

### css-loader

加载 CSS（`.css`文件进行一个解析，而并不会将解析后的`css`插入到页面中），支持模块化、压缩、文件导入等特性

### style-loader

将处理好的 css样式通过 style 标签的形式添加到页面 `head` 中

### autoprefixer-loader(弃用)

处理CSS3属性前缀，**已被弃用，建议直接使用postcss**

### postcss-loader

自动添加 CSS3 部分属性的浏览器前缀

1. ```bash
   # postcss-loader需要搭配 postcss   postcss-preset-env
   npm i postcss postcss-preset-env postcss-loader -D
   ```

2. 创建postcss 配置文件 `postcss.config.js`

   ```js
   // postcss.config.js
   module.exports = {
     plugins: [require('postcss-preset-env')]
   }
   ```

3. 创建 postcss-preset-env 配置文件 `.browserslistrc`

   ```bash
   # 换行相当于 and
   last 2 versions # 回退两个浏览器版本
   > 0.5% # 全球超过0.5%人使用的浏览器，可以通过 caniuse.com 查看不同浏览器不同版本占有率
   IE 10 # 兼容IE 10
   ```

   如果你对 `.browserslistrc` 不同配置产生的效果感兴趣，可以使用 [autoprefixer](https://link.juejin.cn/?target=http%3A%2F%2Fautoprefixer.github.io%2F) 进行在线转化查看效果

### less-loader

处理less文件

### sass-loader

处理sass/scss文件

1. ```bash
   # sass-loader 需要搭配 node-sass
   npm i postcss postcss-preset-env postcss-loader -D
   
   test: /\.(s[ac]|c)ss$/i, *//匹配所有的 sass/scss/css 文件
   ```

### file-loader

分发文件到output目录（默认为 dist），在代码中通过相对 URL 去引用输出的文件（解析字体，图片）**仅推荐webpack4及以下使用**

### url-loader

配置和file-loader类似（解析字体，图片），但是遇到图片格式的模块，当文件小于设定的limit时，可以选择性的把图片转成 `base64` 格式的字符串，并打包到 `js` 中(内链至head上)，对小体积的图片比较合适，大图片不合适。大于 limit 值的时候依然是使用 file-loader 进行拷贝**仅推荐webpack4及以下使用**

### img-loader

加载并且压缩图片文件

### raw-loader

+ 在`webpack `中通过 `import `方式导入文件内容，该`loader `并不是内置的

+ 在html模版引入文件

> 1. 基于HtmlWebpackPlugin**低**版本,使用方法 ${...}
>
>    ```html
>    <script>
>      ${require('raw-loader!babel-loader!../node_modules/lib-flexible/flexible.js')}</script>
>    ```
>
> 2. 基于HtmlWebpackPlugin**高**版本,使用方法<%= ... %>
>
>    ```html
>    <!DOCTYPE html>
>    <html lang="en">
>      <head>
>        <!-- 引用meta -->
>        <%= require('raw-loader!./meta.html') %>
>        <title>Document</title>
>         <!-- 引用js -->
>        <script> 
>    				<%= require('raw-loader!babel-loader!../node_modules/lib-flexible/flexible.js') %> 
>        </script>
>      </head>
>      <body>
>        <div id="root"></div>
>      </body>
>    </html>
>    ```

### eslint-loader

通过 ESLint 检查 JavaScript 代码

## Plugin的作用，常用的Plugin有哪些？

Plugin作用是**赋予其各种灵活的功能**，例如打包优化、资源管理、环境变量注入等，它们会运行在 `webpack` 的不同阶段（钩子 / 生命周期），**贯穿了`webpack`整个编译周期**。

**通过配置文件导出对象中`plugins`属性传入`new`实例对象**

### html-webpack-plugin

配置html模版。将打包生成将s 或者 css 文件可以自动引入到 Html 中

```js
// webpack.config.js
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
 ...
  plugins: [
     new HtmlWebpackPlugin({
       title: "My App",
       filename: "app.html",
       template: "./src/html/index.html"
     }) 
  ]
};
```

**html模版**

```html
<!--./src/html/index.html-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title><%=htmlWebpackPlugin.options.title%></title>
</head>
<body>
    <h1>html-webpack-plugin</h1>
</body>
</html>
```

在 `html` 模板中，可以通过 `<%=htmlWebpackPlugin.options.XXX%>` 的方式获取配置的值

### clean-webpack-plugin

**清理构建目录。**每次打包的时候，打包目录都会遗留上次打包的文件，为了保持打包目录的纯净，我们需要在打包前将打包目录清空

### webpack-dev-server

**开启本地服务**、热更新、静态文件访问、proxy跨域。

+ 当版本 `version >= 4.0.0` 时，需要使用`devServer.static`进行配置，不再有 `devServer.contentBase` 配置项

+ **为什么要配置 contentBase ？**

  因为 webpack 在进行打包的时候，对静态文件的处理，例如图片，都是直接 copy 到 dist 目录下面。但是对于本地开发来说，这个过程太费时，也没有必要，所以在设置 contentBase 之后，就直接到对应的静态目录下面去读取文件，而不需对文件做任何移动，节省了时间和性能开销。

### mini-css-extract-plugin

提取 `CSS` 到一个单独的文件中,使用link标签引入

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
    module: {
        rules: [
            {
              	test:/\.(le|c)ss$/,// 匹配less/css 文件
                use: [
                    MiniCssExtractPlugin.loader, 
                    'css-loader',
                    'less-loader'
                ]
            },
        ]
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: '[name]_[contenthash:8].css'
        }),
    ]
};
```

### terser-webpack-plugin

启动多线程压缩 JS，往下文档有demo

### css-minimizer-webpack-plugin

webpack5，启动多线程压缩 CSS，往下文档有demo

### optimize-css-assets-webpack-plugin

webpack4用法，压缩 CSS

```js
// npm i optimize-css-assets-webpack-plugin cssnano -D
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');

module.exports = {
    mode: 'production',
    ......
    plugins:[
        // 压缩CSS文件
        new OptimizeCSSAssetsPlugin({
            assetNameRegExp: /\.css$/g,
            cssProcessor: require('cssnano')
        }),        
    ],
};
```

### webpack-bundle-analyzer

可视化webpack输出文件的体积，往下文档有demo

### speed-measure-webpack-plugin

构建速度分析，往下文档有demo

**friendly-errors-webpack-plugin**

在设置stats: 'errors-only'的同时，配合friendly-errors-webpack-plugin使用，**让构建⽇志更友好**

1. 依赖安装

```bash
npm i friendly-errors-webpack-plugin -D
```

2. 插件使用

```js
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');

module.exports = {
    ......
    plugins:[
        new FriendlyErrorsWebpackPlugin()
    ],
  	stats: 'errors-only'//只在发生错误时输出
}
```

##  webpack的Loader和Plugin的不同？

**不同的作用:**

- **Loader**直译为"加载器"。Webpack将一切文件视为模块，但是webpack原生是只能解析js文件，如果想将其他文件也打包的话，就会用到`loader`。 所以Loader的作用是让webpack拥有了加载和解析*非JavaScript文件*的能力。
- **Plugin**直译为"插件"。Plugin可以扩展webpack的功能，让webpack具有更多的灵活性。 在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

**不同的用法:**

- **Loader**在`module.rules`中配置，也就是说他作为模块的解析规则而存在。 类型为数组，每一项都是一个`Object`，里面描述了对于什么类型的文件（`test`），使用什么加载(`loader`)和使用的参数（`options`）
- **Plugin**在`plugins`中单独配置。 类型为数组，每一项是一个`plugin`的实例，参数都通过构造函数传入。

## 如何用webpack来优化前端性能？

## 构建速度分析

speed-measure-webpack-plugin

```js
// 费时分析
const SpeedMeasurePlugin = require("speed-measure-webpack-plugin");
const smp = new SpeedMeasurePlugin();
...

const config = {...}

module.exports = (env, argv) => {
  // 这里可以通过不同的模式修改 config 配置
  return smp.wrap(config);
}
```

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8abe8e0dfeb94ffe94adce229d04d0e3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 优化resolve 配置

在开发中我们会有各种各样的模块依赖，这些模块可能来自于自己编写的代码，也可能来自第三方库， `resolve`可以帮助`webpack`从每个 `require/import` 语句中，找到需要引入到合适的模块代码

#### alias

用的创建 `import` 或 `require` 的**别名**，用来简化模块引用，项目中基本都需要进行配置。

特别当我们的项目目录结构比较深的时候，一个文件的路径可能是`./../../`的形式

通过配置`alias`以减少查找过程

```js
const path = require('path')
...
// 路径处理方法
function resolve(dir){
  return path.join(__dirname, dir);
}

 const config  = {
  ...
  resolve:{
    // 配置别名
    alias: {
      '~': resolve('src'),
      '@': resolve('src'),
      'components': resolve('src/components'),
    }
  }
};
```

配置完成之后，我们在项目中就可以

```js
// 使用 src 别名 ~ 
import '~/fonts/iconfont.css'

// 使用 src 别名 @ 
import '@/fonts/iconfont.css'

// 使用 components 别名
import footer from "components/footer";
```

#### extensions

解析模块时**自动添加拓展名**

webpack 默认配置

```js
const config = {
  //...
  resolve: {
    extensions: ['.js', '.json', '.wasm'],
  },
};
```

当我们引入文件的时候，若没有文件后缀名，则会根据数组内的值依次查找。

如果用户引入模块时不带扩展名，例如

```js
import file from '../path/to/file';
```

那么 webpack 就会按照 extensions 配置的数组**从左到右的顺序**去尝试解析模块。当我们配置的时候，则不要随便把所有后缀都写在里面，这会调用多次文件的查找，这样就会减慢打包速度

需要注意的是：

1. 高频文件后缀名放前面；
2. 手动配置后，默认配置会被覆盖

如果想保留默认配置，可以用 `...` 扩展运算符代表默认配置，例如

```js
const config = {
  //...
  resolve: {
    extensions: ['.ts', '...'], 
  },
};
```

#### modules

**解析模块时应该搜索的目录**

`resolve.modules` 用于配置 `webpack` 去哪些目录下寻找第三方模块。默认值为`['node_modules']`，所以默认会从`node_modules`中查找文件 当安装的第三方模块都放在项目根目录下的 `./node_modules `目录下时，所以可以指明存放第三方模块的绝对路径，以减少寻找，配置如下：

```js
const path = require('path');

// 路径处理方法
function resolve(dir){
  return path.join(__dirname, dir);
}

const config = {
  //...
  resolve: {
     // 使用绝对路径指明第三方模块存放的位置，以减少搜索步骤
     modules: [resolve('src'), 'node_modules'],
  },
};
```

告诉 webpack 优先 src 目录下查找需要解析的文件，会大大节省查找时间

### resolveLoader

resolveLoader 与上面的 resolve 对象的属性集合相同， 但仅用于解析 webpack 的 [loader](https://link.juejin.cn?target=https%3A%2F%2Fwebpack.docschina.org%2Fconcepts%2Floaders) 包。

**一般情况下保持默认配置就可以了，但如果你有自定义的 Loader 就需要配置一下**，不配可能会因为找不到 loader 报错

- 例如：我们在 loader 文件夹下面，放着我们自己写的 loader

我们就可以怎么配置

```js
const path = require('path');

// 路径处理方法
function resolve(dir){
  return path.join(__dirname, dir);
}

const config = {
  //...
  resolveLoader: {
    modules: ['node_modules',resolve('loader')]
  },
};
```

### externals

`externals` 配置选项提供了「**从输出的 bundle 中排除依赖**」的方法。此功能通常对 **library 开发人员**来说是最有用的，然而也会有各种各样的应用程序用到它。

例如，用script标签从 CDN 引入 jQuery，而不是把它打包：

1. 引入链接

```html
<script
  src="https://code.jquery.com/jquery-3.1.0.js"
  integrity="sha256-slogkvB1K3VOkzAI8QITxV3VzpOnkeNVsKvtkYLMjfk="
  crossorigin="anonymous"
></script>
```

2. 配置 externals

```js
const config = {
  //...
  externals: {
    jquery: 'jQuery',
  },
};
```

3. 使用 jQuery

```js
import $ from 'jquery';

$('.my-element').animate(/* ... */);
```

我们可以用这样的方法来剥离不需要改动的一些依赖，大大节省打包构建的时间。

### 优化loader配置

在配置 loader 的时候，我们需要更精确的去指定 loader 的作用目录或者需要排除的目录，通过使用 `include` 和 `exclude` 两个配置项，可以实现这个功能，常见的例如：

#### include

符合条件的模块进行解析

path.resolve(__dirname, 'src')

#### exclude

排除符合条件的模块，不解析，优先级更高

例如在配置 babel 的时候

```js
const path = require('path');

// 路径处理方法
function resolve(dir){
  return path.join(__dirname, dir);
}

const config = {
  //...
  module: { 
    noParse: /jquery|lodash/,
    rules: [
      {
        test: /\.js$/i,
        // 只对项目根目录下的 src 目录中的文件采用 babel-loader
        include: resolve('src'),
        exclude: /node_modules/,
        // babel-loader 支持缓存转换出的结果，通过 cacheDirectory 选项开启
        use: ['babel-loader?cacheDirectory'],
      },
      // ...
    ]
  }
};
```

#### noParse

- 不需要解析依赖的第三方大型类库等，可以通过这个字段进行配置，以提高构建速度
- 使用 noParse 进行忽略的模块文件中不会解析 `import`、`require` 等语法

```js
const config = {
  //...
  module: { 
    noParse: /jquery|lodash/,
    rules:[...]
  }

};
复制代码
```

#### IgnorePlugin

防止在 `import` 或 `require` 调用时，生成以下正则表达式匹配的模块：

- `requestRegExp` 匹配(test)资源请求路径的正则表达式。
- `contextRegExp` 匹配(test)资源上下文（目录）的正则表达式。

```js
new webpack.IgnorePlugin({ resourceRegExp, contextRegExp });
// new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)
```

以下示例演示了此插件的几种用法。

1. 安装 moment 插件（时间处理库）

```js
$ npm i -S moment
```

1. 配置 IgnorePlugin

```js
// 引入 webpack
const webpack = require('webpack')

const config = {
  ...
  plugins:[ // 配置插件
    ...
    new webpack.IgnorePlugin({
      resourceRegExp: /^\.\/locale$/,
      contextRegExp: /moment$/,
    }),
  ]  
};
```

目的是将插件中的非中文语音排除掉，这样就可以大大节省打包的体积了

### 多进程配置

**注意**：实际上在小型项目中，开启多进程打包反而会增加时间成本，因为启动进程和进程间通信都会有一定开销。

#### thread-loader

配置在 thread-loader 之后的 loader 都会在一个单独的 worker 池（worker pool）中运行

1. 安装

```bash
$ npm i -D  thread-loader
```

2. 配置

```js
const path = require('path');

// 路径处理方法
function resolve(dir){
  return path.join(__dirname, dir);
}

const config = {
  //...
  module: { 
    noParse: /jquery|lodash/,
    rules: [
      {
        test: /\.js$/i,
        include: resolve('src'),
        exclude: /node_modules/,
        use: [
          {
            loader: 'thread-loader', // 开启多进程打包
            options: {
              worker: 3,
            }
          },
          'babel-loader',
        ]
      },
      // ...
    ]
  }
};
```

#### happypack （弃用）

同样为开启多进程打包的工具，webpack5 已弃用。

利用进程并行编译loader,利用缓存来使得 rebuild 更快,遗憾的是作者表示已经不会继续开发此项目,类似的替代者是**thread-loader**

### 利用缓存

利用缓存可以大幅提升重复构建的速度

#### babel-loader中使用cacheDirectory

- babel 在转译 js 过程中时间开销比价大，将 babel-loader 的执行结果缓存起来，重新打包的时候，直接读取缓存
- 缓存位置： `node_modules/.cache/babel-loader`

具体配置如下：

```js
const config = {
 module: { 
    noParse: /jquery|lodash/,
    rules: [
      {
        test: /\.js$/i,
        include: resolve('src'),
        exclude: /node_modules/,
        use: [
          // ...
          {
            loader: 'babel-loader',
            options: {
              cacheDirectory: true // 启用缓存
            }
          },
        ]
      },
      // ...
    ]
  }
}
复制代码
```

那其他的 loader 如何将结果缓存呢？

cache-loader就可以帮我们完成这件事情

#### cache-loader

- 在一些性能开销较大的 `loader `之前添加 `cache-loader`，以将结果缓存到磁盘里，显著提升二次构建速度

  > 保存和读取这些缓存文件会有一些时间开销，所以请只对性能开销较大的 

- 缓存位置：`node_modules/.cache/cache-loader`

1. 安装

```bash
$ npm i -D cache-loader
```

1. 配置 cache-loader

```js
const config = {
 module: { 
    rules: [
      {
        test: /\.(s[ac]|c)ss$/i, //匹配所有的 sass/scss/css 文件
        use: [
          // 'style-loader',
          MiniCssExtractPlugin.loader,
          'cache-loader', // 获取前面 loader 转换的结果
          'css-loader',
          'postcss-loader',
          'sass-loader', 
        ]
      }
    ]
  }
}
```

#### cache 持久化缓存

通过配置 cache 缓存生成的 webpack 模块和 chunk，来改善构建速度。

```js
const config = {
  cache: {
    type: 'filesystem',
  },
};
```

## 

#### hard-source-webpack-plugin（弃用）

hard-source-webpack-plugin)为模块提供了中间缓存，重复构建时间大约可以减少 80%，但是在 **webpack5 中已经内置了模块缓存，不需要再使用此插件**

#### dll （弃用）

采用webpack的 DllPlugin 和 DllReferencePlugin 引入dll，让一些基本不会改动的代码先打包成静态资源,避免反复编译浪费时间。**在 webpack5.x 中已经不建议使用这种方式进行模块缓存，因为其已经内置了更好体验的 cache 方法**

`DLL`全称是 动态链接库，是为软件在winodw种实现共享函数库的一种实现方式，而Webpack也内置了DLL的功能，为的就是可以共享，不经常改变的代码，抽成一个共享的库。这个库在之后的编译过程中，会被引入到其他项目的代码中

使用步骤分成两部分：

1. **打包一个 DLL 库**

`webpack`内置了一个`DllPlugin`可以帮助我们打包一个DLL的库文件

```js
module.exports = {
    ...
    plugins:[
        new webpack.DllPlugin({
            name:'dll_[name]',
            path:path.resolve(__dirname,"./dll/[name].mainfest.json")
        })
    ]
}
```

2. **引入 DLL 库**

使用 `webpack` 自带的 `DllReferencePlugin` 插件对 `mainfest.json` 映射文件进行分析，获取要使用的`DLL`库

然后再通过`AddAssetHtmlPlugin`插件，将我们打包的`DLL`库引入到`Html`模块中

```js
module.exports = {
    ...
    new webpack.DllReferencePlugin({
        context:path.resolve(__dirname,"./dll/dll_react.js"),
        mainfest:path.resolve(__dirname,"./dll/react.mainfest.json")
    }),
    new AddAssetHtmlPlugin({
        outputPath:"./auto",
        filepath:path.resolve(__dirname,"./dll/dll_react.js")
    })
}
```

## 构建体积分析

借助插件 webpack-bundle-analyzer 我们可以直观的看到打包结果中，文件的体积大小、各模块依赖关系、文件是够重复等问题，极大的方便我们在进行项目优化的时候，进行问题诊断。

1. 安装

```bash
$ npm i -D webpack-bundle-analyzer
```

2. 配置插件

```js
// 引入插件
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin

const config = {
  // ...
  plugins:[ 
    // ...
    // 配置插件 
    new BundleAnalyzerPlugin({
      // analyzerMode: 'disabled',  // 不启动展示打包报告的http服务器
      // generateStatsFile: true, // 是否生成stats.json文件
    })
  ],
};
复制代码
```

3. 修改启动命令

```json
 "scripts": {
    // ...
    "analyzer": "cross-env NODE_ENV=prod webpack --progress --mode production"
  },
复制代码
```

1. 执行编译命令 `npm run analyzer`

打包结束后，会自行启动地址为 `http://127.0.0.1:8888` 的 web 服务，访问地址就可以看到

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ee392f0838bd43e5aeeb405c76f2fbc7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

如果，我们只想保留数据不想启动 web 服务，这个时候，我们可以加上两个配置

```js
new BundleAnalyzerPlugin({
   analyzerMode: 'disabled',  // 不启动展示打包报告的http服务器
   generateStatsFile: true, // 是否生成stats.json文件
})
```

这样再次执行打包的时候就只会产生 state.json 的文件了

### 压缩Html代码

使用`HtmlWebpackPlugin`插件来生成`HTML`的模板时候，通过配置属性`minify`进行`html`优化

```js
// npm install HtmlwebpackPlugin -D
module.exports = {
    ...
    plugin:[
        new HtmlwebpackPlugin({
            ...
            minify:{
                minifyCSS:false, // 是否压缩css
                collapseWhitespace:false, // 是否折叠空格
                removeComments:true // 是否移除注释
            }
        })
    ]
}
```

设置了`minify`，实际会使用另一个插件`html-minifier-terser`

### 启动多线程压缩 CSS

1. 安装 `css-minimizer-webpack-plugin`

```bash
$ npm install -D css-minimizer-webpack-plugin
```

1. 修改 `webapck.config.js` 配置

```js
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')
module.exports = {
    // ...
    optimization: {
        minimize: true,
        minimizer: [
            new CssMinimizerPlugin({
                parallel: true  // 使用多进程并行运行来提高构建速度
            })
        ]
    }
}
```

1. 查看打包结果

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b3174e204994b2e9c845f5bbc144577~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 启动多线程压缩 JS

在生成环境下打包默认会开启 js 压缩，但是当我们手动配置 `optimization` 选项之后，就不再默认对 js 进行压缩，需要我们手动去配置。

因为 webpack5 内置了terser-webpack-plugin插件，所以我们**不需重复安装，直接引用**就可以了，具体配置如下

```js
const TerserPlugin = require('terser-webpack-plugin');

const config = {
  // ...
  optimization: {
    minimize: true, // 开启最小化
    minimizer: [
      new TerserPlugin({
        parallel: true, // 使用多进程并行运行来提高构建速度
      })
    ]
  },
  // ...
}
```

### 图片压缩

一般来说在打包之后，一些图片文件的大小是远远要比 `js` 或者 `css` 文件要来的大，所以图片压缩较为重要

```js
module: {
  rules: [
    {
      test: /\.(png|jpg|gif)$/,
      use: [
        {
          loader: 'file-loader',
          options: {
            name: '[name]_[hash].[ext]',
            outputPath: 'images/',
          }
        },
        {
          loader: 'image-webpack-loader',
          options: {
            // 压缩 jpeg 的配置
            mozjpeg: {
              progressive: true,
              quality: 65
            },
            // 使用 imagemin**-optipng 压缩 png，enable: false 为关闭
            optipng: {
              enabled: false,
            },
            // 使用 imagemin-pngquant 压缩 png
            pngquant: {
              quality: '65-90',
              speed: 4
            },
            // 压缩 gif 的配置
            gifsicle: {
              interlaced: false,
            },
            // 开启 webp，会把 jpg 和 png 图片压缩为 webp 格式
            webp: {
              quality: 75
            }
          }
        }
      ]
    },
  ]
} 
```

### 公共脚本分离

将代码分离到不同的`bundle`中，之后我们可以按需加载，或者并行加载这些文件

默认情况下，所有的`JavaScript`代码（业务代码、第三方依赖、暂时没有用到的模块）在**首页全部都加载，就会影响首页的加载速度**

代码分离可以分出出更小的`bundle`，以及控制资源加载优先级，提供代码的加载性能

这里通过`splitChunksPlugin`来实现，该插件`webpack`已经默认安装和集成，只需要配置即可

默认配置中，chunks仅仅针对于异步（async）请求，我们可以设置为initial或者all

```js
module.exports = {    ...    
     optimization:{        
       			splitChunks:{            
              chunks:"all"        
            }    
     }
}
```

`splitChunks`主要属性有如下：

- Chunks，对同步代码还是异步代码进行处理

  > + async 异步引⼊的库进⾏分离(默认); 
  >
  > + initial 同步引⼊的库进⾏分离;
  >
  > + all 所有引⼊的库进⾏分离(推荐)

- minSize： 拆分包的大小, 至少为minSize，如何包的大小不超过minSize，这个包不会拆分

- maxSize： 将大于maxSize的包，拆分为不小于minSize的包

- minChunks：被引入的次数，默认是1

- test: 匹配出需要分离的包

### ScopeHoisting

Scope Hoisting 即**作用域提升**，原理是将多个模块放在同一个作用域下，并重命名防止命名冲突，**通过这种方式可以减少函数声明和内存开销**。

原理：将所有模块的代码按照引⽤顺序放在⼀个函数作⽤域⾥，然后适当的重命名⼀些变量以防⽌变量名冲突；**必须是ES6语法，CJS不⽀持**

- **webpack4之后其mode为production默认开启**
- webpack构建后的代码存在⼤量闭包代码；⼤量作⽤域包裹代码，导致体积增⼤（模块越多越明显）
- 运⾏代码时创建的函数作⽤域变多，内存开销变⼤

### treeshaking(摇树优化)

**剔除没有使用的代码，以降低包的体积**

+ 概念1个模块可能有多个⽅法，只要其中的某个⽅法被使⽤到了，且代码不可有副作用的。则整个⽂件都会被打到bundle⾥⾯去，treeshaking就是只把⽤到的⽅法打⼊bundle，没⽤到的⽅法会在uglify阶段被擦除掉。

+ 原理

  > 1. ES6 Module引入进行静态分析，故而编译的时候正确判断到底加载了那些模块
  > 2. 静态分析程序流，判断那些模块和变量未被使用或者引用，进而删除对应代码

+ 要求：**必须是ES6的语法**，CJS的⽅式不⽀持。

+ DCE(Deadcodeelimination)：

  > 1. 代码不会被执⾏，不可到达
  > 2. 代码执⾏的结果不会被⽤到
  > 3. 代码只会影响死变量（只写不读）

```js
import React from 'react';
import ReactDOM from 'react-dom';
import {a,b,c} from './tree-shaking'

if(false){
 // 代码不会被执⾏
    a()
}

class Search extends React.Component {
    render() {
        // funcB没有使用，代码执⾏的结果不会被⽤到
        const funcB = b()
        // funcC才是满足条件的
        const funcC = c()
        return( <div> 
            {funcC}搜索文字的内容
        </div>)
    }
}

ReactDOM.render(
    <Search />,
    document.getElementById('root')
);
```

- webpack 默认支持，需要在 .bablerc 里面设置 `model：false`，即可在生产环境下默认开启

```js
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        module: false,// 开启Tree-shaking 
      },
    ],
  ],
};
```

### 

