# Redux

## 三项基本原则

1. 单一数据源：所有状态都保存在单一的store中

2. state是只读的：不能直接对state进行修改，只能用新的state替换旧的state

3. Reducer必须是一个纯函数：固定的输入，就一定会有固定的输出，而且不会有任何副作用


**Redux使用场景**

* 某个组件的状态，需要共享
* 某个状态需要在任何地方都可以拿到
* 在一个组件中需要改变全局状态
* 在一个组件中需要改变另一个组件的状态

例子

```js
import { legacy_createStore as createStore } from 'redux';

// 自定义其形参stata的默认值
const initState = {
    currentUser: {},
    isLogin: false,
}
// 第二步：定义reducer(作用是初始化数据并指定修改方法)
const reducer = function (state = initState, action) {
    // 形参state：状态(共享的数据)，一般会设定默认值
    // 形参action:指定数据的修改方式,一般是一个对象：{type:'', payload}
    switch (action.type) {
        // store.dispatch({type:'login', user})
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

## createStore

store是Redux存储区（一个保存数据的地方），你可以把它看成一个仓库，**规定一个应用只能有一个Store**，store中的数据可以被直接访问，但只能通过提供的reducer进行更新。

```js
 import { legacy_createStore as createStore } from 'redux';;

 const reducer = function (state = initState, action) {
				return state
 }
 const store = createStore(reducer);

export default store;
```

### store.getState()

在组件中获取state，获取仓库最新状态

```js
 store.getState();
```

### store.dispatch({type, payload});

在组件中修改数据，修改数据唯一方式。调用dispatch()方法，其内部会调用reducer()方法

```js
 store.dispatch({type:'login',user:lzp});
```

### store.subscribe(fn) 

监听数据修改；fn是一个函数；state数据更新，会触发

```react
 store.subscribe(()=>{
    console.log('statechange=',store.getState())
    // 为了实现刷新而另组件state改变
    // this.setState({
    //     isLogin:store.getState().isLogin
    // })
 });
