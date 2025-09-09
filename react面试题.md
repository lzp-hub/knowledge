## 什么是 React？

React是一个简单的javascript UI库，用于构建高效、快速的用户界面。

它是一个轻量级库，因此很受欢迎。**它遵循组件设计模式、声明式编程范式和函数式编程概念**，以使前端应用程序更高效。

它**使用虚拟DOM**来有效地操作DOM。

它遵循从高阶组件到低阶组件的**单向数据流**。

## 什么是虚拟DOM？

虚拟DOM（VDOM）它是真实DOM的内存表示,一种编程概念，一种模式。**它会和真实的DOM同步**，比如通过ReactDOM这种库，这个同步的过程叫做调和(reconcilation)。

虚拟DOM更多是一种模式，不是一种特定的技术。

------

## react 的虚拟dom是原理的？

React 是把真实的 DOM 树转换为 JS 对象树，也就是 Virtual DOM。

**React.createElement(type,props,children)**创建虚拟节点(VNode)

- type: 标签名称   字符串
- props: 标签属性  null或者属性对象{...},
- children: 标签内容   文本(字符串)或者标签的子节点[ ]

所谓虚拟`DOM`其实只是一个包含了标签类型`type`，属性`props`以及它包含子元素`children`的对象。

------

## react中虚拟dom是如何转变成真实dom

**ReactDOM.render(VNode,target)** 是 ReactDOM 的最基本方法，用于将内容渲染到指定节点中

- VNode：虚拟节点或 React 组件

- target：挂载点，必须为元素节点; 用原生方法获取节点，例如：document.getElementById('app')

  ```react
  ReactDOM.render(<App />, document.getElementById('root'));
  ```

------

## 为什么React的 VM 可以提高性能？

因为 VM 并不是真实的操作 DOM，通过 **diff 算法**可以避免一些不变要的 DOM 操作，从而提高了性能。

## React中的VM 一定会提高性能吗？

不一定，因为 VM 只是通过 diff 算法避免了一些不需要变更的 DOM 操作，最终还是要操作 DOM 的，并且 diff 的过程也是有成本的。

对于某些场景，比如都是需要变更 DOM 的操作，因为 VM 还会有额外的 diff 算法的成本在里面，所以 VM 的方式并不会提高性能，甚至比原生 DOM 要慢。

------

## 简单介绍下React中的 diff 算法

diff 算法主要基于三个规律：

- DOM 节点的跨层级移动的操作特别少，可以忽略不计
- 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构
- 对于同一层级的一组子节点，可以通过唯一的 id 进行区分

**tree diff**

因为上面的三个策略中的第一点， DOM 节点的跨级操作比较少，那么 diff 算法只会对相同层级的 DOM 节点进行比较。如果发现节点不存在 那么会将该节点以及其子节点完全**删除**，不会再继续比较。如果出现了 DOM 节点的跨层级的移动操作，那么会删除改节点以及其所有的子节点，然后再移动后的位置重新创建。

**component diff**

如果是同一类型的组件，那么会继续对比 VM 数

如果不是同一类型的组件，那么会将其和其子节点完全替换，不会再进行比对

同一类型的组件，有可能 VM 没有任何的变化，如果可以确定的知道这点，那么就可以节省大量的 diff 时间，所以用户可以设置 shouldComponentUpdate() 来判断是否需要进行 diff 算法。

**element diff**

当节点处于同一层级的时候时，有三种操作：INSERT_MAKEUP插入、 MOVE_EXISTING 移动、 REMOVE_NODE 删除

这里 React 有一个优化策略，对于同一层级的同组子节点，添加唯一的 **key** 进行区分。这样的话，就可以判断出来是否是移动节点。通过 key 发现新旧集合中的节点都是相同的节点，就只需要进行移动操作就可以。

------

## 虚拟DOM一定更快吗？

**虚拟DOM／domDiff**

我们常说的虚拟DOM是通过JS对象模拟出来的DOM节点,domDiff是通过特定算法计算出来一次操作所带来的DOM变化。react和vue中都使用了虚拟DOM，我们借着react聊聊虚拟DOM。

react中涉及到虚拟DOM的代码主要分为以下三部分，其中核心是第二步的domDiff算法：

- 把render中的JSX(或者createElement这个API)转化成虚拟DOM
- 状态或属性改变后重新计算虚拟DOM并生成一个补丁对象(domDiff)
- 通过这个补丁对象更新视图中的DOM节点

**虚拟DOM不一定更快**

干前端的都知道DOM操作是性能杀手，因为操作DOM会引起页面的回流或者重绘。相比起来，通过多一些预先计算来减少DOM的操作要划算的多。

但是，“使用虚拟DOM会更快”这句话并不一定适用于所有场景。例如：一个页面就有一个按钮，点击一下，数字加一，那肯定是直接操作DOM更快。使用虚拟DOM无非白白增加了计算量和代码量。即使是复杂情况，浏览器也会对我们的DOM操作进行优化，大部分浏览器会根据我们操作的时间和次数进行批量处理，所以直接操作DOM也未必很慢。

那么为什么现在的框架都使用虚拟DOM呢？因为使用**虚拟DOM可以提高代码的性能下限，并极大的优化大量操作DOM时产生的性能损耗**。同时这些框架也保证了，即使在少数虚拟DOM不太给力的场景下，性能也在我们接受的范围内。

而且，我们之所以喜欢react、vue等使用了虚拟DOM框架，不光是因为他们快，还有很多其他更重要的原因。例如react对**函数式编程的友好**，vue优秀的开发体验等，目前社区也有好多比较这两个框架并打口水战的，我觉着还是在两个都懂的情况下多探究一下原理更有意义一些。

