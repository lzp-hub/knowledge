## javascript me

JavaScript是一种直译式脚本语言，是一种动态类型、弱类型、基于原型的语言，内置支持类型。它的解释器被称为JavaScript引擎，为浏览器的一部分，广泛用于客户端的脚本语言，最早是在HTML(标准通用标记语言下的一个应用)网页上使用，用来给HTML网页增加动态功能。

ECMA 规定其有三部分组成，ECMAScript(语法)、DOM(文档)、BOM(浏览器)

### 预解析

JS 引擎执行代码的阶段:

- **通读代码，声明提升**  
  在通读代码阶段，引擎会先查看是否有语法错误，如果有，就报错。如果没有，则在这个过程中，就会将代码中所有的通过 var 声明的变量和通过 function 声明的函数**提升**到代码的最前面。这个提升行为，就是发生在预解析阶段。
- **执行代码**
  将代码按照规则，一条一条执行。

### 作用域

- 作用域: **变量生效的范围**
- 作用域是根据书写的单词和语法来确定的,所以又叫做词法作用域.
- 划分规则: 只有全局作用域和函数的私有作用域、后面有块级作用域

 script 标签之间是全局作用域 多个 script 标签共享同一个作用域

```JavaScript
    /* 演示单线程 */
// 浏览器在加载 script 标签的代码的时候,是一个 script 标签加载并执行完毕之后,再去加载执行后面的一个.
    <script>
        var a = 10;
    </script>
    <script>
        console.log(a); // 输出10
    </script>
----------------------------
// 但是每一个 script 标签中的变量函数,它们的提升,`只能够在本script标签的范围之内`
    <script>
        console.log(a); // ReferenceError: a is not defined
    </script>
    <script>
        var a = 10;
    </script>
```

#### 访问变量

- 当访问变量的时候，会先查看当前作用域中是否存在该变量
- 如果有，就使用，并终止查找。
- 如果没有，就将会向上一层级作用域中寻找。
- 依次向上，直到找到，或者**到了全局作用域中还没有找到，就会报错。**

```Javascript
// 当前是全局作用域
var num = 100; // 在全局作用域中定义的变量

// 定义一个函数
function demo() {
    var num = 101; // 这是在函数作用域中定义的变量
    console.log(num);
}
// 执行函数
demo(); // 101
```

```JavaScript
var num = 100; // 在全局作用域中定义的变量
// 定义一个函数
function demo() {
    // 因为声明提升的原因 代码在预解析时在此处添加 var num; 
    console.log(num);
    var num = 101; 
}
// 执行函数
demo(); // undefined
```

#### 赋值变量

- 当对一个变量进行赋值的时候，会先查看当前作用域中是否存在该变量
- 如果有，就赋值，并终止查找。
- 如果没有，就将会向上一层级作用域中寻找。
- 依次向上，直到找到，或者**到了全局作用域中还没有找到，就会在全局作用域中悄悄的声明这个变量并赋值**

```js
function demo() {
   num = 101;
}
// 执行函数
demo();
// num是多少
console.log(num); // 101 会在全局作用域中悄悄的声明这个变量并赋值
```



### JS 的糟粕之一

如果一个变量没有通过var声明 就直接赋值，语法上不报错，但是极不推荐

```JavaScript
num = 101; 
console.log(num); // 101
```

```JavaScript
// 报错： num is not defined
console.log(num);
num = 101;
```

### 

## 变量

名称定义规范

- 整体由 数字、 字母、 下划线(\_)、 美元符号($) 组成
- 不可以以数字开头
- 不可以使用关键字、保留字作为变量名称
- JS 变量名称严格区分大小写(对大小写敏感)
- 推荐使用驼峰命名法



## 基本数据类型

- 判定基本数据类型方法 `typeof`
- 强制数据类型转换
  - Boolean() 将其他的数据类型强制转换成布尔值：非 0 即真，非空即真。
  - Number() 将其他的数据类型强制转换成数字：只有纯数字字符组成的字符串转数字，才能转为数字，其他都为 NaN.
  - parseInt() 取整：将别的进制转成十进制 必须传入字符串
  - parseFloat() 取浮点数

