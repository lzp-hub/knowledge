### Redux

### 三项基本原则

1. 单一数据源：所有状态都保存在单一的store中

2. state是只读的：不能直接对state进行修改，只能用新的state替换旧的state

3. Reducer必须是一个纯函数

   > 纯函数指的是，给固定的输入，就一定会有固定的输出，而且不会有任何副作用

**Redux使用场景**

* 某个组件的状态，需要共享
* 某个状态需要在任何地方都可以拿到
* 在一个组件中需要改变全局状态
* 在一个组件中需要改变另一个组件的状态

例子

```js
import { createStore } from 'redux'
// 自定义其形参stata的默认值
const initState = {
    currentUser:{},
    isLogin=false,
}
// 第二步：定义reducer(作用是初始化数据并指定修改方法)
const reducer = function (state = initState, action) {
    // 形参state：状态(共享的数据)，一般会设定默认值
    // 形参action:指定数据的修改方式,一般是一个对象：{type:'',payload}
    switch (action.type) {
        case 'login':
        		// 每次返回的都是一个全新的state对象
            return {
                ...state,
                currentUser: action.user,
                isLogin: true,
            };
        // store.dispatch({type:'logout'})
        case 'logout':
            return initState;
        default:
            return state;
    }
}

// 第一步，创建store；redux.createStore,需要传入reducer
const store = createStore(reducer);

export default store

/*
		在组件件就可以引入store并使用其方法
    * store.getState() 获取仓库最新状态
    * store.dispatch(action) 修改数据唯一方式，会触发reducer函数
    * store.subscribe(fn) 监听数据修改,fn是一个函数；state数据更新，会触发
*/
```

1. 数据存储仓库：Store

   > store是Redux存储区（一个保存数据的地方），你可以把它看成一个仓库，**规定一个应用只能有一个Store**，store中的数据可以被直接访问，但只能通过提供的reducer进行更新。
   >
   > ```js
   >     import { createStore } from 'redux';
   > 
   >     const reducer = function (state = initState, action) {
   >    				return state
   >     }
   >     const store = createStore(reducer);
   > 
   >  export default store;
   > ```
   >
   
2. 状态初始化与更新逻辑：reducer函数

   > Reducer 必须是一个**纯函数**，用于指定state修改逻辑，**它接受当前 state 和 action 作为参数，并根据state和action的值返回新的state**
   >
   > + 第一个参数是state
   > + 第二个参数是action, `{type,payload}`,此action就是store.dispatch(action)里的action
   >
   > ```js
   > const initState = {
   >     currentUser:{},
   >     isLogin=false,
   > }
   > // 第二步：定义reducer(作用是初始化数据并指定修改方法)
   > const reducer = function (state = initState, action) {
   >     // 形参state：状态(共享的数据)，一般会设定默认值
   >     // 形参action:指定数据的修改方式,一般是一个对象：{type:'',payload}
   >     switch (action.type) {
   >         case 'login':
   >         		// 每次返回的都是一个全新的state对象
   >             return {
   >                 ...state,
   >                 currentUser: action.user,
   >                 isLogin: true,
   >             };
   >         // store.dispatch({type:'logout'})
   >         case 'logout':
   >             return initState;
   >         default:
   >             return state;
   >     }
   > }
   > ```

3. 在组件中获取state，获取仓库最新状态

   ```js
    store.getState();
   ```

4. 在组件中修改数据，修改数据唯一方式。调用dispatch()方法，其内部会调用reducer()方法

   ```js
    store.dispatch({type:'login',user:lzp});
   ```

5. store.subscribe(fn) 监听数据修改；fn是一个函数；state数据更新，会触发

   ```react
       componentDidMount(){
           store.subscribe(()=>{
               console.log('statechange=',store.getState())
             	// 为了实现刷新而另组件state改变
               // this.setState({
               //     isLogin:store.getState().isLogin
               // })
           });
       }
   ```

   在react中使用redux的原生：在react里面我们可以在componentDidMount生命周期里定义store.subscribe(fn)；在fn里面可以使用store.getState()获取数据,再让setState()修改当前组件state实现**刷新**；非常麻烦

### react-redux 桥接工具

为了方便使用，Redux 的作者封装了一个 React 专用的库 React-Redux，它提供了一些接口，用于Redux的状态和React的组件展示结合起来，以用于实现状态与视图的一一对应

