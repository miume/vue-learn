### vue起步

- 每个vue应用都需要通过实例化Vue来实现

  ```vue
  <script>
  var vm = new Vue({
    el: '#app',
    data: {
      message: '<h1>菜鸟教程</h1>'
    }
  })
  </script>
  ```

  ### vue模版语法

  - Vue.js使用急于HTML模版语法，允许开发者声明式地将DOM绑定至底层Vue实例的数据

- 插值

  - 文本 （数据最常见的形式使用双大括号{{}} 的文本差值）

  - v-html用来输出html代码

    ```js
    <div>
      <div v-html='message'></div>
    </div>
    ```

  - 属性-HTML属性的值应使用v-bind指令

  - 表达式-vue.js提供了完成的js表达式支持

  - 指令-**带有v-前缀的特殊属性**

    - 指令用于在表达式的值改变时，将某些行为应用到DOM上

    ```js
    <div id='app'>
      <p v-if='seen'>v-if指令将根据表达式seen的值来决定是否插入p元素</p>
    </div>
    ```

  - 参数-参数在指令后以冒号指明

    - v-bind

      ```js
      //href是参数，告知v-bind指令将该元素的href属性与表达式url值绑定
      <div>
        <a v-bind:href='url'>菜鸟教程</a>
      </div>
      ```

    - v-on：用于监听DOM事件

      ```js
      <div v-on:click='doSomethong'></div>
      ```

  - 修饰符-以半圆角.指明的特殊后缀，用于指出一个指令应该以特殊方式绑定

    - .prevent修饰符告诉v-on指令对于触发的事件调用event.preventDefault()

  - 用户输入

    - 在input输入框中可用使用v-model指令来实现双向数据绑定
    - v-model指令用来在input、select、textarea、checkbox、radio等表单控件元素上创建双向数据绑定，根据表单上的值，自动更新绑定元素的值

  - 过滤器

    - 自定义过滤器，被用作一些常见的文本格式化

      ```vue
      <div>
        {{message | capitalize}}  
      </div>
      
      <script>
        new Vue({
          el: 'app',
          data: {
            message: 'vue'
          },
          filters: {
            capitalize: function (value) {
              if (!value) return ''
              value = value.toString()
              return value.charAt(0).toUpperCase() + value.slice(1)
            }
          }
        })
      <script>
      ```

    - 缩写
      - v-bind缩写：
      - v-on缩写@

    