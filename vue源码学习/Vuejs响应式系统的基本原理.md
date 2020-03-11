## Vuejs响应式系统的基本原理

- ##### Object.defineProperty(obj,key,descriptor)，使对象变成可观察的

  ```js
  Object.defineProperty({}, x, {
    enumerable: true,  //可枚举
    configurable: true, //可修改或删除
    get: function () { //获取属性的方法
      return 
    },
    set: function () { //设置属性的方法
      
    }
  })
  ```

  ```js
  function observer (value) {
    if (!value || (typeof value !== 'object')) {
      return;
    }
    
    Object.keys(value).forEach(key => {
      defineReactive(value, key, value[key]);
    })
  }
  
  function defineReactive (obj, key, val) {
    Object.defineProperty(obj, key, {
      enumerable: true, //属性可枚举
      configurable: true, //属性可以被修改或删除
      get: function reactiveGetter () {
        return val;
      },
      set: function reactiveSetter (newVal) {
        if (newVal === val) return;
        val = newVal;
        cb(newVal);
      }
    })
  }
  
  //回调函数，模拟视图更新
  function cb (newVal) {
    console.log('视图更新了！！！')
  }
  
  class Vue {
    /**Vue构造类*/
    constructor (options) {
      this._data = options.data;
      observer(this._data);
    }
  }
  
  let o = new Vue({
    data: {
      test: 'Learn Vue'
    }
  });
  
  o._data.test = 'update Date'; //更新视图
  ```



## 响应式系统的依赖收集追踪原理

```js
let b = new Vue({
  template: 
  	`<div>
				<span>{{text1}}</span>
				<span>{{text2}}</span>
		</div>`,
  data: {
    text1: 'text1',
    text2: 'text2',
    text3: 'text3'
  }
})

b._data.text3 = 'update'; //也会更新视图
```

#####  

### 问题：视图中并没有用到text3变量，所有text3变量的更新不应该触发视图的更新

- 订阅者Dep：主要用来存放Watcher观察者对象

  - 用addSub方法可以在目前的Dep对象中增加一个Watcher的订阅操作
  - 用notify方法通知Dep对象的subs中的所有Watcher对象触发更新操作

- 观察者watcher

- 依赖收集

  - Dep类，用来收集Watcher对象
  - 在对象被读取的时候，会触发reactiveGetter函数把当前的Watcher对象(存放在Dep.target中)收集到Dep类中去。
  - 在对象被写的时候，则会触发reactiveSetter方法，通知Dep类调用notify来触发所有Watcher对象的update方法更新对应视图

  ```js
  function observer (value) {
    if(!value || typeof value !== 'object') return;
    
    Object.keys.forEach(key => {
      defineReactive(value, key, value[key])
    })
  }
  
  function defineReactive(obj, key, val) {
    /**一个Dep类对象*/
    const dep = new Dep();
    Object.defineProperty(obj, key, {
      enumerable: true,
      configurable: true,
      get: function reactiveGetter () {
        /**将Dep.target（当前watcher对象存入dep的subs中）*/
        dep.addSub(Dep.target);
        return val;
      },
      set: function reactvieSetter (newVal) {
        if (newVal === val) return;
        /**在set的时候触发dep的notify来通知所有的Watcher对象更新视图*/
        dep.notify();
      }
  })
  }
  
  class Dep {
    construtor() {
      /**用来存放watcher对象的数组*/
      this.subs = [];
    }
    //在subs中添加一个watcher对象
    addSub (sub) {
      this.subs.push(sub);
    }
    //通知所有watcher对象更新视图
    notify () {
      this.subs.forEach(sub => {
        sub.update();
      })
    }
  }
  
  class Watcher {
    constructor () {
      /**在new一个watcher对象时将该对象赋值给Dep.target,在get中会用到*/
      Dep.target = this;
    }
    
    update () {
      console.log('视图更新啦～～')
    }
  }
  
  class Vue {
    constructor(options) {
      this._data = options.data;
      observer(this._data); //使对象变成可观察的
      /**新建一个watcher观察者对象，这时候Dep.target会指向这个Watcher对象*/
      new Watcher();
      
      /**在这里模拟render过程，为了触发test属性的get函数*/
      console.log('render_',this._data.test);
    }
  }
  ```

- ##### get进行依赖收集

- ##### set通过观察者来更新视图