------

## 为什么不能用数组下标来作为react组件中的key？

react 使用diff算法，使用key来做同级比对。如果使用数组下标作为key，有以下情况：

- 在数组头部或中部插入或删除元素： 所有key对应的节点的值发生更改，进行重新渲染。造成性能损耗
- 而如果使用数组中唯一值来作为key：不管是在何处插入或删除节点，其他key对应的节点的值未发生更改，只需插入或删除操作的数组节点。

## React中的类组件和函数组件之间有什么区别？

无论是使用函数或是类来声明一个组件，它决不能修改它自己的 props。React是**单项数据流**，父组件改变了属性，那么子组件视图会更新。属性 props是外界传递过来的，状态state是组件本身的，状态state可以在组件中任意修改

**类组件Class components**

```react
class Welcome extends React.Component {
  constructor() {
        super();// 调用super后才能使用this
        this.state = {
            data: 'test'
        }
    }
  render() {
    return (
      <h1>Welcome { this.props.name }</h1>
    );
  }
}
ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));
```

**函数组件functional component**

函数组件接收一个单一的 props 对象并返回了一个React元素

```react
function Welcome (props) {
  return <h1>Welcome {props.name}</h1>
}
ReactDOM.render(<Welcome name='react' />, document.getElementById('root'));
```

**区别**

1. 语法上

   > + 函数组件是一个纯函数，它接收一个props对象返回一个react元素。
   > + **类组件需要去继承React.Component并且创建render函数返回react元素**，这将会要更多的代码，虽然它们实现的效果相同。

2. 状态管理

   > + 函数组件是一个纯函数，你不能在组件中使用setState()，这也是为什么把函数组件称作为无状态组件。在react16.8版本中添加了hooks，使得我们可以在函数组件中使用useState钩子去管理state
   > + 类组件的组件中使用state，你可以选择创建一个类组件或者将state提升到你的父组件中，然后通过props对象传递到子组件

3. 生命周期钩子

   > + 不能在函数组件中使用生命周期钩子，原因和不能使用state一样，所有的生命周期钩子都来自于继承的React.Component中，在react16.8版本中添加了hooks，使用useEffect钩子去使用生命周期函数。
   > + 想使用生命周期钩子，那么需要使用类组件

------

## 什么是高阶组件？

高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。基本上，这是从React的组成性质派生的一种模式，我们称它们为“纯”组件， 因为它们可以接受任何动态提供的子组件，但它们不会修改或复制其输入组件的任何行为。

- 高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧
- 高阶组件的参数为一个组件返回一个新的组件

------

## constructor中super与props参数一起使用的目的是什么？

在ES6中，在子类的constructor中必须先调用super才能引用this。

在constructor中可以使用this.props来获取props值

+ super使用props：

  ```react
  class MyComponent extends React.Component {
      constructor(props) {
          super(props);
          console.log(this.props);  // Prints { name: 'sudheer',age: 30 }
      }
  }
  ```

+ super不使用props：

  ```react
  class MyComponent extends React.Component {
      constructor(props) {
          super();
          console.log(this.props); // Prints undefined
          // 但是Props参数仍然可用
          console.log(props); // Prints { name: 'sudheer',age: 30 }
      }
  
      render() {
          // 构造函数外部没有区别
          console.log(this.props) // Prints { name: 'sudheer',age: 30 }
      }
  }
  ```

  上面的代码片段揭示了**this.props行为仅在构造函数中有所不同**。外部构造函数相同。

##  什么是受控组件？

在HTML当中，像`<input>`,`<textarea>`, 和 `<select>`这类表单元素会维持自身状态，并根据用户输入进行更新。但在React中，**可变的状态通常保存在组件的状态属性**中，我们可以在累组件用 setState() 方法，在函数组件用useState进行更新。

**非受控组件**，即组件的状态不受React控制的组件

```
 <input />
```

**受控组件**，受控组件就是组件的状态受React控制。上面提到过，既然通过设置input的value属性, 无法改变输入框值,那么我们把它和state结合在一起,再绑定onChange事件,实时更新value值就行了。

```react
class Demo1 extends Component {
    constructor(props) {
        super(props);
        this.state = {
            value: props.value
        }
    }

    handleChange(e) {
        this.setState({
            value: e.target.value
        })
    }

    render() {
        return (
            <input value={this.state.value} onChange={e => this.handleChange(e)}/>
        )
    }
}
```

## 什么是JSX？

JSX即JavaScript XML。一种在React组件内部构建标签的类XML语法。**JSX 是`React.createElement()`的语法糖**，可以让我们在 js 代码中使用 html 标签来编写结构。严格意义上来说它还是 JS。React在不使用JSX的情况下一样可以工作，然而使用JSX可以提高组件的可读性，因此推荐使用JSX。

## 如何在React中应用样式？

+ 外部样式表

  > 在此方法中，你可以将外部样式表导入到组件使用类中。 但是你应该使用**className**而不是class来为React元素应用样式, 这里有一个例子。
  >
  > ```react
  > import React from 'react';
  > import './App.css';
  > function App() {
  >   return (
  >     <div className="App">
  > 				外部样式表
  >     </div>
  >   );
  > }
  > export default App;
  > ```

+ 内联样式

  > 在这个方法中，我们可以直接将 props 传递给HTML元素，属性为**style**。这里有一个例子。这里需要注意的重要一点是，我们将**javascript对象传递给style**，这就是为什么我们使用 `backgroundColor` 而不是CSS方法`backbackground-color`。
  >
  > ```react
  > import React from 'react';
  > export const Header = () => {
  >     return(
  >         <div style={{backgroundColor:'orange'}}>
  >             内联样式
  >         </div>
  >     )
  > }
  > ```

