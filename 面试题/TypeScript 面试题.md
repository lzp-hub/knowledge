好 👌 那我接着输出 **TypeScript 面试题.md**，保持和 React / Tailwind 一样的结构。

---

# 🟦 TypeScript 面试题 · 高频考点（3–5 年工程师）

## 1) TypeScript 相比 JavaScript 的优势是什么？ 🔥

**一句话回答**
TS 提供静态类型、IDE 提示、可维护性更强，减少运行时错误。

**要点**

- 类型检查；
- 更好重构；
- 接口与泛型；
- 与现代框架（React/Next.js）深度结合。

**详细回答**

- JS 是动态语言，错误多在运行时出现；
- TS 提前在编译期发现 bug；
- 丰富的类型系统（泛型、联合、条件类型）增强表达力。

**扩展/对比**

- **JS vs TS**：TS 增加构建成本，但大型团队维护成本更低。

---

## 2) `interface` vs `type` 🔥

**一句话回答**
`interface` 用于结构描述，可扩展；`type` 更灵活，支持联合/交叉。

**要点**

- interface 可声明合并；
- type 可联合、条件、映射。

**详细回答**

```ts
interface User {
  id: number;
}
interface User {
  name: string;
} // 合并 -> { id, name }

type Status = "active" | "inactive"; // 联合类型
```

**扩展/对比**

- 面试追问：“什么时候用 interface/type？” 👉 interface 用于库定义，type 用于复杂组合。

---

## 3) 泛型（Generics） 🔥

**一句话回答**
泛型让函数/类/Hook 支持类型参数化，增强复用与类型安全。

**要点**

- 函数泛型；
- 泛型约束 `extends`；
- 泛型工具类型。

**详细回答**

```ts
function identity<T>(arg: T): T {
  return arg;
}
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

**扩展/对比**

- **any vs 泛型**：any 放弃检查，泛型保持类型关系。

---

## 4) 常见 Utility Types 🔥

**一句话回答**
TS 内置工具类型如 `Partial`、`Pick`、`Omit`、`Record`，简化类型操作。

**要点**

- Partial：可选字段；
- Pick：选择部分属性；
- Omit：排除属性；
- Record：键值映射。

**详细回答**

```ts
type User = { id: number; name: string };
type UserUpdate = Partial<User>;
type UserPreview = Pick<User, "id">;
```

**扩展/对比**

- 面试常问：“实现 Partial<T>？” 👉 `[K in keyof T]?: T[K]`。

---

## 5) 类型守卫（Type Guards） 🔥

**一句话回答**
类型守卫通过运行时检查，缩小联合类型范围。

**要点**

- typeof / instanceof；
- 自定义守卫 `arg is Type`。

**详细回答**

```ts
function isString(x: unknown): x is string {
  return typeof x === "string";
}
```

**扩展/对比**

- 面试追问：“为什么 unknown 比 any 更安全？” 👉 因为 unknown 必须先守卫。

---

## 6) `any` vs `unknown` vs `never` 🔥

**一句话回答**
`any` 跳过检查，`unknown` 更安全，`never` 表示不会发生的值。

**要点**

- any：失去类型安全；
- unknown：必须缩小；
- never：函数不返回或抛异常。

**详细回答**

```ts
let a: any = 1;
a.toFixed(); // ok
let b: unknown = 1;
b.toFixed(); // ❌
function fail(msg: string): never {
  throw new Error(msg);
}
```

**扩展/对比**

- 面试常考：“什么时候用 unknown？” 👉 API 响应。

---

## 7) keyof 与索引类型 🔥

**一句话回答**
`keyof` 获取属性联合；索引类型 `[K in keyof T]` 遍历属性。

**要点**

- keyof：属性名集合；
- T\[K]：索引访问；
- 常见于工具类型实现。

**详细回答**

```ts
type User = { id: number; name: string };
type Keys = keyof User; // "id" | "name"
type ReadonlyUser = { [K in keyof User]: User[K] };
```

**扩展/对比**

- 高级：实现 DeepPartial → 递归索引类型。

---

## 8) 条件类型与 infer 🔥

**一句话回答**
条件类型 `T extends U ? X : Y`；`infer` 提取类型变量。

**要点**

- 分布式条件：联合类型自动分发；
- infer：提取返回值/参数。

**详细回答**

```ts
type IsString<T> = T extends string ? true : false;
type R = IsString<"a" | 1>; // true | false

type Return<T> = T extends (...a: any[]) => infer R ? R : never;
```

**扩展/对比**

- 面试常考：实现 Exclude\<T,U> → `T extends U ? never : T`。

---

## 9) 协变与逆变 🔥

**一句话回答**
TS 是结构化类型系统，函数参数逆变，返回值协变。

**要点**

- 参数：子类型不能赋给父类型（逆变）；
- 返回值：子类型可赋值给父类型（协变）。

**详细回答**

```ts
type Fn = (a: Animal) => Dog;
let g: (a: Dog) => Animal;
let f: Fn = g; // ❌ 参数逆变
```

**扩展/对比**

- 面试官喜欢问：“为什么函数参数逆变？” 👉 保证类型安全。

---

## 10) 装饰器（Decorator） 🔥

**一句话回答**
装饰器是实验特性，用于注解类/方法/属性，常见于 NestJS/Angular。

**要点**

- `@` 语法；
- 需 tsconfig 开启 `experimentalDecorators`；
- 常见：日志、注入。

**详细回答**

```ts
function Log(target: any, key: string) {
  console.log("Decorating:", key);
}
class A {
  @Log foo() {}
}
```

**扩展/对比**

- React 生态少见，但 MobX `@observable` 用过。

---

## 11) TS 在 React 中的使用 🔥

**一句话回答**
TS 通过泛型定义 Props/State，Hook 返回值类型化。

**要点**

- `useState<T>()`；
- `React.FC<Props>`；
- 自定义 Hook 泛型。

**详细回答**

```ts
interface BtnProps {
  onClick?: () => void;
}
const Button: React.FC<BtnProps> = ({ onClick }) => (
  <button onClick={onClick} />
);
```

**扩展/对比**

- 面试追问：“为什么不推荐 React.FC？” 👉 默认带 children，可能不符合设计。

---

## 12) tsconfig 常见配置 🔥

**一句话回答**
`strict`、`esModuleInterop`、`skipLibCheck` 最常用。

**要点**

- strict：开启严格检查；
- esModuleInterop：兼容 commonjs；
- skipLibCheck：跳过声明文件。

**详细回答**

- 大型项目必须 strict；
- 缺少类型的库用 `@types/xxx` 或自定义 d.ts。

**扩展/对比**

- 面试官追问：“如何给第三方库补充类型？” 👉 声明合并 or 自己写声明文件。

---
