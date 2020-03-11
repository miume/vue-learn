## template模板是怎样通过Compile编译的

#### compile-目前有点难理解。。。

```vue
<div :class="c" class="demo" v-if="isShow">
	<span v-for="item in items">{{item}}</span>
</div>
```

```js
var html = '<div :class="c" class="demo" v-if="isShow"><span v-for="item in items">{{item}}</span></div>'
```

- parse

  - parse会用正则等方式将template模板进行字符串解析，得到指令、class、style等数据，形成AST（抽象语法树）

- optimize

  - 优化：静态节点不必根据数据变化而产生变化，这些节点没有对比的需求
  - 标记静态节点，在patch操作时直接跳过这些被标记的节点的比对，从而达到优化的目的
  - 每个节点会加上static属性，用来标记是否是静态的

- generate

  - 将AST转换为最终得到render function字符串，最终得到render的字符串以及stasticRenderFns字符串

  