## 字符串

### 定义字符串

```js
    // 1. 字符串常量赋值（""或者''）
    var str3 = "100";

    // 2.省略new声明字符串
    var str2 = String(100);

    // 3.通过new运算符去声明字符串
    var str = new String(100);
    console.log(typeof str); // object
    // 转换为字符串的方式就是
    str.toString();
```

### 字符串的属性(length)

- length 属性length 属性表示字符串的长度

```JavaScript
    // 字符串也可以通过下标获取对应的字符
    var a = "abcdefg";
    console.log(a[2]); // c
    console.log(a.length); // 7
```

### 字符串的方法

所有的字符串方法都不会改变原字符串

------

#### **slice(start, end)**

- 功能：提取字符串的某一部分，并返回一个新的字符串。

- 参数：

  - `start`：开始位置（包含）。
  - `end`：结束位置（不包含）。如果省略，则截取到字符串末尾。

- 示例：

  ```js
  const str = "Hello World";
  console.log(str.slice(0, 5)); // "Hello"
  console.log(str.slice(6));    // "World"
  ```

------

#### **indexOf(searchValue, fromIndex)**

- 功能：返回指定子字符串在字符串中首次出现的位置。

- 参数：

  - `searchValue`：要查找的子字符串。
  - `fromIndex`：从哪个位置开始查找（可选，默认 0）。

- 返回值：找到则返回索引，没找到返回 `-1`。

- 示例：

  ```js
  const str = "Hello World";
  console.log(str.indexOf("World")); // 6
  console.log(str.indexOf("o"));     // 4
  console.log(str.indexOf("x"));     // -1
  ```

------

#### **toLowerCase()**

- 功能：将字符串转换为小写。

- 示例：

  ```js
  const str = "Hello World";
  console.log(str.toLowerCase()); // "hello world"
  ```

------

#### **toUpperCase()**

- 功能：将字符串转换为大写。

- 示例：

  ```js
  const str = "Hello World";
  console.log(str.toUpperCase()); // "HELLO WORLD"
  ```

------

#### **replace(searchValue, newValue)**

- 功能：替换字符串中匹配的子串。

- 参数：

  - `searchValue`：要替换的内容（字符串或正则表达式）。
  - `newValue`：新内容。

- **注意**：只会替换第一个匹配项。如果要全局替换，需使用正则表达式 `/xxx/g`。

- 示例：

  ```js
  const str = "Hello World World";
  console.log(str.replace("World", "JavaScript"));   // "Hello JavaScript World"
  console.log(str.replace(/World/g, "JS"));          // "Hello JS JS"
  ```

------

#### **search(regexp)**

- 功能：搜索与正则表达式匹配的值，并返回第一个匹配项的索引。

- 参数：

  - `regexp`：正则表达式对象（也可传字符串，但会被当成正则）。

- 返回值：匹配到返回索引，未匹配到返回 `-1`。

- 示例：

  ```js
  const str = "Hello World";
  console.log(str.search("World"));   // 6
  console.log(str.search(/o/));       // 4
  console.log(str.search(/x/));       // -1
  ```

------

#### **includes(searchValue, fromIndex)**

- 功能：判断字符串是否包含某个子串。

- 参数：

  - `searchValue`：要查找的子字符串。
  - `fromIndex`：从哪个位置开始查找（可选，默认 0）。

- 返回值：布尔值（`true` / `false`）。

- 示例：

  ```js
  const str = "Hello World";
  console.log(str.includes("Hello")); // true
  console.log(str.includes("world")); // false（区分大小写）
  console.log(str.includes("o", 5));  // true（从索引 5 开始找）
  ```

------

#### **trim()**

- 功能：去除字符串两端的空格（包括换行符、制表符等空白字符）。

