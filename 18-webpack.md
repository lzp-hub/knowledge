+ 为什么要用构建工具

> 转换ES6语法、转换JSX、CSS前缀补全/预处理器、压缩混淆、图片压缩...

+ 为什么选择webpack

> 社区⽣态丰富、配置灵活和插件化扩展、官⽅更新迭代速度快

### 环境搭建

#### 安装 webpack 和 webpack-cli

1. 创建空⽬录和 package.json
```
mkdir my-project
cd my-project
npm init -y
```
2. 安装 webpack 和 webpack-cli
> 检查是否安装成功：./node_modules/.bin/webpack -v

```
npm install webpack webpack-cli --save-dev
```
3. 运行通过 npm run build 运⾏构建
> 1. 模块局部安装会在 **node_modules/.bin**⽬录创建软链接，可通过scripts下的命令访问
>
> 2. 若webpack配置文件是更改名字或者更改了目录，我们需要使用--config指定。
>
>    例如："webpack --config webpack.prod.js" 或者 "webpack --config ./build/webpack.config.js" 
```json
{
  "name": "my-project",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "build":"webpack"
  },
  "devDependencies": {
    "webpack": "^5.70.0",
    "webpack-cli": "^4.9.2"
  }
}
```
#### webpack.config.js

```js
// 默认配置⽂件
const path = require('path');

module.exports = {
    // 打包的⼊⼝⽂件
    entry: './src/index.js', //指定默认的 entry 为： ./src/index.js
    // output: './dist/main.js', // 指定默认的 output 为： ./dist/main.js
    // 打包的输出
    output: {
        path: path.join(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    // 环境
    mode: 'production',
  	resolve: {
    	// 路径别名
    	alias: {
     	 "@": path.resolve("./src")
    	},
    // 默认[".js", ".json"]可以省略，但是jsx不可省略，这里将jsx添加为默认扩展名
    	extensions: [".js", ".jsx"]
    },
    // Loader 配置
    module: {
    // test指定匹配规则;use指定使⽤的loader名称
        rules: [
            {
                test:/.js$/,
                use:'babel-loader'
            },
        ]
    },
    // 插件配置
    plugins: [
        new HtmlwebpackPlugin({
            template: './src/index.html'
        })
    ]
};
```

### webpack 基础组成

#### Entry：打包⼊⼝

+ 单⼊⼝：entry 是⼀个字符串
```js
    entry:'./src/index.js'
```
+ 多⼊⼝：entry 是⼀个对象
```js
    entry: {
        index:'./src/index.js',
        search:'./src/search.js'
    }
```

#### Output：出口

将编译后的⽂件输出到磁盘

+ 单页面入⼝

```js
    const path = require('path');
    
    entry:'./src/index.js',
    output: {
        path: path.join(__dirname, 'dist'),
        filename: 'bundle.js'
    },
```

+ 多页面入⼝

```js
    const path = require('path');
    
    entry: {
        index:'./src/index.js',
        search:'./src/search.js'
    },
    output: {
        path: path.join(__dirname, 'dist'),
        // name通过占位符确保文件名称的唯一
        filename: '[name].js'
    }
```

#### Loaders

+ webpack默认只支持处理JS和JSON两种文件类型，通过**Loaders去支持其它文件类型并且把它们转化成有效的模块**，并且可以添加到依赖图中。
+ 本身是一个函数，接受源文件作为参数，返回转换的结果
+ use数组书写顺序需注意：**loader是从右往左加载的**

+ 常⻅的Loaders

> 1. babel-loader	转换$ES6、ES7等JS新特性语法
> 2. css-loader       支持.css文件的加载和解析
> 3. less-loader	  将less文件转换成css
> 4. ts-loader		 将TS转换成JS
> 5. file-loader       进行图片、字体等的打包
> 6. raw-loader      将文件以字符串的形式导入
> 7. thread-loader 多进程打包JS和CSS

#### Plugins

插件⽤于bundle**⽂件的优化，资源管理和环境变量注⼊作⽤于整个构建过程**

- 常⻅的Plugins

> CommonsChunkPlugin			将chunks相同的模块代码提取成公共
> CleanWebpackPlugin				清理构建目录
> ExtractTextWebpackPlugin	   将CSS从 bunlde 文件里提取成一个独立的CsS文件
> CopyWebpackPlugin				将文件或者文件夹拷贝到构建的输出目录
> HtmlWebpackPlugin				 创建html 文件去承载输出的 bundle
> UglifyjsWebpackPlugin			 压缩Js
> ZipWebpackPlugin					将打包出的资源生成一个zip包js

#### Mode

指定当前的构建环境是：production、development还是none

+ development

> 设置 process.env.NODE_ENV 的值为 development .开启
> NamedChunksPlugin # NamedModulesPlugin

+ production

> 设置 process.env.NODE_ENV 的值为 production . 开启
> FlagDependencyUsagePlugin,FlagIncludedChunksPlugin
> ModuleConcatenationPlugin,NoEmitOnErrorsPlugin
> OccurrenceOrderPlugin,SideEffectsFlagPlugin#
> TerserPlugin.

+ none

> 不开启任何优化选项

#### resolve

配置模块如何解析

+ **alias**创建 `import` 或 `require` 的**路径别名**，来确保模块引入变得更简单

+ **extensions**自动解析确定的扩展

  ```js
  const path = require("path");
  
  module.exports = {
    	resolve: {
          // 路径别名
          alias: {
              "@": path.resolve("./src")
          },
          // 默认[".js", ".json"]可以省略，但是jsx不可省略，这里将jsx添加为默认扩展名
          extensions: [".js", ".jsx"]
      },
  }
  ```

