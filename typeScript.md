# typesScript

## webpack + typesScript

+ 初始化package.json

```TS
npm init -y 
```

+ 全局安装typescript语言执行环境，并创建tsconfig.json文件

```TS
// 全局安装ts,使tsc命令行可用
sudo npm i typescript -g 
// 检查安装是否成功
tsc	-h
// 创建tsconfig.json
tsc --init
```

+ webpack搭建，安装相关依赖

```ts
// webpack 相关
npm i webpack webpack-cli webpack-dev-server -D
// ts 相关
npm i ts-loader typescript -D
// 合并配置参数相关
npm i webpack-merge -D
// 构建时，清理上一次构建文件
npm i clean-webpack-plugin -D
// html模版
npm i html-webpack-plugin -D
```

### 目录结构

```
|-/build
    |-webpack.base.config.js
    |-webpack.config.js
    |-webpack.dev.config.js
    |-webpack.prd.config.js
|-package.json 
|-tsconfig.json
|-/src 
    |-index.js
    |-/tpl
    		|-index.html
```

+ build/webpack.base.config.js	webpack基础配置文件

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: './src/index.ts',
    output: {
        filename: 'app.js'
    },
    resolve: {
        extensions: ['.js', '.ts', '.tsx']
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: [{
                    loader: 'ts-loader',
                }],
                exclude: /node_modules/
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/tpl/index.html'
        })
    ],
}
```

+ build/webpack.dev.config.js	webpack开发配置文件

```js
const webpack = require('webpack');
module.exports = {
    plugins: [
        new webpack.LoaderOptionsPlugin({
            options: {
              	// 源代码只能看到行
                devtools: 'cheap-module-eval-source-map'
                // 1. eval: 使⽤eval包裹模块代码,
                // 2. source-map: 产⽣.map⽂件
                // 3. cheap: 不包含列信息
                // 4. inline: 将.map作为DataURI嵌⼊js文件中，不单独⽣成.map⽂件
                // 5. module:包含loader的sourcemap,出错可进一步定位
            }
        })
    ]
}
```

+ build/webpack.pro.config.js	webpack生产配置文件

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

module.exports = {
    plugins: [
        new CleanWebpackPlugin()
    ]
}
```

+ build/webpack.config.js	webpack暴露出去的配置文件

```js
const { merge } = require('webpack-merge')
const baseConfig = require('./webpack.base.config')
const devConfig = require('./webpack.dev.config')
const proConfig = require('./webpack.pro.config')

module.exports = (env, argv) => {
    // 导出函数的第二个参数身上的mode属性来判断开发环境
    let config = argv.mode === 'development' ? devConfig : proConfig;
    return merge(baseConfig, config);
};
```

+ src/tpl/index.html	html模版

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>typescript</title>
</head>
<body>
    <div id="app"></div>
