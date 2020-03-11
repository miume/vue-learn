

## vue安装

```js
npm install vue-cli --global  //全局安装vue-cli脚手架
vue init webpack vue-demo     //创建一个基于webpack模版的新项目

//接着会进行一些配置，默认回车即可

cd ./vue-demo
npm install    //安装依赖
npm run dev    //启动项目
```



### vue条件语句

- v-if / v-else-if / v-else

- 可以在元素和template中使用v-if指令

  ```vue
  <div>
    <p v-if='seen'>v-if指令</p>
    <template v-if='ok'>
    	<h1>
        学习vue基础知识
      </h1>
  		<h2>
        慢慢积累，不要急于求成
      </h2>
    </template>
  </div>
  ```

- v-show：根据条件判断是否展示元素

  ```vue
  <div v-show='ok'></div>
  ```

###  

### vue循环语句

- v-for指令需要以site in sites形式的特殊语法

- v-for可以绑定数据到数组来渲染一个列表

  ```vue
  <div id='app'>
    <ol>
      <li v-for="site in sites" :key=site.name></li>
    </ol>
  </div>
  
  <script>
    new Vue({
      el: '#app',
      data: {
        sites: [
          {name: 'vue'},
          {name: 'react'},
          {name: 'angular'}
        ]
      }
    })
  </script>
  ```

- v-for可以迭代对象

  ```vue
  <ul>
    <li v-for="(value,key,index) in object" :key="value">{{value}}</li>
  </ul>
  ```



### vue计算属性computed

- computed计算属性是基于它的依赖缓存，只有相关依赖发生改变时才会重新取值

- methods，在重新渲染的时候，函数总会重新执行

  ```js
  
  ```

  



### vue监听属性watch

- 通过监听属性watch来监听数据data中的变化

  ```vue
  <div>
    <input v-model='kilometers'>
    <input v-model='meters'>
  </div>
  
  <script>
  	new
  </script>
  ```

  

### vue样式绑定 (字符串-对象-数组)

- class与style是HTML元素的属性，用于设置元素的样式，可以用v-bind来设置样式属性

- vue在处理class和style时，表达式的结果类型除了**字符串**之外，还可以是**对象**或**数组**

  - ##### class属性绑定

    ```vue
    <div v-bind:class="{active: isActive}">
      v-bind:class用来给元素设置样式
    </div>
    
    <div class='static' v-bind:class="{active: isActive, 'text-danger': hasError}">
    </div>
    
    <div v-bind:class='classObject'>
      可以直接绑定数据里的一个对象
    </div>
    
    <div v-bind:class="[activeClass,errorClass]"> 
      数组语法
    </div>
    
    <div v-bind:class="[errorClass, isActive ? activeClass : '']">
      三元表达式来切换列表中的class
    </div>
    
    <script>
      new Vue({
        el: '#app',
        data: {
          classObject: {
            active; true,
            'text-danger': true
          },
          activeClass: 'active',
          errorClass: 'text-danger'
        }
      })
    </script>
    
    <style>
    .active {
    	width: 100px;
    	height: 100px;
    	background: green;
    }
    .text-danger {
    	background: red;
    }
    </style>
    ```

    

  - #### style内联样式

    ```vue
    <div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}">
      可以直接在v-bind：style直接设置样式
    </div>
    
    <div v-bind:style="styleObject">
      v-bind：class可以直接绑定一个样式对象
    </div>
    
    <div v-bind:class="[baseStyles, overridingStyles]">
      v-bind:class可以使用数组将多个样式对象应用到一个元素上
    </div>
    
    <script>
    	new Vue({
        el: '#app',
        data: {
          styleObject: {
            color: 'green',
            fontSize: '30px'
          },
          activeColor: 'red',
          fontSize: 20
        }
      })
    </script>
    ```

    

  ### vue事件处理器

  - #### 事件监听可以使用v-on指令，简写为@

    ```vue
    <div id='app'>
      <button v-on:click="couter += 1">
        点击加1
      </button>
      <p>
        这个按钮被点击了{{counter}}次
      </p>
    </div>
    
    <script>
    new Vue({
      el: '#app',
      data: {
        counter: 0
      }
    })
    </script>
    ```

  - #### 事件修饰符

    - vue.js为v-on提供了事件修饰符来处理DOM事件细节

    - vue.js通过由（.）表示的指令后缀来调用修饰符

      - .stop（表示event.stopPropagation阻止冒泡）

      - .prevent（event.preventDefault）

      - .capture

      - .self

      - .once

        ```vue
        <!--阻止单击事件冒泡-->
        <a v-on:click.stop='doThis'></a>
        
        <a v-on:submoit.stop.prevent='doThat'></a>
        
        <div v-on:click.capture='doThis'>...</div>
        <!--只当事件在该元素本身（而不是子元素）触发时触发回调-->
        <div v-on:click.self='doThat'></div>
        <!--click事件只能点击一次-->
<a v-on:click='doThis'></a>
        ```
      
    