###  解析 ES6

| babel-loader      | 使用 Babel 加载 ES2015+ 代码并将其转换为 ES5  |
| ----------------- | --------------------------------------------- |
| @babel/core       | Babel 编译的核心包                            |
| @babel/preset-env | Babel 编译的预设，可以理解为 Babel 插件的超集 |

1. 依赖安装

```
npm i  @babel/core  @babel/preset-env  babel-loader -D
```
2. 在根目录下新增babel的配置⽂件.babelrc
```js
{
    "presets": [
        "@babel/preset-env",
    ]
} 
```
3. loader使用
```js
    module: {
        rules: [
            {
                test:/.js$/,
                use:'babel-loader'
            },
        ]
    },
```

常见 Babel 预设还有：

- `@babel/preset-flow`
- `@babel/preset-react`
- `@babel/preset-typescript`

### @ 装饰器

对于正在提案中，还未进入 ECMA 规范中的新特性，Babel 是无法进行处理的，必须要安装对应的插件，例如:@ 装饰器写法

1. 依赖安装

```
npm i @babel/plugin-proposal-decorators @babel/plugin-proposal-class-properties -D
```

2. 根目录下babel的配置⽂件.babelrc

```js
{
    "plugins": [    
    ["@babel/plugin-proposal-decorators", { legacy: true }],
    ["@babel/plugin-proposal-class-properties", { loose: true }]
} 
```

### 解析 React

1. 依赖安装

```
npm i @babel/preset-react -D
npm i react react-dom -S
```
2. 增加React的babel preset配置
```js
{
    "presets": [
        "@babel/preset-react"
    ]
} 
```

### 解析Css/Sass/Scss

| 依赖            | 作用                                                    |
| --------------- | ------------------------------------------------------- |
| style-loader    | 将处理好的 css样式通过 `<style>` 标签的形式插⼊到head中 |
| css-loader      | ⽤于加载.css⽂件，并且转换成commonjs对象                |
| **sass-loader** | 将sass/scss转换成css                                    |
| node-sass       | sass-loader 需要搭配 node-sass                          |

1. 依赖按照

```bash
npm i style-loader css-loader sass-loader node-sass -D
```
2. loader使用

```js
    module: {
        rules: [
            {
                test:/\.(s[ac]|c)ss$/,// 匹配sass/scss/css 文件
                use: ['style-loader', 'css-loader','sass-loader']
            },
        ]
    },
```
### 解析less/Css

| 依赖            | 作用                                                    |
| --------------- | ------------------------------------------------------- |
| style-loader    | 将处理好的 css样式通过 `<style>` 标签的形式插⼊到head中 |
| css-loader      | ⽤于加载.css⽂件，并且转换成commonjs对象                |
| **less-loader** | 将less文件转换成css                                     |
| less            | sass-loader 需要搭配 less                               |

1. 依赖安装
```bash
npm i style-loader css-loader less less-loader -D
```
2. loader使用

```js
    module: {
        rules: [
            {
                test:/\.(le|c)ss$/,// 匹配less/css 文件
                use:['style-loader','css-loader','less-loader']
            },
        ]
    },
```

### 解析图片

+ 使用file-loader解析图片

> 1. 依赖安装
> ```js
> // d3f4bf70f2ffdc11072b782a6114c631.png 这个是打包之后图片的哈希
> npm i file-loader -D
> ```
> 2. loader使用
> ```js
>     module: {
>         rules: [
>             {
>                 test: /.(png|jpg|gif|jpeg)$/,
>                 use:"file-loader"
>             },
>         ]
>     },
> ```

+ 使用url-loader解析图片，并且可以设置较⼩资源⾃动转base6，内链至head上

> 1. 依赖安装
>
> ```js
> npm i url-loader -D
> ```
>
> 2. loader使用
>
> ```js
>   module: {
>         rules: [
>             {
>                 test: /.(png|jpg|gif|jpeg)$/,
>                 use: [
>                     {
>                         loader: 'url-loader',
>                         options: {
>                             limit: 102400 //字节 10K 小于会自动base64
>                         }
>                     }
>                 ]
>             }
>         ]
>     }
> ```

###  解析字体

+ 使用file-loader解析字体，

> 1. 依赖安装
>
> ```js
> npm i file-loader -D
> ```
>
> 2. 通过file-loader来实现
>
> ```js
>     module: {
>         rules: [
>             {
>                 test: /.(woff|woff2|eot|ttf|otf)(\?.*)?$/,
>                 use: "file-loader"
>             }
>         ]
>     },
> ```

+ 使用url-loader解析图片，并且可以设置较⼩资源⾃动转base6，内链至head上

> 1. 依赖安装
>
> ```
> npm i url-loader -D
> ```
>
> 2. 通过file-loader来实现
>
> ```js
>     module: {
>         rules: [
>             {
>                 test: /.(woff|woff2|eot|ttf|otf)(\?.*)?$/,
>                 use: [
>                     {
>                         loader: 'url-loader',
>                         options: {
>                             limit: 102400 //字节 10K 小于会自动base64
>                         }
>                     }
>                 ]
>             }
>         ]
>     },
> ```

+ 在css中对字体的引用

```css
@font-face {
    font-family: 'SourceHanSerifSC-Heavy';
    src: url(./images/SourceHanSerifSC-Heavy.otf);
}

.search-text {
    font-family: 'SourceHanSerifSC-Heavy';
}
```

### ⽂件监听
⽂件监听是在发现源码发⽣变化时，**⾃动重新构建出新的输出⽂件**。

原理是轮询判断文件的最后编辑时间是否有变化，某个文件发生了变化，并不会立即告诉监听者，而是先缓存起来，等aggregateTimeout