</body>
</html>
```

+ src/index.ts	webpack 入口文件

```ts
let hello :string = 'hello typescript'
const appEle:any  = document.getElementById('app')
appEle.innerText = hello
```

+ package.json	依赖记录，入口，此处脚本运行是基于webpack5

```json
{
  "main": "./src/index.j",
  "scripts": {
    "start": "webpack serve --open --mode=development --config ./build/webpack.config.js",
    "build": "webpack --mode=production --config ./build/webpack.config.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies": {},
  "devDependencies": {
    "clean-webpack-plugin": "^4.0.0",
    "html-webpack-plugin": "^5.5.0",
    "ts-loader": "^9.3.0",
    "typescript": "^4.7.3",
    "webpack": "^5.73.0",
    "webpack-cli": "^4.10.0",
    "webpack-dev-server": "^4.9.2",
    "webpack-merge": "^5.8.0"
  }
}
```

+ tsconfig.json	typescript配置文件

```json
{
  "compilerOptions": {
     // "incremental": true,                // 增量编译,在第一次编译之后生成一个存储编译的文件，二次编译会增量编译，提高编译速度；增量编译文件可以随意删除，没有影响
     // "tsBuildInfoFile": "./buildFile",   // 增量编译文件的存储位置
     // "diagnostics": true,                // 打印诊断信息

     "target": "es2016",           // 目标语言的版本
     "module": "commonjs",      // 生成代码的模块标准
     // "outFile": "./app.js",     // 将多个相互依赖的文件生成一个文件，可以用在 AMD 模块中

     // "lib": [],                 // TS 需要引用的库，即声明文件，es5 默认 "dom", "es5", "scripthost"

     // "allowJs": true,           // 允许编译 JS 文件（js、jsx）
     // "checkJs": true,           // 允许在 JS 文件中报错，通常与 allowJS 一起使用
     // "outDir": "./out",         // 指定输出目录
     // "rootDir": "./src",           // 指定输入文件目录（用于控制输出结构）

     // "declaration": true,         // 生成声明文件
     // "declarationDir": "./d",     // 声明文件的路径
     // "emitDeclarationOnly": true, // 只生成声明文件,不生成js文件
     // "sourceMap": true,           // 生成目标文件的 sourceMap
     // "inlineSourceMap": true,     // 生成目标文件的 inline sourceMap(sourceMap，会包含在生产的js文件之中)
     // "declarationMap": true,      // 生成声明文件的 sourceMap
     // "typeRoots": [],             // 声明文件目录，默认 node_modules/@types
     // "types": [],                 // 声明文件包，指定要包含而不被源文件引用的类型包名称

     // "removeComments": true,    // 删除注释

     // "noEmit": true,            // 不输出任何文件
     // "noEmitOnError": true,     // 发生错误时不输出文件（有一个错误全部不输入）

     // "noEmitHelpers": true,     // 不生成 helper 函数(减少编译体积)，需额外安装 ts-helpers,
     // "importHelpers": true,     // 通过 tslib 引入 helper 函数，但是文件必须是模块

     // "downlevelIteration": true,    // 降级遍历器的实现（es3/5）

     "strict": true,                        // 开启所有严格的类型检查，为true,下面相关的默认为true
     // "alwaysStrict": false,                 // 在代码中注入 "use strict";
     // "noImplicitAny": false,                // 不允许隐式的 any 类型，必须要有类型注解
     // "strictNullChecks": false,             // 不允许把 null、undefined 赋值给其他类型变量
     // "strictFunctionTypes": false           // 不允许函数参数双向协变
     // "strictPropertyInitialization": false, // 类的实例属性必须初始化
     "strictBindCallApply": false,          // 严格的 bind/call/apply 检查
     "noImplicitThis": false,               // 不允许 this 有隐式的 any 类型

     // "noUnusedLocals": true,                // 检查只声明，未使用的局部变量
     // "noUnusedParameters": true,            // 检查未使用的函数参数
     // "noFallthroughCasesInSwitch": true,    // 防止 switch 语句贯穿
     // "noImplicitReturns": true,             // 每个分支都要有返回值

     "esModuleInterop": true,               // 允许 export = 导出，由import from 导入
     // "allowUmdGlobalAccess": true,          // 允许在模块中访问 UMD 全局变量
     // "moduleResolution": "node",            // 模块解析策略
     // "baseUrl": "./",                       // 解析非相对模块的基地址
     // "paths": {                             // 路径映射，相对于 baseUrl
     //   "jquery": ["node_modules/jquery/dist/jquery.slim.min.js"]
     // },
     // "rootDirs": ["src", "out"],            // 将多个目录放在一个虚拟目录下，用于运行时

     // "listEmittedFiles": true,        // 打印输出的文件
     // "listFiles": true,               // 打印编译的文件（包括引用的声明文件）
 }
}
```

### tsc命令

* 格式：`tsc <...文件> <参数>`

* 参数：

  * --help 显示帮助信息
  * --module 载入扩展模块
  * --target 设置 ECMA 版本
  * --declaration 额外生成一个 .d.ts 扩展名的文件。

  ```bash
      # 以下命令会生成 ts-hw.d.ts、ts-hw.js 两个文件
      tsc ts-hw.ts --declaration
  ```

  * --removeComments 删除文件的注释

  * --out 编译多个文件并合并到一个输出的文件

  * --sourcemap 生成一个 sourcemap (.map) 文件。

    >sourcemap 是一个存储源代码与编译代码对应位置映射的信息文件。

  * --module noImplicitAny 在表达式和声明上有隐含的 any 类型时报错

  * --watch 在监视模式下运行编译器。会监视输出文件，在它们改变时重新编译。

## 基础篇

### 类型基础

#### 强类型语言

不能改变变量的数据类型，除非进行强制类型转换。例如：java、C#、Python

#### 弱类型语言

能够改变变量的数据类型。例如：javaScript、PHP

#### 静态类型语言

在编译阶段确定变量的类型。例如：java、C#、C/C++

#### 动态类型语言

在执行阶段确定变量的类型。例如：javaScript、PHP、Python

### 基本类型

#### number,string,boolean

```ts
let bool: boolean = true
let num: number = 123
let str: string = 'abc'
// ts和js的区别是：变量定义的数据类型，赋值时是不可改变的
// str = 123 // 不能将类型“number”分配给类型“string”
```

#### 数组Array

```ts
let arr1: number[] = [1, 2, 3]
// 范型接口定义
let arr2: Array<number | string> = [1, 2, 3, '4']
```

#### 元组Tuple

```ts
// 元组：确定类型和个数
let tuple: [number, string] = [0, '1']
tuple.push(2) 
console.log(tuple) // 通过push添加元素编译器没有提示，这种方式不可取
// tuple[2] // 长度为 "2" 的元组类型 "[number, string]" 在索引 "2" 处没有元素
```

#### 函数function

```ts
let add = (x: number, y: number) => x + y

let compute: (x: number, y: number) => number
compute = (a, b) => a + b
```

#### 对象object

```ts
let obj: { x: number, y: number } = { x: 1, y: 2 }
obj.x = 3
```

#### symbol

symbol是独一无二的值

```ts
let s1: symbol = Symbol()
let s2 = Symbol()
console.log(s1 === s2) // false
```

#### void

没有任何类型

```ts
let noReturn = () => {}
```

#### any

任意类型，即不指定一个变量的类型，和js变量一样

```ts
let x
x = 1
x = []
x = () => {}
```

#### never

永远不存在的值,出现在抛出异常或者死循环上

```ts
// 永远不会有返回值
let error = () => {
    throw new Error('error')
}
let endless = () => {
    while(true) {}
}
```



### 枚举类型

一组有名字的常量集合，常用于不容易记忆的硬编码，或者在未来中容易改变的常量

#### 数字枚举

```ts
// 枚举成员默认从0开始，逐级递增，也可自定义值
enum Role1 {
    Reporter ,
    Developer,
}

enum Role2 {
    Reporter = 2 ,// 2
    Developer,// 3
}
// console.log(Role1.Reporter) // 0
// console.log(Role2.Reporter) // 2
```

#### 字符串枚举

```ts
// 成员的名称作为了key,字符串枚举不能反向映射
enum Message {
    Success = '恭喜你，成功了',
    Fail = '抱歉，失败了'
}
```

#### 异构枚举

```ts
// 容易引起混淆，不建议使用
enum Answer {
    N,
    Y = 'Yes'
}
```

#### 枚举成员

```ts
// 枚举成员的值定义之后无法修改
// Role.Reporter = 0 // 无法分配到 "Reporter" ，因为它是只读属性
enum Char {
    // const member:在编译阶段被计算出结果
    a,// 无初始值
    b = Char.a,// 对常量成员对引用
    c = 1 + 3,// 常量表达式
    // computed member 表达式保留到程序的执行阶段
    d = Math.random(),
    e = '123'.length,
  
    f = 4 // computed member后面的值需要赋予初始值
}
```

#### 常量枚举

```ts
// 特点：编译之后会被移除,成员只能为const member
// 作用：当我们不需要对象，仅需要对象的值的时候，就可使用常量枚举
const enum Month {
    Jan,
    Feb,
    Mar,
    Apr = Month.Mar + 1,
    // May = () => 5 // 函数报错
}
let month = [Month.Jan, Month.Feb, Month.Mar] // [0,1,2]
```

#### 枚举/枚举成员作为类型

```ts
// 没有初始值
enum E { a, b }
// 枚举成员均为数字
enum F { a = 0, b = 1 }
// 枚举成员均为字符串
enum G { a = 'apple', b = 'banana' }

// 没有初始值的枚举和数字枚举，可以把任意数字赋值给枚举，也可以超出定义，但是两种不同类型的枚举是不能比较的
let e: E = 3
let f: F = 3
// console.log(e === f) // 此条件将始终返回 "false"，因为类型 "E" 和 "F" 没有重叠

let e1: E.a = 1
let e2: E.b = 3
let e3: E.a = 3
// console.log(e1 === e2) // 此条件将始终返回 "false"，因为类型 "E.a" 和 "E.b" 没有重叠
console.log(e1 === e3) // false

// 字符串枚举取值只能是枚举成员的类型
let g1: G = G.a // G.a 或者 G.b
let g2: G.a = G.a  // 定义G.a 取值也只能他自身G.a
```



### 接口

#### 对象类型接口

+ 对象字面量检查原则是鸭式辩型法

+ 绕过对象字面量检查的方式

> 1. 将对象字面量赋值给变量
> 2. 使用类型断言
> 3. 使用字符串索引签名

+ 对象的属性

> 1. 只读属性：不可更改
> 2. 可选属性：可以有可以没有

```ts
interface List {
    readonly id: number; // 只读属性，不可更改
    name: string;
  	/* 
        3.使用字符串索引签名 
        含义：用任意的字符串去索引List,可以得到任意的结果，这样List就支持多个属性了
    */
    // [x: string]: any;
    age?: number; // 可选属性，可以有可以没有
}
interface Result {
    data: List[]
}

function render(result: Result) {
    result.data.forEach((value) => {
        console.log(value.id, value.name)
    		// 可选属性的使用  
        if (value.age) {
            console.log(value.age)
        }
        // value.id++ //只读属性不可更改
    })
}

/* 
    根据鸭式辩型法，我们不能将类型“{ id: number; name: string; sex: string; }”分配给类型“List”。
    对象文字可以只指定已知属性，并且“sex”不在类型“List”中
*/
// render({
//     data: [
//         {id: 1, name: 'A', sex: 'male'},
//         {id: 2, name: 'B', age: 10}
//     ]
// })

// 绕过对象字面量检查的方式
// 1.将对象字面量赋值给变量
let result = {
    data: [
        {id: 1, name: 'A', sex: 'male'},
        {id: 2, name: 'B', age: 10}
    ]
}
render(result)

// 2. 使用类型断言方式1
render({
    data: [
        {id: 1, name: 'A', sex: 'male'},
        {id: 2, name: 'B', age: 10}
    ]
} as Result )
// 使用类型断言方式2，不建议，在react中产生歧义
render(<Result>{
    data: [
        {id: 1, name: 'A', sex: 'male'},
        {id: 2, name: 'B', age: 10}
    ]
})
```

#### 可索引类型接口

```ts
// 字符串索引签名
interface List {
 		// 用任意的字符串去索引List,可以得到任意的结果，只有List就支持多个属性了
  	[x: string]: any;
}

// 数字索引签名
interface StringArray {
  	// 用任意的数字去索引StringArray，都会得到string值；
    [index: number]: string 
}

// 数字索引签名相当于生成了字符串类型的数组
let chars: StringArray = ['a', 'b']

// 两种索引签名的混用
interface Names {
    [x: string]: any;
    [z: number]: string;
   	y: number;
}

// 混用时数字索引签名必须是字符串索引签名的子类,即字符串索引签名要兼容数字索引签名
interface Name {
    [x: string]: string;
   	[z: number]: string;
    // y: number; // 类型“number”的属性“y”不能赋给“string”索引类型“string”
}
```

#### 函数类型接口

```ts
// 变量定义函数类型
let addd1: (x: number, y: number) => number

// 接口定义函数类型
interface Add {
    (x: number, y: number): number
}

// 类型别名定义函数类型
type Add1 = (x: number, y: number) => number

// 调用接口
let addd2: Add = (a: number, b: number) => a + 
```

#### 混合类型接口

```ts
// 混合类型接口
interface Lib {
    (): void;
    version: string;
    doSomething(): void;
}

function getLib() {
    let lib = (() => {}) as Lib
    lib.version = '1.0.0'
    lib.doSomething = () => {}
    return lib;
}
let lib1 = getLib()
lib1()
let lib2 = getLib()
lib2.doSomething()
```

#### 类类型接口

+ 类必须实现接口中的所有属性

+ 接口只能约束类的公有成员，不能约束私有成员、受保护成员、静态成员和构造函数

```ts
interface Human {
    name: string;
    eat(): void;
}

class Asian implements Human {
    constructor(name: string) {
        this.name = name;
    }
    public name: string
    eat() {}
  
    // 类的成员可以大于等于接口定义的属性
    age: number = 0
    sleep() {}
}
```



#### 接口继承接口

+ 抽离可重用的接口
+ 将多个接口整合成一个接口

```ts
interface Human {
    name: string;
    eat(): void;
}

interface Man extends Human {
    run(): void
}

interface Child {
    cry(): void
}

interface Boy extends Man, Child { }

let boy: Boy = {
    name: '',
    eat: () => { },
    run() { },
    cry() { }
}
```

#### 接口继承类

+ 抽象出类的公有成员、私有成员和受保护成员

```ts
class Auto {
    state = 1
    // protected state2 = 1 // 把注释去掉，class C 会报错
}
// 接口继承类：接口把类的成员都抽象类出来，除了抽离公共成员，还会把私有成员、收保护成员也抽取
interface AutoInterface extends Auto {

}
class C implements AutoInterface {
    state = 1
}

// Bus时Auto的子类，自然就继承了state属性
class Bus extends Auto implements AutoInterface {

}
```



### 函数

#### 定义函数

定义函数类型，无需函数体

+ 定义方式

```ts
// function定义，需要对参数定义，返回值可根据类型推断省去
function add1(x: number, y: number) {
    return x + y
}

// 变量定义
let add2: (x: number, y: number) => number

// 类型别名
type add3 = (x: number, y: number) => number

// 接口定义
interface add4 {
    (x: number, y: number): number
}
```

+ 类型要求

> 1. 参数类型必须声明
> 2. 返回值类型可根据类型推断省去

#### 函数参数

+ 参数个数

> 实惨和行参必须一一对应

+ 可选参数

> 可选参数必须位于必选参数之后

+ 默认参数

> 1. 在必选参数前，默认参数不可省略
> 2. 在必选参数后，默认参数可以省略

+ 剩余参数

```ts
// 参数个数
function add1(x: number, y: number) {
    return x + y
}
// add1(1, 2, 3) // 应有 2 个参数，但获得 3 个

// 可选参数必须位于必选参数之后
function add5(x: number, y?: number) {
    return y ? x + y : x
}
add5(1)

// 在必选参数前，默认参数不可省略，在必选参数之后的默认值可以不传
// y需要undefined来获取默认值;q不用
function add6(x: number, y = 1, z: number, q = 1) {
    return x + y + z + q
}
console.log(add6(1, undefined, 3)) // 5

// 剩余参数
function add7(x: number, ...rest: number[]) {
    return x + rest.reduce((pre, cur) => pre + cur);
}
add7(1, 2, 3, 4, 5) // 15
```

#### 函数重载

在typeScript中，预先定义一组名称相同，类型不同的函数声明，并在一个类型最宽松的版本中实现

```ts
// 最容易匹配的写在前面
function add8(...rest: number[]): number;
function add8(...rest: string[]): string;
// 在类型最宽泛的版本中实现重载
function add8(...rest: any[]):any {
    let first = rest[0];
    if (typeof first === 'number') {
        return rest.reduce((pre, cur) => pre + cur);
    }
    if (typeof first === 'string') {
        return rest.join('');
    }
}
console.log(add8(1, 2))
console.log(add8('a', 'b', 'c'))

// 函数重载：目标函数的参数个数要多余源函数
// 目标函数
function overload(a: number, b: number): number
function overload(a: string, b: string): string
// 具体实现是源函数
function overload(a: any, b: any): any { } // 相同参数兼容
// function overload(a: any): any {} // 参数少兼容
// function overload(a: any, b: any, c: any): any {} // 参数多不兼容
// function overload(a: any, b: any) {} // 返回值不兼容
```



### 类

#### 基本实现

+ 类中定义的属性都是实例属性，类中定义的方法都是原型方法
+ 实例属性必选有初始值，或在构造函数中被赋值，或可选成员

#### 继承

+ 子类的构造函数中必须包含super调用

#### 成员修饰符

+ 公有成员：public

> 对所有人可见，所有的成员默认为public

+ 私有成员：private

> 1. 只能在被定义的类中访问，不能通过实例或子类访问
> 2. private constructor：不能被实例化，也不能被继承

+ 受保护成员：protected

> 1. 只能在被定义的类和子类中访问，不能通过实例访问
> 2. protected constructor：不能被实例化，但是可以被继承

+ 只读成员：readonly

> 只读属性，不能更改

+ 静态成员：static

> 只能由类名调用，不能通过实例访问，可继承，然后通过子类名调用

#### 构造函数参数中的修饰符

将属性变为实例属性,可省略在类中定义

```ts
class Dog1 {
    constructor(name: string) {
        this.name = name
    }
    // 类的属性默认都是公有成员，可见的
    public name: string
    run() {
        console.log('run')
    }
    /* 
        私有成员
        1. 只能在被定义的类中访问，不能通过实例或子类访问
				2. private constructor：不能被实例化，也不能被继承
    */
    private pri() { }
    /* 
        受保护成员
        1. 只能在被定义的类和子类中访问，不能通过实例访问
				2. protected constructor：不能被实例化，但是可以被继承
     */
    protected pro() { }
    // 只读属性:不能被修改
    readonly legs: number = 4
    // 静态成员：只能由类名调用，不能通过实例访问，可继承，然后通过子类名调用 
    static food: string = 'bones'

}

// 原型,类中定义的方法都是原型方法
console.log('圆形', Dog1.prototype)

// 实例,类成员的属性，都是实例属性
const dog1 = new Dog1('dog')
console.log('dog1', dog1)

// dog1.pri() // private 私有成员,不能在实例调用
// dog1.pro() // protected 受保护，不能在实例调用
console.log(Dog1.food) // static，只能通过类名来调用

class Husky1 extends Dog1 {
    // 构造函数的行参添加修饰符，将参数自动变成实例的属性，可省略在类中定义
    constructor(name: string, public color: string) {
      	/* 
            1. super()代表父类的实例
            2. 父类的构造函数有参数，子类也一定要有
        */
        super(name)
        // this,需要在super调用之后
        this.color = color
        // this.pri() // private,私有成员不能在子类调用
        this.pro() // protected,受保护成员可以在子类调用
    }
    //    color:string
}
const hus = new Husky1('dog', 'hask')
console.log(Husky1.food) // 子类访问父类的静态属性
```

#### 抽象类 abstract

抽离出一些事物的共性，有利于代码的复用、扩展，也可实现多态

+ 不能被实例化，只能被继承

> 1. 抽象方法具体实现，子类直接复用
> 2. 抽象方法不包含具体实现，子类必须实现

+ 多态

> 多个子类对父类抽象类的方法有不同实现，实现运行时绑定

```ts
abstract class Animal {
    // 抽象方法具体实现，子类直接复用
    eat() {
        console.log('eat')
    }
    // 抽象方法不包含具体实现，子类必须实现
    abstract sleep(): void
}

// let animal = new Animal() // 无法创建抽象类的实例

class Dog extends Animal {
    constructor(name: string) {
        super()
        this.name = name
    }
    public name: string = 'dog'
    sleep() {
        console.log('Dog sleep')
    }
}
let dog = new Dog('wangwang')
dog.eat()

class Cat extends Animal {
    constructor() {
        super()
    }
    sleep() {
        console.log('Cat sleep')
    }
}
let cat = new Cat()

// 多态
let animals: Animal[] = [dog, cat]
animals.forEach(i => {
    i.sleep()
})
```

#### this类型

+ 实现实例方法的链式调用
+ 在继承时，具有多态性，保持父子类之间接口调用的连贯性

```ts
// 实现实例方法的链式调用
class Workflow {
    step1() {
        return this
    }
    step2() {
        return this
    }
}
new Workflow().step1().step2()

//在继承时this也可实现多态（既可以是子类型，也可以是父类型）
class MyFlow extends Workflow {
    next() {
        return this
    }
}
new MyFlow().next().step1().next().step2()
```



### 泛型

#### 支持多种类型的方法

+ 函数重载：多条
+ 联合类型：冗长
+ any ：丢失类型约束
+ 泛型：不预先确定的数据类型，类型在使用的时候才确定

#### 泛型函数

```ts
// 定义泛型函数
function log<T>(value: T): T {
    console.log(value);
    return value;
}

// 调用方式
log<string[]>(['a', ',b', 'c'])
// 推荐调用方式
log(['a', ',b', 'c'])

// 泛型类型别名
type Log1 = <T>(value: T) => T
let myLog1: Log1 = (value) => value
```

#### 泛型接口

```ts
// 泛型约束整个接口成员,定义的时候不存在默认值场景
interface Log3<T> {
    (value: T): T
}
// 泛型约束整个接口成员时，若定义的时候没有添加默认值，在实现的时候，必选指定一个类型
let myLog3: Log3<number> = (value) => value
myLog3(1)

// 泛型约束整个接口成员，定义的时候存在默认值
interface Log4<T = string> {
    (value: T): T
}
let myLog4: Log4 = (value) => value
myLog4('1')
```

#### 泛型类

```ts
// 泛型约束类，但是不能约束类的静态成员
class Log<T> {
    run(value: T) {
        console.log(value)
        return value
    }
}

// 实例的时候传入类型
let log1 = new Log<number>()
log1.run(1)

// 实例的时候不指定类型时，T可为任意类型
let log2 = new Log()
log2.run({ a: 1 })
log2.run([])
```

#### 泛型约束

T extends U (表示泛型T必须具有U的属性)

```ts
interface Length {
    length: number
}

// 泛型约束：具有length属性的才能满足
function logAdvance<T extends Length>(value: T): T {
    console.log(value, value.length);
    return value;
}

// logAdvance(22) // 类型“number”的参数不能赋给类型“Length”的参数
logAdvance([1])
logAdvance('123')
logAdvance({ length: 3 })
```



### 类型检查机制

#### 类型推断

+ 含义：根据某些规则自动地为变量推断出类型
+ 作用：辅助开发，提高开发效率

```ts
// 变量的类型推断
let a = 1;
let b = [1, null, 'a']
let c = { x: 1, y: 'a' }

// 函数的类型推断 定义函数参数，以及返回值
let d = (x = 1) => x + 1

// 根据事件处理推断，从左到右，上下文推断
window.onkeydown = (event) => {
    // console.log(event.button) // // keyboardEvent 上不存在属性button
}
```

#### 类型断言

+ 含义：用自己声明的类型覆盖类型推断，增加代码灵活性，避免滥用
+ 方式

> 1. 表达式 as type，推荐使用
> 2. <type>表达式

+ 弊端：没有按照接口的约定赋值，不会报错

#### 类型兼容性

如果X（目标类型）=  X（源类型），类型X兼容类型Y，Y是X的子类型

+ 接口兼容性

> 成员少的兼容成员多的

```ts
    let s: string = 'a'
    // （不允许把 null、undefined 赋值给其他类型变量）strictNullChecks: false ,字符串兼容null,也就是null是字符串的子类型
    str = null

    // 接口兼容性
    interface X {
        a: any;
        b: any;
    }
    interface Y {
        a: any;
        b: any;
        c: any;
    }
    let x: X = { a: 1, b: 2 }
    let y: Y = { a: 1, b: 2, c: 3 }
    x = y
    // y = x // 类型 "X" 中缺少属性 "c"，但类型 "Y" 中需要该属性
```

+ 函数兼容性

> 1. 固定参数：函数参数多的兼容函数参数少的
>
> ```ts
>     type Handler = (a: number, b: number) => void
> 		function hof(handler: Handler) {
>         return handler
>     }
> 
> 		let handler1 = (a: number) => { }
>     hof(handler1)
> 
>     let handler2 = (a: number, b: number, c: number) => { }
>     // hof(handler2) // 不兼容
> ```
>
> 2. 可选参数和剩余参数
>
> ```ts
>     let a = (p1: number, p2: number) => { }
>     let b = (p1?: number, p2?: number) => { }
>     let c = (...args: number[]) => { }
>     
>     // 固定参数可以兼容可选参数和剩余参数
>     a = b
>     a = c
> 
>     // 可选参数不兼容固定参数和剩余参数; （不允许函数参数双向协变）strictFunctionTypes:true时，以下不兼容
>     // b = a
>     // b = c
> 
>     // 剩余参数可以兼容固定参数和可选参数
>     c = a
>     c = b
> ```
>
> 3. 参数类型：必须匹配
>
> ```ts
> 	  type Handler = (a: number, b: number) => void
> 
>     function hof(handler: Handler) {
>         return handler
>     }
>     // 参数类型需一致,a: number 兼容
>     let handler3 = (a: string) => { }
>     // hof(handler3) // a: string 不兼容
> ```
>
> 4. 参数为对象：严格模式下成员多的兼容成员少的，非严格模式,相互兼容
>
> ```ts
>     interface Point3D {
>         x: number;
>         y: number;
>         z: number;
>     }
>     interface Point2D {
>         x: number;
>         y: number;
>     }
>     let p3d = (point: Point3D) => { }
>     let p2d = (point: Point2D) => { }
>     p3d = p2d
>     // "strictFunctionTypes": true, 严格模式下，不兼容
>     // p2d = p3d
> ```
>
> 5. 返回值类型：目标函数的返回值必须与源函数性相同，或者其子类型
>
> ```ts
>     let f = () => ({ name: 'Alice' })
>     let g = () => ({ name: 'Alice', location: 'Beijing' })
>     f = g
>     // g = f // 不能将类型“() => { name: string; }”分配给类型“() => { name: string; location: string; }”。
> ```

+ 枚举兼容性

```ts
    enum Fruit { Apple, Banana }
    enum Color { Red, Yellow }

		// 枚举和数字类型可以相互兼容
    let fruit: Fruit.Apple = 100
    let no: number = Fruit.Apple
    // 不同枚举之间是不兼容的
    // let color: Color.Red = Fruit.Apple // 不能将类型“Fruit.Apple”分配给类型“Color.Red”
```

+ 类兼容性

> 1. 静态成员和构造函数不在比较范围
> 2. 两个类具有相同的实例成员，它们的实例相互兼容
> 3. 类中包含私有成员或受保护成员，这时候只有其父类和子类的实例相互兼容

```ts
    class A {
      	// 构造函数不在比较范围
        constructor(p: number, q: number) { }
        id: number = 1
      	
      	// 类中包含私有成员或受保护成员，这时候只有其父类和子类的实例相互兼容
        private name: string = '' 
    }
    class B {
      	// 构造函数不在比较范围
        constructor(p: number) { }
        // 静态属性不在比较范围
        static s = 1
      
        id: number = 2
    }
    class C extends A { }
    let aa = new A(1, 2)
    let bb = new B(1)
    // aa = bb // A类中含有私有成员或者受保护成员，不会与其他类兼容
    bb = aa
    let cc = new C(1, 2)

    // 含有private私有属性，父类和子类才兼容
    aa = cc
    cc = aa
```

+ 泛型兼容性

```ts
    // 只有类型参数被接口使用的时候，才会影响泛型兼容性
    interface Empty<T> {
        // value: T // T未使用，不影响
    }
    let obj1: Empty<number> = {};
    let obj2: Empty<string> = {};
    obj1 = obj2
    obj2 = obj1

    //如果两个泛型函数的定义相同，但是没有指定类型参数，那么它们之间也是可以相互兼容的
    let log1 = <T>(x: T): T => {
        console.log('x')
        return x
    }
    let log2 = <U>(y: U): U => {
        console.log('y')
        return y
    }
    log1 = log2
    log2 = log1
```

#### 类型保护

+ 含义：typescript能够在特定的区块中保证变量属于某种确定的类型,可以对类型进行预判

+ 根据类型保护可以创建区块，方法如下：

> 1. instanceof
> 2. typeof
> 3. in
> 4. 类型保护函数：在其返回值使用类型谓词 x is type 来告诉ts

```ts
enum Type { Strong, Week }
class Java {
    helloJava() {
        console.log('Hello Java')
    }
    java: any
}
class JavaScript {
    helloJavaScript() {
        console.log('Hello JavaScript')
    }
    js: any
}

function getLanguage(type: Type, x: string | number) {
    let lang = type === Type.Strong ? new Java() : new JavaScript();
    // // 在每一处都加上类型断言,麻烦。因此我们可以利用类型保护机制来对类型进行预判
    // if ((lang as Java).helloJava) {
    //     (lang as Java).helloJava();
    // } else {
    //     (lang as JavaScript).helloJavaScript();
    // }
  
    // instanceof：判断实例是否使用某个类
    if (lang instanceof Java) {
        lang.helloJava()
        // lang.helloJavaScript()
    } else {
        lang.helloJavaScript()
    }

    // in 判断属性是否存在对象中
    if ('java' in lang) {
        lang.helloJava()
    } else {
        lang.helloJavaScript()
    }

    // typeof 判断基本类型
    if (typeof x === 'string') {
        console.log(x.length)
    } else {
        console.log(x.toFixed(2))
    }
  
  	// 使用类型保护函数
  	function isJava(lang: Java | JavaScript): lang is Java {
    		// 变量 is type 类型谓词
    		return (lang as Java).helloJava !== undefined
		}
  
    if (isJava(lang)) {
          lang.helloJava();
      } else {
          lang.helloJavaScript();
      }

    return lang;
}
getLanguage(Type.Week, 1)
```



### 高级类型

#### 交叉类型(& 类型并集)

+ 含义：将多个类型合并为一个类型，新的类型将具有所有类型的特性
+ 场景：混入

```ts
interface DogInterface {
    run(): void
}
interface CatInterface {
    jump(): void
}

//交叉类型：使用 & 链接，所有类型的并集
let pet: DogInterface & CatInterface = {
    run() {},
    jump() {}
}
```

#### 联合类型(｜类型交集)

+ 含义：类型不确定，可能为多个类型中的一个
+ 场景：多类型支持

```ts
// 联合类型：使用 ｜ 链接，满足其中一种条件即可
let a: number | string = 1
// 字符串字面量联合类型
let b: 'a' | 'b' | 'c'
// 数字字面量联合类型
let c: 1 | 2 | 3 

class Dog implements DogInterface {
    run() {}
    eat() {}
}
class Cat  implements CatInterface {
    jump() {}
    eat() {}
}
enum Master { Boy, Girl }
function getPet(master: Master) {
    let pet = master === Master.Boy ? new Dog() : new Cat();
    // 上面语句相当于定义了联合类型： let pet: Dog | Cat
    // pet.run() // 在未确定具体类型时，不能访问非共有成员
    // pet.jump() // 在未确定具体类型时，不能访问非共有成员
  
    // 一个对象是联合类型，在未确定具体类型时，只能访问所有类型的共有成员
    pet.eat()
    return pet
}
```

+ 可区分的联合类型：结合联合类型和字面量类型的类型保护方法

> 一个类型如果是多个类型的联合类型，且每个类型之间有一个公共属性。那么我们可以凭借这个公共属性，创建不同的类型保护区块

```ts
interface Square {
    kind: "square";
    size: number;
}
interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
interface Circle {
    kind: "circle";
    radius: number;
}
// 可区分的联合类型：1个类型如果是多个类型的联合类型，且每个类型之间有一个公共属性。
// 那么我们可以凭借这个公共属性，创建不同的类型保护区块
type Shape = Square | Rectangle | Circle
function area(s: Shape) {
    switch (s.kind) {
        case "square":
            return s.size * s.size;
        case "rectangle":
            return s.height * s.width;
        case 'circle':
            return Math.PI * s.radius ** 2
        default:
            // 防止漏选情况的发生:如果s是never类型，说明前面的分支都覆盖了全部情况，这里不会走到
            return ((e: never) => {throw new Error(e)})(s)
    }
}
console.log(area({kind: 'circle', radius: 1}))
```

#### 字面量类型

限定变量取值范围

+ 字符串字面量
+ 数字字面量

#### 索引类型

实现对对象属性的查询和访问，然后配合泛型约束来建立对象，对象属性，以及属性值之间的约束关系。常常应用于从一个对象中选取某些属性的值。

1. keyof T（索引查询操作符）：表示类型T公共属性名的字面量联合类型
2. T[K]（索引访问操作符）：对象T的属性K所代表的类型
3.  T extends U（泛型约束）：表示泛型变量T通过继承某个类型来获得某些属性

```ts
let obj = {
    a: 1,
    b: 2,
    c: 3,
}
// 从对象中获取某些属性值，并且建立集合
function getValues(obj: any, keys: string[]) {
    return keys.map(key => obj[key])
}
// 这种场景是我们不想看到的
console.log(getValues(obj, ['d', 'e'])) // [undefined, undefined]

// 使用ts对不在obj里的属性进行约束：keys里面的属性，一定是obj的属性
function getValuesTs<T, K extends keyof T>(obj: T, keys: K[]): T[K][] {
    return keys.map(key => obj[key])
}
console.log(getValuesTs(obj, ['a', 'b'])) //  [1, 2]
// console.log(getValuesTs(obj, ['d', 'e'])) // 不能将类型“"d"”分配给类型“"a" | "b" | "c"”

// keyof T（索引查询操作符）
// 表示类型T公共属性名的字面量联合类型
interface Obj {
    a: number;
    b: string;
}
let key: keyof Obj  // 相当于let key: 'a'|'b'

// T[K]（索引访问操作符） 
// 对象T的属性K所代表的类型
let value: Obj['b']
// 相当于声明了let value: string

// 泛型约束：表示泛型变量T通过继承某个类型来获得某些属性
// T extends U
```

#### 映射类型

从旧类型创建出新类型。本质上是一些预先定义的泛型接口，通常还会集合索引类型获取对象的属性和属性值，从而将对象拼凑成我们想要的结构

```ts
interface Obj {
    a: string;
    b: number;
    c: boolean;
}
```

1.  Readonly<T>：将T的所有属性变为只读

   ```ts
   /**
    *  Readonly<T>：将T的所有属性变为只读
    * 索引查询操作符： keyof T，表示类型T公共属性名的字面量联合类型
    * P in 相当于执行一次for in操作,把P依次绑定到T所有属性的联合类型
    * 索引访问操作符： T[P]，对象T的属性P所代表的类型
    * 
    * type Readonly<T> = {
       readonly [P in keyof T]: T[P];
   };
    */
   type ReadonlyObj = Readonly<Obj>
   /* 
       type ReadonlyObj = {
       readonly a: string;
       readonly b: number;
       readonly c: boolean;
   }
   */
   ```

2. Partial<T>：将T的所有属性变为可选属性

   ```ts
   /**
    * Partial<T>：将T的所有属性变为可选属性
    * 
    *  type Partial<T> = {
       [P in keyof T]?: T[P];
   };
    */
   
   type PartialObj = Partial<Obj>
   /* 
       type PartialObj = {
       a?: string;
       b?: number;
       c?: boolean;
   }
   */
   ```

3. Pick<T,K>：选取以K为属性的对象T的子集

   ```ts
   /**
    * Pick<T,K>：选取以K为属性的对象T的子集
    * 
    *  type Pick<T, K extends keyof T> = {
       [P in K]: T[P];
   };
    */
   type PickObj = Pick<Obj, 'a' | 'b'>
   /* 
       type PickObj = {
       a: string;
       b: number;
   }
   */
   ```

   **以上3个都属于同态：只作用于T的属性，第4个是非同态**

4. Record<T, K> 创新属性为K的新对象，属性值的类型为T

   ```ts
   // 非同态
   /**
    * Record<T, K> 创新属性为K的新对象，属性值的类型为T
    * 
    *  type Record<K extends keyof any, T> = {
       [P in K]: T;
   };
    */
   
   type RecordObj = Record<'x' | 'y', Obj>
   /* 
       type RecordObj = {
       x: Obj;
       y: Obj;
   }
   */
   ```

#### 条件类型



## 工程篇

### 模块系统

#### ES6

+ 导出：export单独导出，export default默认导出

  ```ts
  // 单独导出变量
  export let a = 1
  
  // 批量导出对象
  let b = 2
  let c = 3
  export { b, c }
  
  // 导出接口
  export interface P {
      x: number;
      y: number;
  }
  
  // 导出函数
  export function f() {
      console.log('fff')
  }
  
  // 导出时起别名
  function g() {}
  export { g as G }
  
  // 默认导出，无需函数名（默认导出即使起了名字也是无效的）
  export default function () {
      console.log("I'm default")
  }
  
  // 引入外部模块，重新导出
  export { str as hello } from './b'
  ```

+ 导入:import

  ```ts
  import { a, b, c } from './a'; // 批量导入
  import { P } from './a';       // 导入接口
  import { f as F } from './a';  // 导入时起别名
  import * as All from './a';    // 导入模块中的所有成员，绑定在 All 上
  import myFunction from './a';  // 不加{ }，导入默认
  
  // c.ts导入了a.ts可以说c.ts依赖了a.ts    依赖可以理解导入
  
  console.log(a, b, c)
  
  let p: P = {
      x: 1,
      y: 1
  }
  
  F()
  
  console.log(All)
  
  myFunction()
  ```

#### Commonjs

+ 导出：次级导出exports、顶级导出module.exports

  ```ts
  // 整体导出
  // module.exports = {}
  
  // 导出多个变量
  exports.c = 3
  exports.d = 4
  ```

  **如果同时存在次级导出和顶级导出，则会只生效顶级导出；次级导出不生效**

+ 导入：require

  ```ts
  let c1 = require('./a.node')
  ```

#### TypeScript

+ ES6模块系统和Commonjs模块系统最后不要混用

+ ts兼容性写法

  > 由于commonjs在引入的es6模块，默认导出会添加上default属性，因此我们在tsconfig.json上把"esModuleInterop": true，这样允许 export = 导出，由import from 导入

  ```ts
  // 导出
  export = function () {
      console.log("I'm default")
  }
  
  // 使用commonjs导入方式
  import c4 = require('../es6/d')
  // 使用import from导入方式2
  // "esModuleInterop": true, 允许 export = 导出，由import from 导入
  import c4 from '../es6/d'
  ```

### 命名空间

命名空间用namespace声明，在命名空间内可以定义任意多变量

+ 实现原理：立即执行函数构成的闭包

  > 命名空间会被编译成立即执行函数，函数创建了一个必包，必包之内有私有成员，导出的变量挂载在一个全局变量上

+ 原则：**命名空间与模块不要混用**，不要在一个模块下使用命名空间，命名空间最好在一个全局环境使用

+ 引用方法：把ts文件编译成js文件，通过script标签在html引用

+ 要点

  > 1. 局部变量对外不可见
  > 2. 导出成员对外可见
  > 3. 多个文件可共享同名命名空间
  > 4. 依赖关系：/// <reference path="a.ts" />

```ts
// a.ts
namespace Shape {
    // 局部变量对外不可见,仅在命名空间下可见
    const pi = Math.PI

    // 导出成员对外可见,要想在全局下可见，需要用export导出
    export function cricle(r: number) {
        return pi * r ** 2
    }
}

/* 
    实现原理：命名空间会被编译成立即执行函数，函数创建了一个必包.
    必包之内有私有成员，导出的变量挂载在一个全局变量上

    var Shape;
    (function (Shape) {
        // 这些变量仅在命名空间下可见
        var pi = Math.PI;
        // 要想在全局下可见，需要用export导出
        function cricle(r) {
            return pi * Math.pow(r, 2);
        }
        Shape.cricle = cricle;
    })(Shape || (Shape = {}));

*/
```

```ts
/* 
    三极线指令：path是相对路径
    Shape.square方法是在a.ts中，需要引进
*/
/* 
    命名空间的拆分，同名命名空间即使在不同的文件中，但是他们也是共享同一个空间的
    保留命名空间是对全局变量的一种兼容；在一个完全模块化的系统中，不必使用命名空间
*/

// 在b.ts引用同名的命名空间
/// <reference path="a.ts" /> 
namespace Shape {
    export function square(x: number) {
        return x * x
    }
    // 命名空间中，导出的成员不能重复定义
    // export function cricle(r: number) {
    //     return pi * r ** 2
    // }
}

// 命名空间的调用方法
console.log(Shape.cricle(2))
console.log(Shape.square(2)) 

// 命名空间的别名问题：import和模块中的import没有关系
import cricle = Shape.cricle
console.log(cricle(2))

```

### 声明合并

会把具体相同名字的声明合并为一个声明，避免对接口成员对遗漏。如果声明的时候属于全局模块，另一个声明可以在任意模块中 

#### 接口之间合并

1. 非函数成员：保证唯一性，不唯一的必须类型相同

2. 函数成员成为函数重载，其重载顺序

   > 1. 函数的参数是字符串字面量，会提升到最顶端
   > 2. 接口之间，后面的接口会排在前面
   > 3. 接口内部，按照书写排列

```ts
/* 
    接口声明合并
    interface a {
        foo(bar: number): number; // 3
    }

    interface a {
        foo(a: string): string; // 1
        foo(b: string[]): string[]; // 2
    }
*/

interface A {
    x: number;
    y: any;
    foo(bar: number): number; // 5
    foo(bar: 'a'): string; // 2
}

// 实现
interface A {
    // y: number; // 后续属性声明必须属于同一类型。属性“y”的类型必须为“any”，但此处却为类型“number”
    foo(a: string): string; // 3
    foo(b: string[]): string[]; // 4
    foo(bar: 'b'): string; // 1
}
```

#### 命名空间之间合并

导出的成员不可重复定义或实现

#### 命名空间与类合并

为类增加静态成员

```ts
// 类和命名空间的声明合并，类位置需要在命名空间之前
class C {}
namespace C {
  	// // export 不可少
    export let state = 10
}
console.log(C.state)
```

#### 命名空间与函数合并

为函数添加属性和方法

```ts
// 函数和命名空间的声明合并，函数位置需要在命名空间之前
function Lib() {}
namespace Lib {
    export let version = '2.0'
}
console.log(Lib.version)
```

#### 命名空间与枚举合并

为枚举添加属性和方法

```ts
// 枚举和命名空间的声明合并
enum Color {
    Red,
    Yellow,
    Blue
}
namespace Color {
    export function mix() {}
}
console.log(Color)
```

 

### tsconfig.json

没有配置，编译器会按照默认配置来编译当前目录下所有ts文件（ts、d.ts 、tsx），配置选项会合并

#### 文件选项

+ files：需要编译的单个文件列表
+ include：需要编译的文件或者目录,支持文件通配符
+ exclude：需要排除的文件或者目录,支持文件通配符
+ extends：配置文件继承

```ts
// tsconfig.base.json
{
    /* 编译的单个文件的列表 */
    "files": [
      //  "src/a.ts"
    ],
    /* 编译的文件或者目录，支持文件通配符 */
    // src/* src的一级目录
    // src/*/* src的二级目录
    // src/**/* src的下的所有文件
    "include": [
       "src"
    ],
    /* 排除的文件或者目录,支持文件通配符 */
    "exclude": [
       "src/lib"
    ]
 }
```

#### 编译选项

```ts
{
   /* 继承配置 */
   "extends": "./tsconfig.base.json",
   /* 可以覆盖 extends的配置*/
   "exclude": ["src/lib/**/*"],
   "compilerOptions": {
      // "incremental": true,                // 增量编译,在第一次编译之后生成一个存储编译的文件，二次编译会增量编译，提高编译速度；增量编译文件可以随意删除，没有影响
      // "tsBuildInfoFile": "./buildFile",   // 增量编译文件的存储位置
      // "diagnostics": true,                // 打印诊断信息

      // "target": "es5",           // 目标语言的版本
      // "module": "amd",      // 生成代码的模块标准
      // "outFile": "./app.js",     // 将多个相互依赖的文件生成一个文件，可以用在 AMD 模块中

      // "lib": [],                 // TS 需要引用的库，即声明文件，es5 默认 "dom", "es5", "scripthost"

      // "allowJs": true,           // 允许编译 JS 文件（js、jsx）
      // "checkJs": true,           // 允许在 JS 文件中报错，通常与 allowJS 一起使用
      "outDir": "./out",         // 指定输出目录
      "rootDir": "./src",           // 指定输入文件目录（用于控制输出结构）

      // "declaration": true,         // 生成声明文件
      // "declarationDir": "./d",     // 声明文件的路径
      // "emitDeclarationOnly": true, // 只生成声明文件,不生成js文件
      // "sourceMap": true,           // 生成目标文件的 sourceMap
      // "inlineSourceMap": true,     // 生成目标文件的 inline sourceMap(sourceMap，会包含在生产的js文件之中)
      // "declarationMap": true,      // 生成声明文件的 sourceMap
      // "typeRoots": [],             // 声明文件目录，默认 node_modules/@types
      // "types": [],                 // 声明文件包，指定要包含而不被源文件引用的类型包名称

      // "removeComments": true,    // 删除注释

      // "noEmit": true,            // 不输出任何文件
      // "noEmitOnError": true,     // 发生错误时不输出文件（有一个错误全部不输入）

      // "noEmitHelpers": true,     // 不生成 helper 函数(减少编译体积)，需额外安装 ts-helpers,
      // "importHelpers": true,     // 通过 tslib 引入 helper 函数(需额外安装 ts-helpers)，但是文件必须是模块

      // "downlevelIteration": true,    // 降级遍历器的实现（es3/5）

      "strict": true,                        // 开启所有严格的类型检查，为true,下面相关的默认为true
      // "alwaysStrict": false,                 // 在代码中注入 "use strict";
      // "noImplicitAny": false,                // 不允许隐式的 any 类型，必须要有类型注解---不允许：funtion(x){}
      // "strictNullChecks": false,             // 不允许把 null、undefined 赋值给其他类型变量---不允许let x: number = null
      // "strictFunctionTypes": false           // 不允许函数参数双向协变
      // "strictPropertyInitialization": false, // 类的实例属性必须初始化   
      // "strictBindCallApply": false,          // 严格的 bind/call/apply 检查
      // "noImplicitThis": false,               // 不允许 this 有隐式的 any 类型

      "noUnusedLocals": true,                // 检查只声明，未使用的局部变量,但是文件必须是模块
      "noUnusedParameters": true,            // 检查未使用的函数参数,
      "noFallthroughCasesInSwitch": true,    // 防止 switch 语句贯穿
      "noImplicitReturns": true,             // 每个分支都要有返回值

      "esModuleInterop": true,               // 允许 export = 导出，默认import 变量 = require('')，可更改为由import from 导入，两种都可
      // "allowUmdGlobalAccess": true,          // 允许在模块中访问 UMD 全局变量
      // "moduleResolution": "node",            // 模块解析策略(默认node)
      // "baseUrl": "./",                       // 解析非相对模块的基地址
      // "paths": {                             // 路径映射，相对于 baseUrl
      //   "jquery": ["node_modules/jquery/dist/jquery.slim.min.js"]
      // },
      // "rootDirs": ["src", "out"],            // 将多个目录放在一个虚拟目录下，用于运行时

      "listEmittedFiles": true,        // 控制台打印输出的文件
      // "listFiles": true,               // 控制台打印编译的文件（包括引用的声明文件）
  }
}
```

+ 部分测试用例

> 1. "noEmitHelpers": 不生成 helper 函数(减少编译体积)，需额外安装 ts-helpers,
>     "importHelpers": 通过 tslib 引入 helper 函数(需额外安装 ts-helpers)，但是文件必须是模块
>
>    ```ts
>    class A { }
>                
>    class B extends A {}
>                
>    export = B
>    ```
>
> 2. "strictBindCallApply": 严格的 bind/call/apply 检查
>
>    ```ts
>    // "strictBindCallApply": true, 关闭严格的 bind/call/apply 检查后，不会提示报错
>    function add(x: number, y: number) {
>        return x 
>    }
>    
>    // add.call(undefined, 1, '3') // 类型“string”的参数不能赋给类型“number”的参数
>    ```
>
> 3. "noImplicitThis": 不允许 this 有隐式的 any 类型
>
>    ```ts
>    class C {
>        a: number = 1
>        getA() {
>            // "noImplicitThis": true,   // 不允许 this 有隐式的 any 类型
>            // return function() {
>            //     console.log(this.a) // 会提示this具有隐式类型any,因为他没有类型注释
>            // }
>    
>            // 更改为箭头函数
>            return () => {
>                console.log(this.a)
>            }
>        }
>    }
>    c() // Cannot read properties of undefined (reading 'a')
>    ```
>
> 4. "noFallthroughCasesInSwitch": 防止 switch 语句贯穿
>
>    ```ts
>    // "noFallthroughCasesInSwitch": true,    // 防止 switch 语句贯穿
>    // 开启之后没有break的case会报错
>    function test (){
>        switch (key) {
>            case '1':
>                console.log(key)
>                // break; // 不写break,会提示switch 语句中的 Fallthrough 情况
>            case '2':
>                console.log(key)
>                // 在函数内没有break有retrun，不会报错
>                return
>            default:
>                break;
>        }
>    }
>    ```
>
> 5. "rootDirs": ["src", "out"] 根据其.d.ts声明文件将多个目录放在一个虚拟目录下，用于运行时

#### 工程引用

可以灵活的配置输出目录，可以使输出之间产生依赖关系，有利于把大的项目拆分为小的项目；同时还可以利用增量编译提升编译速度

+ composite：工程可以被引用，和进行增量编译增量编译
+ declaration：生成声明文件，工程引用时是必须开启
+ references：配置所依赖的工程，引入了什么模块
+ 使用tsc --build构建模式:单独的构建一个工程，相关依赖也会自动跟着构建

```ts
// tsconfig.json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    // 指定输出目录
    "outDir": "../../dist/client",
    "strict": true,
    // 工程可以被引用，和进行增量编译增量编译 
    "composite": true,
    // 生成声明文件，工程引用是必须开启
    "declaration": true,
    // 配置所依赖的工程，引入了什么模块
    "references": [
        { "path": "../common" }
    ]
  }
}
/* 
  使用tsc --build构建模式:单独的构建一个工程，相关依赖也会自动跟着构建
  构建命令 tsc -build src/server --verbose   
  清除工程构建命名  tsc -build src/server --clean   
  --verbose:打印出构建信息

  1,解决输出目录结构问题
  2，单个工程构建问题
  3，通过增量编译提升了编译速度  
*/
```



### 编译工具

#### ts-loader

+ 默认配置是同时进行语言转换和类型检查的，但其身上options参数的transpileOnly开启之后，只做语言转化，不去做类型

+ transpileOnly: true，可需要配合fork-ts-checker-webpack-plugin插件做独立的类型检查进程, 使用该插件后，类型检查不通过无法进行build

```js
const forkTsCheckerWebpackPlugin  = require('fork-ts-checker-webpack-plugin')

