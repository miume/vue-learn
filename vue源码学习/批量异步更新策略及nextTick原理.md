##批量异步更新策略及nextTick原理

- Vuejs如何修改data中的数据后修改视图
  - Setter -> Dep -> Watcher -> patch --> 视图

### 为什么需要异步更新？

```vue
<template>
	<div>
    <div>{{number}}</div>
    <div @click="handleClick">click</div>
  </div>
</template>

<script>
  export default {
  data () {
    return {
      number: 0
    }
  },
  methods: {
    handClick () {
      for(let i = 0; i < 1000; i++) {
        this.number++;
      }
    }
  }
}
</script>
```

问题： 当点击click按钮的时候，会被循环增加1000次，会触发number的setter1000次，DOM会被更新1000次！！！

解决：Vuejs在默认情况下，每次触发某个数据的setter方法后，对应的Watcher对象会被push进一个队列queue中，在下一个tick时候将queue全部出队，执行



### nextTick

- Vuejs实现一个nextTick函数，传入一个回调函数cb，这个cb会被存储到一个队列中，在下一个tick时触发队列中的所有cb事件
- 同一个tick时，同一个Watcher只会被执行一次，也就是队列queue中不应该出现重复的Watcher对象

- Vuejs源码用Promise、setTimeOut、setImmediate等方式在microtask中创建一个事件，目的是在当前调用栈执行完毕以后才会去执行这个事件

- 下面用setTimeout来模拟这个方法

  ```js
  let callbacks = [];
  let pending = false;
  function nextTick (cb) {
    callbacks.push(cb);
    if (!pending) {
      pending = true;
      setTimeout(flushCallbacks, 0);
    }
  }
  
  function flushCallbacks () {
    pending = false;
    const = copies = callbacks.slice(0);
    callbacks.length = 0; //可以达到清空数组的目的
    for (let i = 0; i < copies.length; i++) {
      copies[i]();
    }
  }
  ```

- 问题：当将number增加1000次时，先将对应的Watcher对象给push进一个队列queue中去，等下一个tick的时候再去执行。**1000次，都是同一个Watcher对象，该怎么处理？？？**

  - 需要执行一个过滤操作，同一个Watcher在同一个tick的时候应该只被执行一次，即queue中不应该出现重复的Watcher对象

  ```js
  let uid = 0;
  class Watcher {
    constructor () {
      this.id = ++uid;
    }
    
    update () {
      console.log('watch' + this.id + ' update');
      queueWatcher(this);//将Watcher对象自身传递给queueWatcher方法
    }
    
    run () {
      console.log('watch' + this.id + ' 视图更新啦！！')
    }
  }
  
  let has = {};
  let queue = [];
  let waiting = false;
  
  function queueWater(Watcher) {
    const id = watcher.id;
    if (has[id] == null) { //用来判断是否已经存在相同的Watcher对象
      has[id] = true;
      queue.push(watcher);
      
      if(!waiting) {
        waiting = true;
        nextTick(flushSchedulerQueue);
      }
    }
  }
  
  function flushSchedulerQueue () {
    let watcher, id;
    for (let index = 0; index < queue.length; index++) {
      watcher = queue[index];
      id = watcher.id;
      has[id] = null;
      watcher.run();
    }
    waiting = false;
  }
  ```

  