> 缺陷:每次需要⼿动刷新浏览器

+ 启动方式有两种，任意一种均满足

> 1. 在package.json使用命令行"webpack --watch"
>
>    ```js
>    // package.json
>      "scripts": {
>        "watch":"webpack --watch"
>      },
>    ```
>
> 2. 在配置webpack.config.js中设置watch:true
>
>    ```js
>    module.exports = {
>        ...
>           // 默认false，也就是不开启
>        watch: true,
>        // 只有开启监听模式时，watchOptions才有意义
>        wathcOptions: {
>            // 默认为空，不监听的文件或者文件夹，支持正则匹配
>            ignored: /node_modules/,
>            // 监听到变化发生后会等300ms再去执行，默认300ms 
>            aggregateTimeout: 300,
>            // 判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的，默认每秒问1000次
>            poll: 1000
>        }
>    };
>    ```

### 热更新

**不用手动刷新浏览器，不输出⽂件**，⽽是放在内存中。配合webpack内置的**HotModuleReplacementPlugin**插件使⽤

1. 下载依赖
```js
npm i webpack-dev-server -D
```
2. plugins使用
```js
const webpack = require('webpack');
const path = require("path");

module.exports = {
    // 热更新，需要注意在development开发模式
    mode: 'development',
    ......
    plugins:[
        new webpack.HotModuleReplacementPlugin(),
    ],
    devServer: {
      	// static:'./dist',
      	// 告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要
      	contentBase: path.join(__dirname, "./public"), //拼接绝对路径
        hot: true
    }
};

```
3. package.json中添加webpack-dev-server启动脚本
```js
  // --open,每次构建的时候自动开启一个浏览器
  "scripts": {
    "dev": "webpack-dev-server --open"
  },
```
+ 了解另一种热更新


![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-15%2002.14.58.png)

+ 热更新原理

![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-15%2002.15.28.png)

+ 初始化：1 >> 2 >> A >> B >> 5
+ 热更新：3 >> 4 >> 5

### 文件指纹
打包后输出的⽂件名的后缀,适用版本管理(缓存判定)

| 占位符        | 解释                       |
| ------------- | -------------------------- |
| [ext]         | 文件后缀名                 |
| [name]        | 文件名                     |
| [path]        | 文件相对路径               |
| [folder]      | 文件所在文件夹             |
| [hash]        | 每次构建生成的唯一 hash 值 |
| [chunkhash]   | 根据 chunk 生成 hash 值    |
| [contenthash] | 根据文件内容生成hash 值    |

表格里面的 `hash`、`chunkhash`、`contenthash` 你可能还是不清楚差别在哪

- **hash** ：任何一个文件改动，整个项目的构建 hash 值都会改变；
- **chunkhash**：文件的改动只会影响其所在 chunk 的 hash 值；
- **contenthash**：每个文件都有单独的 hash 值，文件的改动只会影响自身的 hash 值；

**生成方式**

1. Hash：和整个项⽬的构建相关，只要项⽬⽂件有修改，整个项⽬构建的hash值就会更改

   > 图片或者字体指纹使⽤[hash]，添加至file-loader配置下的name属性
```js
            // d3f4bf70f2ffdc11072b782a6114c631.png >>> logo_d3f4bf70.png
            {
                test: /.(png|jpg|gif|jpeg)$/,
                // use:"file-loader"
                use: [
                    {
                        loader: 'file-loader',
                        // 添加文件名
                        options: {
                            name: '[name]_[hash:8].[ext]'
                        }
                    }
                ]
            },
            {
                test: /.(woff|woff2|eot|ttf|otf)$/,
                // use: "file-loader"
                use: [
                    {
                        loader: 'file-loader',
                         // 添加文件名
                        options: {
                            name: '[name]_[hash:8].[ext]'
                        }
                    }
                ]
            }
```
2. Chunkhash：和webpack打包的chunk有关,不同的entry会⽣成不同的chunkhash值

   > 设置output的filename，可使⽤[chunkhash],js指纹
```js
    // search.js >>> search_6ff6411a.js
    output: {
        path: path.join(__dirname, 'dist'),
        // filename: '[name].js'
        filename: '[name]_[chunkhash:8].js'
    },
```
3. Contenthash：根据⽂件内容来定义hash，⽂件内容不变，则contenthash不变

   > 1. 设置MiniCssExtractPlugin的filename，提取css文件，对文件使⽤[contenthash]，css指纹
   > 2. style-loader是提取样式至header与**MiniCssExtractPlugin**功能排斥
### 提取CSS文件