- 示例：

  ```js
  const str = "   Hello World   ";
  console.log(str.trim()); // "Hello World"
  ```

## 数字

## 布尔值

## undefined

## null

## function

### **函数的调用过程**

1. 在执行栈根据函数名找到储存在栈里面的函数体(以字符串的形式存储)
2. 开辟一个执行空间(开辟作用域)
3. 解析函数代码
4. 传递参数
5. 执行内部代码
6. 执行完释放该执行空间。

### 函数的参数

- 当形参比实参多: 多余的形参的值为 undefined
- 当形参比实参少: 没有形参接收多余的实参

### 函数的传参规则

函数可以执行时传递参数。那么如果在函数内部修改传递进去的参数，会不会影响到函数外部的值呢？

- 基本数据类型的数据在传递时，复制传递 也就是复制了一份并传递进去 所以在函数内部，如何操作都只是在操作副本。与外部的数据无关。
- 引用数据类型的数据在传递时，传递引用 也就是把地址复制了一份并传递进去 所以函数内部是可以得到函数外部的数据保存的地址的，如果在函数内部打点或者方括号修改地址内容，则会影响到函数外部，**如果在函数内部使用=修改变量保存的内容，则不会影响函数外部。**

```JavaScript
    // 在函数内部修改值类型参数
    var a = 123;
    function demo(b) {
        b = 12;
    }
    demo(a);
    console.log(a); // 123
```

```JavaScript
    // 在函数内部使用等号修改引用类型参数
    var obj = {};
    function demo(b) {
        b = 123;
    }
    demo(obj);
    console.log(obj); // {}
```

```JavaScript
    // 在函数内部使用方括号或者点语法修改引用类型参数
    var obj = {};
    function demo(b) {
        b.a = 10;
        b["b"] = 11;
    }
    demo(obj);
    console.log(obj); // {a: 10, b: 11}
```

```JavaScript
    // 在函数内部先使用方括号或者点语法修改 再使用等号
    var obj = {};
    function demo(b) {
        b.a = 10;
        b["b"] = 11;
        b = {};
        b.c = 12;
      console.log('b',b) // {c: 12}
    }
    demo(obj);
    console.log(obj); // {a: 10, b: 11}
```

### arguments

arguments是函数内部的一个成员，只有在函数执行的时候才会存在。可以使用它来获取本次函数在执行时，所有传递的实参。

```JavaScript
function sum() {
    console.log(arguments);
}
sum(1); // => [1]
sum(2); // => [2]
sum(1, 2, 3, 4, 5); // => [1, 2, 3, 4, 5]
```

arguments 是一个类数组对象，也叫做伪数组对象。

- 可以使用 `arguments[idx]` 来获取对应的值。 `idx`是 从`0`开始的。 这个`idx`，也可以叫做 `下标` 或者 `索引`
- arguments.length可以获取实参的个数

```JavaScript
function sum() {
    console.log(arguments[2]);
}
sum(1, 2, 3, 4); // => 3
sum(1, 2); // => undefined
```

类数组变为数组:

```js
// 方法一：
var args = Array.prototype.slice.call(arguments, 0);
// 方法二：Array 实例在访问原型上方法的写法
var args = [].slice.call(arguments, 0);
// 方法三：
var args = [];
for (var i = 1; i < arguments.length; i++) {
  args.push(arguments[i]);
}
```

### return 关键字

作用: 该关键字是用于在函数内部 `返回内容` 并 `中止函数执行` 的。

### 函数的属性(name、length)

- `name` 它是函数的名称 函数的名称不论是表达式还是声明 都是变量的名字

- `length` 它是函数的形参的个数

```JavaScript
    function demo(a, b, c) {}
    console.log(demo.name); // "demo"
    console.log(demo.length); // 3

    var fun = function() {}
    console.log(fun.name); // "fun"
    console.log(fun.length); // 0
```

### 递归函数

递归函数指的是函数自己再函数体内部调用自己。**无停止条件的递归 就是死循环 写递归函数，先写停止条件**

