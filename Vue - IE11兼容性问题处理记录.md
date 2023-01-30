# IE11兼容性问题处理记录

> 首先先说明一下场景和环境：公司有一个平台的项目要兼容IE11，而要兼容IE11主要的问题在于IE11不支持ES6语法，所以大致的目标就是解决ES6的语法问题。平台采用的是`vue2.0`+`cli4.0`+`elementui2.0`，当然还有很多其它的依赖就不一一列举了。
>
> 问题记录日期：2022-7-12

## BabelPolyfill安装与配置

### 安装BabelPolyfill

```bash
npm install babel-polyfill --save
npm install core-js --save 
```

### 配置vue.config.js

```js
module.exports = {
  chainWebpack: config => {
    //也可以在main.js中引入
    config.entry('main').add('babel-polyfill');
    config.entry.app = ['babel-polyfill', './src/main.js'];
    ...
 },
 transpileDependencies: [...],
 ...
}
```

> `transpileDependencies`里面处理的是`node_modules`里面一些依赖存在的兼容性问题，如果报错是`node_modules`里面的依赖导致的那就把对应的依赖加进来。

### 在main.js中引入

根据ES6的标准说明：

> Babel 默认只转换新的 JavaScript 句法（syntax），而不转换新的 API，比如`Iterator`、`Generator`、`Set`、`Map`、`Proxy`、`Reflect`、`Symbol`、`Promise`等全局对象，以及一些定义在全局对象上的方法（比如`Object.assign`）都不会转码。
>
> 举例来说，ES6 在`Array`对象上新增了`Array.from`方法。Babel 就不会转码这个方法。如果想让这个方法运行，可以使用`core-js`和`regenerator-runtime`(后者提供generator函数的转码)，为当前环境提供一个垫片。

所以在main中引入了`core-js/stable`和`regenerator-runtime/runtime`

```js
//切记要置顶！
import 'core-js/stable'
import 'regenerator-runtime/runtime'
```

### 配置babel.config.js

`polyfills`注释了，这个配置就是按需引入，可以提高一些dev时候的性能，但是需要对项目十分了解，具体用了哪些要知道，如果启用该配置要在main.js引入（import 'babel-polyfill'），相应的`vue.config.js`的`chainWebpack`中的要去掉`babel-polyfill`的配置，不然会重复了。

```js
module.exports = {
  presets: [
    ['@vue/app', 
      { 
        'useBuiltIns': 'entry', 
        //polyfills: ['es6.promise', 'es6.symbol','es6.call','es6.array'],
        corejs:3 
      }
    ]
  ],
  ...
}
```

## 遇到的问题以及解决方案

> 以下问题以IE版本11为前提！

### ES6语法问题

在控制台看到报错：“SCRIPT1003: 缺少 ':' ”、“SCRIPT1003: 语法错误':' ”……这些出错的问题正常都是es6语法不兼容的问题导致的，遇到这种问题建议是先找到报错的具体位置，在判断是否是`node_modules`引用的依赖导致的，若是则在`vue.config.js`相应的`transpileDependencies`中加入相应的依赖，若不是那就找到具体的报错位置然后找到相关代码进行修改，一般后者的可能性较小，因为在`vue.config.js`的`chainWebpack`里面已经配置了主要代码的兼容性处理。

**例1：**

以下一个错误可以看到错误的地方应该是在./node_modules/vue-contextmenujs/src/index.js里面，那就可以确定vue-contextmenujs存在兼容性问题，所以这时候就需要将其加入到transpileDependencies里面去了。

![image-20220711184753599](.\image\image-20220711184753599.png)

![image-20220711184854899](.\image\image-20220711184854899.png)

```js
transpileDependencies: ['element-ui', 'echarts','vue-contextmenujs','devextreme','@devextreme'],
```

---

**例2**

以下错误就是主要代码里面导致的错误，找到具体那一份代码后可以看到确实如报错所描述的一样，el-table标签的stripe属性定义了两次，所以去掉一个就可以。这在当前的Chrome、Firefox等其它浏览器中是不会报错的，但是在IE11上就会了。

![微信截图_20220711160839](image/微信截图_20220711160839.png

![微信截图_20220711160839](image/微信截图_20220711160839.png)

![image-20220712103116036](image/image-20220712103116036.png)

### 样式问题

**1. 平台的主题控制没有生效**

排查后发现是`project.length&& projectClassList.add(...project)`两段类似这样的代码导致的。`projectClassList = document.documentElement.classList`，首先扩展运算符是没有问题的，`document.documentElement.classList`本身也没有问题，但是两者结合就有问题了，目前还没找到具体的原因，只是换了种写法。

```js
//themeContor.js
//before：
project.length&& projectClassList.add(...project)
//after：
if(project.length){
    for (let i of project){
      projectClassList.add(i)
    }
 }
```

**2. el-tabs没用flex布局导致样式错乱**

这个我就弄了一个叫element-fix的文件，然后直接在`pfIndex.scss`中引入

```scss
.el-tabs{
  .el-tabs__header{
    flex: none !important;
  }
  .el-tabs__content{
    flex: auto !important;
  }
}
```

### 其他异常

 **1. SecurityError**

```
SCRIPT5022: SecurityError sockjs.js (1683,3)
```

找到/node_modules/sockjs-client/dist/sockjs.js

找到代码的 1605行注释掉！

```js
  try {
    // self.xhr.send(payload);
  } catch (e) {
    self.emit('finish', 0, '');
    self._cleanup(false);
  }
};
```

**2. ChunkLoadError**

遇到以下报错可能是更改的代码还没更新，重启浏览器或者刷新页面

![image-20220711160601463](.\image\image-20220711160601463.png)

## 总结

这次对IE11的兼容主要就是ES6语法的兼容，以及部分样式上的调整，而ES6语法兼容主要就是依靠转码器（babel），对主要代码和部分依赖进行转码，将其转为ES5的语法。在问题处理的流程上主要就是先将项目从平台中独立出来，然后去掉一些没用的代码，接着就是引入转码器，然后从登录页开始由近及远的将报错一一处理。事实上，只要排查到能从首页进入到登录页，那语法的兼容问题就差不多了，当然一些特殊的页面也要留意一下，最后也要整体过一下系统。在处理语法问题的同时也要将样式问题一并处理掉。关于报错的排查一方面就是快速定位到问题出现在那个地方，另一方面就是网上查找有没有相识的案例可以参考。在后续的开发中建议是开启ESLint，因为IE的对代码的要求比较严谨，像上面一个元素重复定义一个属性的情况就能有效的避免其再次发生，也可以及时发现并纠正错误，对代码的规范化有一定的好处。

### 解决The code generator has deoptimised the styling of xxxx.js as it exceeds the max of 500kb

- 这个问题其实就是babel处理文件的大小被限制在了500kb，解决如下：

```js
*// babel.config.js:*
 module.exports = {    
 	compact: false, 
 }
```
