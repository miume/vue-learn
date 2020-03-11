## Vue.js路由

- vue.js路由允许通过不同的URL访问不同的内容

```js
npm install vue-router
```



- <router-link>是一个组件，该组件用于设置一个导航链接，切换不同HTML内容

  - <router-link>会被默认渲染成一个<a>标签

  - to：表示目标路由的链接。当被点击后，内部会立刻把to的值传到router.push()

    ```vue
    <router-link to='home'>Home</router-link>
    
    <router-link :to="'home'">Home </router-link>
    
    <router-link :to="{path: 'home'}">Home</router-link>
    
    <router-link :to="{name: 'user', params: {userId: 123}}">User</router-link>
    
    <router-link :to="{path: 'register', query: {plan: 'private'}}">Register</router-link>
    ```

  - replace

    ```vue
    <router-link :to="{path: '/abc'}" repplace>当点击时，会调用router.replace(),导航后不会留下history记录</router-link>
    ```

  - append

    ```vue
    <!--设置append属性后，则在当前路径前添加基路径
      如果从/a导航到/b，若配置了append则路径为 /a/b，否则为/b
    -->
    <router-link :to="{path: 'relative/path'}" append></router-link>
    ```

  - tag

    ```vue
    <!--设置tag属性，将router-link渲染成某种标签-->
    <router-link to="/foo" tag="li">foo</router-link>
    <!--渲染结果为-->
    <li>foo</li>
    ```

  - active-class：设置链接激活时使用的CSS类名（模糊匹配）

  - exact-active-class：配置当链接被精确匹配时，应该激活的class（精确匹配）

  - event：声明可以用来触发导航的事件。可以是一个字符串也可以是一个包含字符串的数组

    ```vue
    <router-link :to="{path: '/router'}" event="mouseover">Router link</router-link>
    ```

- <router-view></router-view>路由出口，路由匹配到的组件会渲染在这里

```vue
<div id='app'>
  <h1>
    学习vue-router
  </h1>
</div>
<router-link to="/foo">go to foo</router-link>
<router-link to="/bar">go to bar</router-link>
<!--路由出口
  路由匹配的组件将渲染在这里
-->
<router-view></router-view>
<script>
  //1、定义路由组件（也可以从其它文件import进来）
  const Foo = { template: '<div>foo</div>' }
  const Bar = { template: '<div>bar</div>' }
  
  //2、定义路由
  //每个路由应该映射一个组件
  const routes = [
    { path: '/foo', component: Foo },
    { path: '/bar', component: Bar }
  ]
  
  //3、创建router实例，然后传routes配置
  const router = new VueRouter({
    routes
  })
  
  //4、创建和挂载根实例
  const app = new Vue({
    router
  }).$mount('#app')
</script>
```

