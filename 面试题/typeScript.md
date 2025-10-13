## 一、基础与类型系统

### 1. TypeScript 的基本类型有哪些？ 🔥

**一句话回答**
TS 在 JS 基础上增加了静态类型系统，包括 `any`、`unknown`、`never`、`void` 等特殊类型。

**要点**

- 基本：string、number、boolean、null、undefined。
- 特殊：**any、unknown、never、void。**
- 引用：object、array、**tuple、enum**。
- **高级：泛型、联合、交叉、字面量。**

**详细回答**

- `any`：关闭类型检查。
- `unknown`：安全的 `any`，使用前必须缩小类型。
- `never`：表示不可能出现的值（抛异常、死循环）。
- `void`：函数无返回值。

**扩展/对比**

- 面试官常问：“any vs unknown 有什么区别？” 👉 any 可随意操作，unknown 必须先类型缩小。

---

### 2. null 与 undefined 在 TS 中的处理

**一句话回答**
TS 中 `null` 和 `undefined` 默认属于所有类型，但开启 `strictNullChecks` 后需单独声明。

**要点**

- 严格模式下必须显式允许。
- 常见写法：`string | null`。

**详细回答**

```ts
let name: string = null; // 非严格模式下允许
let name: string | null = null; // 严格模式下写法
```

**扩展/对比**

- 面试官常问：“strictNullChecks 有什么作用？” 👉 避免空值错误。

---

### 3. 类型断言 vs 类型推断

**一句话回答**
推断是 TS 自动判断类型，断言是开发者强制告诉 TS 类型。

**要点**

- 推断：从值自动推导。
- 断言：`as` 或 `<T>`。

**详细回答**

```ts
let str = "hello"; // 推断为 string
const el = document.getElementById("app") as HTMLDivElement; // 断言
```

**扩展/对比**

- 建议优先依赖类型推断，断言作为兜底。

---

### 4. any、unknown、never 的区别 🔥

**一句话回答**
any 关闭检查，unknown 需缩小后使用，never 表示不可能有值。

**要点**

- any：不安全。
- unknown：更安全。
- never：函数不返回。

**详细回答**

```ts
function fail(msg: string): never {
  throw new Error(msg);
}
```

**扩展/对比**

- 面试官常问：“什么时候用 unknown？” 👉 当类型无法确定时，用它替代 any。

---

### 5. void 与 never 的区别

**一句话回答**
void 表示没有返回值，never 表示永远不会有返回值。

**要点**

- void：函数执行但无返回。
- never：函数不会结束。

**详细回答**

```ts
function log(): void {
  console.log("hi");
}
function error(): never {
  throw new Error();
}
```

**扩展/对比**

- never 常用于类型守卫。

---

## 二、泛型与高级类型

### 6. 泛型的作用与使用 🔥

**一句话回答**
泛型提供了在不确定类型时的复用能力，常用于函数、类、接口。

**要点**

- 占位符 `<T>`。
- 函数泛型。
- 约束：`extends`。

**详细回答**

```ts
function identity<T>(arg: T): T {
  return arg;
}
identity<number>(1);
identity("hi"); // 类型推断
```

**扩展/对比**

- 泛型是 TS 核心，面试官常问“泛型约束如何写”。

---

### 7. 常见 Utility Types 🔥

**一句话回答**
TS 内置工具类型简化了常见类型操作，如 Partial、Pick、Omit、Record。

**要点**

- Partial<T>：所有属性可选。
- Pick\<T,K>：提取部分属性。
- Omit\<T,K>：排除部分属性。
- Record\<K,T>：键值对映射。

**详细回答**

```ts
interface User {
  id: number;
  name: string;
  age: number;
}
type UserPreview = Pick<User, "id" | "name">;
type UserOptional = Partial<User>;
```

#### 1. `Partial<T>`

**作用**：将所有属性变为可选

```ts
type Partial<T> = {
  [P in keyof T]?: T[P];
};

interface User {
  id: number;
  name: string;
  age: number;
}

type UserOptional = Partial<User>;
/* 等价于：
type UserOptional = {
  id?: number;
  name?: string;
  age?: number;
}
*/
```

------

#### 2. `Required<T>`

**作用**：将所有属性变为必填

```ts
type Required<T> = {
  [P in keyof T]-?: T[P];
};
// -? 是 “移除可选” 的操作，常用于把所有属性从 可选 变成 必选

type UserRequired = Required<Partial<User>>;
/* 等价于：
type UserRequired = {
  id: number;
  name: string;
  age: number;
}
*/
```

------

#### 3. `Readonly<T>`

**作用**：所有属性只读

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type UserReadonly = Readonly<User>;
/* 等价于：
type UserReadonly = {
  readonly id: number;
  readonly name: string;
  readonly age: number;
}
*/
```

------

#### 4. `Pick<T, K>`

**作用**：从类型 `T` 中选择部分属性

```ts
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

type UserPreview = Pick<User, "id" | "name">;
/* 等价于：
type UserPreview = {
  id: number;
  name: string;
}
*/
```

------

#### 5. `Omit<T, K>`

**作用**：从类型 `T` 中排除部分属性

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;

type UserWithoutAge = Omit<User, "age">;
/* 等价于：
type UserWithoutAge = {
  id: number;
  name: string;
}
*/
```

------

#### 6. `Record<K, T>`

**作用**：构造一个对象类型，键为 `K`，值为 `T`