+ HOC 与 context 通信在 react-redux 底层中的原理

  > `<Provider/>`：利用**Context**共享redux的数据
  > `connect()`：使用**高阶组件**把数据作为props传入React组件

#### `<Provider/>` 容器组件

* React-Redux 提供Provider组件，接受Redux的store作为props
* 可结合`connect`方法可以让容器组件方便获取state和dispatch

```react
// react-redux第一步：利用Provider共享store
import { Provider } from 'react-redux';
import store from '@/store';

ReactDOM.render(
    <Provider store={store}>
        <App/>
    </Provider>,
    document.getElementById('app')
)
```

#### `connect()`高阶组件

connect( )方法为React-Redux核心方法，它把react组件与redux中的Store真正连接在一起，connect方法接受两个参数(Function类型)，用于定义了 UI 组件的业务逻辑，返回一个高阶组件

* 格式：`connect([mapStateToProps],[mapDispatchToProps])(某组件)`

  > * 第一个参数：mapStateToProps
  >   * 负责组件的输入逻辑，即将**state**映射到 UI 组件的参数(props)
  >   * 必须返回一个对象
  >
  > * 第二个参数：mapDispatchToProps
  >   * 负责组件的输出逻辑，即将用户对 UI 组件的操作映射成 Action
  >   * 必须返回一个对象
  >   * 不写mapDispatchToProps参数，默认在props里添加了dispatch方法
  >
  > ```react
  > import { connect } from 'react-redux'
  > function mapStateToProps(state,ownprops) {
  >     //state：redux中的state
  >     //ownprops: Cart组件自身的props
  >     return {
  >       //将state中购物车页面的goodsinf数据映射到props，Cart组件中通过props.data访问
  >       data: state.shoppingCart.goodsinf
  >     }
  > }
  > function mapDispatchToProps(dispatch,ownprops) {
  >     // dispatch: redux中的dispatch方法
  >     // ownprops：Cart组件自身的props
  >     return {
  >       dispatch,//书写了mapDispatchToProps，需要手动添加回dispatch
  >       logout() {
  >         	// 自定义dispatch方法，Cart组件中通过props.logout访问
  >           dispatch({ type: 'logout' })
  >       }
  >     }
  > }
  > // 类组件可使用装饰器
  > // @connect(mapStateToProps,mapDispatchToProps)
  > 
  > // 调用connect()，其返回值是高阶组件
  > // 当前组件包装了connect()，不写第二个参数默认在组件props里添加了dispatch方法;
  > // 当connect(a,b)添加了第二个参数；需要手动添加dispatch方法；
  > Cart = connect(mapStateToProps,mapDispatchToProps)(Cart);
  > export default App
  > ```
  >
  

### combineReducers实现redux的模块化

复杂的应用需要使用多个Reducer来实现业务逻辑，把reducer设置成单独模块，并使用**combineReducers**合并成一个大的Reducer后使用

```js
    import { createStore,combineReducers } from "redux";

    import productsReducer from './productsReducer';
    import cartReducer from './cartReducer';
    //合并Reducer
    const allReducers = {
        products: productsReducer,
        cart: cartReducer
    }
    const rootReducer = combineReducers(allReducers);

    // 合并模块化之后，每个模块里面的数据，获取的时候会多出了一层模块名
    // 例如：props.state.goodsinf >>> props.state.cart.goodsinf
    const store = createStore(rootReducer);

		export default store;
```

### redux简单版createStore

```js
// 手动实现一个简单版的createStore封装
// 当你不会封装函数时，先调用。查看返回值。
// * store.getState() 获取仓库最新状态
// * store.dispatch(action) 修改数据唯一方式
// * store.subscribe(fn) 监听数据修改，fn是一个函数，因为state数据更新，会触发
export function createStore() {
    let state = reducer();
    const listener = []
    // 获取state
    getState = function () {
        return state;
    }
    // 设置state
    dispatch = function (action) {
        // 获取新的state
        state = reducer(state, action)
        // 执行监听state的函数
        for (let i = 0; i < listener.length; i++) {
            typeof (listener[i]) === 'function' && listener[i]()
        }
    }
    // 监听state 
    subscribe = function (fn) {
        // fn的代码在修改state时触发,放到设置state函数体内
        // 可能多个地方使用到监听,保存至数组中
        listener.push(fn);
        // 监听分返回值是取消监听
        return function unsbuscribe() {
            const idx = listener.indexOf(fn);
            // 将其从监听数组中删除即可
            listener.splice(idx, 1)
        }
    }
    // 修改reducer
    replaceReducer = function (newReducer) {
        reducer = newReducer;
    }
    return {
        getState,
        dispatch,
        subscribe,
        replaceReducer
    }
}
```

