### 重学前端：Vuex的核心API&基本原理

> 基础知识重新学习，若有不对欢迎指正，感恩。

## Vuex是什么？

`Vuex`是`Vue`的状态管理工具，为了更方便多个组件共享状态。

举个🌰：

组件A有一个登陆状态，子组件a需要使用这个状态，可以直接从A通过props传递过来，那如果a的子组件也需要这个状态，再通过props传输就过于麻烦了。再另一个功能中B组件也需要登陆状态，这样传来传去就特别混乱。

Vuex的思路就是创建一个状态中心，集中的管理这种状态，让状态值从组件中剥离出来。

## 如何使用Vuex？

Vuex有如下几个概念

### State-顾名思义状态仓库

储存所有状态的对象

```
state: { age: 25 }
```

```html
<p>{{this.$store.state.age}}</p>
```

### Getters-派生状态

```js
state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    //生成一个过滤todos后的state
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
```

相当于组件中的computed属性，更有效率的使用**State**。

## Mutation-仓库内的管理员

对于State状态仓库，不方便直接修改state仓库的状态，就有同过mutation（转化器）来修改state。

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
 mutations: {
   incre(state, payload) {
      state.count += payload.age;
   }
	}
})


```

mutation是通过commit来调用的。

```js
// script
methods: {
   add() {
      this.$store.commit('incre', {age: 1});
   }
}
```

## Action-仓库外的接线员

mutation的操作state是同步的，需要异步修改state，就引入了action概念，可以理解为接线员，外部不仅可以直接操作mutation进行同步修改，也可以操作action，等完成异步操作后，action再通知mutation修改state。

```js
  actions: {
    checkout ({ commit, state }, products) {
    // 把当前购物车的物品备份起来
    const savedCartItems = [...state.cart.added]
    // 发出结账请求，然后乐观地清空购物车
    commit(types.CHECKOUT_REQUEST) //调用一个mutation操作
    // 购物 API 接受一个成功回调和一个失败回调
    shop.buyProducts(
      products,
      // 成功操作
      () => commit(types.CHECKOUT_SUCCESS),//调用一个mutation操作
      // 失败操作
      () => commit(types.CHECKOUT_FAILURE, savedCartItems) //调用一个mutation操作
    )
  }

	
```

action是通过dispatch来调用的。

```js
store.dispatch('checkout')
```

## Module-数据仓库的书架

当业务复杂，数据中心的数据多了，就需要类似学校图书馆的书架，将不同业务的数据分离出来。

```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
```

在模版中调用

```js
store.state.a // -> moduleA 的状态a
store.state.b // -> moduleB 的状态b
```

## 总结

简单来说就是

+ Vue components 可以dispatch（分发）一个Action。
+ Action通过commit（提交）调用Mutation（转换器）。
+ Mutation才能修改数据。
+ 当然Vue components 也可以commit一个Mutation，但是数据只能同步修改。
+ Getter也是一个state里的数据，但是可以使用类似computed随着state的值而改变。

![image-20200407224613313](/Users/zhouyefei1/Library/Application Support/typora-user-images/image-20200407224613313.png)

Vue components  dispatch（分发）Action，Action commit（提交）Mutation（转换器），mutation修改State，state修改才能改变Components的渲染。

## 辅助函数

### mapState-帮助state生成计算属性

```js
import {mapState} from 'vuex';

computed: {
  // 映射 this.age 为 this.$store.state.age
  ...mapState([
      'age'
   ])
}
```

### mapGetters-帮助Getters生成计算属性 

```js
import {mapState} from 'vuex';

computed: {
  // getter 混入 computed 对象 映射 this.age 为 `this.$store.getters.age`
  ...mapGetters([
      'age'
   ])
}
```

### mapMutations-帮助Mutations生成方法

```js
import {mapMutations} from 'vuex';
methods: {
    add() {
      // this.incre({age: 1})映射this.$store.commit('incre', {age: 1});
      this.incre({age: 1});
    },
    ...mapMutations([
      'incre'
    ])
}
```

### mapActions-帮助Actions生成方法

```js
import {mapActions} from 'vuex';
methods: {
    add() {
      // this.increAsyn({age: 1})映射成this.$store.dispatch('increAsyn', {age: 1});
      this.increAsyn({age: 1});
    },
    ...mapActions([
      'increAsyn'
    ])
}
```

## 手写简易Vuex

```js
let Vue;
class Store {
    constructor(options = {}) {
        this.vm = new Vue({
            data: {
                state: options.state
            }
        })
        let getters = options.getters;

        this.getters = {}
        Object.keys(getters).forEach(getterName => {
            Object.defineProperty(this.getters, getterName, {
                get: () => {
                    return getters[getterName](this.state)
                }
            })
        })
        let mutations = options.mutations || {}
        this.mutations = {}
        Object.keys(mutations).forEach(mutaitonName => {
            this.mutations[mutaitonName] = payload => {
                mutations[mutaitonName](this.state, payload);
            }
        })
        let actions = options.actions || {}
        this.actions = {}
        Object.keys(actions).forEach(actionName => {
            this.actions[actionName] = payload => {
                actions[actionName](this, payload);
            }
        })
    }
    commit = (method, payload) => {
        this.mutations[method](payload);
    }
    dispatch(method, payload) {
        this.actions[method](payload)
    }
    get state() {
        return this.vm.state
    }
}

const install = (_Vue) => {
    Vue = _Vue;
    Vue.mixin({
        beforeCreate() {
            if(this.$options && this.$options.store) {
                // 给根实例增加$store属性
                this.$store = this.$options.store
            } else {
                // 有可能单独创建了一个实例没有父亲，那就无法获取到store属性
                this.$store = this.$parent && this.$parent.$store
            }
        }
    })
}
export default {
    install,
    Store
}
```