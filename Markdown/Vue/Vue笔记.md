# 基础

## 组件通信方式

---

### props/@on+$emit

通过 `props` 可以把父组件的消息传递给子组件：

```js
// parent.vue    
<child :title="title"></child>
 
// child.vue
props: {
    title: {
        type: String,
        default: '',
    }
}
```

这样一来 `this.title` 就直接拿到从父组件中传过来的 `title` 的值了。注意，不应该在子组件内部直接改变 `prop`。而通过 `@on+$emit` 组合可以实现子组件给父组件传递信息。

```js
// parent.vue
<child @changeTitle="changeTitle"></child>
 
// child.vue
this.$emit('changeTitle', 'bubuzou.com')
```

###  $attrs/$listeners

`ue_2.4` 中新增的 `$attrs/$listeners` 可以进行跨级的组件通信。`$attrs` 包含了父级作用域中不作为 `prop` 的属性绑定（`class` 和 `style` 除外）：

```js
// 父组件 index.vue
<list class="list-box" title="标题" desc="描述" :list="list"></list>
 
// 子组件 list.vue
props: {
    list: [],
},
mounted() {
    console.log(this.$attrs)  // {title: "标题", desc: "描述"}
}
```

在上面的父组件 index.vue 中我们给子组件 list.vue 传递了4个参数，但是在子组件内部 props 里只定义了一个 list，那么此时 this.$attrs 的值是什么呢？首先要去除 props 中已经绑定了的，然后再去除 class 和 style，最后剩下 title 和 desc 结果和打印的是一致的。基于上面代码的基础上，我们在给 list.vue 中加一个子组件：

```js
// 子组件 list.vue
<detail v-bind="$attrs"></detial>
 
// 孙子组件 detail.vue
// 不定义props，直接打印 $attrs
mounted() {
    console.log(this.$attrs)  // {title: "标题", desc: "描述"}
}
```

在子组件中我们定义了一个 `v-bind="$attrs"` 可以把父级传过来的参数，去除 `props`、`class` 和 `style` 之后剩下的继续往下级传递，这样就实现了跨级的组件通信。

$attrs 是可以进行跨级的参数传递，实现父到子的通信；同样的，通过 $listeners 用类似的操作方式可以进行跨级的事件传递，实现**子到父**的通信。$listeners 包含了父作用域中不含` .native` 修饰的 v-on 事件监听器，通过 v-on="$listeners" 传递到子组件内部。

```js
// 父组件 index.vue
<list @change="change" @update.native="update"></list>
 
// 子组件 list.vue
<detail v-on="$listeners"></detail>
 
// 孙子组件 detail.vue
mounted() {
    this.$listeners.change()
    this.$listeners.update() // TypeError: this.$listeners.update is not a function
}
```

### provide/inject

`provide/inject` 组合以允许一个祖先组件向其所有子孙后代注入一个依赖，可以注入属性和方法，从而实现跨级父子组件通信。在开发高阶组件和组件库的时候尤其好用。

```js
// 父组件 index.vue
data() {
    return {
        title: 'bubuzou.com',
    }
}
provide() {
    return {
        detail: {
            title: this.title,
            change: (val) => {
                console.log( val )
            }
        }
    }
}
 
// 孙子组件 detail.vue
inject: ['detail'],
mounted() {
    console.log(this.detail.title)  // bubuzou.com
    this.detail.title = 'hello world'  // 虽然值被改变了，但是父组件中 title 并不会重新渲染
    this.detail.change('改变后的值')  // 执行这句后将打印：改变后的值 
}
```

> `provide` 和 `inject` 的绑定对于原始类型来说并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的 property 还是可响应的。这也就是为什么在孙子组件中改变了 `title`，但是父组件不会重新渲染的原因。

### EventBus

EventBus能进行**兄弟组件之间的通信**，甚至任意2个组件间通信。利用 `Vue` 实例实现一个 `EventBus` 进行信息的发布和订阅，可以实现在任意2个组件之间通信。有两种写法都可以初始化一个 `eventBus` 对象：

写法1：通过导出一个 Vue 实例，然后再需要的地方引入

```js
// eventBus.js
import Vue from 'vue'
export const EventBus = new Vue()

//使用 EventBus 订阅和发布消息：
import {EventBus} from '../utils/eventBus.js'

// 订阅处
EventBus.$on('update', val => {})

// 发布处
EventBus.$emit('update', '更新信息')
```

写法2：在 main.js 中初始化一个全局的事件总线

```js
// main.js
Vue.prototype.$eventBus = new Vue()

// 需要订阅的地方
this.$eventBus.$on('update', val => {})

// 需要发布信息的地方
this.$eventBus.$emit('update', '更新信息')
```

如果想要移除事件监听，可以这样来：

```js
this.$eventBus.$off('update', {})
```

