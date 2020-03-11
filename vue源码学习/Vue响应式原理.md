## Vue响应式原理

Vuejs的响应式原理依赖于Object.defineProperty实现

- vue通过设定对象属性的setter/getter方法来监听数据的变化，通过getter来进行依赖收集，而每个setter方法就是一个观察者，在数据变更的时候通知订阅者更新视图

- #### 1、怎么将数据data变成可观察的observable

  - 在初始化数据时会调用observe，将vue数据设置成observable
  - 当数据发生改变时就会触发set，对订阅者进行回调

  ```js
  function observe(value, cb) {
    Object.keys(value).forEach(key => defineReactive(value,key,value[key],cb))
  }
  
  function defineReactive(obj, key, val, cb) {
    Object.defineProperty(obj, key, {
      enumerable: true,
      configurable: true,
      get: () => {
        return val
      },
      set: newVal => {
        val = newVal;
        cb(); //订阅者收到消息的回调
      }
    })
  }
  
  class Vue {
    constructor(options) {
      this._data = options.data;
      observe(this._data, option.render)
    }
  }
  
  let app = new vue({
    el: '#app',
    data: {
      text: 'text',
      text2: 'text2'
    },
    render() {
      console.log('render')
    }
  })
  ```

- #### 2、依赖收集

  - **为了防止在data声明的变量a在实际模板中实际并没有被用到，但变量a的更新也会导致重新执行渲染**
  - 当对data上的对象进行修改值的时候会触发它的setter，那么取之的时候自然就会触发getter 事件，所以只要在最开始进行一次render时，将所有被渲染所依赖的data中的数据就会被getter收集到Dep的subs中去

  ```js
  /**定义一个依赖收集类*/
  class Dep {
    constructor () {
      this.subs = [];
    }
    
    //添加
    addSub (sub: Watcher) {
      this.subs.push(sub);
    }
    
    removeSub (sub: Watcher) {
      remove(this.subs, sub)
    }
    //通知更新
    notify () {
      const subs = this.subs.slice();
      for(let i = 0, l = subs.length; i < l; i++) {
        subs[i].update()
      }
    }
  }
  
  function remove (arr, item) {
    if(arr.length) {
      const index = arr.indexOf(item);
      if(index > -1) {
        return arr.splice(index, 1)
      }
    }
  }
  ```

  - ##### Watcher

    - 订阅者，当依赖收集的时候会addSub到sub中，在修改data中数据的时候会触发dep对象的notify，通知所有Watcher对象去修改对应视图

    ```js
    class Watcher {
      constructor (vm, expOrFn, cb, options) {
        this.cb = cb;
        this.vm = vm;
        
        /**将观察者本身赋值给全局的target，只有被target标记过的才会进行依赖收集*/
        Dep.target = this;
        /**触发渲染操作进行依赖收集*/
        this.cb.call(this.vm);
      }
      
      update () {
        this.cb.call(this.vm);
      }
    }
    ```

  - 开始依赖收集

    - 将观察者Watcher实例赋值给全局的Dep.target，然后触发render操作只有被D ep.target标记过的才会进行依赖收集。有Dep.target的对象才会将Watcher实例push到subs中，在对象被修改触发setter操作时dep会调用subs中的Watcher实例中的update方法进行渲染

    ```js
    class Vue {
      constructor (options) {
        this._data = options.data;
        observer(this._data, options.render);
        let watcher = new Watcher(this);
      }
    }
    
    function defineReactive (obj, key, val, cb) {
      //在闭包内存储一个Dep（收集依赖）对象
      const dep = new Dep();
      
      Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get: () => {
          if(Dep.target) {
            //Watcher对象存在全局的Dep.target中
            dep.addSub(Dep.target);
          }
        },
        set: newVal => {
          //只有之前addSub中的函数才会触发
          dep.notify();
        }
      })
    }
    
    Dep.target = null;
    ```

    

