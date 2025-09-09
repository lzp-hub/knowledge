## VueX

* 了解Vuex
  Vuex是一个专门为Vue.js应用程序开发的全局状态管理模式, 它采用集中式存储管理所有组件的公共状态, 并以相应的规则保证状态以一种可预测的方式发生变化

* 为什么要使用Vuex
  vue组件是Vue最强大的功能之一，所以要掌握Vue，组件间的通信是必须掌握的技能，但如果你使用 vue.js 开发过大型项目, 那么我想你可能会对 vue 组件之间的通信感到崩溃 。
  vuex的出现就是为了解决多组件间的数据通讯（下图为：Vuex的核心工作原理）
  ![Alt text](/Users/lizhanpeng/Desktop/myself_md/框架/vue/img/vuex.png "vuex")

### 使用步骤

1. 安装vuex ： npm install vuex
2. 引入vuex
3. 使用vuex： Vue.use(vuex)
4. 创建一个store
5. 创建state数据交由store来管理
6. 将创建好的store注入到Vue根实例里

### new Vuex.Store属性

1. state：共享的数据。原理是提供一个响应式数据

   > 获取方法：**this.$store.state.xxx**

2. getters:派生属性,类似vue里面的computed里面的get方法。原理是借助Vue的计算属性computed来实现缓存

   > 在里面自定义的方法，其第一个形参为state
   > 调用方法：**this.$store.getters.xxx**

3. mutations:存放普通方法，对state数据的增删改查。原理是更改state方法

   > 在里面自定义的方法，其第一个形参为state,第二个形参payload为触发函数时接收的实参,只能接收一个
   > 调用方法：**this.$store.commit(type,payload)**
   >
   > 注意：mutations只能写同步方法，不能写异步，比如axios、setTimeout等，这些都不能写，mutations的主要作用就是为了修改state的。如果在mutations中写异步，也能够调成功，但是由于是异步的，不能被调试工具追踪到，所有不推荐这样写，不利于调试,这是官方的约定。

4. actions：定义调用mutations的方法，也可存放逻辑复杂或异步代码。原理是触发mutations方法

   > 在里面自定义的方法，其第一个形参context与store实例部分具有相同属性和方法的对象, 例如：commit/dispatch/getters/state等的方法；第二个形参payload为触发函数时接收的实参,只能接收一个
   > 调用方式:**this.$store.dispatch(type,payload**)
   >
   > + action可以提交mutation,然后mutation去更改state
   > + action不要直接去操作state，而是去操作mutation
   > + action包含异步操作，类似于axios请求，可以都放在action中写
   > + action中的方法默认的就是异步，**并且返回promise**

5. modules: 模块化。原理是Vue.set动态添加state到响应式数据中

   * 做法：在store文件夹自定义一个modules文件夹，将划分的各类store存放进去；然后引入至index.js，写到Vuex.Store实例的moudules属性里即可

   * state数据层会添加一层模块名；其他的不变；

     > 原来获取方式:$store.state.count >>>模块化之后的获取方式：$store.state.模块名.count

```js
import Vue from "vue";
import Vuex from "vuex";//npm install vuex

Vue.use(Vuex);//让Vuex 绑定Vue

// 创建一个store
export default new Vuex.Store({
  // state里面的数据，在其他页面可以使用this.$store.state获取
  // 保持数据的持久性：store数据刷新会回到初始值。一般刷新的时候会发送请求获取数据或者拿本地储存的数据；
  state: {
    count: 0
  },
  mutations: {
    // 在mutations存放普通方法，对state数据的增删改查；
    // 调用方法：this.$store.commit(type,payload)
    add(state, payload) {
      // 形参state 就是this.state
      // 形参payload 触发函数时传入的参数,只能接收一个
      state.count = payload + state.count;
    },
  },
  actions: {
    // 可以包含**任意异步**(例如ajax请求)操作
    // 在actions定义调用mutations的方法，也可存放逻辑复杂或异步代码。
    // 调用方法：this.$store.dispatch("cutItem",{})
    // context与store实例部分具有相同属性和方法的对象, 例如：commit/dispatch/getters/state等的方法；
      // payload 触发函数时传入的参数，只能接收一个
     async getToken(context){
         const res = await axios.get('/接口url')
         context.commit('set_token',{token:res.token}
       )
    }
  },
  getters: {
    //getters派生属性,类似vue里面的computed里面的get方法；场景：购物车的数量和总价  
    // 调用方法：this.$store.getters.filter
    filter(state) {
      	// 形参state是this.state
      if (state.count > 0 && state.count < 6) {
        // getters里面的函数记得返回值
        return "不及格";
      } else if (state.count >= 6 && state.count < 8) {
        return "良好";
      } else if (state.count >= 8 && state.count <= 10) {
        return "秀儿";
      } else {
        return "好开心";
      }
    },
  },
  modules: {
    // VueX的模块化
    // state数据层会添加一层模块名；其他的不变；
    // 原来$store.state.count >>> $store.state.模块名.count
    module1： {
    		// namespaced为true，则在使用mutations时，就必须要加上模块名
      	namespaced: true, 
  		  state: {},
  			getters: {},
  			mutations: {},
  			actions: {},
  			modules: {}
  	}，
    module2： {
        // namespaced不写，默认为false，则在使用mutations时，不需要加模块名
  		  state: {},
  			getters: {},
  			mutations: {},
  			actions: {},
        modules: {}
  	}  
  }
});

new Vue({
  store,// 在组件中可以this.$store访问
  render: h => h(App),
}).$mount('#app')
```

vuex详细理解

https://juejin.cn/post/6969553033545941022

# mapState、mapGetters、mapMutations、mapActions             