```JavaScript
// a变量一定要定义在函数外部
var a = 0;
function demo() {
    a++;
    if (a >= 10) {
        return;
    }
    demo();
}
demo(); // 循环让a自加10次 之后就停止
```

### 作用域

- 作用域: 规定变量起作用的范围
- 划分规则: 只有全局作用域和函数的

### IIEF

Immediately Invoked Function Expression，意为立即调用的函数表达式，也就是说，声明函数的同时立即调用这个函数。

javascript 引擎规定，如果 function 关键字出现在行首，一律解释成函数声明语句。所以 function 需要（）包囊

```js
(function () {})();
```

IIFE 方式和正常的先声明再调用相比较，有如下的特点：

1.  实现立即执行，不需要先声明再调用。
2.  匿名函数自身不污染全局环境，同时为内部变量提供作用于环境空间。
3.  缩写形参有利于内部代码的压缩

### 回调函数

函数 a 有一个参数，这个参数是个函数 b，当函数 a 执行完以后执行函数 b。那么这个过程就叫回调。

- 将回调函数 b，**以参数的形式传给 a 函数**，但是要等函数 a 执行完以后再执行函数 b
- 也可以直接在函数 a 里面调用函数 b 如：function a(){...; b(); }

### 函数中的 this

this 是作用域中的一个引用类型对象，它可以让开发者更加方便的去复用函数。通过 this 可以直接访问所绑定的对象里面的其他属性，但是不能够通过`this = 修改` 

#### this 的特点

在函数定义的时候，无法确定指向（箭头函数除外）；只有在函数调用的时候，才可以确定指向

#### this 指向

1. 没有明确调用者，非严格模式指向window，严格模式指向undefined
2. 上一级的对象所调用、多层级对象调用，都是指向它**上一级的对象**。obj.fn()这里 this 是 obj；obj.subObj.fn();这里 this 是 subObj
3. 定时器函数指向window；IIFE指向window
4. new 关键字就是去创建一个对象实例
   在构造函数中，如果返回值是一个引用类型{}或 function(){}，那么 this 指向的就是该引用类型，如果返回值不是一个对象，那么 this 还是指向函数的实例。
5. 箭头函数中的 this 始终指向父级对象，在定义时就绑定了

#### fn.call(obj, a, b)

#### fn.apply(obj, [a, b])

#### newFn=fn.bind(obj, a, b)

## 数组

### 数组的定义

构造函数，通过 new 创建数组

```JS
    var arr = new Array();
```

- 传参规则：
  - 当没有参数时， 定义的是一个空数组
  - **当参数只有一个并且是数字时，该数字表示数组的长度**
  - 当参数有多个时，参数就是每一个成员

字面量

```JS
    var arr = [];
```

### 数组的 length 属性

- 数组是一组数据，length 属性表示这个数组中的内容的个数。也就是数组的长度
- length 可读可写。

```JavaScript
    var arr = [1, 2, 3, 4];
    console.log(arr.length); // 4
    arr.length = 1; // 修改数组的长度
    console.log(arr); // 输出 [1]
    console.log(arr.length); // 1
```

### 数组的方法

**修改原数组**：`push`、`pop`、`shift`、`unshift`、`reverse`、`sort`、`splice`、`fill`、`copyWithin`

**不修改原数组**：`concat`、`slice`、`join`、`indexOf`、`includes`、`map`、`filter`、`reduce`、`some`、`every`、`find`、`findIndex`、`flat`、`flatMap`、`at`、`toReversed`、`toSorted`、`toSpliced`、`with`

**工具型**：`Array.isArray`、`Array.from`、`Array.of`

**迭代器**：`keys`、`values`、`entries`

## 对象

### delete 

该关键字只能用来删除对象的属性

```JavaScript
    var obj = {
        name: "张三"
    }
    delete obj["name"];
    console.log(obj); // {}
```

### for (var name in obj) {}