```js
// 下载依赖 npm i mini-css-extract-plugin -D
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

// style-loader是提取样式至header（在此不适用）,
// 应使用Mini热更新原理CssExtractPlugin.loader生成一个css文件  search_44babd1a.css

module.exports = {
    ......
    module: {
        rules: [
            {
                test:/\.(le|c)ss$/,// 匹配less/css 文件
                // use:['style-loader','css-loader','less-loader']
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

### 压缩JS文件

在生成环境下打包默认会开启 js 压缩，但是当我们手动配置 `optimization` 选项之后，就不再默认对 js 进行压缩，需要我们手动去配置。

因为 webpack5 内置了**terser-webpack-plugin**插件，所以我们**不需重复安装，直接引用**就可以了，具体配置如下

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

### 压缩CSS ⽂件

+ webpack4用法

> 1. 依赖安装
>
> ```js
> npm i optimize-css-assets-webpack-plugin cssnano -D
> ```
>
> 2. plugins使用
>
> ```js
> const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
> 
> module.exports = {
>     mode: 'production',
>     ......
>     plugins:[
>         // 压缩CSS文件
>         new OptimizeCSSAssetsPlugin({
>             assetNameRegExp: /\.css$/g,
>             cssProcessor: require('cssnano')
>         }),        
>     ],
> };
> ```

webpack5用法

> 1. 依赖安装
>
> ```
> npm i css-minimizer-webpack-plugin -D
> ```
>
> 2. plugins使用
>
> ```js
> const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
> 
> module.exports = {
>     mode: 'production',
>     ......
>     optimization: {
>         minimizer: [
>           new CssMinimizerPlugin(),
>         ],
>       }
>    
> };
> ```

### html⽂件模版的压缩

修改 html-webpack-plugin，设置压缩参数

1. 安装依赖
```js
npm i html-webpack-plugin -D
```
2. 依赖使用
```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: {
        index: './src/index.js',
        search: './src/search.js'
    },
    mode: 'production',
    ......
    plugins:[
        // 每个入口文件都会有一个HtmlWebpackPlugin模版
        new HtmlWebpackPlugin({
          	// favicon: "./public/favicon.ico",
            template: path.join(__dirname, 'src/index.html'),
            filename: 'index.html',
            chunks: ['index'],
            inject: true,
            minify: {
                html5: true,
                collapseWhitespace: true,
                preserveLineBreaks: false,
                minifyCSS: true,
                minifyJS: true,
                removeComments: false
            }
        }),
        new HtmlWebpackPlugin({
            template: path.join(__dirname, 'src/search.html'),
            filename: 'search.html',
            chunks: ['search'],
            inject: true,
            minify: {
                html5: true,
                collapseWhitespace: true,
                preserveLineBreaks: false,
                minifyCSS: true,
                minifyJS: true,
                removeComments: false
            }
        })         
    ], 
};
```

### 清理构建⽬录

每次修改文件，都会生成新的文件，造成构建的输出⽬录 output ⽂件越来越多。因此需要清理构建目录

+ 方式1.  手动通过 npm scripts 清理

```js
rm -rf ./dist && webpack
// 或者
// 安装 rimraf 这个依赖
rimraf ./dist && webpack
```
+ 方式2. 避免构建前每次都需要⼿动删除 dist，使⽤ **clean-webpack-plugin** 插件，其默认会删除 output 指定的输出⽬录

> 1. 依赖安装
>
> ```
> npm i clean-webpack-plugin -D
> ```
>
> 2. plugins使用
>
> ```js
> const { CleanWebpackPlugin } = require('clean-webpack-plugin');
>  
> module.exports = {
>       mode: 'production',
>     ......
>     plugins:[
>        new CleanWebpackPlugin(),
>     ],
> };
> ```

### CSS3属性前缀

![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-15%2002.33.33.png)

**使用postcss-loader和autoprefixer插件根据根据 Can I Use 规则自动补全**

#### 使用autoprefixer

1. 依赖安装
```js
npm i autoprefixer postcss-loader -D
```
2. plugins使用
> + 低版本，如  "postcss-loader": "^3.0.0"用法
>
> ```js
> module.exports = {
>     ......
>     module: {
>         rules: [
>             {
>                 test: /.less$/,
>                 use: [
>                     'style-loader',
>                     'css-loader',
>                     'less-loader',
>                     'postcss-loader',
>                     // postcss-loader低版本,直接在其options添加plugins
>                     {
>                         loader: 'postcss-loader',
>                         options: {
>                             plugins: () => [
>                                 require('autoprefixer')({
>                                     browsers: ['last 2 version', '>1%']
>                                 })
>                             ]
>                         }
>                     } 
>                 ]
>             },
>         ]
>     },
> };
> ```
> 
> + 高版本，如   "postcss-loader": "^7.0.0",
>
> ```js
>module.exports = {
>     ......
>     module: {
>         rules: [
>             {
>                 test: /.less$/,
>                 use: [
>                     'style-loader',
>                     'css-loader',
>                     'less-loader',
>                     'postcss-loader'
>                 ]
>             },
>         ]
>     },
> };
> 
> // 项目根目录下新建一个postcss.config.js文件
> module.exports = {
>     plugins: [
>         require('autoprefixer')({
>             overrideBrowserslist: ["last 2 version", ">1%"]
>         })
>     ]
> }
> ```

#### 使用postcss-preset-env

1. 创建 postcss 配置文件 `postcss.config.js`

   ```js
   // postcss.config.js
   module.exports = {
     plugins: [require('postcss-preset-env')]
   }
   ```

2. 创建 postcss-preset-env 配置文件 `.browserslistrc`

   ```bash
   # 换行相当于 and
   last 2 versions # 回退两个浏览器版本
   > 0.5% # 全球超过0.5%人使用的浏览器，可以通过 caniuse.com 查看不同浏览器不同版本占有率
   IE 10 # 兼容IE 10
   ```

   如果你对 `.browserslistrc` 不同配置产生的效果感兴趣，可以使用 [autoprefixer](https://link.juejin.cn/?target=http%3A%2F%2Fautoprefixer.github.io%2F) 进行在线转化查看效果

### 移动端CSS px<=>rem

**rem 是相对根元素的相对单位 font-size of the root element**

1. 依赖安装 px2rem-loader
```js
npm i px2rem-loader -D

npm i lib-flexible -S
// 可以使⽤⼿淘的lib-flexible库，⻚⾯渲染时动态计算根元素的 font-size 值
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

**资源内联**

+ 代码层⾯：⻚⾯框架的初始化脚本,上报相关打点,css 内联避免⻚⾯闪动
+ 请求层⾯：减少 HTTP ⽹络请求数 (⼩图⽚或者字体内联 (url-loader，小于limit配置，会自动内联))