module.exports = {
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: [{
                    loader: 'ts-loader',
                    options: {
                        transpileOnly: true // 默认为false 
                    }
                }],
                exclude: /node_modules/
            }
        ]
    },
    plugins: [
        new forkTsCheckerWebpackPlugin()
    ],
}
```

#### awesome-typescript-loader

+ 同样是进行语言转换和类型检查，但其更适合与babel集成，使用babel的转义和缓冲

+ 与ts-loader的区别：开启transpileOnly的时候，不需要安装额外的插件，使用其自身的checkerPlugin插件就可以把类型检查放在独立进程中进行，但是存在检查遗漏                            

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { checkerPlugin } = require('awesome-typescript-loader')

module.exports = {
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: [{
                    loader: 'awesome-typescript-loader',
                    options: {
                        transpileOnly: true
                    }

                }],
                exclude: /node_modules/
            }
        ]
    },
    plugins: [
        new checkerPlugin()
    ],
}
```

#### Babel

只做语言转化，不支持类型检查

> babel 7 之前需要将ts转为js,然后再执行babel解析js
>
> babel 7 之后才可以之间将ts >>> babel >>> js

+ 转换typeScript

  >1. 安装依赖
  >
  >   ```
  >   @babel/cli
  >   @babel/core
  >   @babel/preset-env
  >   
  >   @babel/preset-typescript // 将ts转化为js
  >   
  >   @babel/plugin-proposal-class-properties // 支持类
  >   @babel/plugin-proposal-object-rest-spread // 支持扩展语法
  >   ```
  >
  >2. 在根目录下新增.babelre文件
  >
  >   ```
  >   {
  >       "presets": [
  >           "@babel/env",
  >           "@babel/preset-typescript"
  >       ],
  >       "plugins": [
  >           "@babel/proposal-class-properties",
  >           "@babel/proposal-object-rest-spread"
  >       ]
  >   }
  >   ```
  >
  >3. 无法编译的typeScript特性
  >
  >   ```ts
  >   // 1,命名空间在babel中编译会报错
  >   namespace N {
  >       export const n = 1
  >   }
  >   
  >   // 2，类型断言的写法
  >   // let s = <A>{} // 这种不支持
  >   let s = {} as A
  >   s.a = 1
  >   
  >   // 3，常量枚举不支持
  >   const enum E { A, B }
  >   
  >   // 4,默认导出,测试仅这种不支持
  >   export = s
  >   ```