```

在react中使用redux的原生：在react里面我们可以在componentDidMount生命周期里定义store.subscribe(fn)；在fn里面可以使用store.getState()获取数据,再让setState()修改当前组件state实现**刷新**；非常麻烦

## reducer

Reducer 必须是一个**纯函数**，用于指定state修改逻辑，**它接受当前 state 和 action 作为参数，并根据state和action的值返回新的state**

- 第一个参数是state
- 第二个参数是action, `{type,payload}`,此action就是store.dispatch(action)里的action

```js
const initState = {
    currentUser:{},
    isLogin=false,
}
// 第二步：定义reducer(作用是初始化数据并指定修改方法)
const reducer = function (state = initState, action) {
    // 形参state：状态(共享的数据)，一般会设定默认值
    // 形参action:指定数据的修改方式,一般是一个对象：{type:'', payload}
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
```

## 手写简单版createStore

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

# react-redux

为了方便使用，Redux 的作者封装了一个 React 专用的库 React-Redux，它提供了一些接口，用于Redux的状态和React的组件展示结合起来，以用于实现状态与视图的一一对应

+ HOC 与 context 通信在 react-redux 底层中的原理

  > `<Provider/>`：利用**Context**共享redux的数据
  > `connect()`：使用**高阶组件**把数据作为props传入React组件

## `<Provider/>` 容器组件

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

## `connect()`高阶组件

connect( )方法为React-Redux核心方法，它把react组件与redux中的Store真正连接在一起，connect方法接受两个参数(Function类型)，用于定义了 UI 组件的业务逻辑，返回一个高阶组件

格式：`connect([mapStateToProps],[mapDispatchToProps])(某组件)`

- 第一个参数：mapStateToProps
  - 负责组件的输入逻辑，即将**state**映射到 UI 组件的参数(props)
  - 必须返回一个对象
- 第二个参数：mapDispatchToProps
  - 负责组件的输出逻辑，即将用户对 UI 组件的操作映射成 Action
  - 必须返回一个对象
  - 不写mapDispatchToProps参数，默认在props里添加了dispatch方法

```react
import { connect } from 'react-redux'

function mapStateToProps(state,ownprops) {
    //state：redux中的state
    //ownprops: Cart组件自身的props
    return {
      //将state中购物车页面的goodsinf数据映射到props，Cart组件中通过props.data访问
      data: state.shoppingCart.goodsinf
    }
}
function mapDispatchToProps(dispatch,ownprops) {
    // dispatch: redux中的dispatch方法
    // ownprops：Cart组件自身的props
    return {
      dispatch,//书写了mapDispatchToProps，需要手动添加回dispatch
      logout() {
        	// 自定义dispatch方法，Cart组件中通过props.logout访问
          dispatch({ type: 'logout' })
      }
    }
}
// 类组件可使用装饰器
// @connect(mapStateToProps,mapDispatchToProps)

Cart = connect(mapStateToProps,mapDispatchToProps)(Cart);
export default App
```

## combineReducers实现redux的模块化

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

## bindActionCreators

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

## Generator与Iterator

### 生成器函数Generator

1. 执行生成器函数并不会执行函数中的代码，而是返回一个**迭代器**

2. 定义函数时添加星号: *

3. 在生成器函数中,yield暂停并返回，可以有多个;return结束并返回，只有一个

```js
function * create(){
    console.log('start')//调用 create()并不会执行。
    yield 10;
    console.log(1)
    yield 50;   // 暂停并返回
    console.log(2)
    return 100; //结束并返回
    console.log(3)
}
p = create()
p.next()//输出start,返回值{value: 10, done: false} 
p.next()//输出1，返回值{value: 50, done: false}
p.next()//输出2，返回值{value: 100, done: true}
p.next()//代码不会执行了，直接返回 {value: undefined, done: true}
```

### 迭代器Iterator

内部包含多个值的对象，要获取迭代器中的值，必须调用`next()`方法，没调用一次next就是一次迭代，next方法的返回值格式：{ value,done }

+ value：每一次迭代返回的值
+ done: 标识迭代器是否迭代完成
+ <suspended> 没有执行完，<closed> 执行完

**数组、字符串其内置身上有symbol.iterator()方法;可用for...of遍历具有迭代器的数据(不断执行迭代器的next()方法，直到迭代完成)**

## redux-saga 中间件 

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



# 搭建项目 react-redux RTK

```shell
npm create vite@latest

> npx
> create-vite

│
◇  Project name:
│  react-redux-demo
│
◇  Select a framework:
│  React
│
◇  Select a variant:
│  JavaScript
│
◇  Use rolldown-vite (Experimental)?:
│  No
│
◇  Install with npm and start now?
│  No
│
◇  Scaffolding project in /Users/lizhanpeng/Desktop/demo/react-redux-demo...
│
└  Done. Now run:

  cd react-mobx-demo
  yarn
  yarn dev
```

## 依赖

```
yarn add @reduxjs/toolkit react-redux
```

---

## 目录建议

```
src/
  store/
    index.js
    counter/
      slice.js
      selectors.js
    cart/
      slice.js
  App.jsx
  main.jsx
```

---

## src/store/counter/slice.js

```js
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";

// 2 秒后 +1（等价于 MobX 的 incrementAsync）
export const incrementAsync = createAsyncThunk(
  "counter/incrementAsync",
  async () => {
    await new Promise((r) => setTimeout(r, 2000));
    return 1; // payload
  }
);

const counterSlice = createSlice({
  name: "counter",
  initialState: { count: 0 },
  reducers: {
    increment(state) {
      state.count += 1;
    },
    decrement(state) {
      state.count -= 1;
    },
    reset() {
      return { count: 0 };
    },
  },
  extraReducers: (builder) => {
    builder.addCase(incrementAsync.fulfilled, (state, action) => {
      state.count += action.payload;
    });
  },
});

export const { increment, decrement, reset } = counterSlice.actions;
export default counterSlice.reducer;
```

---

## src/store/counter/selectors.js

```js
import { createSelector } from "@reduxjs/toolkit";

export const selectCount = (s) => s.counter.count;
export const selectDouble = createSelector([selectCount], (count) => count * 2);
```

---

## src/store/cart/slice.js

```js
import { createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
  name: "cart",
  initialState: { list: [] },
  reducers: {
    setList(state, action) {
      state.list = action.payload;
    },
    pushItem(state, action) {
      state.list.push(action.payload);
    },
    clear(state) {
      state.list = [];
    },
  },
});