### 内联html、js

##### raw-loader

1. 依赖安装

   ```
   npm i raw-loade -D
   ```

2. 在html模版引入文件,

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

   **引用能成功，但是编译器终端会报Expression expected错误提示**

### 内联CSS

+ 实现方式1，style-loader，借用其singleton属性，可将所以的styles标签合并成一个
+ 实现方式2，使用html-inline-css-webpack-plugin插件

### 多⻚⾯应⽤(MPA)
每⼀次⻚⾯跳转的时候，后台服务器都会给返回⼀个新的 html ⽂档，这种类型的⽹站也就是多⻚⽹站，也叫做多⻚应⽤
+ 多⻚⾯打包，每次新增或删除⻚⾯需要改 webpack 配置
+ 多⻚⾯打包通⽤⽅案：安装glob依赖，利⽤ glob.sync;动态获取 entry 并使用html-webpack-plugin插件为其设置html模版

> 遵循一些规则：每个入口js文件满足目录条件为 src/文件名/index.js，每个入口的html模版满足src/文件名/index.html

1. 依赖安装
```js
npm i glob -D
```
2. 修改webpack配置
```js
const glob = require('glob');
const path = require('path
const HtmlWebpackPlugin = require('html-webpack-plugin');

const setMPA = () => {
    const entry = {};
    const htmlWebpackPlugins = [];
    /* 
        获取符合条件的入口文件：
        entryFiles =[
            '/Users/lzp/Desktop/webpack_Tech/my-project/src/index/index.js',
            '/Users/lzp/Desktop/webpack_Tech/my-project/src/search/index.js'
        ]
    */
    const entryFiles = glob.sync(path.join(__dirname, './src/*/index.js'));

    Object.keys(entryFiles) // [ '0', '1' ]
        .map((index) => {
            // /Users/lzp/Desktop/webpack_Tech/my-project/src/index/index.js
            const entryFile = entryFiles[index];
            /*  match = 
                [
                    'src/index/index.js',
                    'index',
                    index: 50,
                    input: '/Users/lzp/Desktop/webpack_Tech/my-project/src/index/index.js',
                    groups: undefined
                ]
            */
            const match = entryFile.match(/src\/(.*)\/index\.js/);
            // 获取入口名称
            const pageName = match && match[1];
            entry[pageName] = entryFile;
            // 为入口文件添加一个HtmlWebpackPlugin模版
            htmlWebpackPlugins.push(
                new HtmlWebpackPlugin({
                    template: path.join(__dirname, `src/${pageName}/index.html`),
                    filename: `${pageName}.html`,
                    chunks: [pageName],
                    inject: true,
                    minify: {
                        html5: true,
                        collapseWhitespace: true,
                        preserveLineBreaks: false,
                        minifyCSS: true,
                        minifyJS: true,
                        removeComments: false
                    }
                })
            );
        });

    return {
        entry,
        htmlWebpackPlugins
    }
}

module.exports = {
  	// entry: {
    //     index: './src/index.js',
    //     search: './src/search.js'
    // },
    entry,
    ...
    plugins: [
        // new HtmlWebpackPlugin({
        //     template: path.join(__dirname, 'src/index.html'),
        //     filename: 'index.html',
        //     chunks: ['index'],
        //     inject: true,
        //     minify: {
        //         html5: true,
        //         collapseWhitespace: true,
        //         preserveLineBreaks: false,
        //         minifyCSS: true,
        //         minifyJS: true,
        //         removeComments: false
        //     }
        // }),
        // new HtmlWebpackPlugin({
        //     template: path.join(__dirname, 'src/search.html'),
        //     filename: 'search.html',
        //     chunks: ['search'],
        //     inject: true,
        //     minify: {
        //         html5: true,
        //         collapseWhitespace: true,
        //         preserveLineBreaks: false,
        //         minifyCSS: true,
        //         minifyJS: true,
        //         removeComments: false
        //     }
        // }),
       ...
    ].concat(htmlWebpackPlugins)
};
```

### source map

SourceMap 是一种映射关系，当项目运行后，如果出现错误，我们可以**利用 SourceMap 反向定位到源码位置**

```js
const IS_DEV = process.env.NODE_ENV === 'development'

module.exports = {
   ......
   devtool: IS_DEV ? 'cheap-module-eval-source-map' : 'none',
}; 
```

| 关键字     | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| inline     | 将.map作为DataURI嵌⼊js文件中，不单独⽣成.map⽂件            |
| hidden     | 生成 SourceMap 文件，但不使用                                |
| eval       | `eval(...)` 包裹模块代码;eval("sourceURL=webpack://my-project/./src/index/index.js?") |
| nosources  | 不生成 SourceMap                                             |
| cheap      | 只需要定位到行信息，**不需要列信息**                         |
| module     | 展示源代码中的错误位置，                                     |
| source-map | 产⽣.map⽂件                                                 |

![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-16%2015.14.35.png)

```js
const IS_DEV = process.env.NODE_ENV === 'development'

module.exports = {
   ......
   devtool: IS_DEV ? 'cheap-module-eval-source-map' : 'none',
}; 
```

### 公共脚本分离

Webpack4 内置的，替代CommonsChunkPlugin插件

+ 参数

  > + chunks 参数:async 异步引⼊的库进⾏分离(默认); initial 同步引⼊的库进⾏分离;all 所有引⼊的库进⾏分离(推荐)
  > + test: 匹配出需要分离的包
  > + minChunks: 设置最⼩引⽤次数为2次
  > + minuSize: 分离的包体积的⼤⼩

+ 利⽤ SplitChunksPlugin **分离基础包**