+ 在babel中如何做类型检查： 安装typescript，新开终端输入脚步："tsc --watch",开启类型检查

  ```json
    "scripts": {
      "build": "babel src --out-dir dist --extensions \".ts,.tsx\"",
      "type-check": "tsc --watch",
    },
  ```

**两种编译工具不要混用**

### 代码检查工具

#### ESLint

#### typescrip-eslint

基于typescript的AST,支持创建基于类型信息的规则（tsconfig.json）

1. 依赖安装

   ```
   eslint
   @typescript-eslint/eslint-plugin  // 识别ts特殊语法
   @typescript-eslint/parser // eslint提供解析器
   ```

2. 根目录创建配置文件.eslintrc.json

   ```json
   {
       "env": {
   		"browser": true,
   		"es6": true,
   		"node": true
   	},
       "parser": "@typescript-eslint/parser",
       "plugins": ["@typescript-eslint"],
       "parserOptions": {
           /* 类型信息 */
           "project":"./tsconfig.json"
       },
       "extends":[
           "plugin:@typescript-eslint/recommended"
       ],
       "rules": {
            "@typescript-eslint/no-inferrable-types":"off",
            "@typescript-eslint/no-var-requires": 0
       }
   }
   ```

3. package.json新增启动脚步

   ```json
     "scripts": {
       "lint": "eslint src --ext .js,.ts",
     },
   ```