export const { setList, pushItem, clear } = cartSlice.actions;
export default cartSlice.reducer;
```

---

## src/store/index.js

```js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counter/slice";
import cartReducer from "./cart/slice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    cart: cartReducer,
  },
});
```

---

## src/main.jsx

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { store } from "./store";

import App from "./App";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
```

---

## src/App.jsx

```jsx
import { useDispatch, useSelector } from "react-redux";
import {
  increment,
  decrement,
  reset,
  incrementAsync,
} from "./store/counter/slice";
import { selectDouble, selectCount } from "./store/counter/selectors";

export default function App() {
  const dispatch = useDispatch();

  const count = useSelector(selectCount);
  const double = useSelector(selectDouble);
  const cartLen = useSelector((s) => s.cart.list.length);

  return (
    <div className="App">
      <h3>计数器案例（Redux Toolkit / JS）</h3>
      <div>点击次数：{count}</div>
      <div>double: {double}</div>
      <div>cart数量: {cartLen}</div>

      <button onClick={() => dispatch(increment())}>加1</button>
      <button onClick={() => dispatch(incrementAsync())}>异步加1</button>
      <button onClick={() => dispatch(decrement())}>减1</button>
      <button onClick={() => dispatch(reset())}>重置</button>
    </div>
  );
}
```

# 搭建项目 redux@4 + react-redux@7 经典旧版

```shell
npm create vite@latest

> npx
> create-vite

│
◇  Project name:
│  react-redux-demo
│
◇  Select a framework:
│  React
│
◇  Select a variant:
│  JavaScript
│
◇  Use rolldown-vite (Experimental)?:
│  No
│
◇  Install with npm and start now?
│  No
│
◇  Scaffolding project in /Users/lizhanpeng/Desktop/demo/react-redux-demo...
│
└  Done. Now run:

  cd react-mobx-demo
  yarn
  yarn dev
```

## 依赖

```
yarn add redux@4 react-redux@7
```

---

## 目录建议

```
src/
  store/
    index.js          # createStore + DevTools
    counter.js        # counter reducer + actions + selectors（包含 double 选择器）
    cart.js           # cart reducer + actions
  
  main.jsx            # 入口
  App.jsx             # app
  AppConnect.jsx      # 写法一：connect 版本
  AppHooks.jsx        # 写法二：hooks 版本
```

---

## src/store/counter.js

```js
// action types
export const INCREMENT = "counter/INCREMENT";
export const DECREMENT = "counter/DECREMENT";
export const RESET = "counter/RESET";

// action creators（同步）
export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });
export const reset = () => ({ type: RESET });

const initialState = { count: 0 };

// 经典 reducer（纯函数）
export default function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { count: state.count + 1 };
    case DECREMENT:
      return { count: state.count - 1 };
    case RESET:
      return initialState;
    default:
      return state;
  }
}

// 选择器（等价于 MobX 的 getter double）
export const selectCount = (s) => s.counter.count;
export const selectDouble = (s) => s.counter.count * 2;
```

## src/store/cart.js

```js
// action types
export const SET_LIST = "cart/SET_LIST";
export const PUSH_ITEM = "cart/PUSH_ITEM";
export const CLEAR = "cart/CLEAR";

// action creators
export const setList = (list) => ({ type: SET_LIST, payload: list });
export const pushItem = (item) => ({ type: PUSH_ITEM, payload: item });
export const clear = () => ({ type: CLEAR });

const initialState = { list: [] };

export default function cartReducer(state = initialState, action) {
  switch (action.type) {
    case SET_LIST:
      return { ...state, list: action.payload };
    case PUSH_ITEM:
      return { ...state, list: state.list.concat(action.payload) };
    case CLEAR:
      return { ...state, list: [] };
    default:
      return state;
  }
}
```

## src/store/index.js