```js
module.exports = {
    ......
    plugins: [
        new HtmlWebpackPlugin({
            template: path.join(__dirname, 'src/index.html'),
            filename: 'index.html',
            // 需要将vendors也添加进来,webpack5不需要
            chunks: ['vendors','index'],
            inject: true,
            minify: {
                html5: true,
                collapseWhitespace: true,
                preserveLineBreaks: false,
                minifyCSS: true,
                minifyJS: true,
                removeComments: false
            }
        }),
    ],
    optimization: {
        splitChunks: {
            cacheGroups: {
                vendors: {
                  	test: /[\\/]node_modules[\\/](react|react-dom|lodash|moment|mobx|mobx-react-lite|axios|antd)[\\/]/,
                    name: 'vendors',
                    chunks: 'all'
                }
            }
        }
    },
}

```
+ 利⽤ SplitChunksPlugin **分离⻚⾯公共⽂件**，一般使用在多个入口上，因为单页面引用的都是属于同一个

```js
module.exports = {
    ......
    plugins: [
        new webpack.HotModuleReplacementPlugin(),
        new HtmlWebpackPlugin({
            template: path.join(__dirname, 'src/index.html'),
            filename: 'index.html',
            // 需要将commons也添加进来，webpack5不需要
            chunks: ['commons','index'],
            inject: true,
            minify: {
                html5: true,
                collapseWhitespace: true,
                preserveLineBreaks: false,
                minifyCSS: true,
                minifyJS: true,
                removeComments: false
            }
        }),
    ],
    optimization: {
        splitChunks: {
            cacheGroups: {
                commons: {
                  	minSize: 0, // 分离的包体积的⼤⼩
                    name: 'commons',
                    chunks: 'all',
                    minChunks: 2 // 设置最⼩引⽤次数为2次
                }
            }
        }
    },
}
```

### treeshaking(摇树优化)

mode:production情况下默认开启；

+ 概念：1个模块可能有多个⽅法，只要其中的某个⽅法被使⽤到了，且代码不可有副作用的。则整个⽂件都会被打到bundle⾥⾯去，treeshaking就是只把⽤到的⽅法打⼊bundle，没⽤到的⽅法会在uglify阶段被擦除掉。

+ 原理：利⽤ES6模块的特点，只能作为模块顶层的语句出现；import的模块名只能是字符串常量；importbinding是immutable的

+ 要求：必须是ES6的语法，CJS的⽅式不⽀持。

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
### scopehoisting

通过scopehoisting可以减少函数声明代码和内存开销,webpack4之后其mode为production默认开启。

+ 原理：将所有模块的代码按照引⽤顺序放在⼀个函数作⽤域⾥，然后适当的重命名⼀些变量以防⽌变量名冲突；必须是ES6语法，CJS不⽀持

+ webpack构建后的代码存在⼤量闭包代码；⼤量作⽤域包裹代码，导致体积增⼤（模块越多越明显）

+ 运⾏代码时创建的函数作⽤域变多，内存开销变⼤
    ![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-16%2018.27.04.png)

  
```js
// wabpack4之前的做法；wabpack4之后已内置
const webpack = require('webpack');

module.exports = {
    ......
    plugins: [
        ...
        new webpack.optimize.ModuleConcatenationPlugin()
    ],
}

```

### 代码分割
对于⼤的Web应⽤来讲，将所有的代码都放在⼀个⽂件中显然是不够有效的，特别是当你的某些代码块是在某些特殊的时候才会被使⽤到。webpack有⼀个功能就是将你的代码库分割成chunks（语块），当代码运⾏到需要它们的时候再进⾏加载。
+ 适⽤的场景
> 抽离相同代码到⼀个共享块.脚本懒加载，使得初始下载的代码更⼩

+ 懒加载JS脚本的⽅式
> 1:CommonJS：require.ensure;ES6：
> 2:动态import（⽬前还没有原⽣⽀持，需要babel转换），**好像webpack5自带支持**

### 动态import

webpack5好像内置了

1. 安装依赖
```js
npm i @babel/plugin-syntax-dynamic-import --D
```
2. .babelrc 文件新增
```js
{
    ...
    "plugins":[
        "@babel/plugin-syntax-dynamic-import"
    ]
} 
```
3. 引用
```js
onClick={ 
  // import('./text.js') 返回Promise
    import('./text.js').then((Text) => {
      console.log('Text',Text)
    });
}
```

### ESLint
+ ⾏业⾥⾯优秀的ESLint规范实践

  > Airbnb:eslint-config-airbnb、eslint-config-airbnb-base
+ ESLint如何执⾏落地，有两种方式

  > 1. CI/CD系统集成;
  > 2.  webpack集成

+ 演示webpack继承---eslint-config-airbnb规范https://www.npmjs.com/package/eslint-config-airbnb

  1. 依赖安装

     ```
     npm i eslint eslint-plugin-import eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-jsx-a11y  -D
     npm i @babel/eslint-parser - D
     ```
     
  2. 创建eslint配置文件.eslintrc
  
     ```js
     {
         // 解析器
         "parser": "@babel/eslint-parser",
         // 继承
         // "extends": "airbnb",
         // 环境
         "env": {
             "browser": true,
             "node": true
         },
          "parserOptions": {
         		"ecmaVersion": 12
       	},
         // 修改规则
         "rules": {
             "indent": ["error", 4]
         }
     }
     ```