- #### 按键修饰符
  
    - vue允许v-on在监听键盘事件时添加按键修饰符
  
      - .enter
      - .tab
      - .delete
      - .esc
      - .space
      - .up
      - .down
      - .left
      - .right
      - .ctrl
      - .alt
      - .shift
      - .meta
  
      ```vue
      <input v-on:keyup.13='submit'>
      ```
  
  
  
  ### vue表单
  
  - 可以使用v-model指令在表单控件元素上创建双向数据绑定
  
    ```vue
    <div id='app'>
      <!--input和textarea元素-->
      <input v-model='message' placeholder='请输入...'>
      <textarea v-model='textMessage' placeholder='请输入多行文本'> 		    </textarea>
     
      <!--实现复选框的双向绑定-->
      <div>
        <p>
          单个复选框
        </p>
        <input type='checkbox' id='checkbox' v-model="checked"><label for="checkbox">{{checked}}</label>
        
        <p>多个复选框</p>
        <input type='checkbox' id='vue' value='vue' v-model='checkedNames'><label for='vue'>Vue</label>
         <input type='checkbox' id='react' value='react' v-model='checkedNames'><label for='vue'>React</label>
         <input type='checkbox' id='angular' value='angular' v-model='checkedNames'><label for='angular'>Angular</label>
        <br>
        <span>选择的值为{{checkNames}}</span>
      </div>
      
      
      <!--单选按钮-->
       <p>单选框</p>
        <input type='radio' id='vue' value='vue' v-model='picked'><label for='vue'>Vue</label>
         <input type='radio' id='react' value='react' v-model='picked'><label for='vue'>React</label>
         <input type='radio' id='angular' value='angular' v-model='picked'><label for='angular'>Angular</label>
        <br>
        <span>选择的值为{{picked}}</span>
      
      
      <!--select列表-->
      <select v-model='selected' name='style'>
        <option value=''>请选择一个学习网站</option>
        <option value='vue'>Vue</option>
        <option value='react'>React</option>
      </select>
      <br>
      <p>
        选择的学习框架是{{selected}}
      </p>
    </div>
    
    <script>
    new Vue({
      el: '#app',
      data: {
        message: '',
        textMessage: '',
        checkedNames: [],
        checked: false,
        picked: 'vue',
        selected: ''
      }
    })
    </script>
    ```
  
  - 修饰符
    - .lazy
    - .number
    - .trim



### Vue.js组件

- 组件component可以扩展HTML元素，封装可重用的代码

- #### 全局组件

  ```vue
  Vue.component(tagName,option)  //tagName-组件名，options为配置项
  
  Vue.component('runoob', {
    template: '<h1>自定义组件!</h1>'
  })
  ```

  

- #### 局部组件

  - 在实例选项中注册局部组件

    ```vue
    <script>
      var Child = {
        template: '<h1>自定义组件</h1>'
      }
      
      //创建根实例
      new Vue({
        el: '#app',
        components: {
          'runoob': Child
        }
      })
    </script>
    ```