```js
import {
  legacy_createStore as createStore,
  combineReducers,
  compose,
  applyMiddleware,
} from "redux";
import counter from "./counter";
import cart from "./cart";

// DevTools（老派写法：有就用）
const composeEnhancers =
  (typeof window !== "undefined" &&
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__) ||
  compose;

const rootReducer = combineReducers({ counter, cart });

// 不使用中间件也可以保持结构（空的 applyMiddleware），也可直接省略
export const store = createStore(
  rootReducer,
  composeEnhancers(applyMiddleware())
);
```

---

## src/main.jsx（验证两者效果）

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { store } from "./store";

import App from "./App";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);
```

---

## src/App.jsx

```jsx
import AppConnect from "./AppConnect";
import AppHooks from "./AppHooks";

function App() {
  return (
    <>
      <AppConnect />
      <AppHooks />
    </>
  );
}

export default App;
```

## src/AppConnect.jsx（写法一：connect 旧派）

```jsx
import React from "react";
import { connect } from "react-redux";
import {
  increment,
  decrement,
  reset,
  selectCount,
  selectDouble,
} from "./store/counter";

function AppConnect({ count, double, cartLen, increment, decrement, reset }) {
  // 不用任何中间件：组件里 setTimeout 实现“异步加1（2s）”
  const incrementAsync = () => {
    setTimeout(() => {
      increment(); // 已通过 mapDispatchToProps 映射成 props 方法
    }, 2000);
  };

  return (
    <div className="App">
      <h3>计数器案例（redux@4，无 saga）— connect 版</h3>
      <div>点击次数：{count}</div>
      <div>double: {double}</div>
      <div>cart数量: {cartLen}</div>

      <button onClick={increment}>加1</button>
      <button onClick={incrementAsync}>异步加1（2s）</button>
      <button onClick={decrement}>减1</button>
      <button onClick={reset}>重置</button>
    </div>
  );
}

// 读取全局 state 的最小切片
const mapStateToProps = (state) => ({
  count: selectCount(state),
  double: selectDouble(state),
  cartLen: state.cart.list.length,
});

// 映射 action（对象简写）
const mapDispatchToProps = { increment, decrement, reset };

export default connect(mapStateToProps, mapDispatchToProps)(AppConnect);
```

---

## src/AppHooks.jsx（写法二：hooks 版）

```jsx
import React from "react";
import { useDispatch, useSelector } from "react-redux";
import {
  increment,
  decrement,
  reset,
  selectCount,
  selectDouble,
} from "./store/counter";

