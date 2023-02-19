# 基础

## Cli2更换浏览器图标(favicon)的方式

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

[v-model的修饰符 - 简书 (jianshu.com)](https://www.jianshu.com/p/2ad32bb94cc2)

####  v-model.lazy

使用`.lazy`修饰符，会转变为在`change`事件中同步，简单粗暴的讲就是，此时数据并不会像我们以前见到的`v-model`那样实时更新数据，而是在**失去焦点或者回车时更新**，见下面示例代码

####  v-model.number

需要明确的是，我们输入的数字，虽然输入的是数字，但实际上是`String`，不论你是否让type=‘number’，那么`.number`修饰符就可以将输入的内容转化为`Number`类型

####  v-model.trim

自动过滤输入的首尾空格

## 数据双向绑定 

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

打印router

## 防止用户未登录直接修改地址路径来访问页面进行拦截功能实现

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

# Webpack

## Process.env.NODE_ENV

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

在 Vue 2 中，mixin 是将部分组件逻辑抽象成可重用块的主要工具。但是，他们有几个问题：

- Mixin 很容易发生冲突：因为每个 mixin 的 property 都被合并到同一个组件中，所以为了避免 property 名冲突，你仍然需要了解其他每个特性。
- 可重用性是有限的：我们不能向 mixin 传递任何参数来改变它的逻辑，这降低了它们在抽象逻辑方面的灵活性。

为了解决这些问题，我们添加了一种通过逻辑关注点组织代码的新方法：[组合式 API](https://v3.cn.vuejs.org/guide/composition-api-introduction.html)。

# 组件

## v-resize

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

# 问题

## 点击button按钮页面自动刷新问题

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