4. 配合VSCode ESLint 插件，启用保存自动修复eslint.autoFixOnSave

#### babel-eslint

支持typescript没有的额外的语法检查，抛弃typescript，不支持类型检查

**两种底层机制不一样，不要一起使用； babeL体系建议使用babel-eslint,否则typescrip-eslint**

### 单元测试

#### ts-jest

有类型检查

1. 依赖安装

   ```
   npm i jest babel-jest @types/jest -D
   ```

2. package.json新增测试脚本

   ```json
     "scripts": {
       "test": "jest"
     },	
   ```

3. 在终端利用npx ts-jest config:init脚步创建jest.config.js测试配置文件

   ```js
   /** @type {import('ts-jest/dist/types').InitialOptionsTsJest} */
   module.exports = {
     preset: 'ts-jest',
     // 测试环境
     testEnvironment: 'node',
   };
   ```

4. 根目录添加test/math.test.ts测试用例

   ```ts
   function add(a: number, b: number) {
       return a + b;
   }
   test('add: 1 + 2 = 3', () => {
       // 断言
       expect(math.add(1, 2)).toBe(3);
   });
   
   // 在测试用例支持类型检查
   // let x: number = '1' // 不能将类型“string”分配给类型“number”
   ```

5. 终端执行 npm run test 脚步即可实现jest测试

#### babel-jest

无类型检查

操作与test-jest一样，只是依赖改变了

```
npm i jest babel-jest @types/jest -D
```



