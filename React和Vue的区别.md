## React和Vue的区别

1. #### 数据是不是可变的

   - ##### React整体是函数式思想，把组件设计成纯组件，状态和逻辑通过参数传入。所有React中是单向数据流

   - ##### React提倡单向数据流，onChange/setState()模式

   ```js
   class Home extends React.Component() {
     componentWillUnMount() {
       this.setState = () => {
         return;
       }
     }
     constructor() {
       super(props);
       
       //设置initial state
       this.state = {
         data: []
       }
       
       //ES6 类中函数必须手动绑定
       this.handleChange = this.handleChange.bind(this)
     }
     
     handleChange(event) {
       this.setState({
         text: event.target.value
       })
     }
   }
   
   //React 生命周期
   
   constructor
   componentWillMount
   render
   componneDidMount
   
   //更新
   componentWillRecieveProps
   shouldComponentUpdate
   componentWillUpdate
   render
   componentDidUpdate
   
   //注销期
   componentWillUnMount
   ```

   - Vue是基于可变数据的
     - 通过对每一个属性建立Watcher来监听，当属性变化时，响应式的更新对应虚拟DOM

   ```vue
   /**在模版template里面写html*/
   <template>
   </template>
   
   /**在style里面写css*/
   <style>
   </style>
   
   /**在script里面写js*/
   <script>
     import Ant from './ant.vue';
     
     export default {
       components: , //用，逗号隔开
       mixins: [],
       data () {
         return {
           name: 'javaScript'
         }
       },
       computed:{
         
       },
       methods: {
         
       },
       watch: {
         
       },
       beforeCreated() {
       },
       created() {
         
       },
       beforeMount() {
         
       },
       Mounted() {
         
       },
       beforeUpdated() {
         
       },
       updated() {
         
       },
       beforeDertroy() {
         
       },
       destryed() {
         
       },
       
     }
   </script>
   ```

   

2. #### 通过js来操作一切，还是用各自的处理方式

   - React是all in js ，通过js来生成html，通过js来操作css，所以设计jsx
   - Vue是把html，css，js整合到一起，分别保留各自的处理方式

3. #### 类式的组件写法，还是声明式的写法

   - React是类式的写法，api很少
   - Vue是声明式的写法，