### webpack打包库和组件
webpack除了可以⽤来打包应⽤，也可以⽤来打包js库
+ 演示打包压缩版和⾮压缩版本；⽀持AMD/CJS/ESM模块引⼊
```js
|-/dist 
    |-large-number.js 
    |-large-number.min.js 
|-webpack.config.js 
|-package.json 
|-index.js 
|-/src 
    |-index.js
```
+ webpack.config.js,其output身上的属性
>  + library: 'largeNumber', // 指定库的全局变量
>  + libraryTarget: 'umd', // ⽀持库引⼊的⽅式
>  + libraryExport: 'default' // 支持默认导出

```js
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
    entry: {
        'large-number': './src/index.js',
        'large-number.min': './src/index.js'
    },
    output: {
        filename: '[name].js',
        library: 'largeNumber', // 指定库的全局变量
        libraryTarget: 'umd', // ⽀持库引⼊的⽅式
        libraryExport: 'default' // 支持默认导出
    },
    mode: 'none',
    // 指定压缩文件 ps:我们需要一个压缩版一个非压缩版
    optimization: {
        minimize: true,
        minimizer: [
            new TerserPlugin({
                include: /\.min\.js$/, // 通过include设置只压缩min.js结尾的⽂件
            })
        ]
    }
}
```
+ package.json
> 使用devDependencies依赖和scripts命令

```js
{
  "name": "large-number",
  "version": "1.0.0",
  "description": "大整数加法打包",
  "main": "index.js",
  "scripts": {
    "build": "webpack",
    "prepublish": "webpack"
  },
  "devDependencies": {
    "terser-webpack-plugin": "^5.3.1",
    "webpack": "^5.70.0",
    "webpack-cli": "^4.9.2"
  }
}

```
+ index.js
> 入口文件我们根据环境来判定使用的是哪个版本

```js
if (process.env.NODE_ENV === 'production') {
    module.exports = require('./dist/large-number.min.js');
} else {
    module.exports = require('./dist/large-number.js');
}
```



### 统计信息stats

写业务逻辑的人员如果不关系构建日志，可设置stats: 'errors-only'，表示只在发生错误的时输出

![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-16%2021.46.52.png)

```js
// 在生产时build看不出效果，那有没有更优的方案呢？
module.exports = {
    	...
     stats: 'errors-only'//只在发生错误时输出
}
```

### 优化命令⾏的构建⽇志

在设置stats: 'errors-only'的同时，配合friendly-errors-webpack-plugin使用，让构建⽇志更友好

1. 依赖安装
```js
npm i friendly-errors-webpack-plugin -D
```
2. 插件使用
```js
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');

module.exports = {
    ......
    plugins:[
        new FriendlyErrorsWebpackPlugin()
    ]
}
```
3. 使用效果

![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-16%2021.50.59.png)

### 构建异常和中断处理

webpack4之前的版本构建失败不会抛出错误码(errorcode)

+ 在CI/CD如何判断构建是否成功？
> 如果在线下我们可以在每次构建完成后我们可以在终端输入 echo $?来获取错误码，但是对于CI/CD线上部署呢？我们又如何主动捕获并处理构建错误

+ 主动捕获并处理构建错误
```js
module.exports = {
    mode: 'production',
    ......
    plugins:[
        function() {
        // webpack4之前this.hooks.done.tap 改为this.plugin
        // webpack4之后，这里的this指代compiler，写法如下
            this.hooks.done.tap('done', (stats) => {
                if (stats.compilation.errors && stats.compilation.errors.length && process.argv.indexOf('--watch') == -1)
                {
                    // do something，构建日志等等
                    console.log('build error');
                    // 通过node.js写入错误
                    process.exit(stats.compilation.errors.length);
                }
            })
        }  
    ]
}
```

### 功能模块设计

![](./image/webpack/%E6%88%AA%E5%B1%8F2022-06-17%2002.03.49.png)

# 



## 离散知识点

### process.env

webpack是npm生态中的一个模块，webpack的运行依赖于node的环境，没有node是不能打包的。这里的**process.env就是Nodejs提供的一个API**，它返回一个包含用户环境信息的对象。如果我们给Nodejs 设置一个环境变量，并把它挂载在 `process.env`返回的对象上，便可以在代码中进行相应的环境判断。

据开发环境不同，设置不同的变量、参数、和引入依赖，使打包更加的自动化

+ process.env属性返回一个对象，包含了当前Shell的所有环境变量。比如，process.env.HOME返回用户的主目录。

+ 在webpack中通常的做法是，新建一个环境变量NODE_ENV，用它确定当前所处的开发阶段，生产阶段设为production，开发阶段设为development，需要读取时只需要process.env.NODE_ENV即可。

  > 要说明的是，NODE_ENV 这个名称只是开发社区的一种共识，名称内容是可以修改的。如果需要，你也可以把它定义为 NODE_abc或者xxx都行

在Webpack配置文件中，经常会看到如下类似的代码：

```java
// webpack.config.js
module.exports = {
  mode: process.env.NODE_ENV === 'production' ? 'production' : 'development'
}
```

我们需要先设置这个process.env.NODE_ENV环境变量呢？在webpack项目里，我们可以通过设置package.json来实现，但是Windows 系统和Mac系统有区别。

+ Windows 系统

  ```json
  // package.json
  {
    ...
    "scripts": {
      "dev": "set NODE_ENV=development &&  webpack-dev-server --open --hot",
      "build": "set NODE_ENV=production &&  --progress --hide-modules"
    }
  }
  ```

  Mac 系统

  ```json
  // package.json
  {
    ...
    "scripts": {
      "dev": "export NODE_ENV=development &&  webpack-dev-server --open --hot",
      "build": "export NODE_ENV=production && --progress --hide-modules"
    }
  }
  ```


那么问题又来了，我在Windows 开发部署的项目，可能在 Mac 系统无法正常打包，反之亦然。为了解决这个问题，有人就开发了 cross-env。