### bindActionCreators

利用redux的`bindActionCreators`把`Action Creator`中默认导出的所有方法(export default中的方法)绑定到组件props并自动**隐式调用dispatch(action)**

1. 在./userActions.js文件定义Action Creator

   ```js
   /**
    * Action Creator 定义在 ./userActions.js
    */
   function login(user){
        return {
            type:'login',
            user
        }
    }
   function logout(){
       return {
           type:'logout'
       }
   }
    export default {
        login,
        logout
    }
   ```

2. bindActionCreators默认导出的所有方法(export default中的方法)绑定到组件props,并自动隐式调用dispatch(action)

   ```js
   import { bindActionCreators } from 'redux';
   import { connect } from 'react-redux';
   import userActions from './userActions';//自定义封装的Action Creator
   //...
   const mapDispatchToProps = dispatch =>{
   		/*  return {
           dispatch,
           login(user){
               dispatch(userAction.login(user))
           },
           logout(){
               dispatch(userAction.logout())
           }
       } */
     	// bindActionCreators默认导出的所有方法(export default中的方法)绑定到组件props
       // 并自动隐式调用dispatch(action),如上面代码
       return bindActionCreators(userAction,dispatch)
   }
   
   MyComponent = connect(state=>state, mapDispatchToProps)(MyComponent)
   export default MyComponent;
   ```

### Generator与Iterator

+ 生成器函数Generator

  > 1. 执行生成器函数并不会执行函数中的代码，而是返回一个**迭代器**
  >
  > 2. 定义函数时添加星号: *
  >
  > 3. 在生成器函数中,yield暂停并返回，可以有多个;return结束并返回，只有一个
  >
  >    ```js
  >    function * create(){
  >        console.log('start')//调用 create()并不会执行。
  >        yield 10;
  >        console.log(1)
  >        yield 50;   // 暂停并返回
  >        console.log(2)
  >        return 100; //结束并返回
  >        console.log(3)
  >    }
  >    p = create()
  >    p.next()//输出start,返回值{value: 10, done: false} 
  >    p.next()//输出1，返回值{value: 50, done: false}
  >    p.next()//输出2，返回值{value: 100, done: true}
  >    p.next()//代码不会执行了，直接返回 {value: undefined, done: true}
  >    ```

+ 迭代器Iterator

  > 内部包含多个值的对象，要获取迭代器中的值，必须调用`next()`方法，没调用一次next就是一次迭代，next方法的返回值格式：{ value,done }
  >
  > + value：每一次迭代返回的值
  > + done: 标识迭代器是否迭代完成
  > + <suspended> 没有执行完，<closed> 执行完

**数组、字符串其内置身上有symbol.iterator()方法;可用for...of遍历具有迭代器的数据(不断执行迭代器的next()方法，直到迭代完成)**

### redux-saga 中间件 

**对 dispatch 改装，实现 redux 异步处理中间件**
redux中的action仅支持原始对象，处理有副作用的action(异步请求)，可以使用中间件。**中间件可以在发出action，到reducer函数接受action之间，执行具有副作用的操作**。

+ saga原理：生成器和迭代器配合
+ saga中间件会在适当的时候自动调用迭代器的next()方法
+ 使用中间件需要用到redux的**applyMiddleware**方法接受

```js
    import {createStore,applyMiddleware} from 'redux';
    // 1.引入saga
    import createSagaMiddleware from 'redux-saga';
    // 自定义的rootSaga文件和reducer文件
    import rootSaga from './middleware/rootSaga.js';
    import reducer from './reducers'

    // 2.创建saga中间件
    const sagaMiddleware = createSagaMiddleware();

    // 3.将saga中间件sagaMiddleware 连接至 Store
    const store  = createStore(reducer,applyMiddleware(sagaMiddleware));

    // 4.saga中间件引入并运行自定义Saga配置
    sagaMiddleware.run(rootSaga);
```