上面介绍了两种写法，推荐使用第二种全局定义的方式，可以避免在多处导入 EventBus 对象。这种组件通信方式只要订阅和发布的顺序得当，且事件名称保持唯一性，理论上可以在任何 2 个组件之间进行通信，相当的强大。但是方法虽好，可不要滥用，建议只用于简单、少量业务的项目中**，如果在一个大型繁杂的项目中无休止的使用该方法，将会导致项目难以维护**。

### Vuex进行全局的数据管理

Vuex 是一个专门服务于 Vue.js 应用的状态管理工具。适用于中大型应用。Vuex 中有一些专有概念需要先了解下：

- State：用于数据的存储，是 store 中的唯一数据源；

- Getter：类似于计算属性，就是对 State 中的数据进行二次的处理，比如筛选和对多个数据进行求值等；
- Mutation：类似事件，是改变 Store 中数据的唯一途径，只能进行同步操作；
- Action：类似 Mutation，通过提交 Mutation 来改变数据，而不直接操作 State，可以进行异步操作；
- Module：当业务复杂的时候，可以把 store 分成多个模块，便于维护；

对于这几个概念有各种对应的 map 辅助函数用来简化操作，比如 mapState，如下三种写法其实是一个意思，都是为了从 state 中获取数据，并且通过计算属性返回给组件使用。

```js
computed: {
    count() {
        return this.$store.state.count
    },
    ...mapState({
        count: state => state.count
    }),
    ...mapState(['count']),
},
```

又比如 `mapMutations`， 以下两种函数的定义方式要实现的功能是一样的，都是要提交一个 `mutation` 去改变 `state` 中的数据：

```js
methods: {
    increment() {
        this.$store.commit('increment')
    },
    ...mapMutations(['increment']),
}
```

接下来就用一个极简的例子来展示 `Vuex` 中任意2个组件间的状态管理。

```js
//新建 store.js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
    
export default new Vuex.Store({
    state: {
        count: 0,
    },
    mutations: {
        increment(state) {
            state.count++
        },
        decrement(state) {
            state.count--
        }
    },
})
```

```js
//创建一个带 store 的 Vue 实例
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './utils/store'
    
new Vue({
    router,
    store,
    render: h => h(App)
}).$mount('#app')
```

```vue
<!--任意组件 A 实现点击递增-->
<template>
    <p @click="increment">click to increment：{{count}}</p>
</template>
<script>
import {mapState, mapMutations} from 'vuex'
export default {
    computed: {
        ...mapState(['count'])
    },
    methods: {
        ...mapMutations(['increment'])
    },
}
</script>
```

```vue
<!--任意组件 B 实现点击递减-->
<template>
    <p @click="decrement">click to decrement：{{count}}</p>
</template>
<script>
import {mapState, mapMutations} from 'vuex'
export default {
    computed: {
        ...mapState(['count'])
    },
    methods: {
        ...mapMutations(['decrement'])
    },
}
</script>
```

以上只是用最简单的 vuex 配置去实现组件通信，当然真实项目中的配置肯定会更复杂，比如需要对 State 数据进行二次筛选会用到 `Getter`，然后如果需要异步的提交那么需要使用Action，再比如如果模块很多，可以将 store 分模块进行状态管理。

### **Vue.observable实现mini vuex**

这是一个 `Vue2.6` 中新增的 `API`，用来让一个对象可以响应。我们可以利用这个特点来实现一个小型的状态管理器。

```js
// store.js
import Vue from 'vue'
 
export const state = Vue.observable({
    count: 0,
})
 
export const mutations = {
    increment() {
        state.count++
    }
    decrement() {
        state.count--
    }
}
```

```vue
<!--parent.vue-->
<template>
    <p>{{ count }}</p>
</template>
<script>
import { state } from '../store'
export default {
    computed: {
        count() {
            return state.count
        }
    }
}
</script>
```

```js
// child.vue
import  { mutations } from '../store'
export default {
    methods: {
        handleClick() {
            mutations.increment()
        }
    }
}
```

### children/root

通过给子组件定义 `ref` 属性可以使用 `$refs` 来直接操作子组件的方法和属性。

```vue
<child ref="list"></child>
```

比如子组件有一个 `getList` 方法，可以通过如下方式进行调用，实现父到子的通信：

```js
this.$refs.list.getList()
```

## 修饰符

---

### 表单修饰符

表单类的修饰符都是和 v-model 搭配使用的，比如：v-model.lazy、v-model-trim 以及 v-model.number 等。

- **.lazy**：对表单输入的结果进行延迟响应，通常和 v-model 搭配使用。正常情况下在 input 里输入内容会在 p 标签里实时的展示出来，但是加上 .lazy 后则需要在输入框失去焦点的时候才触发响应。

```vue
<input type="text" v-model.lazy="name" />
<p>{{ name }}</p>
```