export default function AppHooks() {
  const dispatch = useDispatch();
  const count = useSelector(selectCount);
  const double = useSelector(selectDouble);
  const cartLen = useSelector((s) => s.cart.list.length);

  const incrementAsync = () => {
    setTimeout(() => dispatch(increment()), 2000);
  };

  return (
    <div className="App">
      <h3>计数器案例（redux@4，无 saga）— hooks 版</h3>
      <div>点击次数：{count}</div>
      <div>double: {double}</div>
      <div>cart数量: {cartLen}</div>

      <button onClick={() => dispatch(increment())}>加1</button>
      <button onClick={incrementAsync}>异步加1（2s）</button>
      <button onClick={() => dispatch(decrement())}>减1</button>
      <button onClick={() => dispatch(reset())}>重置</button>
    </div>
  );
}
```

---

# **Redux / React-Redux / RTK** 

------

### 1. Redux 三大原则是什么？为什么这样设计？

**一句话回答**
 单一数据源、状态只读、用纯函数 reducer，保证状态可预测、可追踪、易调试。

**要点**

- 单向数据流
- 不可变更新
- 纯函数无副作用

**详细回答**
 单一数据源便于全局一致与集中管理；状态只读，所有变化经由 action→reducer，有完整轨迹；reducer 是纯函数（旧 state + action → 新 state），不做 IO/随机，保证可预测性与时间旅行调试。

**扩展/对比**
 Context 只是传值机制，不提供不可变约束、调试、时间旅行；MobX 走响应式，Redux 走显式 action 与单向流，适合强可追踪的复杂业务。

------

### 2. 为什么必须“不可变更新（immutable）”？

**一句话回答**
 为了可靠的变更检测（浅比较）、时间旅行调试和避免共享引用带来的隐性副作用。

**要点**

- 浅比较 === 提高性能
- DevTools 回放
- 避免引用共用导致 UI 不更新

**详细回答**
 变更检测通常靠浅比较（引用变更即变化）；如果直接修改原对象，React/选择器可能察觉不到；每次返回新对象可累积快照，支持回溯和重放，定位问题更容易。

**扩展/对比**
 RTK 内置 **Immer**，可以写“可变代码”（`state.count++`），内部会生成不可变的新状态，减少手误。

------

### 3. Redux 的数据流是什么？

**一句话回答**
 View → `dispatch(action)` → middleware → reducer → new state → 订阅者更新。

**要点**

- 单向、可追踪
- 中间件在 action→reducer 之间
- 订阅触发 UI 更新

**详细回答**
 UI 触发 `dispatch`；中间件链可做日志、权限、异步；reducer 纯函数产生新 state；`subscribe` 通知视图层重渲染或做副作用。

**扩展/对比**
 和双向绑定不同，单向流降低复杂度；和事件风暴不同，Redux 有严格入口（action）和纯更新（reducer），利于定位。

------

### 4. 何时使用 Redux？何时不需要？

**一句话回答**
 跨组件/页面共享复杂状态、需要可追踪调试时用；仅局部 UI 状态用组件 state/Context。

**要点**

- 共享、可追踪
- 复杂异步与缓存
- 局部 UI 别滥用

**详细回答**
 用户/权限、购物车、通知、复杂表格缓存、跨路由共享是常见场景；Modal 开关、表单局部值这类局部 UI 放组件内部即可。

**扩展/对比**
 RTK Query 更适合“请求 + 缓存”；Saga 适合复杂副作用编排；仅传值用 Context 足矣。

------

### 5. `connect` 与 Hooks 有何区别？该选哪个？

**一句话回答**
 `connect`（HOC）类/函数组件皆可且默认 pure；Hooks（`useSelector/useDispatch`）更直观，是函数组件首选。

**要点**

- `connect` 订阅切分细、历史多
- Hooks 简洁直观、灵活
- 别在同组件混用

**详细回答**
 `connect` 天生浅比较、性能稳；Hooks 更贴近现代写法、逻辑清晰。老项目/类组件继续 `connect` 很正常；新项目 Hooks 优先。

**扩展/对比**
 复杂派生用 `reselect`；`connect(mapState)(Comp)` 命名导出和默认导出要清晰，避免 Fast Refresh 规则误伤。

------

### 6. 为什么 `createStore` 被弃用？RTK 的优势是什么？

**一句话回答**
 `createStore` 已标记弃用，RTK 的 `configureStore` 内置 DevTools、默认中间件、更安全的默认配置与更好的 DX。

**要点**

- DevTools 与默认中间件
- 更少样板代码
- 更少坑点，类型体验更好

**详细回答**
 RTK 把“推荐实践”内置：不可变/可序列化检查、thunk、DevTools 开关等；配合 `createSlice` 自动生成 action+reducer；`createAsyncThunk` 统一异步流程；RTK Query 处理数据请求+缓存。

**扩展/对比**
 旧项目需要用旧 API 可 `import { legacy_createStore as createStore } from 'redux'` 消除警告；新项目强烈建议 RTK。

------

### 7. `createSlice` 的价值？与手写 reducer 有何不同？

**一句话回答**
 `createSlice` 自动生成 action + reducer，内置 Immer，不可变更新无需手写对象展开/拷贝。

**要点**

- action 与 reducer 紧耦合
- 少样板、强约束
- 组合 `extraReducers` 处理外部 action

**详细回答**
 `createSlice({ name, initialState, reducers })` 会产出 `actions` 与 `reducer`；在 `reducers` 中可以写“可变更新”，内部用 Immer 生成新 state；`extraReducers` 处理 `createAsyncThunk` 的各阶段或其它切片的 action。

**扩展/对比**
 手写 switch 易繁琐且易错；slice 更聚合、可维护性更好。

------

### 8. 如何在 Redux 中表达“计算属性（MobX 的 getter）”？

**一句话回答**
 用 **选择器**，并用 `createSelector` 做 memo 化派生。

**要点**

- 不把派生值放 state
- `reselect` 缓存结果
- 仅在依赖变化时重算

**详细回答**
 例如 `double = count * 2`，写成：

```js
const selectCount = (s) => s.counter.count;
export const selectDouble = createSelector([selectCount], (c) => c * 2);
```

这样能避免重复计算与不必要渲染。

**扩展/对比**
 MobX getter = computed；Redux 对应 selector。原则都是“派生不入库”。

------

### 9. Thunk / Saga / RTK Query 该如何取舍？

**一句话回答**
 简单流程用 Thunk（或 `createAsyncThunk`），请求与缓存用 RTK Query，复杂副作用编排再考虑 Saga。

**要点**

- Thunk：轻量直观
- RTK Query：自动缓存、状态、失效
- Saga：并发/取消/防抖等流程编排

**详细回答**
 常规 CRUD 请求与缓存直接上 RTKQ；需要取消任务、竞态控制、跨模块流程或复杂时序，Saga 更合适；简单按钮异步就 `createAsyncThunk` 即可。

**扩展/对比**
 统一团队技术栈更重要：别三者混用到一团糟，有了 RTKQ，很多“写请求状态管理”的样板都可以删掉。

------

### 10. 为什么 Redux 强调“可序列化”？

**一句话回答**
 为了持久化、日志、时间旅行与可预测性；非可序列化数据容易导致难以调试与传输。

**要点**

- RTK 默认 `serializableCheck`
- 避免放入函数、类实例、DOM、Promise、Date/Map（需自处理）

**详细回答**
 默认检查能在开发期发现“非序列化 payload/state”；如确需（如 `Date`、`File`），可转换或在 `serializableCheck.ignoredPaths/Actions` 里豁免。

**扩展/对比**
 RTK Query 的 cache 通常是可序列化对象；全局大对象或实例尽量放在模块内存/Ref，不入 store。

------

### 11. 如何做状态规范化（Normalized State）？为什么要这样做？

**一句话回答**
 用扁平结构（`ids + entities`），避免深层嵌套导致的昂贵拷贝；RTK 提供 `createEntityAdapter`。

**要点**

- byId / allIds 模式
- 快速 CRUD
- 自动选择器

**详细回答**
 `createEntityAdapter()` 提供 `addOne/upsertMany/removeOne` 等方法和选择器生成，配合扁平结构在大表格/列表场景显著提升性能与可维护性。

**扩展/对比**
 不规范化会导致 reducer 更新一处牵动全体、拷贝巨大对象；规范化使更新局部化、可组合。

------

### 12. 如何避免不必要的重渲染？

**一句话回答**
 最小化订阅切片 + memo 化选择器 + 合理拆分组件 + 避免 render 内新引用。

**要点**

- `useSelector` 选最小数据
- `createSelector` 缓存
- `React.memo` + 切组件边界

**详细回答**
 不要 `useSelector((s)=>s)` 订阅全树；派生数据放 selector；把大组件拆分为多个有针对性的订阅点；在 render 中少创建新对象/函数（或用 `useMemo/useCallback` 但别滥用）。

**扩展/对比**
 `connect` 默认 pure，性能常不错；Hooks 写法需要自己约束依赖与新引用产生。

------

### 13. 代码分割与动态注入 reducer 怎么做？

**一句话回答**
 路由级按需加载模块，并在进入页面时 `injectReducer` 到 store。

**要点**

- 动态 `store.replaceReducer`
- 或封装 `injectReducer(key, reducer)`
- 注意重复注入与卸载

**详细回答**
 大型应用不应一次性加载所有 reducer；可以维护一个 reducers 映射，在新模块加载时合并并 `replaceReducer`；注意避免重复注入与热更新行为。

**扩展/对比**
 RTK 没有开箱的动态注入，但与旧 API 完全兼容；配合路由懒加载即可。

------

### 14. 测试怎么写（Reducer / Selector / 异步）？

**一句话回答**
 reducer 断言输入输出，selector 输入 state 断返回值，异步 thunk/saga 用 mock/step-by-step 断言派发顺序或 effect。

**要点**

- 纯函数好测
- 选择器测 memo 行为
- Thunk/Saga 测派发/效果顺序

**详细回答**
 Reducer：给初始/特定 state + action，断新 state；Selector：断结果和缓存命中；Thunk：mock API + 断 `dispatch` 顺序；Saga：`expect(generator.next().value).toEqual(put(...))` 逐步断言。

**扩展/对比**
 RTK Query 测试可用 `msw` 模拟服务端，断缓存与状态变迁。

------

### 15. RTK 与旧版 Redux 的差异与优势？

**一句话回答**
 RTK 是官方“现代 Redux”，内置最佳实践（`configureStore`、`createSlice`、`createAsyncThunk`、RTK Query），更安全、更省样板、更易维护。

**要点**

- 更少样板（actions/reducer 一体化）
- DevTools/中间件默认
- 异步/缓存有标准方案

**详细回答**
 旧版需要手配 `createStore`、中间件、compose DevTools、手写 action types/switch；RTK 把通用套路都内置并强约束，减少误用与团队分歧。迁移可先 `configureStore`，再逐步把 reducer 改成 slice，最后引入 RTK Query。

**扩展/对比**
 旧项目维护可继续用 `connect` 与 `legacy_createStore`；新项目强烈推荐 RTK + Hooks + RTK Query。

------

### 16. 为什么不要在 reducer 里做副作用？

**一句话回答**
 reducer 必须是纯函数；副作用会破坏可预测性与时间旅行调试。

**要点**

- 不做 IO/随机/定时器
- 副作用应放中间件或组件/监听器

**详细回答**
 reducer 只根据输入返回新状态；异步与副作用放 thunk/saga/RTK Query/listenerMiddleware 或组件层的 effect 里，保证更新轨迹清晰。

**扩展/对比**
 RTK 提供 `createListenerMiddleware` 处理“状态变化后的副作用”，不污染 reducer。

------

### 17. Fast Refresh 报“only-export-components”怎么处理？

**一句话回答**
 确保文件只导出组件或类型；HOC 包装可分到单独文件或先导出原组件再导出包装。

**要点**

- Barrel 文件可局部禁用
- 先定义组件→再 `export default memo(Comp)`

**详细回答**
 `export default observer(App)` 这类“调用表达式导出”易被规则误伤；可把 `observer(App)` 放到 `App.observed.jsx` 或：

```jsx
export function AppInner(){...}
const App = observer(AppInner);
export default App;
```

**扩展/对比**
 这不是 Redux 专属问题，但在 React 项目里常遇到，面试会看你是否了解工程化细节。

------

### 18. 真实项目里 Action 的命名/划分建议？

**一句话回答**
 领域事件式命名（`user/loginSucceeded`），按领域切片组织，避免动词过泛与跨域耦合。

**要点**

- `sliceName/actionName`
- 事件语义清晰
- 避免“setXxx”滥用

**详细回答**
 `createSlice` 会自动前缀 `name`；尽量表达业务语义而非纯数据操作，如 `cart/itemAdded` > `setList`。方便审计与搜索。

**扩展/对比**
 RTK Query 的 endpoints 也建议 RESTful 语义；统一命名提升团队可读性与调试效率。

------

## 附：RTK 与旧版 Redux 快速对照（可贴在文档结尾）

| 主题                | 旧版（redux@4）                            | RTK（推荐）                              |
| ------------------- | ------------------------------------------ | ---------------------------------------- |
| Store               | `legacy_createStore` + 手配中间件/DevTools | `configureStore`（默认 DevTools/中间件） |
| Reducer/Action      | 手写 types + switch                        | `createSlice` 自动生成，Immer 内置       |
| 异步                | thunk/saga 自配                            | `createAsyncThunk`；**RTK Query**        |
| 选择器              | `reselect`                                 | 同（RTK 内置 reselect）                  |
| 状态结构            | 手动规范化                                 | `createEntityAdapter`                    |
| 可序列化/不可变检查 | 手工约束                                   | 默认中间件检查                           |
| DX/样板             | 多                                         | 少                                       |
| 迁移                | —                                          | 先 `configureStore` → 再改 slice/RTKQ    |

------

要不要我把这些问答直接合并进你的“Redux 笔记”，做成一版**“面试速背 + 实战速查”排版**（带最小代码片段与目录范式）？你可以直接打印或当项目 wiki 用。