```ts
type Record<K extends keyof any, T> = {
  [P in K]: T;
};

type Role = "admin" | "user";
type Permissions = Record<Role, boolean>;
/* 等价于：
type Permissions = {
  admin: boolean;
  user: boolean;
}
*/
```

------

#### 7. `Exclude<T, U>`

**作用**：从 `T` 中排除可以赋值给 `U` 的类型

```ts
type Exclude<T, U> = T extends U ? never : T;

type T1 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
```

------

#### 8. `Extract<T, U>`

**作用**：从 `T` 中提取可以赋值给 `U` 的类型

```ts
type Extract<T, U> = T extends U ? T : never;

type T2 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
```

------

#### 9. `NonNullable<T>`

**作用**：排除 `null` 和 `undefined`

```ts
type NonNullable<T> = T extends null | undefined ? never : T;

type T3 = NonNullable<string | null | undefined>; // string
```

------

#### 10. `ReturnType<T>`

**作用**：获取函数的返回值类型

```ts
type ReturnType<T extends (...args: any) => any> = 
  T extends (...args: any) => infer R ? R : any;

function getUser() {
  return { id: 1, name: "Tom" };
}
type UserType = ReturnType<typeof getUser>; // { id: number; name: string }
```

------

#### 11. `Parameters<T>`

**作用**：获取函数参数类型元组

```ts
type Parameters<T extends (...args: any) => any> = 
  T extends (...args: infer P) => any ? P : never;

function sum(a: number, b: string) {
  return a + b;
}
type Params = Parameters<typeof sum>; // [number, string]
```

#### 2. `valueof`（自定义实现）

其实就是把 **`T[keyof T]`** 取出来：

```ts
type ValueOf<T> = T[keyof T];

type UserValues = ValueOf<User>;
// number | string
```

---

### 8. 联合类型与交叉类型

**一句话回答**
联合是“或”，交叉是“与”。

**要点**

- 联合：`|`。
- 交叉：`&`。

**详细回答**

```ts
type A = string | number;
type B = { id: number } & { name: string };
```

**扩展/对比**

- 面试官常问：“交叉类型冲突怎么办？” 👉 以 never 处理。

---

### 9. 类型守卫 🔥

**一句话回答**
类型守卫是缩小联合类型范围的方式。

**要点**

- typeof。
- instanceof。
- in 操作符。
- 自定义守卫。

**详细回答**

```ts
function isString(x: unknown): x is string {
  return typeof x === "string";
}
```

**扩展/对比**

- 面试官常问：“unknown 类型如何使用？” 👉 配合守卫。

---

### 10. 条件类型与分布式条件类型

**一句话回答**
条件类型用三元表达式表达类型逻辑，联合类型会分发。

**要点**

- 基本语法：`T extends U ? X : Y`。
- 分布式条件类型。

**详细回答**

```ts
type IsString<T> = T extends string ? true : false;
type Result = IsString<"a" | 1>; // true | false
```

**扩展/对比**

- 面试官可能让你写“Exclude\<T,U>”的实现。

---

## 三、工程实践与类型设计

### 11. 如何定义 API 响应类型

**一句话回答**
通过泛型接口和 Utility Types 定义灵活的 API 响应类型。

**要点**

- 泛型接口。
- Partial、Pick 等工具。

**详细回答**

```ts
interface ApiResponse<T> {
  code: number;
  data: T;
  msg: string;
}
type UserResponse = ApiResponse<{ id: number; name: string }>;
```

**扩展/对比**

- 面试官常问：“如何避免重复定义？” 👉 泛型。

---

### 12. 如何在项目中用 TS 提升可维护性 🔥

**一句话回答**
通过类型约束、接口抽象、泛型工具，避免低级错误并提升开发效率。

**要点**

- 接口统一 API 响应。
- 类型定义组件 Props。
- TS + ESLint 检查。

**详细回答**

- 在项目中 TS 类型定义减少了调试时间。
- 大型项目中，TS 提升团队协作效率。

**扩展/对比**

- 面试官常问：“TS 在你项目中带来了什么价值？” 👉 类型安全 + 开发效率。

---

### 13. TS 中的模块化与声明文件

**一句话回答**
TS 支持 ESModule 与 CommonJS，同时可通过 `.d.ts` 定义类型声明。

**要点**

- import/export。
- declare module。

**详细回答**

```ts
declare module "*.png" {
  const value: string;
  export default value;
}
```

**扩展/对比**

- 面试官可能让你写声明文件。

---

### 14. keyof 与索引访问类型 🔥

**一句话回答**
keyof 获取对象所有键，索引访问获取具体属性的类型。

**要点**

- keyof T → 联合类型。
- T\[K] → 属性类型。

**详细回答**

```ts
type Keys = keyof { id: number; name: string }; // "id" | "name"
type IdType = { id: number; name: string }["id"]; // number
```

**扩展/对比**

- 面试官常问：“如何写一个取对象某字段的函数？” 👉 用 keyof 约束。

---

### 15. infer 关键字 🔥

**一句话回答**
infer 用于在条件类型中推断类型。

**要点**

- 常用于提取函数返回值。

**详细回答**

```ts
type Return<T> = T extends (...args: any[]) => infer R ? R : never;
type R = Return<() => string>; // string
```

**扩展/对比**

- 面试官常问：“手写实现 ReturnType<T>。”