- **.trim**：过滤输入内容的首尾空格，这个和直接拿到字符串然后通过 str.trim() 去除字符串首尾空格是一个意思。
- **.number**：如果输入的第一个字符是数字，那就只能输入数字，否则他输入的就是普通字符串。

### 事件修饰符

`Vue` 的事件修饰符是专门为 `v-on` 设计的，可以这样使用：`@click.stop="handleClick"`，还能串联使用：`@click.stop.prevent="handleClick"`。

```vue
<div @click="doDiv">
    click div
    <p @click="doP">click p</p>
</div>
```

- **.stop**：阻止事件冒泡，和原生 event.stopPropagation() 是一样的效果。如上代码，当点击 p 标签的时候，div 上的点击事件也会触发，加上 .stop 后事件就不会往父级传递，那父级的事件就不会触发了。
- **.prevent**：阻止默认事件，和原生的 event.preventDefault() 是一样的效果。比如一个带有 href 的链接上添加了点击事件，那么事件触发的时候也会触发链接的跳转，但是加上 .prevent 后就不会触发链接跳转了。
- **.capture**：默认的事件流是：捕获阶段-目标阶段-冒泡阶段，即事件从最具体目标元素开始触发，然后往上冒泡。而加上 .capture 后则是反过来，外层元素先触发事件，然后往深层传递。
- **.self**：只触发自身的事件，不会传递到父级，和 .stop 的作用有点类似。
- **.once**：只会触发一次该事件。
- **.passive**：当页面滚动的时候就会一直触发 onScroll 事件，这个其实是存在性能问题的，尤其是在移动端，当给他加上 .passive 后触发的就不会那么频繁了。
- **.native**：现在在组件上使用 v-on 只会监听自定义事件 (组件用 $emit 触发的事件)。如果要监听根元素的原生事件，可以使用 .native 修饰符，比如如下的 el-input，如果不加 .native 当回车的时候就不会触发 search 函数。

```vue
<el-input type="text" v-model="name" @keyup.enter.native="search"></el-input>
```

> 串联使用事件修饰符的时候，需要注意其顺序，同样2个修饰符进行串联使用，顺序不同，结果大不一样。 `@click.prevent.self` 会阻止所有的点击事件，而 `@click.self.prevent` 只会阻止对自身元素的点击。

### **鼠标按钮修饰符**

- `.left`：鼠标左键点击；
- `.right`：鼠标右键点击；
- `.middle`：鼠标中键点击；

### **键盘按键修饰符**

`Vue` 提供了一些常用的按键码：

