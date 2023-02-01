# Vue使用ES2021语法报错问题处理

## 起因

> 项目技术栈：vue2.x + cli3.x + antDesign1.x + ...

我在使用ES2021语法后，运行后报错了，报错代码如下：

```js
this.dataList = res.map(i=>{
    //以下两行代码报错
    //??= 属于逻辑判断运算符，是ES2021的语法
    i['assigneeId'] ??= ''; 
    i['assigneeType'] ??= 'role';
    return i
})
```

报错内容：

>  You may need an additional loader to handle the result of these loaders.

## 原因

@vue/cli-plugin-babel3.x及以下版本估计只支持到ES2015，而@vue/cli-plugin-babel4.x则支持到ES2021甚至更高。由于cli4.x的@vue/cli-plugin-babel默认也是4.x的，所以我在cli4.x的项目中并没有出现此问题。

## 解决方案

### 方案一

如果只针对单个语法进行处理可以参考这篇文章-[vue项目使用可选链操作符编译报错问题](https://blog.csdn.net/roylinziyang/article/details/123280896)，其中@babel/plugin-proposal-optional-chaining中的optional-chaining指的就是链式判断运算符，其它的语法可以根据对应的英文名上npm找找。

### 方案二

个人觉得上面的方法虽然可行，但是治标不治本，所以我这边直接将@vue/cli-plugin-babel直接升级到更高的版本，目前使用来看没有啥问题，也能正常打包。后面有遇到坑的时候再做补充吧。