+ 定义**样式对象**并使用它

  > 因为我们将javascript对象传递给style属性，所以我们可以在组件中定义一个style对象并使用它。下面是一个示例，你也可以将此对象作为 props 传递到组件树中。
  >
  > ```react
  > import React from 'react';
  > 
  > const footerStyle = {
  >     width: '100%',
  >     backgroundColor: 'green',
  >     padding: '50px',
  >     font: '30px',
  >     color: 'white',
  >     fontWeight: 'bold'
  > }
  > 
  > export const Footer = () => {
  >     return(
  >         <div style={footerStyle}>
  >             All Rights Reserved 2019
  >         </div>
  >     )
  > }
  > ```

------

## 简述下 React 的生命周期？每个生命周期都做了什么？

![](https://i.loli.net/2021/07/04/ur5i6B4VbwSd3vU.png)

**挂载**: 当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

- constructor()
- render()
- componentDidMount()

**更新**: 当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

- shouldComponentUpdate()
- render()
- componentDidUpdate

**卸载**: 当组件从 DOM 中移除时会调用如下方法：

- componentWillUnmount()

**错误处理**: 渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

- componentDidCatch()

### constructor()

如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现constructor构造函数。在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前前调用 super(props)。否则，this.props 在构造函数中可能会出现未定义的 bug。

+ 通过给 this.state 赋值对象来**初始化内部 state**。
+ 为事件处理函数绑定实例
+ 只能在构造函数中直接为 this.state 赋值。在 constructor() 函数中不要调用 setState() 方法，如需在其他方法中赋值，你应使用 this.setState() 替代。
+ 要避免在构造函数中引入任何副作用或订阅。如遇到此场景，请将对应的操作放置在 componentDidMount 中。

### componentDidMount()

componentDidMount() 会在**组件挂载后（插入 DOM 树中）立即调用**。依赖于 **DOM 节点的初始化**应该放在这里。如需通过**网络请求**获取数据，此处是实例化请求的好地方。这个方法是比较适合**添加订阅**的地方。如果添加了订阅，请不要忘记在 componentWillUnmount() 里取消订阅

> 某些依赖组件 DOM 节点的操作；发起ajax请求；设置 setInterval、setTimeout 等计时器操作

### componentDidUpdate()

componentDidUpdate() **会在更新后会被立即调用**。首次渲染不会执行此方法。

当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。（例如，当 props 未发生变化时，则不会执行网络请求）

> 执行依赖新 DOM 节点的操作。依据新的属性发起新的ajax请求。

```js
componentDidUpdate(prevProps) {
  // 典型用法（不要忘记比较 props）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

你也可以在 componentDidUpdate() 中直接调用 setState()，但请注意它必须被**包裹在一个条件语句**里，正如上述的例子那样进行处理，否则会导致死循环。

### componentWillUnmount()

componentWillUnmount() 会在**组件卸载及销毁之前**直接调用。在此方法中执行必要的清理操作。

> 例如，清除 timer，取消网络请求或清除在 componentDidMount() 中创建的订阅等。

componentWillUnmount() 中不应调用 setState()，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。

### shouldComponentUpdate()

根据 shouldComponentUpdate() 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。

**当 props 或 state 发生变化时，shouldComponentUpdate() 会在渲染执行之前被调用。返回值默认为 true。首次渲染或使用 forceUpdate() 时不会调用该方法。**

此方法仅作为性能优化的方式而存在。不要企图依靠此方法来“阻止”渲染，因为这可能会产生 bug。你应该考虑使用内置的 **PureComponent** 组件，而不是手动编写 shouldComponentUpdate()。PureComponent 会对 props 和 state 进行**浅层比较**，并减少了跳过必要更新的可能性。

如果你一定要手动编写此函数，可以将 this.props 与 nextProps 以及 this.state 与nextState 进行比较，并返回 false 以告知 React 可以跳过更新。请注意，返回 false 并不会阻止子组件在 state 更改时重新渲染。

我们**不建议**在 shouldComponentUpdate() 中进行深层比较或使用 JSON.stringify()。这样非常影响效率，且会损害性能。

```js
// 数据更新会执行该函数。根据返回值控制组件是否刷新；不写该函数的时候，返回值默认为true，即允许组件刷新，
shouldComponentUpdate(nextProps, nextState) {
// nextProps：props将要改变的值
// this.props: 当前值
// nextState: 将要改变的state
// this.state： 当前state

// 由于父组件刷新，会默认更新子组件，即使它依赖的数据没有更新，因此我们需要性能优化
// 这里，有更简单的方法实现简单判断；我们可以在把类组件的React.Component更改为PureComponent
// 这样内部帮我们做了`shouldComponentUpdate`的简单判断，
// 让state和props不变的情况下自动`return false`，从而实现性能优化
// 这时候，我们就不用定义shouldComponentUpdate函数了；
} 
```

**浅比较只比较了第一层**，复杂数据结构可能会导致更新问题

### componentDidCatch()

此生命周期在**后代组件抛出错误后被调用**。 它接收两个参数：

- error —— 抛出的错误。
- info —— 带有 componentStack key 的对象，其中包含有关组件**引发错误的栈信息**。

componentDidCatch() 会在“提交”阶段被调用，因此允许执行副作用。 它应该用于记录错误之类的情况。

```react
class ErrorBoundary extends React.Component {
    constructor(props) {
        super(props)
        this.state = { errData: { error: null, errorInfo: null } }
    }
   	// 错误边界
    componentDidCatch(error, errorInfo) {
        this.setState({
            errData: { error, errorInfo }
        })
    }
    handleClick = () => {
        location.reload()
    }
    render() {
        if (this.state.errData && this.state.errData.error) {
            return (
                <div>
                    <Button onClick={this.handleClick}>重新加载</Button>
                </div>
            )
        }
        return this.props.children
    }
}
export default ErrorBoundary
```

```react
import React from 'react'
import ErrorBoundary from '@components/common/ErrorBoundary'

const DouyinIMCenter = () => {
    return (
        <ErrorBoundary>
           被包囊的后代组件代码抛出错误，会显示自定义的UI界面
        </ErrorBoundary>
    )
}

export default DouyinIMCenter
```

## React父子组件生命周期执行顺序

1. 父组件constructor
2. 父组件componentWillMount
3. 父组件**render**
4. 子组件constructor
5. 子组件componentWillMount
6. 子组件render
7. 子组件componentDidmount
8. 父组件componentDidMount

**父组件先开始执行constructor，待其render之后，执行子组件constructor，再等子组件挂载后componentDidmount，父组件才componentDidmount挂载**

------

## React.PureComponent 和 React.Component 有什么区别？

PureComponent 和 Component的区别是：Component需要手动实现 shouldComponentUpdate，而 PureComponent 通过浅对比默认实现了 shouldComponentUpdate 方法。

------

## 在shouldComponentUpdate 或 componentWillUpdate 中使用 setState 会发生什么?

当调用 setState 的时候，实际上会将新的 state 合并到状态更新队列中，并对 partialState 以及 _pendingStateQueue 更新队列进行合并操作。最终通过 enqueueUpdate 执行 state 更新。

如果在 shouldComponentUpdate 或 componentWillUpdate 中使用 setState，会使得 state 队列（_pendingStateQueue）不为 null，从而调用 updateComponent 方法，updateComponent 中会继续调用 shouldComponentUpdate 和 componentWillUpdate，因此造成**死循环**。

------

## 为什么不能直接使用 this.state 改变数据？

react中不能直接修改state，因为并**不会重新触发render**。

```js
//Wrong
this.state.message =”Hello world”;
```

而是需要使用setState()方法，状态改变时，组件通过重新渲染做出响应。

```js
//Correct
this.setState({message: ‘Hello World’});
```

setState通过一个**队列机制来实现 state 更新**。当执行 setState 的时候，会将需要更新的 state 合并后放入状态队列，而不会立刻更新 this.state。队列机制可以**高效的批量更新** state，如果不通过 setState 而直接修改 this.state，那么该 state 将不会被放入状态队列中，当下次调用 setState 并对状态队列进行合并时，将会忽略之前被直接修改的 state，而造成无法预知的错误。

------

## React 中如果绑定事件使用匿名函数有什么影响？

```react
class Demo {
  render() {
    return <button onClick={(e) => {
      alert('我点击了按钮')
    }}>
      按钮
    </button>
  }
}
```

这样的写法，因为使用的是匿名函数，所以组件**每次都会认为是一个新的 props，不会使用缓存优化**，在性能上会有一定的损耗。

------

## 简述下 React 的事件代理机制？

React 并不会把所有的处理函数直接绑定在真实的节点上。而是把所有的事件绑定到结构的最外层，使用一个统一的事件监听器，这个**事件监听器上维持了一个映射来保存所有组件内部的事件监听和处理函数**。

当组件挂载或卸载时，只是在这个统一的事件监听器上插入或删除一些对象。

当事件发生时，首先被这个统一的事件监听器处理，然后在映射里找到真正的事件处理函数并调用。

这样做的优点是解决了兼容性问题，并且简化了事件处理和回收机制（不需要手动的解绑事件，React 已经在内部处理了）。但是有些事件 React 并没有实现，比如 **window 的 resize** 事件。

------

## React 的事件代理机制和原生事件绑定混用会有什么问题？

**React 合成事件是如何工作的**

React 的事件系统沿袭了**事件委托**的思想。在 React 中，除了少数特殊的不可冒泡的事件（比如媒体类型的事件）无法被事件系统处理外，**绝大部分的事件都不会被绑定在具体的元素上，而是统一被绑定在页面的 document 上**。当事件在具体的 DOM 节点上被触发后，最终都会冒泡到 document 上，document 上所绑定的统一事件处理程序会将事件分发到具体的组件实例。在分发事件之前，React 首先会对事件进行包装，把原生 DOM 事件**包装成合成事件**。

- React 合成事件和 DOM 原生事件混用，**先执行原生事件，再去执行合成事件**
- 原生事件中使用 `e.stopPropagation()` 即会阻止原生事件冒泡，也会阻止合成事件的冒泡；但在合成事件中使用 `e.stopPropagation()`只能会阻止合成事件的冒泡却不会阻止原生事件的冒泡。

```react
import React, { useEffect, useRef } from 'react'

function Demo() {
	const dome = useRef(null)
	const wrapper = useRef(null)

	useEffect(() => {
		dome.current.addEventListener('click', clickDOMButton, false)
		wrapper.current.addEventListener('click', clickDOMWrapper, false)
	}, [])

	function clickDOMWrapper() {
		console.log('wrapper DOM event')
	}

	function clickDOMButton(e) {
		console.log('button DOM event')
		e.stopPropagation()
		/* 
			结果只打印了 button DOM event ，那就意味着在原生事件中使用 e.stopPropagation() 即会阻止原生事件冒泡，也会阻止合成事件的冒泡
		*/
	}

	function clickReactWrapper() {
		console.log('wrapper React event')
	}

	function clickReactButton(e) {
		console.log('button React event')
		// e.stopPropagation()
		/* 
			button DOM event
			wrapper DOM event
			button React event
			结论：在合成事件阻止，只阻止了合成事件的冒泡却不会影响原生事件的冒泡
		*/
	}

	return (
		<div ref={wrapper} onClick={clickReactWrapper}>
			<button ref={dome} onClick={clickReactButton}>
				按钮
			</button>
		</div>
	)
}

export default Demo
```

**我们在平时的开发中应该尽可能的避免 React 的事件代理机制和原生事件绑定混用。**

------

## React 的事件代理机制和原生事件绑定有什么区别？

- 事件传播与阻止事件的传播： React 的**合成事件并没有实现事件捕获，只支持了事件冒泡**。阻止事件传播 React 做了兼容性处理，只需要 stopPropagation() 即可，原生存在兼容性问题。
- 事件类型：React 是 原生事件类型 的一个子集（React 只是实现了 DOM level3 的事件接口，有些事件 React 并没有实现，比如 window 的 resize 事件。）**阻止 React 事件冒泡的行为只能用于 React 合成事件系统，但是在原生事件中的阻止冒泡行为，却可以阻止 React 合成事件的传播。**
- 事件的绑定方式：原生事件系统中支持多种不同的绑定事件的方式，React 中只有一种
- 事件对象：原生中存在 IE 的兼容性问题，React 做了兼容处理。

------

## React中为什么要给组件设置 key？

+ 在开发过程中，我们需要保证某个元素的 key 在其**同级元素中具有唯一性**。

+ 在 React Diff 算法中React 会借助元素的 Key 值来**判断该元素是新创建的还是被移动**而来的元素，从而减少不必要的元素重新渲染。

+ 此外，React 还需要借助 Key 值来**判断元素与本地状态的关联**

------

##  React 中的 ref 有什么用？

+ 如果把 ref 放到 React 组件中，那么我们获得的就是**组件的实例**，因此就可以调用实例的方法。
+ 我们就可以通过 ref 得到 DOM 节点，例如设计到动画，或者媒体播放器，input聚焦等

在16.3之前，尽量使用**callback ref**来创建ref

```react
 class MyComponent extends React.Component {
   constructor(props) {
     super(props);
     this.setMyRef = null;
   }
   render() {
     return < div ref={ (el) => this.setMyRef = el } />;
   }
 }
```

在`React`的16.3及以后的版本中，可以在实例的构造函数中使用`React.createRef()`方法来创建`ref`

```react
 class MyComponent extends React.Component {
   constructor(props) {
     super(props);
     this.myRef = React.createRef();
   }
   render() {
     return <div ref={this.myRef} />;
   }
 }
```

**ref 无法用于无状态组件**，无状态组件挂载时只是方法调用，没有新建实例。在 v16 之后，函数组件可以使用 **useRef**来获取实例。

------

## dangerouslySetInnerHTML

dangerouslySetInnerHTML属性，你可以在 React 应用程序中的 HTML 元素上使用，以编程方式设置其内容。你可以直接在元素上使用这个属性，而不是使用选择器来抓取HTML元素，然后设置其`innerHTML` 。

当使用`dangerouslySetInnerHTML` ，React也知道该特定元素的内容是动态的，对于该节点的子节点，它只是跳过与虚拟DOM的比较，以获得一些额外的性能。

正如该属性的名称所暗示的，使用它可能是危险的，因为它使你的代码**容易受到跨站脚本（XSS）攻击**。特别是当你从第三方来源获取数据或渲染用户提交的内容时，这就成为一个问题。

**何时使用`dangerouslySetInnerHTML`**

你需要设置DOM元素的HTML内容的一个用例是当你用来自**富文本编辑器**的数据填充一个`<div>` 。想象一下，你有一个网页，人们可以提交评论，你允许他们使用一个富文本编辑器。在这种情况下，富文本编辑器的输出很可能是带有标签的HTML，如`<p>`,`<b>`, 和`<img>`

```react
const App = () => {
  const data = 'lorem <b>ipsum</b>';

  return (
    <div
      dangerouslySetInnerHTML={{__html: data}}
    />
  );
}

export default App;
```

请注意，它应该是一个带有传**递给`__html` 键的对象**`dangerouslySetInnerHTML` 。除此之外，你使用`dangerouslySetInnerHTML` 属性的元素不应该有任何孩子，因此要使用**`<div>` 元素作为自闭标签**。

传递对象的要求只是另一种**保障措施**，以防止开发者在没有阅读文档和意识到潜在危险的情况下使用它。

## dompurify的HTML净化工具

一个JavaScript事件被附加到一个HTML元素上,将表明一个HTML元素如何被利用来运行恶意脚本的例子。

```react
const App = () => {
  const data = `lorem <b onmouseover="alert('mouseover');">ipsum</b>`;

  return (
    <div
      dangerouslySetInnerHTML={{__html: data}}
    />
  );
}

export default App;

const App = () => {
  const data = `lorem ipsum <img src="" onerror="alert('message');" />`;

  return (
    <div
      dangerouslySetInnerHTML={{__html: data}}
    />
  );
}

export default App;
```

幸运的是，有针对HTML的净化工具**dompurify**，可以检测出HTML代码中潜在的恶意部分，然后输出一个干净安全的版本

即使在我们信任数据来源的情况下，使用消毒剂也是很好的做法。在使用DOMPurify包的情况下，上面的一个例子会是这样的。

```react
import DOMPurify from 'dompurify'

const App = () => {
  const data = `lorem <b onmouseover="alert('mouseover');">ipsum</b>`
  const sanitizedData = () => ({
    __html: DOMPurify.sanitize(data)
  })

  return (
    <div
      dangerouslySetInnerHTML={sanitizedData()}
    />
  );
}

export default App;
```

`sanitizedData` 函数返回一个带有`__html` 键的对象，它有一个从**DOMPurify.sanitize** 函数返回的值。

正如预期的那样，当我们将鼠标悬停在粗体字上时，并没有执行警报函数。

`dangerouslySetInnerHTML` 只不过是React中`innerHTML` 的替代品，应该谨慎使用。虽然这个名字暗示了使用它的危险性，但通过使用一个完善的净化器采取必要的措施，确保代码是干净的，在React节点内呈现时不会运行意外的脚本。

------

## React Hooks带来了什么便利？

在没有 hooks 之前，我们使用函数定义的组件中，不能使用 React 的 state、各种生命周期钩子类组件的特性。在 React 16.8 之后，推出了新功能： Hooks，通过 hooks 我们可以再函数定义的组件中使用类组件的特性。

好处:

- 跨组件复用: 其实 render props / HOC 也是为了复用，相比于它们，Hooks 作为官方的底层 API，最为**轻量**，而且改造成本小，不会影响原来的组件层次结构和传说中的**嵌套地狱**；

- 相比而言，类组件的实现更为复杂
  
  > - 不同的生命周期会使逻辑变得分散且混乱，不易维护和管理；
  > - 时刻需要关注this的指向问题；
  > - 代码复用代价高，高阶组件的使用经常会使整个组件树变得臃肿；
  
- 状态与 UI 隔离: 正是由于 Hooks 的特性，状态逻辑会变成更小的粒度，并且极容易被抽象成一个自定义 Hooks，组件中的状态和 UI 变得更为清晰和隔离。

**注意**:

- 避免在 **循环/条件判断/嵌套函数** 中调用 hooks，保证调用顺序的稳定；
- 不能在useEffect中使用useState，React 会报错提示；
- 类组件不会被替换或废弃，不需要强制改造类组件，两种方式能并存

------

## 列举几个常见的 Hook?

- 状态钩子 (useState): 用于定义组件的 State，类似类定义中 this.state 的功能
- 生命周期钩子 (useEffect): 类定义中有许多生命周期函数，而在 React Hooks 中也提供了一个相应的函数 (useEffect)，这里可以看做componentDidMount、componentDidUpdate和componentWillUnmount的结合。
- useCallback: 缓存回调函数，**避免传入的回调每次都是新的函数实例**而导致依赖组件重新渲染，具有性能优化的效果；
- useMemo: 用于缓存传入的 props，避免依赖的组件每次都重新渲染；
- useRef: 获取组件的真实节点；
- useContext: 获取 context 对象

------

## Hooks当中的useEffect是如何区分生命周期钩子的

useEffect可以看成是 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 三者的结合。

useEffect(callback, [source])接收两个参数，调用方式如下：

```react
useEffect(() => {
   console.log('mounted');
   
   return () => {
       console.log('willUnmount');
   }
 }, [source]);
```

生命周期函数的调用主要是通过第二个参数`[source]`来进行控制，有如下几种情况：

- [source]参数不传时，则每次都会优**先调用上次保存**的函数中返回的那个函数，然后再调用外部那个函数；

  > 初始化，还是先调用外部那个函数

- [source]参数传[]时，则外部的函数只会在初始化时调用一次，返回的那个函数也只会最终在组件卸载时调用一次；

- [source]参数有值时，则只会监听到数组中的**值发生变化后才优先调用返回的那个函数**，再调用外部的函数。

  > 初始化，还是先调用外部那个函数

## 说说你对自定义hook的理解

通过自定义 Hook，可以将**组件逻辑提取到可重用的函数**中。所谓的自定义Hook，实际上就是把很多重复的逻辑都放在一个函数里面，通过**闭包的方式给`return`出来**

> 单个值可以直接return、两个值就return数组、三个值及以上就return对象

+ 使用setTimeout自定义hook倒计时

  ```react
  const useCountDown = (initCount: number) => {
  	const [count, setCount] = useState(initCount)
  
  	useEffect(() => {
  		const timer = setTimeout(() => {
  			setCount(count - 1)
  		}, 1000)
  		if (count === 0) {
  			clearTimeout(timer)
  		}
  		// 每次清楚上一次开启的定时器
  		return () => {
  			clearTimeout(timer)
  		}
  	}, [count])
    
  	return count
  }
  ```

+ 使用setInterval自定义hook倒计时

  ```react
  function _useCountDown(initN: number) {
  	const timer = useRef(null)
  	const [count, setCount] = useState(initN)
  
  	useEffect(() => {
  		timer.current = setInterval(() => {
  			setCount(count => count - 1)
  		}, 1000)
  
  		return () => {
  			clearInterval(timer.current)
  		}
  	}, [])
  
  	useEffect(() => {
  		if (count === 0) {
  			clearInterval(timer.current)
  		}
  	}, [count])
  
  	return count
  }
  ```

------

## React中的路由懒加载是什么？

**React.lazy**

在实际的使用中，首先是引入组件方式的变化：

```react
// 不使用 React.lazy
// import OtherComponent from './OtherComponent';
// 使用 React.lazy
const OtherComponent = React.lazy(() => import('./OtherComponent'))

const App: React.FC = () => {
    return (
      		<Suspense fallback={<div>...</div>}>
        			<Switch>
  					   		<Route path="/home" component={OtherComponent} />
              <Switch>
           </Suspense>
    )
}
```

**React.lazy 接受一个函数作为参数，这个函数需要调用 import() 。它需要返回一个 Promise，该 Promise 需要 resolve 一个 export  defalut 的 React 组件。**

React.lazy 方法返回的是一个 lazy 组件的对象，类型是 react.lazy，并且 lazy 组件具有 _status 属性，与 Promise 类似它具有 Pending、Resolved、Rejected 三个状态，分别代表组件的加载中、已加载、和加载失败三中状态。

需要注意的一点是，React.lazy 需要配合 **Suspense** 组件一起使用，在 Suspense 组件中渲染 React.lazy 异步加载的组件。如果单独使用 React.lazy，React 会给出错误提示。

**Suspense 组件**

Suspense 内部主要通过**捕获组件的状态去判断如何加载**，上面我们提到 React.lazy 创建的动态加载组件具有 Pending、Resolved、Rejected 三种状态，当这个组件的状态为 Pending 时显示的是 Suspense 中 fallback 的内容，只有状态变为 resolve 后才显示组件。

React.lazy() 和 React.Suspense 的提出为现代 React 应用的性能优化和工程化提供了便捷之路。 React.lazy 可以让我们像渲染常规组件一样**处理动态引入的组件**，结合 Suspense 可以更优雅地展现组件懒加载的过渡动画以及处理加载异常的场景。

## 在 React 中可以做哪些性能优化？

#### 1. 使用React.PureComponent , shouldComponentUpdate

父组件状态的每次更新，都会导致子组件的重新渲染，即使是传入相同props。但是这里的重新渲染不是说会更新DOM,而是每次都会调用diif算法来判断是否需要更新DOM。这对于大型组件例如组件树来说是非常消耗性能的。
在这里我们就可以使用React.PureComponent , shouldComponentUpdate生命周期来确保只有当组件props状态改变时才会重新渲染

```react
export default function ParentComponent(props) {
  return (
    <div>
      <SomeComponent someProp={props.somePropValue}
    <div>
      <AnotherComponent someOtherProp={props.someOtherPropValue} />
    </div>
   </div>
 )
}

export default function SomeComponent(props) {
  return (
    <div>{props.someProp}</div>  
  )
}

// 只要props.somePropValue 发生变化，不论props.someOtherPropValue是否发生变化该组件都会发生变化
export default function AnotherComponent(props) {
  return (
    <div>{props.someOtherProp}</div>  
  )
}
```

我们可以使用React.PureComponent 或shouldComponentUpdate 进行如下优化：

```react
// 第一种优化
class AnotherComponent extends React.PureComponent {
  render() {
    return <div>{this.props.someOtherProp}</div>   
  }
}

//第二种优化
class AnotherComponent extends Component {
  shouldComponentUpdate(nextProps) {
    return this.props !== nextProps
  }
  render() {
    return <div>{this.props.someOtherProp}</div>   
  }
}
```

PureComponent会进行浅比较来判断组件是否应该重新渲染，对于传入的基本类型props，只要值相同，浅比较就会认为相同，对于传入的引用类型props，浅比较只会认为传入的props是不是同一个引用，如果不是，哪怕这两个对象中的内容完全一样，也会被认为是不同的props。
需要注意的是在对于那些可以忽略渲染时间的组件或者是状态一直变化的组件则要谨慎使用PureComponent，因为进行浅比较也会花费时间，这种优化更适用于大型的展示组件上。大型组件也可以拆分成多个小组件，并使用memo来包裹小组件，也可以提升性能。

#### 2. 使用React.Memo来缓存组件

提升应用程序性能的一种方法是实现memoization。Memoization是一种优化技术，主要通过存储昂贵的函数调用的结果，并在再次发生相同的输入时返回缓存的结果，以此来加速程序。
**父组件的每次状态更新，都会导致子组件重新渲染，即使传入子组件的状态没有变化，为了减少重复渲染，我们可以使用React.memo来缓存组件，这样只有当传入组件的状态值发生变化时才会重新渲染**。如果传入相同的值，则返回缓存的组件。示例如下：

```javascript
export default React.memo((props) => {
  return (
    <div>{props.value}</div>  
  )
});
```

#### 3. 使用useMemo缓存大量的计算

有时渲染是不可避免的，但如果您的组件是一个功能组件，重新渲染会导致每次都调用大型计算函数，这是非常消耗性能的，我们可以使用新的useMemo钩子来“记忆”这个计算函数的计算结果。这样**只有传入的参数发生变化后，该计算函数才会重新调用计算新的结果**。
通过这种方式，您可以使用从先前渲染计算的结果来挽救昂贵的计算耗时。总体目标是减少JavaScript在呈现组件期间必须执行的工作量，以便主线程被阻塞的时间更短。

```javascript
// 避免这样做
function Component(props) {
  const someProp = heavyCalculation(props.item);
  return <AnotherComponent someProp={someProp} /> 
}
  
// 只有 `props.item` 改变时someProp的值才会被重新计算
function Component(props) {
  const someProp = useMemo(() => heavyCalculation(props.item), [props.item]);
  return <AnotherComponent someProp={someProp} /> 
}
```

#### 4. 避免使用内联对象

+ 使用内联对象时，**react会在每次渲染时重新创建对此对象的引用**，这会导致接收此对象的组件将其视为不同的对象,因此，该组件对于prop的浅层比较始终返回false,导致组件一直重新渲染。
  许多人使用的内联样式的间接引用，就会使组件重新渲染，可能会导致性能问题。为了解决这个问题，我们可以保证该对象只初始化一次，指向相同引用。

  > 内联CSS样式，可以将样式赋值给对象，并将对象定义在组件之外

+ 另外一种情况是**传递一个对象，同样会在渲染时创建不同的引用**，也有可能导致性能问题，我们可以利用**ES6扩展运算符将传递的对象解构**。这样组件接收到的便是基本类型的props，组件通过浅层比较发现接受的prop没有变化，则不会重新渲染。示例如下：

```javascript
// Don't do this!
function Component(props) {
  const aProp = { someProp: 'someValue' }
  return <AnotherComponent style={{ margin: 0 }} aProp={aProp} />  
}

// Do this instead :)
const styles = { margin: 0 };
function Component(props) {
  const aProp = { someProp: 'someValue' }
  return <AnotherComponent style={styles} {...aProp} />  
}
```

#### 5. 避免使用匿名函数

虽然匿名函数是传递函数的好方法（特别是需要用另一个prop作为参数调用的函数），但它们在每次渲染上都有不同的引用。这类似于上面描述的内联对象。**为了保持对作为prop传递给React组件的函数的相同引用，您可以将其声明为类方法（如果您使用的是基于类的组件）或使用useCallback钩子来帮助您保持相同的引用（如果您使用功能组件**）。
当然，有时内联匿名函数是最简单的方法，实际上并不会导致应用程序出现性能问题。这可能是因为在一个非常“轻量级”的组件上使用它，或者因为父组件实际上必须在每次props更改时重新渲染其所有内容。因此不用关心该函数是否是不同的引用，因为无论如何，组件都会重新渲染。

```javascript
// 避免这样做
function Component(props) {
  return <AnotherComponent onChange={() => props.callback(props.id)} />  
}

// 优化方法一，函数组件使用useCallback
function Component(props) {
  const handleChange = useCallback(() => props.callback(props.id), [props.id]);
  return <AnotherComponent onChange={handleChange} />  
}

// 优化方法二，累组件声明为类方法
class Component extends React.Component {
  handleChange = () => {
   this.props.callback(this.props.id) 
  }
  render() {
    return <AnotherComponent onChange={this.handleChange} />
  }
}
```

#### 6. 延迟加载不是立即需要的组件

React.Lazy和React.Suspense

延迟加载实际上不可见（或不是立即需要）的组件，React加载的组件越少，加载组件的速度就越快。因此，如果您的初始渲染感觉相当粗糙，则可以在初始安装完成后通过在需要时加载组件来减少加载的组件数量。同时，这将允许用户更快地加载您的平台/应用程序。最后，通过拆分初始渲染，您将JS工作负载拆分为较小的任务，这将为您的页面提供响应的时间。这可以使用新的React.Lazy和React.Suspense轻松完成。

```javascript
// 延迟加载不是立即需要的组件
const MUITooltip = React.lazy(() => import('@material-ui/core/Tooltip'));

function Tooltip({ children, title }) {
  return (
    <React.Suspense fallback={children}>
      <MUITooltip title={title}>
        {children}
      </MUITooltip>
    </React.Suspense>
  );
}

function Component(props) {
  return (
    <Tooltip title={props.title}>
      <AnotherComponent />
    </Tooltip>
  )
}
```

#### 7. 调整CSS而不是强制组件加载和卸载

渲染成本很高，尤其是在需要更改DOM时。每当你有某种手风琴或标签功能，例如想要一次只能看到一个项目时，你可能想要卸载不可见的组件，并在它变得可见时将其重新加载。如果加载/卸载的组件“很重”，则此操作可能非常消耗性能并可能导致延迟。在这些情况下，最好通过CSS隐藏它，同时将内容保存到DOM。
尽管这种方法并不是万能的，因为安装这些组件可能会导致问题（即组件与窗口上的无限分页竞争），但我们应该选择在不是这种情况下使用调整CSS的方法。另外一点，将不透明度调整为0对浏览器的成本消耗几乎为0（因为它不会导致重排），并且应尽可能优先于更该visibility 和 display。
**有时在保持组件加载的同时通过CSS隐藏可能是有益的，而不是通过卸载来隐藏。对于具有显著的加载/卸载时序的重型组件而言，这是有效的性能优化手段**。

> 就是利用三目运算符，通过opacity或visibility来控制渲染，而不是display:none

```react
// 避免对大型的组件频繁对加载和卸载
function Component(props) {
  const [view, setView] = useState('view1');
  return view === 'view1' ? <SomeComponent /> : <AnotherComponent />  
}

// 使用该方式提升性能和速度
const visibleStyles = { opacity: 1 };
const hiddenStyles = { opacity: 0 };
function Component(props) {
  const [view, setView] = useState('view1');
  return (
    <React.Fragment>
      <SomeComponent style={view === 'view1' ? visibleStyles : hiddenStyles}>
      <AnotherComponent style={view !== 'view1' ? visibleStyles : hiddenStyles}>
    </React.Fragment>
  )
}
```

#### 8. 使用React.Fragment避免添加额外的DOM

在react规定组件中必须有一个父元素。为了避免创建额外的不必要的div，减少不必要的加载时间，我们可以使React.Fragment来避免创建不必要的元素。

```react
function Component() {
        return (
            <React.Fragment>
                <h1>Hello world!</h1>
                <h1>Hello there!</h1>
                <h1>Hello there again!</h1>
            </React.Fragment>
        )
}
```

#### 9. 给子组件设置一个唯一的 key

因为在 diff 算法中，会用 key 作为唯一标识优化渲染