- `.enter`
- `.tab`
- `.delete` (捕获“删除”和“退格”键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

另外，你也可以直接将 `KeyboardEvent.key` 暴露的任意有效按键名转换为 `kebab-case` 来作为修饰符，比如可以通过如下的代码来查看具体按键的键名是什么：

```vue
<input @keyup="onKeyUp">
 
onKeyUp(event) {
    console.log(event.key)  // 比如键盘的方向键向下就是 ArrowDown
}
```

### .exact修饰符

`.exact` 修饰符允许你控制由精确的系统修饰符组合触发的事件。

```vue
<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button v-on:click.ctrl="onClick">A</button>
 
<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button v-on:click.ctrl.exact="onCtrlClick">A</button>
 
<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button v-on:click.exact="onClick">A</button>
```

### .sync/$emit('update:value)修饰符

`.sync` 修饰符常被用于子组件更新父组件数据。直接看下面的代码：

```vue
// parent.vue
<child :title.sync="title"></child>
 
// child.vue
this.$emit('update:title', 'hello')
```

> 注意带有 .sync 修饰符的 v-bind 不能和表达式一起使用

如果需要设置多个 `prop`，比如：

```vue
<child :name.sync="name" :age.sync="age" :sex.sync="sex"></child>
```

可以通过 `v-bind.sync` 简写成这样：

```vue
<child v-bind.sync="person"></child>
 
person: {
    name: 'bubuzou',
    age: 21,
    sex: 'male',
}
```

`Vue` 内部会自行进行解析把 `person` 对象里的每个属性都作为独立的 `prop` 传递进去，各自添加用于更新的 `v-on` 监听器。而从子组件进行更新的时候还是保持不变，比如：

```js
this.$emit('update:name', 'hello')
```



## Cli2更换浏览器图标(favicon)的方式

---

把准备好的图片文件放到根目录，修改 build 文件夹下 webpack.prod.conf.js （生产环境）和 webpack.dev.conf.js（开发环境） 文件：

```js
var path = require('path') // 一般vue-cli会自带，当然了，没有的话，再手动引入

// HtmlWebpackPlugin 中添加 favicon
new HtmlWebpackPlugin({
  filename: 'index.html',
  template: 'index.html',
  favicon: path.resolve('favicon.ico'), // 引入图片地址
  inject: true
})
```



## V-model的修饰符

---

[v-model的修饰符 - 简书 (jianshu.com)](https://www.jianshu.com/p/2ad32bb94cc2)

####  v-model.lazy

使用`.lazy`修饰符，会转变为在`change`事件中同步，简单粗暴的讲就是，此时数据并不会像我们以前见到的`v-model`那样实时更新数据，而是在**失去焦点或者回车时更新**，见下面示例代码

####  v-model.number

需要明确的是，我们输入的数字，虽然输入的是数字，但实际上是`String`，不论你是否让type=‘number’，那么`.number`修饰符就可以将输入的内容转化为`Number`类型

####  v-model.trim

自动过滤输入的首尾空格

## 数据双向绑定 

---

```vue
v-bind:value.sync

$emit('update:keyName',Data)

```

作用：对一个 prop 进行“双向绑定”

应用场景：关闭弹框组件

实例：

子组件：

```vue
<template>
  <div v-if="ifshow">
    <p>子组件：{{title}}</p>
    <button @click="close">关闭弹窗</button>
  </div>
</template>
<script>
export default {
    props:{
        title:{
            type:String
        },
        ifshow:{
            type:Boolean
        }

    },
    methods:{
        close(){
            this.$emit('update:ifshow',false);
        }
    }
};
</script>     

```

父组件：

```vue
<template>
  <div>
    <p>父组件：{{title}}  <button @click="show">展示弹窗</button></p>
    <child2 :title="title" :ifshow.sync='ifshow'></child2>
  </div>
</template>
<script>
import child2 from "./child2.vue";
export default {
  components: {
    child2
  },
  data() {
    return {
      title: "努力着，从不放弃",
      ifshow:false
    };
  },
  methods: {
    show(){
      this.ifshow = true;
    }
  }
};
</script>

```

## 理解 v-model、v-bind、$attrs、 $listener 和 .sync 及其在跨组件数据双向绑定上的应用

### v-model 的本质

```js
<MyList v-model="lovingVue"></MyList
<!-- 其实是以下的语法糖 -->
<MyList :value="lovingVue" @input="(data) => lovingVue = data"></MyList>
```

v-model 的本质是：父组件给子组件传一个名为 `value` 的`prop`，然后对子组件挂载一个名为`input` 的事件监听。当子组件手动 `emit` 这个input 事件时，携带的载荷自动赋值到`v-model`后绑的父组件变量上。（所以其实不是自动双向绑定，还是需要手动`emit` input事件的。）

### v-bind的本质

本质是 批量传入props

```vue
<Component v-bind="{a: foo, b: bar, c: baz}" />
 <!-- 相当于 -->
<Component
    v-bind:a=foo
    :b=bar
    :c=baz 
/>
```

### $attrs的本质

父组件以形如:foo="xxx"或者v-bind="{age:12}"传给子组件的属性，但凡没有被子组件的props接收的，都会被扔到子组件的$attrs里去。

（另外，被props指名接收的，都放入子组件的$props里。）

### $listeners的本质

父组件以 @eventName="fn" 或者 v-on:eventName="fn" 对子组件挂载事件监听。对子组件而言，父组件监听的事件都放在$listeners里。

如果对后代组件使用v-on="$listeners"，则相当于对后代组件批量挂载了父组件对自己的事件监听。因此后代组件的emit也会触发父组件的事件方法。

层级关系说明：父组件>子组件>后代组件

![image-20220124141651209](https://gitee.com/letefly/NoteImages/raw/master/img/2023/02/image-20220124141651209.png)

### .sync的本质

```vue
<Component 
    :foo="val"
    @update:foo="(payload)=>{ val = payload }"
/>
<!-- 实质是 语法糖 -->
<Component :foo.sync="val" />
```

```js
// 子组件使用
this.$emit('update:foo', payload)
```

实质是更方便地实现数据双向绑定。即数据流向父->子天然实现，子->父只需子组件emit相关事件即可，从而实现双向绑定。

```vue
<!-- A Component -->
<template>
    <BComponent v-model="modalShow"></BComponent>
</template>
 
<script>
    this.modalShow = true // 往下传递状态，直接到C
</script>
 
 
<!-- B Component -->
<template>
    <CComponent v-bind="$attrs" v-on="$listeners"></CComponent>
</template>
 
<script>
    // 中间组件也可以中途向两边更改状态,一致性需要手动保持
    // this.$attrs.value = true
    // this.$emit('input', true)
</script>
 
 
<!-- C Component -->
<script>
    props: {
        value:boolean
    },
    methods: {
       handleClose(){
           this.$emit('input', false) // 往上emit状态，直接到A
       } 
    }
</script>
```

总结一下，主要是中间传递组件的 v-bind="$attrs" v-on="$listeners"。

父组件更改了数据，会因为 $atrrs 的传递自然地传递到后代组件。而后代组件 emit 一个 input 事件，会因为 $listeners 自然地冒到父组件处。又因为父组件的 v-model 而自动把新数据赋值到父组件变量上，因此实现了所谓的"双向绑定"。

## Require与Import

---

[vue中require与import之间的区别_CaseyWei-CSDN博客](https://blog.csdn.net/caseywei/article/details/90710749?utm_term=%E5%9C%A8vue%E9%A1%B9%E7%9B%AE%E4%B8%AD%E7%9A%84require&utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~all~sobaiduweb~default-0-90710749&spm=3001.4430)

- require的基本语法

  在导出的文件中定义module.export,导出的对象的类型不予限定（可以是任何类型，字符串，变量，对象，方法），在引入的文件中调用require()方法引入对象即可

```js
//a.js中
module.export = {
    a: function(){
     console.log(666)
  }
}
```

```js
//b.js中
var obj = require('../a.js')
obj.a()  //666
```

【注】:本质上是将要导出的对象赋值给module这个的对象的export属性，在其他文件中通过require这个方法访问该属性

- import的基本语法
  核心概念：导出的对象必须与模块中的值一一对应，换一种说法就是**导出的对象与整个模块进行解构赋值**。对的，你没有听错。抓住重点，解构赋值！！！！！

  ```js
  //a.js中
  export default{    //（最常使用的方法,加入default关键字代表在import时可以使用任意变量名并且不需要花括号{}）
       a: function(){
           console.log(666)
     }
  }
   
  export function(){  //导出函数
   
  }
   
  export {newA as a ,b,c}  //  解构赋值语法(as关键字在这里表示将newA作为a的数据接口暴露给外部，外部不能直接访问a)
   
  //b.js中
  import  a  from  '...'  //import常用语法（需要export中带有default关键字）可以任意指定import的名称
   
  import {...} from '...'  // 基本方式，导入的对象需要与export对象进行解构赋值。
   
  import a as biubiubiu from '...'  //使用as关键字，这里表示将a代表biubiubiu引入（当变量名称有冲突时可以使用这种方式解决冲突）
   
  import {a as biubiubiu,b,c}  //as关键字的其他使用方法
  ```

**它们之间的区别**

- require 是赋值过程并且是运行时才执行， import 是解构过程并且是编译时执行。require可以理解为一个全局方法，所以它甚至可以进行下面这样的骚操作，是一个方法就意味着可以在任何地方执行。而import必须写在文件的顶部。​	

```js
var a = require(a() + '/ab.js')
```

- require的性能相对于import稍低，因为require是在运行时才引入模块并且还赋值给某个变量，而import只需要依据import中的接口在编译时引入指定模块所以性能稍高

## This的指向问题

---

对于使用function定义的函数，它里面使用的**this是由它的(执行时的)直接调用者决定**。如果没有**直接调用者**，在非严格模式下，this指向**window**。

**箭头函数没有自己的this**，在它里面使用的this指向的是**定义箭头函数时(注意：并非执行时)**所处的**宿主对象**。

showMessage1()里的this指的是window，而showMessage2()里的this指的是vue实例。

```js
showMessage1: function(){
	setTimeout(function() {
		document.getElementById("id1").innerText = this.message;
	}, 10)
},
showMessage2: function() {
	setTimeout(() => {
		document.getElementById("id2").innerText = this.message;
	}, 10)
}
```

showMessage1里使用了匿名函数，this指向window，showMessage2里定义的箭头函数宿主对象为Vue实例，所以它里面使用的this指向Vue实例。

**为什么要let that = this ？**

> 为了保留this指向，在一些内层函数内（如封装的请求），他的this指向并不是当前的Vue实例，所以要定义一个值保存一下this，在需要访问调用Vue实例的时候使用

## This.$nextTick(callback)--在更新Dom后再触发的回调

---

官方解释：

​	可能你还没有注意到，Vue 在更新 DOM 时是**异步**执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。**如果同一个 watcher 被多次触发，只会被推入到队列中一次。**这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部对异步队列尝试使用原生的 `Promise.then`、`MutationObserver` 和 `setImmediate`，如果执行环境不支持，则会采用 `setTimeout(fn, 0)` 代替。

​	例如，当你设置 `vm.someData = 'new value'`，该组件不会立即重新渲染。当刷新队列时，组件会在下一个事件循环“tick”中更新。多数情况我们不需要关心这个过程，但是如果你想基于更新后的 DOM 状态来做点什么，这就可能会有些棘手。虽然 Vue.js 通常鼓励开发人员使用“数据驱动”的方式思考，避免直接接触 DOM，但是有时我们必须要这么做。为了在数据变化之后等待 Vue 完成更新 DOM，可以在数据变化之后立即使用 `Vue.nextTick(callback)`。这样回调函数将在 DOM 更新完成后被调用。

​	在组件内使用 `vm.$nextTick()` 实例方法特别方便，因为它不需要全局 `Vue`，并且回调函数中的 `this` 将自动绑定到当前的 Vue 实例上：

```javascript
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function () {
    return {
      message: '未更新'
    }
  },
  methods: {
    updateMessage: function () {
      this.message = '已更新'
      console.log(this.$el.textContent) // => '未更新'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => '已更新'
      })
    }
  }
})
```

因为 `$nextTick()` 返回一个 `Promise` 对象，所以你可以使用新的 [ES2017 async/await](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function) 语法完成相同的事情：

```javascript
methods: {
updateMessage: async function () {
this.message = '已更新'
console.log(this.$el.textContent) // => '未更新'
await this.$nextTick()
console.log(this.$el.textContent) // => '已更新'
}
}
```

# Vue Router

## 查看所有路由信息

---

打印`$router`，`options`里面只能看到**静态的路由**，通过`addRouters`添加的路由在`matcher`-`addRouters`-`[Scopes]`里面的o或者i中。

## 防止用户未登录直接修改地址路径来访问页面进行拦截功能实现

---

在入口文件main.js判断是否存在用户的token，若不存在则跳转到登录页面：

```typescript
// 全局前置导航钩子 beforeEach
// 会在路由即将改变前触发
router.beforeEach((to, from, next) => {
  let isLogin = window.localStorage.getItem('token')
  if (isLogin) {
    next()
  } else {
    if (to.path === '/login') {
      next()
    } else {
      Message.error('没有访问权限或登录已过期，请重新登录！')
      next('/login')
    }
  }
})
```

# Vue CLI

[官方指南](https://cli.vuejs.org/zh/guide/)

## @vue/cli

---

CLI (`@vue/cli`) 是Vue CLI的组成部分之一，是一个全局安装的 npm 包，提供了终端里的 `vue` 命令。它可以通过 `vue create` 快速搭建一个新项目，或者直接通过 `vue serve` 构建新想法的原型。你也可以通过 `vue ui` 通过一套图形化界面管理你的所有项目。

## Vue引入Scss全局变量

---

在vue.config.js配置如下：

```js
css: {
    loaderOptions: {
      //scss引入全局变量
      // 不同 sass-loader 版本对应关键字， v8-: data   v8: prependData   v10+: additionalData
      sass: {
        data: `@import "@/style/variables.scss";`
      },
      scss: {
        data: `@import "@/style/variables.scss";`
      }
    }
  },
```

## Process.env.NODE_ENV

---

在node中，有全局变量process表示的是当前的node进程。
process.env包含着关于系统环境的信息，但是process.env中并不存在NODE_ENV这个东西。

> NODE_ENV是一个用户自定义的变量，在webpack中它的用途是**判断生产环境或开发环境**。

在vue中我们可以使用`console.log(process);`命令印如下信息：

```dart
$ node process.js
process {
  title: 'node',
  version: 'v4.4.4',
  moduleLoadList: 
   [....],
  versions: 
   { http_parser: '2.5.2',
     node: '4.4.4',
     v8: '4.5.103.35',
     uv: '1.8.0',
     zlib: '1.2.8',
     ares: '1.10.1-DEV',
     icu: '56.1',
     modules: '46',
     openssl: '1.0.2h' },
  arch: 'x64',
  platform: 'darwin',
  release: 
   { name: 'node',
     lts: 'Argon',
     sourceUrl: 'https://nodejs.org/download/release/v4.4.4/node-v4.4.4.tar.gz',
     headersUrl: 'https://nodejs.org/download/release/v4.4.4/node-v4.4.4-headers.tar.gz' },
  argv: 
   [ '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin/node',
     '/Users/tugenhua/个人demo/process.js' ],
  execArgv: [],
  env: 
   { TERM_PROGRAM: 'Apple_Terminal',
     SHELL: '/bin/zsh',
     TERM: 'xterm-256color',
     TMPDIR: '/var/folders/l7/zndlx1qs05v29pjhvkgpmhjm0000gn/T/',
     Apple_PubSub_Socket_Render: '/private/tmp/com.apple.launchd.7Ax4C1EWMx/Render',
     TERM_PROGRAM_VERSION: '404',
     TERM_SESSION_ID: '82E05668-442D-4180-ADA3-8CF64D85E5A9',
     USER: 'tugenhua',
     SSH_AUTH_SOCK: '/private/tmp/com.apple.launchd.MYOMheYcL3/Listeners',
     PATH: '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin',
     PWD: '/Users/tugenhua/个人demo',
     LANG: 'zh_CN.UTF-8',
     XPC_FLAGS: '0x0',
     XPC_SERVICE_NAME: '0',
     SHLVL: '1',
     HOME: '/Users/tugenhua',
     LOGNAME: 'tugenhua',
     SECURITYSESSIONID: '186a8',
     OLDPWD: '/Users/tugenhua/工作文档/sns_pc',
     ZSH: '/Users/tugenhua/.oh-my-zsh',
     PAGER: 'less',
     LESS: '-R',
     LC_CTYPE: 'zh_CN.UTF-8',
     LSCOLORS: 'Gxfxcxdxbxegedabagacad',
     NVM_DIR: '/Users/tugenhua/.nvm',
     NVM_NODEJS_ORG_MIRROR: 'https://nodejs.org/dist',
     NVM_IOJS_ORG_MIRROR: 'https://iojs.org/dist',
     NVM_RC_VERSION: '',
     MANPATH: '/Users/tugenhua/.nvm/versions/node/v4.4.4/share/man:/usr/local/share/man:/usr/share/man:/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.13.sdk/usr/share/man:/Applications/Xcode.app/Contents/Developer/usr/share/man:/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/share/man',
     NVM_PATH: '/Users/tugenhua/.nvm/versions/node/v4.4.4/lib/node',
     NVM_BIN: '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin',
     _: '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin/node',
     __CF_USER_TEXT_ENCODING: '0x1F5:0x19:0x34' },
  pid: 14034,
  features: 
   { debug: false,
     uv: true,
     ipv6: true,
     tls_npn: true,
     tls_sni: true,
     tls_ocsp: true,
     tls: true },
  _needImmediateCallback: false,
  config: {},
  nextTick: [Function: nextTick],
  _tickCallback: [Function: _tickCallback],
  _tickDomainCallback: [Function: _tickDomainCallback],
  stdout: [Getter],
  stderr: [Getter],
  stdin: [Getter],
  openStdin: [Function],
  exit: [Function],
  kill: [Function],
  mainModule: 
   Module {
     id: '.',
     exports: {},
     parent: null,
     filename: '/Users/tugenhua/个人demo/process.js',
     loaded: false,
     children: [],
     paths: 
      [ '/Users/tugenhua/个人demo/node_modules',
        '/Users/tugenhua/node_modules',
        '/Users/node_modules',
        '/node_modules' ] } }
```

# Vue Mixin

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

例子：

```js
// 定义一个混入对象
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

// 定义一个使用混入对象的组件
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "hello from mixin!"
```

##  选项合并

---

当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

比如，数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。

```js
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

同名钩子函数将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子**之前**调用。

```js
var mixin = {
  created: function () {
    console.log('混入对象的钩子被调用')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件钩子被调用')
  }
})

// => "混入对象的钩子被调用"
// => "组件钩子被调用"
```

值为对象的选项，例如 `methods`、`components` 和 `directives`，将被合并为同一个对象。两个对象**键名冲突时，取组件对象的键值对**

```js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```

## 全局混入

---

混入也可以进行全局注册。使用时格外小心！一旦使用全局混入，它将影响**每一个**之后创建的 Vue 实例。使用恰当时，这可以用来为自定义选项注入处理逻辑。

```js
// 为自定义的选项 'myOption' 注入一个处理器。
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

请谨慎使用全局混入，因为它会影响每个单独创建的 Vue 实例 (包括第三方组件)。大多数情况下，只应当应用于自定义选项，就像上面示例一样。推荐将其作为[插件](https://cn.vuejs.org/v2/guide/plugins.html)发布，以避免重复应用混入。

## 自定义选项合并策略

---

自定义选项将使用默认策略，即简单地覆盖已有值。如果想让自定义选项以自定义逻辑合并，可以向 `Vue.config.optionMergeStrategies` 添加一个函数：

```js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // 返回合并后的值
}
```

对于多数值为对象的选项，可以使用与 `methods` 相同的合并策略：

```js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods	
```

可以在 [Vuex](https://github.com/vuejs/vuex) 1.x 的混入策略里找到一个更高级的例子：

```js
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```

##  不足

---

在 Vue 2 中，mixin 是将部分组件逻辑抽象成可重用块的主要工具。但是，他们有几个问题：

- Mixin 很容易发生冲突：因为每个 mixin 的 property 都被合并到同一个组件中，所以为了避免 property 名冲突，你仍然需要了解其他每个特性。
- 可重用性是有限的：我们不能向 mixin 传递任何参数来改变它的逻辑，这降低了它们在抽象逻辑方面的灵活性。

为了解决这些问题，我们添加了一种通过逻辑关注点组织代码的新方法：[组合式 API](https://v3.cn.vuejs.org/guide/composition-api-introduction.html)。

# 组件

## v-resize

---

[GitHub - theshying/v-resize: 🎉实时监听元素width/height属性变化的自定义vue指令](https://github.com/theshying/v-resize)

####  介绍

v-resize 是一个能够实时监听dom元素尺寸变化的自定义vue指令， 我们不需要关心是什么引起变化，无论是flexbox弹性计算引起的变化，还是窗口resize均能监听到，甚至通过控制台修改元素的尺寸。

总之只要这个元素的大小发生变化，均能监听到，且性能很好，不需要去轮询元素的大小。

 原理

在支持resizeObserve的浏览器下，我们会优先使用原生resizeObserve来监听变化，否则我们会使用iframe来模拟window的resize事件实现监听

#### 使用

```nginx
npm install @theshy/v-resize --save
```

```vue
//在main.js引入并注册
import vResize from '@theshy/v-resize'
Vue.use(vResize)

//在组件App.vue中

<template>
  <div v-resize="resizeHandler">
  </div>
</template>

<script>
export default {
    name: "App",
    methods: {
        resizeHandler(size) {
            console.log(size); //{width:xx , height: xxx}
        },
    },
};
</script>

```

## vue-ls

---

[官方网址](https://www.npmjs.com/package/vue-ls) | 

### **作用**

Vue-ls 是 Vue 的一个插件，用于操作 Local Storage(本地存储)、Session Storage(会话存储)、Memory(内存存储)。

### 使用

```js
import Vue from 'vue'
import Storage from 'vue-ls'

// vue-ls 的配置
const storageOptions = {
    namespace: 'vue_',   // key 键的前缀（随便起）
  	name: 'ls',          // 变量名称（随便起） 使用方式：Vue.变量名称 或 this.$变量名称
  	storage: 'local'     // 作用范围：local、session、memory
}

Vue.use(Storage, storageOptions)
```

```js
// login.vue
methods: {
  setKey () {
    this.$ls.set('name', 'cez')
  }
}
```

结果：![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/02/20200821121151187.png)

### API

#### **Vue.ls.get (name, def)**

- 作用：获取存储中的 key
- name：要获取的 key；
- def：默认为 null。如果 key 不存在，则返回 def。

```js
methods: {
    getKey () {
      // age 和 age2 都不存在
      const age = this.$ls.get('age')
      const age2 = this.$ls.get('age2', 22)
      console.log(age)    // null
      console.log(age2)   // 22
    }
  }
```

#### **Vue.ls.set (name, value, expire)**

- 作用：设置一个 key，并且可以设置有效时间。
- expire：默认为 null。name 的有效时间，单位为毫秒。

```js
methods: {
  setKey () {
     this.$ls.set('age', 22)   // age 的有效时间为永久，除非自动清除
     this.$ls.set('name', 'cez', 3000)   // name 的有效时间为 3s，3s 后为 null
  }
}
```

**Vue.ls.remove (name)**

- 作用：从存储中删除某一个 key，成功返回 true，否则返回 false。

```js
methods: {
    removeKey () {
        const age = this.$ls.remove('age')
        console.log(age)   // undefined：不管删除成功还是删除失败都会返回 undefined，和官方解析不一样，不知道为什么？？
    }
}
```

官方解析：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/02/20200821142703252.png)

#### **Vue.ls.clear ( )**

- 作用：清空所有 key。

```js
methods: {
    clearKey () {
        this.$ls.clear()
    }
}
```

#### **Vue.ls.on (name, callback)**

- 作用：设置侦听器，监听 key，若发生变化时，就会触发回调函数 callback。
- callback 接受三个参数：
- newValue：存储中的新值
- oldValue：存储中的旧值
- url：修改来自选项卡的 url

#### **Vue.ls.off (name, callback)**

- 作用：删除设置的侦听器

# 问题

## 点击button按钮页面自动刷新问题

---

###  问题描述：

在vue中使用原生的button，绑定点击事件后点击按钮会自动刷新页面。

### 原因：

原生按钮`button`默认`type='submit'`这个属性值是默认具有表单提交功能的，所以在`非IE浏览器`下会存在点击后刷新页面的问题

### 解决方案：

- 解决方案1：在原生`button`按钮中更改`type='button'`属性值就可以了，此时它就是一个`单纯`的按钮了
- 解决方案2:  可以使用`input`标签，然后再设置`type='button'`也能解决
- 解决方案3:  自己写块元素,通过样式更改为按钮
- 解决方案4:  采用elementUI的`el-button`。

# 其它

##  在Vue中，给数组的对象原型新增方法

---

首先创建一个js，并写入相应的代码，这里要注意的是不能使用箭头函数，因为箭头函数和匿名函数的this指向是不一样的，详情参见本文的“vue中this指向问题”

```js
/*prototypeExpand 对象原型的扩展*/
/**
 * 移除数组某一元素
 * @param callback 回调函数
 * @returns {number}
 */
Array.prototype.remove = function (callback){
  let index = this.findIndex(callback);
  if (index > -1) {
    this.splice(index, 1);
  }
  return index
}
```

然后再在main.js中引入。

