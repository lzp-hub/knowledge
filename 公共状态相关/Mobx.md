## **mobx**

**简介**

MobX 是一个专注于状态管理的库，在 React 世界的流行程度仅次于拥有官方背景的 Redux。但 MobX 有自己独特的优势，它通过运用透明的函数式响应编程使状态管理变得简单、高效、自由。

### 核心原理

利用**defineProperty**(<=v5)或**Proxy**(v6)拦截对象属性的变化，实现数据的**Observable**，在 get 中依赖收集，set 中触发依赖绑定的监听函数。
假如你之前关注过 Vue.js、Knockout 等的一些 MVVM 框架的响应式原理，那么你应该会感到非常熟悉。是的，它们的原理如出一辙。

**核心概念**

不仅是原理，基础概念、顶层的 Api 设计也十分相似。Vue.js 中 data、computed、watch，几乎可以与 Mobx 中的[observable-state](https://link.juejin.cn?target=https%3A%2F%2Fzh.mobx.js.org%2Fobservable-state.html)、[computed](https://link.juejin.cn?target=https%3A%2F%2Fzh.mobx.js.org%2Fcomputeds.html)、[reaction](https://link.juejin.cn?target=https%3A%2F%2Fzh.mobx.js.org%2Freactions.html)等概念一一对应。最大的不同是，MobX 通过 actions 约束对 state 的更新方式，实现了对状态的管理这一重要步骤。整体运行流程如下图所示。

![alt 运行流程](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b09cacf0f1a64213a93c85c1d2f96814~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

**安装**

**mobx** 这个包，提供了 MobX 所有的与具体框架平台无关的基础 Api。比如（observable、makeObservable、action等）。

```css
npm i mobx
```

如果在 react 中使用，需要添加针对 react 开发的包 **mobx-react**

```css
npm i mobx mobx-react
```

如果你在 react 开发中，只使用函数式组件，没有使用类组件，那么可以将 mobx-react 替换为一个更轻量的包 **mobx-react-lite**

```markdown
npm i mobx mobx-react-lite

相比mobx-react这个全量包，
1. 去掉了对class components的支持，
2. 并且移除了provider、inject
（原因：这两个HOC在React官方已经提供了React.createContext之后变得不是那么必要了）
```

### 声明Store

相比直接使用**普通对象**，MobX 更推荐使用**类**的方式去创建 Store，主要原因是 class 对 TS 的类型系统更友好，更容易被索引实现自动补全等功能。

1. 直接使用**普通对象**的方式 （不推荐）

```javascript
import { observable,action } from 'mobx'

const userStore = observable({
  roleType:1
})

export const changeRoleType = action((val)=>{
  userStore.roleType = val
})

export default userStore
```

2. 使用**类 + 装饰器** （V6 版本之前的推荐方式）

``` js
import { observable } from 'mobx'

class UserStore{
  @observable roleType = 1
  
  @action changeRoleType(val){
    this.data = val
  }
}

export default UserStore
```

3. 使用**类 + makeObservable** （V6 版本的推荐方式）（**不再推荐装饰器**的原因可以在Q&A章节找到）

   > + 使用**`makeObservable(target, annotations?, options?)`**
   >
   >    一般情况下，`makeObservable` 是在类的构造函数中调用的，并且它的第一个参数是 `this` 。 `annotations` 参数将会为每一个成员映射 [注解](https://zh.mobx.js.org/observable-state.html#可用的注解)。需要注意的是，当使用 [装饰器](https://zh.mobx.js.org/enabling-decorators.html) 时，`annotations` 参数将会被忽略。
   >
   > ```js
   > import { makeObservable,observable,computed,action } from 'mobx'
   > 
   > class UserStore{
   >   constructor(){
   >     makeObservable(this,{
   >       roleType:observable,
   >       roleName:computed,
   >       changeRoleType:action
   >     })
   >   }
   >   roleType = 1
   >   get roleName(){
   >     return roleMap[roleType]
   >   }
   >   changeRoleType(val){
   >     this.roleType = val
   >   }
   > }
   > export default UserStor
   > ```
   >
   > + 使用**`makeAutoObservable(target, overrides?, options?)`**
   >
   >   `makeAutoObservable` 就像是加强版的 `makeObservable`，在默认情况下它将推断所有的属性。你仍然可以使用 `overrides` 重写某些注解的默认行为。
   >
   > ```js
   > import { makeAutoObservable } from 'mobx'
   > 
   > class UserStore{
   >   class UserStore{
   >   constructor(){
   >     makeAutoObservable(this)
   >     /* 无需显示的声明，会自动应用合适的MobX-Api去修饰。比如
   >     （1）值字段会被推断为observable、
   >     （2）get 修饰的方法，会推断为computed、
   >     （3）普通方法，会自动应用action 
   >     （4）如果你有自定义调整某些字段的需求，请参考此方法的[其他入参]
   >     	(https://zh.mobx.js.org/observable-state.html#makeautoobservable)
   >     */
   >   }
   >   roleType = 1
   >   get roleName(){
   >     return roleMap[roleType]
   >   }
   >   changeRoleType(val){
   >     this.roleType = val
   >   }
   > }
   > export default UserStore
   > ```

### 常用方法

#### observable

表示定义一个state值，包装后的状态是一个可观察数据（Observable Data）

#### @action.bound或@aciton

表示定义修改state的方法，里面是修改state的逻辑，通过这种方式可以直接通过this.xxx来修改state

> action 装饰器/函数遵循 javascript 中标准的绑定规则。 但是，action.bound 可以用来自动地将动作绑定到目标对象（即当前Store的实例）,**所以action.bound 不要和箭头函数一起使用**

#### computed

计算属性，表示根据现有的数据state计算出来的衍生值，将函数当作属性用（**函数的return值**）

get 一个函数

```js
    @computed
    get canRedo() {
        return this.currentLayerHistoryIndex < this.layerHistory.length - 1
    }
```

#### isObservable

isObservable(obj) 判断是否为observable 类型的参数方法，返回boolean

#### toJS

将observable类型的转为普通类型

```js
import { isObservable, toJS,observable } from 'mobx'

const list = observable([1,2])
console.log(isObservable(list)) // true
console.log(list,toJS(list)) // toJS(list)转为普通数组
```

#### runInAction

mobx不允许在@action中的Promise.then或async @aciton等异步操作中直接进行state的数据更新操作

Promise.then中：

```js
class Store {
    @observable githubProjects = []
    @observable state = "pending" // "pending" / "done" / "error"

    @action
    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(
            projects => {
                const filteredProjects = somePreprocessing(projects)
                // 将‘“最终的”修改放入一个异步动作中
                runInAction(() => {
                    this.githubProjects = filteredProjects
                    this.state = "done"
                })
            },
            error => {
                // 过程的另一个结局:...
                runInAction(() => {
                    this.state = "error"
                })
            }
        )
    }
}
复制代码
```

async的action中：

```csharp
 @action
    async fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        try {
            const projects = await fetchGithubProjectsSomehow()
            const filteredProjects = somePreprocessing(projects)
            // await 之后，再次修改状态需要动作:
            runInAction(() => {
                this.state = "done"
                this.githubProjects = filteredProjects
            })
        } catch (error) {
            runInAction(() => {
                this.state = "error"
            })
        }
    }
```

### mobx-react

MobX提供了一个mobx-react包帮助开发者方便地在React中使用MobX。`observer/@observer`就来自这个包。下面介绍mobx-react中另外两个常用API。

#### observer

**自动订阅**在react组件**渲染期间**被使用到的**可观察对象属性**，当他们变化发生时，组件就会自动进行**重新渲染**

```js
import { observer } from 'mobx-react'
// 函数组件
observer(MyComponent);

// 在类组件
@observer MyComponent;
```

#### inject

`inject`是一个高阶组件，它和`Provider`结合使用，用于从**`Provider`提供的state中选取所需数据，作为props传递给目标组件**。 常用方式有如下两种：

```js
import { observer, inject } from 'mobx-react'
// 函数组件
inject("store1", "store2")(observer(MyComponent));

// 在类组件
@inject("store1", "store2");
@observer MyComponent;
```

**`@inject`与`@observer`的书写有顺序问题。@inject应该在前面，写错顺序会导致`inject`失效。**

#### Provider

`Provider`是一个React组件，利用React的**context机制把应用所需的state传递给子组件**。它的作用与react-redux提供的`Provider`组件是相同的。

一个简单示例：

```tsx
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import { observer, inject, Provider } from 'mobx-react'
import { observable } from 'mobx'

@inject('store')
@observer
// inject从context中取出store对象，注入到组件的props中
class App extends Component {
  render () {
    const { store } = this.props
    return (
      <div>
        <ul>
          {store.map(todo => (
            <TodoView todo={todo} key={todo.id} />
          ))}
        </ul>
      </div>
    )
  }
}
const TodoView = observer(({ todo }) => {
  return <li>{todo.title}</li>
})

// 构造store及其初始数据
const storeTodos = observable([])
todos.push({ id: 1, title: 'Task1' })
todos.push({ id: 2, title: 'Task2' })

ReactDOM.render(
  {/* Provider向context中注入store对象 */}
  <Provider store={storeTodos}>
    <App />      
  </Provider>, document.getElementById("root")
);
```

### MobX + useContext

函数组件+useContext是目前开发React应用的首选方式。MobX顺应趋势，推出了新的hook Api，这已经成为使用MobX的主流方式。

1. store.js文件

   ```js
   import { RouterStore } from 'mobx-react-router'
   
   export const routerStore = new RouterStore()
   
   export { default as authStore } from './authStore'
   export { default as globalStore } from './global'
   ```

2. RootProvider.tsx文件，把store注入Context中

   ```js
   import React, { createContext, useContext } from 'react'
   import * as store from '@store/index'
   
   export const RootContext = createContext<IAllStore>(null)
   
   /**
    * useContext获取store里存储的值
    *
    * @export
    * @returns
    */
   export function useRootStore() {
       return useContext(RootContext)
   }
   
   /**
    * RootProvider
    *
    * 替代mobx Provider
    *
    * @export
    * @param {{ children?: React.ReactNode }} { children }
    * @returns
    */
   export default function RootProvider({ children }: { children?: React.ReactNode }) {
   	return <RootContext.Provider value={{ ...store }}>{children}</RootContext.Provider>
   }
   ```

3. RootProvider注入组件

   ```react
   import React from 'react'
   import ReactDOM from 'react-dom'
   import { configure } from 'mobx'
   import { Router } from 'react-router-dom'
   import { createHashHistory } from 'history'
   import { syncHistoryWithStore } from 'mobx-react-router'
   
   import RootProvider from './RootProvider'
   import * as store from './store'
   import App from '@components/App'
   configure({ enforceActions: 'observed' })
   
   const hashHistory = createHashHistory()
   const history = syncHistoryWithStore(hashHistory, store.routerStore)
   const render = () => {
       const element = (
           <RootProvider>
               <Router history={history}>
                   <App />
               </Router>
           </RootProvider>
       )
       ReactDOM.render(element, document.getElementById('app'))
   }
   
   render()
   ```

4. 在组件是获取store数据

   ```react
   import { useRootStore } from './RootProvider'
   
   const demo = () => {
     const { globalStore } = useRootStore()
     console.log('globalStore',globalStore)
     return <div>test</div>
   }
   
   export default observer(demo)
   ```

   

