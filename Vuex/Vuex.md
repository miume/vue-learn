## Vuex

- 专门为Vue.js应用程序开发的状态管理模式

  ```js
  new Vue({
    //state
    data () {
      return {
        count: 0
      }
    },
    
    //view
    template: `<div>{{count}}</div>`,
    
    //actions
    methods: {
      increment () {
        this.count++
      }
    }
  })
  ```

  - ###### State：驱动应用数据源

  - ###### view：以声明方式将state映射到视图

  - ###### actions：响应在view上的用户输入导致的状态变化

  #### 适用于以下情况

  - 多个试图依赖于同一状态
  - 来自多个视图的行为需要变更同一状态

- #### state

  - Vuex通过**store**选项，提供一种机制将状态从根组件注入到每个需要用state的子组件中（Vue.use(Vuex)）

    ```js
    import Vue from 'vue';
    import Vuex from 'vuex';
    import counter from './counter';
    
    Vue.use(Vuex);
    
    const store = new Vuex.store({
      state: {
        count: 0
      },
      mutations: {
        increment (state) {
          state.count++
        }
      }
    })
    
    new Vue({
      el: '#app',
      store, //将store对象提供给“store”选项，这可以把“store”实例注入所有子组件
      components: {counter},
      template: `<div class='app'><counter/></div>`
    })
    ```

    

  - ##### 如何在Vue组件中获得Vuex状态？

    - 在Vue组件的计算属性中返回Vuex中的某个状态
    - 子组件可以通过**this.$store**访问到

    ```js
    const Counter = {
      template: `<div>{{ count }}</div>`,
      computed: {
        count () {
          return this.$store.state.count
        }
      }
    }
    ```

- #### mapState 辅助函数

  - 当一个组件需要多个状态时，声明多个计算属性会有些重复和冗余，可以使用mapState辅助函数帮助生成计算属性

    ```js
    import { mapState } from 'vuex';
    
    export default {
      //...
      computed: mapState({
        count: state => state.count,
        countAlias: 'count',//可以直接使用字符串参数
      })
    }
    ```

  - 当映射的计算属性的名称与state的子节点名称相同时，可以给mapState传一个字符串数组

    ```js
    export default {
      //...
      computed: mapState(['count'])
    }
    ```

  - 如何实现与局部计算 属性混合使用

    ```js
    export default {
      //...
      computed: {
        localComputed () {},
        ...mapState({'count'}), //使用对象展开元算符将此对象混入到外部对象中
      }
    }
    ```

    

- #### Getter

  - Vuex允许在store中定义getter（可以认为是store的计算属性）

  - getter的返回值会根据它的依赖被缓存起来，只有当它的依赖值发生改变才会被重新计算

    ```js
    const store = new Vuex.store({
      state: {
        todos: [
          {id: 1, text: 'Vue', done: true},
          {id: 2, text: 'react', done: true},
          {id: 3, text: 'Angular', done: false}
        ]
      },
      getters: {
        //一个参数为state，第二个对象为getters,第三个参数rootStateb=表示根结点状态
        doneTodos: (state,getters,rootState) => state.todos.filter(todo => todo.done)
      },
      getTodoById: (state) => (id) => state.todos.find(todo => todo.id === id)
    })
    ```

  - 如何在Vue组件中调用?

    ```js
    export default {
      //...
      computed: {
        todo () {
          return this.$store.getters.doneTodos
        }
      }
    }
    ```

    

- #### mapGetters辅助函数

  - mapGetter辅助函数仅仅将store中的getter映射到局部计算属性中

    ```js
    export default {
      //...
      computed: {
        localComputed () {},
        ...mapGetters(['doneTodos','getTodoById']),
        //...mapGetters({doneCount: 'doneTodos'}) 可以实现将一个getter属性另取一个名字
      }
    }
    ```

- #### Mutations（变动-突变）：更改Vuex的store中状态的唯一方法是提交mutation

  ```js
  const store = new Vuex.store({
    state: {
      count: 0
    },
    
    //类似于事件注册
    mutations: {
      increment(state,n) {
        state.count += n
   		},
      decrement (state, payload) {
        state.count -= payload.number
      }
  })
  ```

  - 要唤醒一个mutation handler，需要以相应的type调用store.commit方法

    ```js
    store.commit('increment')
    store.commit('increment',10)
    store.commit('decrement', {number: 10})
    ```

  - ##### mutution必须是同步函数

- #### mapMutations辅助函数

  - mapMutations辅助函数将组件中的methods映射为store.commit调用

    ```js
    import { mapMutations } from 'vuex';
    
    export default {
      //...
      methods: {
        ...mapMutations(['increment'])
      }
    }
    ```

    

- #### Action

  - Action提交的是mutation，而不是直接变更状态

  - Action可以**包含任意异步操作**

  - Action函数接受一个与store实例具有相同方法和属性的context对象 { commit, state, getters} = context

    ```js
    import Vue from 'vue'
    import Vuex from 'vuex'
    
    const store = new Vuex.store({
      state: {
        count: 0
      },
      mutations: {
        increment (state) {
          state.count++
        }
      },
      actions: {
        /**
        increment (context) {
          context.commit('increment')
        },
        */
        //利用参数结构来简化代码
        increment ({ commit }) {
          commit('increment')
        }
      }
    })
    ```

    

  - ##### 分发Action

    ```js
    store.dispatch('increment')
    ```

  - mutations必须同步执行，而Action不受约束，可以在Action内部执行异步操作

    ```js
    const store = new Vuex.store({
      state: {
        count: 0
      },
      mutations: {
        increment (state) {
          state.count++
        }
      },
      actions: {
        incrementAsync ({ commit }) {
          setTimeout(() => {
            commit('increment')
          }, 1000)
        }
      }
    })
    ```

    

- #### mapActions辅助函数

  - mapActions辅助函数将组件的methods映射为store.dispatch调用

  ```js
  import { mapActions } from 'vuex';
  
  export default {
    //...
    methods: {
      ...mapActions(['incrementAsync'])
    }
  }
  ```



- #### Module

  - Vuex允许将store分割成模块module
  - 每个模块module拥有自己的state、mutation、action、getter、甚至是嵌套子模块

  ```js
  const moduleA = {
    state: {},
    mutations: {},
    actions: {},
    getters: {}
  }
  
  const moduleB = {
    state: {},
    mutations: {},
    actions: {
      //rootState表示跟节点状态
      getSum ({ state, commit, rootState }) {
        
      }
    }
  }
  
  const store = new Vuex.store({
    modules: {
      a: moduleA,
      b: moduleB
    }
  })
  
  store.state.a
  store.state.b
  ```

  