rootSaga.js文件里自定义saga action

+ takeEvery 是普通执行异步action的方法
+ takeLatest也是执行异步action的方法，但是其内部做了防抖处理(最后一次生效)
+ put 就是 dispatch
+ call、apply等效于原生js中的call、apply，主要是为了方便测试（单元测试，点对点测试）

```js
import request from '@/utils/request'//封装的axios请求
import { call, apply, put, takeEvery, takeLatest } from 'redux-saga/effects';

//  自定义生成器函数管理saga action，中间件会在适当的时候自动调用迭代器的next()方法
function* initial() {
    // 监听异步action：saga action
    yield takeEvery('login_async',login);   // 可同时执行多个action
    // yield takeLatest('login_async', login); // takeLatest做了防抖优化，只生效最后一次
    // ···全部的saga action定义在这里
}

function* login(sagaAction) {
    // 在组件里调用dispatch({type:'login_async',data:{username,password,remember}})
    // 形参sagaAction就接收dispatch传递的整个action对象：{type: "login_async", data: {…}} */
    const { data } = yield request.get('/user/login', {
        params: sagaAction.data
    })
    
    // call方法实现
    // const {data} = yield call(request.get,'/user/login',{    
    //     params:sagaAction.data
    // })
		
    // 等待异步请求结果返回后自动执行next()方法，调用同步reducer action
    const action = { type: 'login', user: data.data }
    yield put(action)// 这里 put 就是 dispatch
}
export default initial;
```

* 调用

  > 即使对dispatch进行了改装，调用方式还是那个；
  > this.props.dispatch({ type: 'login_async', data: { username, password } })

* 实现流程

  > 1. 组件里发起异步action：dispatch({type:'login_async',data:{username,password}})
  > 2. 生成器监听器匹配到异步action，通过**takeEvery()**或**takeLatest()**发送异步请求方法。
  > 3. 待请求响应数据回来，会自动调用**next()**方法，来执行**put()**方法或者**dispatch()**方法



# 面试题

## 说说你对Redux的理解？其工作原理？

**Redux是什么？**

`React`是用于构建用户界面的，帮助我们解决渲染`DOM`的过程

而在整个应用中会存在很多个组件，每个组件的`state`是由自身进行管理，包括组件定义自身的`state`、组件之间的通信通过`props`传递、使用`Context`实现数据共享

如果让每个组件都存储自身相关的状态，理论上来讲不会影响应用的运行，但在开发及后续维护阶段，我们将花费大量精力去查询状态的变化过程

这种情况下，如果**将所有的状态进行集中管理，当需要更新状态的时候，仅需要对这个管理集中处理，而不用去关心状态是如何分发到每一个组件内部的**

`redux`就是一个实现上述集中管理的容器，遵循三大基本原则：

- **单一数据源**
- **state 是只读的**
- **使用纯函数来执行修改**

注意的是，`redux`并不是只应用在`react`中，还与其他界面库一起使用，如`Vue`

**工作原理**

`redux `要求我们把数据都放在 `store `公共存储空间

一个组件改变了 `store` 里的数据内容，其他组件就能感知到 `store `的变化，再来取数据，从而间接的实现了这些数据传递的功能

工作流程图如下所示：