- #### props是父组件用来传递数据的一个自定义属性

  ```vue
  <div id='app'>
    <child message='父组件传递数据'></child>
  </div>
  
  <script>
    Vue.component({
      props: ['message'],  //申明props
      template: '<span>{{message}}</span>'
    })
    
    //创建根实例
    new Vue({
      el: '#app'
    })
  </script>
  ```

- #### 动态props

  - 可以用v-bind动态绑定props的值到父组件的数据中。每当父组件的数据变化时，该变化也会传导给子组件

    ```vue
    <div id='app'>
      <input v-model='parentMsg'>
      <br>
      <child v-bind:message='parentMsg'></child>
    </div>
    
    <script>
      Vue.component('child', {
        props: ['message'],
        template: '<span>{{ message }}</span>'
      })
      
      //创建根实例
      new Vue({
        el: '#app',
        data: {
          parentMsg: '父组件内容'
        }
      })
    </script>
    ```

- #### props验证

  - 组件可以为props指定验证要求

  - 可以为props中的值提供一个带有验证需求的对象，而不是一个字符串数组

    ```vue
    <script>
      Vue.component('my-component', {
        propA: [String, Number], //基础的类型检查
        required: true,  //	必填项
        default: 100, //默认值
        validator: function(value) { //自定义验证函数
    
        }
    })
    </script>
    ```

- #### 自定义事件

  - 父组件使用props传递数据给子组件，而子组件则使用自定义事件将数据传递回去

  - v-on绑定自定义事件

    - $on(eventName)监听事件
    - $emit(eventName)触发事件

    ```vue
    <div id='app'>
      <p>
        {{total}}
      </p>
      <button-counter v-on:increment="incrementTotal"></button-counter>
      <button-counter v-on:increment="incrementTotal"></button-counter>
    </div>
    
    <script>
      //组件中的data必须是一个函数：这样每个实例可以维护一份被返回对象的独立的拷贝，实例间互不影响
    	Vue.component('button-counter', {
        template: '<button v-on:click="incrementHandler">{{ counter }}</button>',
        data: function() {
          return {
            couter: 0
          }
        },
        methods: {
          incrementHandler: function () {
            this.counter += 1;
            this.$emit('increment');  //触发父组件的方法
          }
        }
      })
      
      new Vue({
        el: '#app',
        data: {
          total: 0
        },
        methods: {
          incrementTotal: function () {
            this.total += 1;
          }
        }
      })
    </script>
    ```

- Vue.js过渡和动画

  - vue提供了内置的过渡封装组件，该组件用于包裹要实现过渡效果的组件

  ```vue
  <transition name="nameoftransition">
    <div></div>
  </transition>
  ```

  

- Vue.js混入

  - 混入mixins定义了一部分可复用的方法或者计算属性。

  - 当组件使用混入对象时，所=所有混入对象的选项将被混入该组件本身的选项

    ```vue
    <script>
      //定义一个混入对象
      var myMixin = {
        created: function () {
          this.startmixin()
        },
        methods: {
          startmixin: function () {
            document.write("欢迎来到混入实例")
          }
        }
      }
      
      var Component = Vue.extend({
        mixins: [myMixin]
      })
    </script>
    ```

  - 选项合并

    - 当组件和混入对象含有同名属性created时，会先执行混入对象
    - 当组件和混入对象中methods选项中有相同的函数名，则Vue实例优先级会较高

- Vue.js Ajax （axios）

  - Axios是一个基于Promise的Http库，可以用在浏览器和node.js中

    ```js
    npm install axios
    
    axios({
      url: url,
      method: 'put'/'get'/'post'/'delete'，
      params,
      data
    }).then( response => {
      
    }).catch( error => {
      
    } )
    ```

  - axios.all([axios1, axios2])：执行多个并发请求