### cross-env

区分环境本地开发和部署线上，肯定是有不同的需求

**本地环境：**

- 需要更快的构建速度
- 需要打印 debug 信息
- 需要 live reload 或 hot reload 功能
- 需要 sourcemap 方便定位问题
- ...

**生产环境：**

- 需要更小的包体积，代码压缩+tree-shaking
- 需要进行代码分割
- 需要压缩图片体积
- ...

cross-env是一个跨平台设置环境变量的第三方包，它可以让你只配置一行命令，就能轻松地在多个平台设置环境变量。

1. 安装依赖

```bash
npm install --save-dev cross-env 
```

2. 配置package.json

```cpp
// package.json
{
  ...
    "dev": "cross-env NODE_ENV=development APP_ENV=dev webpack serve --mode development", 
    "build": "cross-env NODE_ENV=production APP_ENV=prod webpack --mode production"
  },
}
```

3. process.env.NODE_ENV全局化

```tsx
// webpack.config.js
const path = require('path')

// 打印环境变量
console.log('process.env.NODE_ENV=', process.env.NODE_ENV) 
console.log('process.env.APP_ENV=', process.env.APP_ENV)

const config = {
  entry: './src/index.js', // 打包入口地址
  ...
  plugins: [
    new webpack.DefinePlugin({
       "process.env.NODE_ENV" : JSON.stringify(process.env.NODE_ENV),
       "process.env.APP_ENV": JSON.stringify(process.env.APP_ENV)
    })
	]
}

module.exports = (env, argv) => {
  console.log('argv.mode=',argv.mode) // 打印 mode(模式) 值
  // 这里可以通过不同的模式修改 config 配置
  return config;
}
```

全局化之后，不仅仅在打包配置中使用process.env.NODE_ENV，在其他业务逻辑模块中，仍可使用它

4. 测试一下看看

- 执行 `npm run build`

```bash
process.env.NODE_ENV= production
process.env.APP_ENV= prod
argv.mode= production
```

- 执行 `npm run dev`

```bash
process.env.NODE_ENV= development
process.env.APP_ENV= dev
argv.mode= development
```

这样我们就可以不同的环境来动态修改 Webpack 的配置

### __dirname

+ 当前 js 檔所在**資料夾**的絕對路徑

  > /Users/lizhanpeng/Desktop/伊的家/项目/ERP/ydj-erp/build

### __filename

+ 当前 **js 檔**的絕對路徑，就是在 **`__dirname`上加上文件的名字**。

  > /Users/lizhanpeng/Desktop/伊的家/项目/ERP/ydj-erp/build/plugins.js

**process.cwd() 与./ 一样：總是回傳執行 node 指令時所在的資料夾之絕對路徑**

在 Node 中, 文件裡的**相對路徑**代表的是執行 node 時所在的資料夾的相對路徑, 並不是你所執行的 JS 檔本身的相對路徑。

只有**在 `require()` 中使用時, 效果相同於 `__dirname`** , 不會因為啟動腳本的資料夾不同而改變, 在其他情況下則跟 `process.cwd()` 效果相同, 是相對於啟動腳本所在資料夾的路徑。因此, 只有在 `require()` 時才能使用相對路徑, 其他情況一律使用絕對路徑是最安全的方式。

### Path.join

将路径片段使用**特定的分隔符**``连接起来形成路径，并规范化生成的路径。若任意一个路径片段类型错误，会报错。

收无限的参数且都是string类型的，该方法返回值也是`string`类型的(是一个路径)。

- 如果是空字符串，则会跳过
- 如果 字符串中存在 `\`， 就会删除`\`，然后进行拼接。比如：`\aa` 就会变成 `aa`；`nba\kobe`转为`nbakobe`。（不一定正确，`\b`就有点特殊）
- 如果传入的参数，不是`字符串类型`，直接报错
- 如果传入的是`..`， 则会退一级

```js
// 这里我们就当__dirname是 /root

path.join(__dirname, "dist") // 输出：/root/dist

path.join(__dirname, "dist", "/dir") // 输出：/root/dist/dir

path.join(__dirname, "dist", "/dir", "..") // 输出：/root/dist

path.join(__dirname, "dist", "/dir", "../test") // 输出：/root/dist/test

path.join(__dirname, "dist", "/dir", "/..test") // 输出：/root/dist/dir/..test

path.join(__dirname, "/dist", "..") // 输出：/root
```



### Path.resolve

将参数字符串解析为一个**绝对路径**。解析的过程值**从右到左解析的**。知道构造成一个绝对的路径。 `/` 作为**根路径**。

接收无限的参数且都是string类型的，该方法返回值也是`string`类型的(是一个路径)。

- 从左往右解析，主要一遇到`/`，就会停止解析。
- 遇到`..`也会退一级
- 如果解析到最左边，**还是没有遇到`/`，就会读取磁盘的路径**

```js
// 这里我们就当__dirname是 /root

path.resolve(__dirname, "./dist") // 输出：/root/dist

path.resolve(__dirname, "dist", "dir") // 输出：/root/dist/dir

path.resolve(__dirname, "/dist") // 输出：/dist

path.resolve(__dirname, "/dist", "../") // 输出：/

path.resolve(__dirname, "/dist", "..") // 输出：/

path.resolve(__dirname, "/dist", "..", "/test") // 输出：/test

path.resolve(__dirname, "dist", "dir", "/test") // 输出：/test

path.resolve(__dirname, "dist", null, "/test") // 输出：报错，参数一定要字符串类型的！

console.log(path.join('/james', 'nba/kobe', 'curry', '', '\aa', 'bb'));
// /james/nba/kobe/curry/aa/bb
```