![](https://static.vue-js.com/27b2e930-e56b-11eb-85f6-6fac77c0c9b3.png)

**如何使用**

创建一个`store`的公共数据区域

```js
import { createStore } from 'redux' // 引入一个第三方的方法
const store = createStore() // 创建数据的公共存储区域（管理员）
```

还需要创建一个记录本去辅助管理数据，也就是`reduecer`，本质就是一个函数，接收两个参数`state`，`action`，返回`state`

```js
// 设置默认值
const initialState = {
  counter: 0
}

const reducer = (state = initialState, action) => {
}
```

然后就可以将记录本传递给`store`，两者建立连接。如下：

```js
const store = createStore(reducer)
```

如果想要获取`store`里面的数据，则通过`store.getState()`来获取当前`state`

```js
console.log(store.getState());
```

下面再看看如何更改`store`里面数据，是通过`dispatch`来派发`action`，通常`action`中都会有`type`属性，也可以携带其他的数据

```js
store.dispatch({
  type: "INCREMENT"
})

store.dispath({
  type: "DECREMENT"
})

store.dispatch({
  type: "ADD_NUMBER",
  number: 5
})
```

下面再来看看修改`reducer`中的处理逻辑：

```js
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case "INCREMENT":
      return {...state, counter: state.counter + 1};
    case "DECREMENT":
      return {...state, counter: state.counter - 1};
    case "ADD_NUMBER":
      return {...state, counter: state.counter + action.number}
    default: 
      return state;
  }
}
```

注意，`reducer`是一个纯函数，不需要直接修改`state`

这样派发`action`之后，既可以通过`store.subscribe`监听`store`的变化，如下：

```js
store.subscribe(() => {
  console.log(store.getState());
})
```

在`React`项目中，会搭配`react-redux`进行使用

完整代码如下：

```js
const redux = require('redux');

const initialState = {
  counter: 0
}

// 创建reducer
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case "INCREMENT":
      return {...state, counter: state.counter + 1};
    case "DECREMENT":
      return {...state, counter: state.counter - 1};
    case "ADD_NUMBER":
      return {...state, counter: state.counter + action.number}
    default: 
      return state;
  }
}

// 根据reducer创建store
const store = redux.createStore(reducer);

store.subscribe(() => {
  console.log(store.getState());
})

// 修改store中的state
store.dispatch({
  type: "INCREMENT"
})
// console.log(store.getState());

store.dispatch({
  type: "DECREMENT"
})
// console.log(store.getState());

store.dispatch({
  type: "ADD_NUMBER",
  number: 5
})
// console.log(store.getState());
```

## 说说对Redux中间件的理解？常用的中间件有哪些？实现原理？

**中间件（Middleware）**是介于应用系统和系统软件之间的一类软件，它使用系统软件所提供的基础服务（功能），衔接网络上应用系统的各个部分或不同的应用，能够达到资源共享、功能共享的目的。

了解到了`Redux`整个工作流程，当`action`发出之后，`reducer`立即算出`state`，整个过程是一个同步的操作；那么如果需要支持异步操作，或者支持错误处理、日志监控，这个过程就可以用上中间件

`Redux`中，中间件就是放在就是在`dispatch`过程，在分发`action`进行拦截处理，其本质上一个函数，对**`store.dispatch`方法进行了改造，在发出 `Action `和执行 `Reducer `这两步之间，添加了其他功能**

**常用的中间件**

有很多优秀的`redux`中间件，如：

- redux-thunk：用于异步操作
- redux-saga：用于异步操作
- redux-logger：用于日志记录

上述的中间件都需要通过`applyMiddlewares`进行注册，作用是将所有的中间件组成一个数组，依次执行

然后作为第二个参数传入到`createStore`中

```js
import {createStore,applyMiddleware} from 'redux';
const store = createStore(
  reducer,
  applyMiddleware(thunk, logger)
);
```

## 你在React项目中是如何使用Redux的? 项目结构是如何划分的？

`redux`是用于数据状态管理，而`react`是一个视图层面的库；如果将两者连接在一起，可以使用官方推荐`react-redux`库，其具有高效且灵活的特性

使用`react-redux`分成了两大核心：

- Provider
- connection

**Provider**

在`redux`中存在一个`store`用于存储`state`，如果将这个`store`存放在顶层元素中，其他组件都被包裹在顶层元素之上

那么所有的组件都能够受到`redux`的控制，都能够获取到`redux`中的数据

使用方式如下：

```react
<Provider store = {store}>
    <App />
<Provider>
```

**connection**

connect`方法将`store`上的`getState `和 `dispatch `包装成组件的`props

可以传递两个参数：

- mapStateToProps
- mapDispatchToProps

```js
import { connect } from "react-redux";

connect(mapStateToProps, mapDispatchToProps)(MyComponent)
```

**mapStateToProps**

把`redux`中的数据映射到`react`中的`props`中去，组件内部就能够通过`props`获取到`store`中的数据

```react
class Foo extends Component {
    constructor(props){
        super(props);
    }
    render(){
        return(
         // 这样子渲染的其实就是state.bar的数据了
            <div>this.props.foo</div>
        )
    }
}

const mapStateToProps = (state) => {
    return {
        // 意思是将state中的某个数据映射到props中
        foo: state.bar
    }
}

Foo = connect(mapStateToProps)(Foo)
export default Foo
```

**mapDispatchToProps**

将`redux`中的`dispatch`映射到组件内部的`props`中

```react
class Foo extends Component {
    constructor(props){
        super(props);
    }
    render(){
        return(
         
             <button onClick = {this.props.onClick}>点击increase</button>
        )
    }
}

const mapDispatchToProps = (dispatch) => { 
  // 默认传递参数就是dispatch
  return {
    onClick: () => {
      dispatch({
        type: 'increatment'
      });
    }
  };
}
Foo = connect(state=>state,mapDispatchToProps)(Foo);
export default Foo;
```

------

## Redux中的connect有什么作用？

connect负责连接React和Redux

1. 获取state

   > connect 通过 **context获取 Provider 中的 store**，通过 store.getState() 获取整个store tree 上所有state

2. 包装原组件

   > 将state和action通过props的方式传入到原组件内部 `wrapWithConnect` 返回—个 `ReactComponent` 对象 Connect，Connect重新 render 外部传入的原组件 `WrappedComponent` ，并把 connect 中**传入的 `mapStateToProps`，`mapDispatchToProps`与组件上原有的 props 合并**后，通过属性的方式传给 `WrappedComponent`

3. 监听store tree变化

   > connect缓存了`store tree`中state的状态，通过当前state状态 和变更前 state 状态进行比较，从而**确定是否调用 `this.setState()`方法触发 Connect 及其子组件的重新渲染**

------

## Redux 中异步的请求怎么处理

一般的异步请求，可以在 `componentDidmount` 中直接进⾏请求，⽆须借助redux。

但是在⼀定规模的项⽬中,上述⽅法很难进⾏异步流的管理,通常情况下我们会借助redux的异步中间件进⾏异步处理。

redux异步流中间件其实有很多，当下主流的异步中间件有两种`redux-thunk`、`redux-saga`。

**redux-thunk优点:**

- 体积⼩: redux-thunk的实现⽅式很简单，只有不到20⾏代码
- 使⽤简单: redux-thunk没有引⼊像`redux-saga`或者`redux-observable`额外的范式，上⼿简单

**redux-thunk缺陷:**

- 样板代码过多: 与redux本身⼀样,通常⼀个请求需要⼤量的代码,⽽且很多都是重复性质的
- 耦合严重: 异步操作与redux的action偶合在⼀起,不⽅便管理
- 功能孱弱: 有⼀些实际开发中常⽤的功能需要⾃⼰进⾏封装

**redux-saga优点:**

- 异步解耦: 异步操作被被转移到单独 saga.js 中，不再是掺杂在 action.js 或 component.js 中
- action摆脱`thunk function`: dispatch 的参数依然是⼀个纯粹的 action (FSA)，⽽不是充满 “⿊魔法” thunk function
- 异常处理: 受益于 `generator function` 的 saga 实现，代码异常/请求失败 都可以直接通过 `try/catch` 语法直接捕获处理
- 功能强⼤: `redux-saga`提供了⼤量的 Saga 辅助函数和 Effect 创建器供开发者使⽤,开发者⽆须封装或者简单封装即可使⽤
- 灵活: redux-saga可以将多个Saga可以串⾏/并⾏组合起来,形成⼀个⾮常实⽤的异步flow
- 易测试，提供了各种case的测试⽅案，包括mock task，分⽀覆盖等等

**redux-saga缺陷:**

- 额外的学习成本: `redux-saga`不仅在使⽤难以理解的 `generator function`，⽽且有数⼗个API，学习成本远超redux-thunk。最重要的是你的额外学习成本是只服务于这个库的，与`redux-observable`不同，`redux-observable`虽然也有额外学习成本但是背后是rxjs和⼀整套思想
- 体积庞⼤: 体积略⼤,代码近2000⾏，min版25KB左右
- 功能过剩: 实际上并发控制等功能很难⽤到，但是我们依然需要引⼊这些代码
- ts⽀持不友好: yield⽆法返回TS类型

`redux-saga`可以捕获action，然后执行一个函数，那么可以把异步代码放在这个函数中。

## mobx 和 redux 有什么区别？

**共同点**

- 为了解决状态管理混乱、无法有效同步的问题，统一维护管理应用状态
- 某一状态只有一个可信数据来源（通常命名为store，指状态容器）
- 操作更新状态方式统一，并且可控（通常以action方式提供更新状态的途径）
- 支持将store与React组件连接，如`react-redux`，`mobx-react`

**区别**

**Redux**更多的是遵循Flux模式的一种实现，是一个 JavaScript 库，它关注点主要是以下几方面∶

- Action∶ 一个JavaScript对象，描述动作相关信息，主要包含type属性和payload属性∶

- Reducer∶ 定义应用状态如何响应不同动作（action），如何更新状态;

- Store∶ 管理action和reducer及其关系的对象，主要提供以下功能∶

  > - 维护应用状态并支持访问状态(getState());
  > - 支持监听action的分发，更新状态(dispatch(action));
  > - 支持订阅store的变更(subscribe(listener));

- 异步流∶ 由于Redux所有对store状态的变更，都应该通过action触发，异步任务（通常都是业务或获取数据任务）也不例外，而为了不将业务或数据相关的任务混入React组件中，就需要使用其他框架配合管理异步任务流程，如redux-thunk，redux-saga等;

**Mobx**是一个透明函数响应式编程的状态管理库，它使得状态管理简单可伸缩∶

- Action∶定义改变状态的动作函数，包括如何变更状态;
- Store∶ 集中管理模块状态（State）和动作(action)
- Derivation（衍生）∶ 从应用状态中派生而出，且没有任何其他影响的数据

**对比总结**

- redux将数据保存在单一的store中，mobx将数据保存在分散的多个store中
- redux使用`plain object`保存数据，需要手动处理变化后的操作;mobx适用`observable`保存数据，数据变化后自动处理响应的操作
- redux使用不可变状态，这意味着状态是只读的，不能直接去修改它，而是应该返回一个新的状态，同时使用纯函数;mobx中的状态是可变的，可以直接对其进行修改
- mobx相对来说比较简单，在其中有很多的抽象，mobx更多的使用面向对象的编程思维;redux会比较复杂，因为其中的函数式编程思想掌握起来不是那么容易，同时需要借助一系列的中间件来处理异步和副作用
- mobx中有更多的抽象和封装，调试会比较困难，同时结果也难以预测;而redux提供能够进行时间回溯的开发工具，同时其纯函数以及更少的抽象，让调试变得更加的容易

## Redux 和 Vuex区别?

**相同点**

- state 共享数据
- 流程一致：定义全局state，触发，修改state
- 原理相似，通过全局注入store。

**不同点**

- 从实现原理上来说：
  - Redux 使用的是不可变数据，而Vuex的数据是可变的。Redux每次都是用新的state替换旧的state，而Vuex是直接修改
  - Redux 在检测数据变化的时候，是通过 diff 的方式比较差异的，而Vuex其实和Vue的原理一样，是通过 getter/setter来比较的
- 从表现层来说：
  - vuex定义了state、getter、mutation、action四个对象；redux定义了state、reducer、action。
  - vuex中state统一存放，方便理解；reduxstate依赖所有reducer的初始值
  - vuex有getter,目的是快捷得到state；redux没有这层，react-redux mapStateToProps参数做了这个工作。
  - vuex中mutation只是单纯赋值(很浅的一层)；redux中reducer只是单纯设置新state(很浅的一层)。他俩作用类似，但书写方式不同
  - vuex中action有较为复杂的异步ajax请求；redux中action中可简单可复杂,简单就直接发送数据对象（{type:xxx, your-data}）,复杂需要调用异步ajax（依赖redux-thunk插件）。
  - vuex触发方式有两种commit同步和dispatch异步；redux同步和异步都使用dispatch

通俗点理解就是，vuex 弱化 dispatch，通过commit进行 store状态的一次更变；取消了action概念，不必传入特定的 action形式进行指定变更；弱化reducer，基于commit参数直接对数据进行转变，使得框架更加简易;

**共同思想**

- 单一的数据源
- 变化可以预测

本质上∶ redux与vuex都是对mvvm思想的服务，将数据从视图中抽离的一种方案。