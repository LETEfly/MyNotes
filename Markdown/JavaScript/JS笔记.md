# 全局属性/函数

##  eval()函数--将字符串用作代码执行

---

`eval()` 函数会将传入的字符串当做 JavaScript 代码进行执行，返回字符串中代码的返回值。如果返回值为空，则返回 `undefined`。如果 `eval()` 的参数不是字符串， `eval()` 会将参数原封不动地返回。

eval是存在安全隐患的，不推荐使用！

```js
eval(string)
```

## encodeURI()，encodeURIComponent()，escape()区别

---

**encodeURI()函数：**

encodeURI() 函数可把字符串作为 URI 进行编码。该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。

该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的：;/?:@&=+$,#

**encodeURIComponent() 函数：**

encodeURIComponent() 函数可把字符串作为 URI 组件进行编码。该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。其他字符（比如 ：;/?:@&=+$,# 这些用于分隔 URI 组件的标点符号），都是由一个或多个十六进制的转义序列替换的。

**escape()函数（已弃用）：**

escape() 函数可对字符串进行编码，这样就可以在所有的计算机上读取该字符串。该方法不会对 ASCII 字母和数字进行编码，也不会对下面这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。其他所有的字符都会被转义序列替换。

 **总结：**

 通过对三个函数的分析，我们可以知道：escape()除了 ASCII 字母、数字和特定的符号外，对传进来的字符串全部进行转义编码，因此如果想对URL编码，最好不要使用此方法。而encodeURI() 用于编码整个URI,因为URI中的合法字符都不会被编码转换。encodeURIComponent方法在编码单个URIComponent（指请求参 数）应当是最常用的，它可以将参数中的中文、特殊字符进行转义，而不会影响整个URL。

# 运算符

## (0, fn)(...)写法的作用

---

```cpp
console.log((1, 2)); // 结果为2
console.log((a = b = 3, c = 4)); // 结果为4
```

可以看出**逗号操作符** 对它的每个操作对象求值（从左至右），然后返回**最后一个操作对象的值**。

```js
function test() {
  var x = 2, y = 4;
  console.log(eval('x + y'));  // 直接调用，使用本地作用域，结果是 6
  var geval = eval; // 等价于在全局作用域调用
  console.log(geval('x + y')); // 间接调用，使用全局作用域，throws ReferenceError 因为`x`未定义
  (0, eval)('x + y'); // 另一个间接调用的例子
}
```

这里的` (0, eval)('x + y');`是对eval的间接调用，且使用的是全局作用域

```js
var a = {
  foo: function() {
    console.log(this === window);
  }
};

a.foo(); // Returns 'false' in console
(0, a.foo)(); // Returns 'true' in console
```

这时候 `this`的是全局对象 `window`, 所以打印结果为 `true`，所以该写法的作用就是将函数的作用域提升至全局作用域。

## a=b=1和a=1,b=1的区别

---

```js
function test(){var a = b = 1}
test()//调用
console.log(b, a)//b为1，a为undefined（报错）
```

结论：a = b = 1的写法会导致b变为全局变量，而a=1,b=1则是标准的写法，所以通常情况下不推荐a = b = 1的写法；其次a = b  = c = 1则是将b，c变为全局变量。当然，这要在局部作用域（test）被调用之后才会发生变化

# 数组

## 删除数组的指定元素

---

```js
Arr.splice(Arr.findIndex(item=>item==value),1)
```

## Array.flat() 数组扁平化

---

数组的成员有时还是数组，Array.prototype.flat()用于将嵌套的数组“拉平”，变成一维数组。该方法返回一个新数组，对原数据没有影响。

```js
[1, 2, [3, 4]].flat()
// [1, 2, 3, 4]
```

上面代码中，原数组的成员里面有一个数组，flat()方法将子数组的成员取出来，添加在原来的位置。

flat()默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将flat()方法的参数写成一个整数，表示想要拉平的层数，默认为1。

```js
[1, 2, [3, [4, 5]]].flat()
// [1, 2, 3, [4, 5]]
[1, 2, [3, [4, 5]]].flat(2)
// [1, 2, 3, 4, 5]
```

上面代码中，flat()的参数为2，表示要拉平两层的嵌套数组。

如果不管有多少层嵌套，都要转成一维数组，可以用Infinity关键字作为参数。

```js
[1, [2, [3]]].flat(Infinity)
// [1, 2, 3]
```

如果原数组有空位，flat()方法会跳过空位。

```js
[1, 2, , 4, 5].flat()
// [1, 2, 4, 5]
```

flatMap()方法对原数组的每个成员执行一个函数，相当于执行Array.prototype.map(),然后对返回值组成的数组执行flat()方法。该方法返回一个新数组，不改变原数组。

```js
// 相当于 [[2, 4], [3, 6], [4, 8]].flat()
[2, 3, 4].flatMap((x) => [x, x * 2])
// [2, 4, 3, 6, 4, 8]
```

## Array erery() 数组检测

------

every() 方法用于检测数组所有元素是否都符合指定条件（通过函数提供）。

every() 方法使用指定函数检测数组中的所有元素：

- 如果数组中检测到有一个元素不满足，则整个表达式返回 *false* ，且剩余的元素不会再进行检测。
- 如果所有元素都满足条件，则返回 true。

**注意：** every() 不会对空数组进行检测。

**注意：** every() 不会改变原始数组。

**语法：array.every(function(currentValue,index,arr), thisValue)**

## Array.concat(arr1,arr2...)--数组拼接

------

concat() 方法用于连接两个或多个数组。

concat() 方法不会更改现有数组，而是返回一个新数组，其中包含已连接数组的值。

```js
var sedan = ["S60", "S90"];
var SUV = ["XC40", "XC60", "XC90"];
var wagon = ["V60", "V90", "V90CC"];
var Volvo = sedan.concat(SUV, wagon);
```

## 数组元素出现次数计算

---

```js
Arr.map(item=>item.subject).reduce((prev,next)=>{
  prev[next] = (prev[next]+1)||1
  return prev
},{})
```

## new Set()--数组去重

---

```js
Array.form(new set(Arr))//方法一
[...new set(Arr)]//方法二
```

`Array.from()` 方法从一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例。

`set()`集合,和python集合特性一样,元素唯一，是ES6新增的一个构造函数，用来生成Set数据结构。

## 数组清空的三种方式

---

方式1，splice

```js
var ary = [1,2,3,4];
ary.splice(0,ary.length);
console.log(ary); // 输出 []，空数组，即被清空了	
```

方式1，splice

这种方式很有意思，其它语言如Java，其数组的length是只读的，不能被赋值。Java中会报错，编译通不过。

```js
var ary = [1,2,3,4]; 
ary.length = 0; 
console.log(ary); // 输出 []，空数组，即被清空了
```

方式3，赋值为[]

```js
var ary = [1,2,3,4]; 
ary = []; // 赋值为一个空数组以达到清空原数组
```

Ext库Ext.CompositeElementLite类的 [clear](http://docs.sencha.com/#method-Ext.CompositeElementLite-clear) 方法使用这种方式清空。

方式1，2 保留了数组其它属性，方式3 则是重新建立个对象

## 数组与字符串互相转换

---

数组转字符串

- Arr.toString()
- Arr.join('-') 输出："a-b-c"

字符串转数组：`split()`

- "a,b,c".split(',') 输出："[a,b,c]"

- "abc".split('') 输出同上


## .filter(Boolean)

---

```js
['',0,false,'x',1,true].filter(Boolean)
//Boolean是一个函数，传入一个值，Boolean函数会判断并返回Boolean值
//如：Boolean('') -> false
//所以上面的表达式的结果为：['x', 1, true]
```

#  对象

## hasOwnProperty()--检查对象是否有该属性

---

`hasOwnProperty()`函数用于指示一个对象自身(不包括原型链)是否具有指定名称的属性。如果有，返回`true`，否则返回`false`。

该方法属于`Object`对象，由于所有的对象都"继承"了Object的对象实例，因此几乎所有的实例对象都可以使用该方法。

IE 5.5+、FireFox、Chrome、Safari、Opera等主流浏览器均支持该函数。

**语法**

```
object.hasOwnProperty( propertyName )
```

# Date 对象

## Date.setDate() 设置一个月的某一天

---

```js
var d = new Date("July 21, 1983 01:15:00");
d.setDate(15);
```

 输出结果:

```
Fri Jul 15 1983 01:15:00 GMT+0800 (中国标准时间)
```

# Window 对象

## Window localStorage

---

```JS
// 存储
localStorage.setItem("lastname", "Smith");
// 检索
document.getElementById("result").innerHTML = localStorage.getItem("lastname");
// 删除
localStorage.removeItem("key");
```

# 函数

## 立即执行函数与单例模式

---

立即执行函数常用于第三方库，好处在于隔离作用域，任何一个第三方库都会存在大量的变量和函数，为了避免变量污染（命名冲突），开发者们想到的解决办法就是使用立即执行函数。

1.什么是立即执行函数（IIFE）

在了解立即执行函数之前先明确一下函数声明、函数表达式及匿名函数的形式，如下图：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/20181224114050294.png)

接下来看立即执行函数的两种常见形式：`( function(){…} )()`和`( function (){…} () )`，一个是一个匿名函数包裹在一个括号运算符中，后面再跟一个小括号，另一个是一个匿名函数后面跟一个小括号，然后整个包裹在一个括号运算符中，这两种写法是等价的。要想立即执行函数能做到立即执行，要注意两点，一是函数体后面要有小括号()，二是函数体必须是函数表达式而不能是函数声明。先看下图：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/20181224114050315.png)

从图中可以看出，除了使用()运算符之外，！，+，-，=等运算符都能起到立即执行的作用。这些运算符的作用就是将匿名函数或函数声明转换为函数表达式，如下图所示，函数体是函数声明的形式，使用运算符将其转换为函数表达式之后就可达到立即执行效果。

2.使用立即执行函数的好处

通过定义一个匿名函数，创建了一个新的函数作用域，相当于创建了一个“私有”的命名空间，该命名空间的变量和方法，不会破坏污染全局的命名空间。此时若是想访问全局对象，将全局对象以参数形式传进去即可，如jQuery代码结构:

![img](https://gitee.com/letefly/NoteImages/raw/master/img/20181224114050352.png)

其中window即是全局对象。给其传入参数这样的好处是，可以缩短查询时的作用域链。作用域隔离非常重要，是一个JS框架必须支持的功能，jQuery被应用在成千上万的JavaScript程序中，必须确保jQuery创建的变量不能和导入他的程序所使用的变量发生冲突。

单例模式的实现：

```js
var car = {
    speed:0,
    start:function(){ this.speed=40; },
    getspeed:function(){ return this.speed; }
};
car.start();
console.log(car.getspeed()); //print 40
```

这个对象有其成员变量`spee`及成员函数`start`和`getspeed`，但是它的成员变量没有私有化，同时它也没有办法被继承。要实现对象的继承，你可以使用构造函数和原型继承。但怎么才能将成员变量私有化来实现对象的封装呢（而且有时候我们也不想那么麻烦使用原型）？

```js
function car() {
    var speed = 0;
    return { //返回的是一个对象
        start:function() {
            speed = 50;
        },
        getspeed:function () {
         return speed;
        }
    }
}
 
var car1 = car();
car1.start();
console.log(car1.getspeed()); //print 50
```

答案就是使用闭包，有了闭包函数来帮你创建`car`对象，这个函数就类似于工厂方法，它可以根据你的需要创建多个不同的对象。不过开发的时候经常遇到这样的情况，就是我们希望**对象只有一份**，比如jQuery库的对象，它必须确保整个程序只有一份，多了也没有。在后端开发模式中，这叫**单例模式**，可以通过私有化构造函数来实现，那么在js里呢？

既然函数没法私有化，那么唯一的办法就是让这个工厂方法能且只能被调用一次。不能多次调用，那这个函数一定要是匿名函数；而且只能被调用一次，那就必须在声明的时候立马执行。这时候，我们就可以用到立即执行函数了：

```js
var car = (function () {
    var speed = 0;
    return {
        start:function () {
           speed=60;
        },
        getspeed:function () {
           return speed;
        }
    }
})();
 
car.start();
console.log(car.getspeed()); //print 60  
```

这里的car已经不是一个函数了，而是这个匿名的工厂方法执行完返回的对象，该对象拥`start`和`getspeed`两个成员函数，而这两个函数所需要访问的`speed`变量对外不可见。同时你无法再次调用这个匿名的工厂方法来创建一个相同的对象。一个单例的，有着私有成员的对象就这么建好了。

# Math函数

## 浮点数精度调整

### **toFixed()**

四舍五入，保留**两位**小数，数据类型变为String，可以通过`Number(xx)`再转回Number类型：

```js
var num =2.446242342;  
num.toFixed(2);//结果为："2.45"
Number(num.toFixed(2));//结果为：2.45
```

### **Math.floor()**

不四舍五入 ，向下**取整**，不改变数据类型：

```js
Math.floor(2.15555);//结果为：2
```

不四舍五入，保留**两位**小数，不改变数据类型：

```js
let num = 2.15555
Math.floor(num * 100) / 100;//结果为：2.15
```

可以用`Math.ceil`或`Math.round`代替`Math.floor`，`Math.ceil`为**向上取整**，`Math.round`为**四舍五入**。

```js
Math.round(2.15555*100)/100;//结果为：2.16
Math.ceil(2.15555*100)/100;//结果为：2.16
```



# 事件

## 事件的防抖与节流

---

###  前言

 以下场景往往由于事件频繁被触发，因而频繁执行DOM操作、资源加载等重行为，导致UI停顿甚至浏览器崩溃。

1. window对象的resize、scroll事件

2. 拖拽时的mousemove事件

3. 射击游戏中的mousedown、keydown事件

4. 文字输入、自动完成的keyup事件

 实际上对于window的resize事件，实际需求大多为停止改变大小n毫秒后执行后续处理；而其他事件大多的需求是以一定的频率执行后续处理。针对这两种需求就出现了debounce和throttle两种解决办法。

### 防抖(Debounce)

**1.定义**

如果用手指一直按住一个弹簧，它将不会弹起直到你松手为止。

也就是说当调用动作n毫秒后，才会执行该动作，若在这n毫秒内又调用此动作则将重新计算执行时间。

**2.接口定义：**

```js
/**
* 空闲控制 返回函数连续调用时，空闲时间必须大于或等于 idle，action 才会执行
* @param idle   {number}    空闲时间，单位毫秒
* @param action {function}  请求关联函数，实际应用需要调用的函数
* @return {function}    返回客户调用函数
*/
debounce(idle,action)
```

**3.简单实现**

```js
var debounce = function(idle, action){
  var last
  return function(){
    var ctx = this, args = arguments
    clearTimeout(last)
    last = setTimeout(function(){
        action.apply(ctx, args)
    }, idle)
  }
}
```

### **节流(throttle)**

**1.定义**

如果将水龙头拧紧直到水是以水滴的形式流出，那你会发现每隔一段时间，就会有一滴水流出。也就是会说预先设定一个执行周期，当调用动作的时刻大于等于执行周期则执行该动作，然后进入下一个新周期。

**2.接口定义**

```js
/**
* 频率控制 返回函数连续调用时，action 执行频率限定为 次 / delay
* @param delay  {number}    延迟时间，单位毫秒
* @param action {function}  请求关联函数，实际应用需要调用的函数
* @return {function}    返回客户调用函数
*/
throttle(delay,action)
```

**3.简单实现**

```js
var throttle = function(delay, action){
  var last = 0return function(){
    var curr = +new Date()
    if (curr - last > delay){
      action.apply(this, arguments)      last = curr     }
  }
}
```

### 示例

使用前:

```js
//模拟一段ajax请求
functionajax(content) { 
    console.log( 'ajax request '+ content)
} 
let inputa = document.getElementById( 'unDebounce')
inputa.addEventListener( 'keyup', function(e) { ajax(e.target.value)})
```

运行效果:

![img](https://gitee.com/letefly/NoteImages/raw/master/img/image-202201241416512091.gif)

可以看到，我们只要按下键盘，就会触发这次ajax请求。不仅从资源上来说是很浪费的行为，而且实际应用中，用户也是输出完整的字符后，才会请求。

使用防抖后:

```js
//模拟一段ajax请求
function ajax(content) { 
    console.log( 'ajax request '+ content)
} 
function debounce(fun, delay) { 
    return function(args) { 
        let that = this 
        let _args = args 
        clearTimeout(fun.id) 
        fun.id = setTimeout( () => {
            fun.call(that, _args) }, delay) 
    }
} 
let inputb = document.getElementById( 'debounce') 
let debounceAjax = debounce(ajax, 500)
inputb.addEventListener( 'keyup', function(e) { debounceAjax(e.target.value) }) 
```

运行效果:

![img](https://gitee.com/letefly/NoteImages/raw/master/img/image-202201241416512092.gif)

可以看到，我们加入了防抖以后，当你在频繁的输入时，并不会发送请求，只有当你在指定间隔内没有输入时，才会执行函数。如果停止输入但是在指定间隔内又输入，会重新触发计时。

使用节流后:

```js
function throttle(fun, delay) {
    let last, deferTimer 
    return function(args) { 
        let that = this 
        let _args = arguments
        let now = + newDate() 
        if(last && now < last + delay) {
            clearTimeout(deferTimer) 
            deferTimer = setTimeout( function() {
                last = now fun.apply(that, _args) 
            }, delay) 
        } else{
            last = now fun.apply(that,_args) 
        } 
    } 
} 
let throttleAjax = throttle(ajax, 1000) 
let inputc = document.getElementById( 'throttle')
inputc.addEventListener( 'keyup', function(e) {
    throttleAjax(e.target.value)
}) 
```

运行效果:

![img](https://gitee.com/letefly/NoteImages/raw/master/img/3bd0ba7612e041b1a7324092da6faacb.gif)

### **throttle-debounce插件**

```js
/*
Throttle execution of a function. Especially useful for rate limiting execution of handlers on events like resize and scroll.
Params:
delay: number – A zero-or-greater delay in milliseconds. For event callbacks, values around 100 or 250 (or even higher) are most useful.
noTrailing: boolean – Optional, defaults to false. If noTrailing is true, callback will only execute every delay milliseconds while the throttled-function is being called. If noTrailing is false or unspecified, callback will be executed one final time after the last throttled-function call. (After the throttled-function has not been called for delay milliseconds, the internal counter is reset).
callback: Function – A function to be executed after delay milliseconds. The this context and all arguments are passed through, as-is, to callback when the throttled-function is executed.
debounceMode: boolean – If debounceMode is true (at begin), schedule clear to execute after delay ms. If debounceMode is false (at end), schedule callback to execute after delay ms.
Returns:
A new, throttled, function.
*/
function throttle (delay, noTrailing, callback, debounceMode) 
```

```js
/*
Debounce execution of a function. Debouncing, unlike throttling, guarantees that a function is only executed a single time, either at the very beginning of a series of calls, or at the very end.
Params:
delay: number – A zero-or-greater delay in milliseconds. For event callbacks, values around 100 or 250 (or even higher) are most useful.
atBegin: boolean – Optional, defaults to false. If atBegin is false or unspecified, callback will only be executed delay milliseconds after the last debounced-function call. If atBegin is true, callback will be executed only at the first debounced-function call. (After the throttled-function has not been called for delay milliseconds, the internal counter is reset).
callback: Function – A function to be executed after delay milliseconds. The this context and all arguments are passed through, as-is, to callback when the debounced-function is executed.
Returns:
A new, debounced function.
*/
function debounce (delay, atBegin, callback)
```

# Methods

## 判断当前设备是PC还是手机、平板方法

---

```js
this.isMobile = navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i)
```

输出的结果是null即为电脑

##  树形结构扁平化

---

```js
 DelayeringTree(tree){
    let arr = []
    for(let i of tree){
      let item = this.CloneDeep(i)
      delete item['children']
      arr.push(item)
      if(i.children){
        arr.push(...this.DelayeringTree(i.children))
      }
    }
    return arr
  }
```

##  对象数组根据某一键值排序

---

```js
sortListByKey(Arr, key) {
  Arr = Arr.sort((start, end) => {
    if(start[key] > end[key])
      return 1;
    else if(start[key] < end[key])
      return -1;
    else
      return 0;

  })
},
```

# 正则表达式

##  过滤富文本中的标签

---

```js
val.replace(/<[^>]+>/g, "")
```

## Split通过多个符号拆分数组

---

```js
'(false||(1==3&&1==1))'.split(/([!()])|(&&)|(\|\|)/g).filter(Boolean)
//结果：['(', 'false', '||', '(', '1==3', '&&', '1==1', ')', ')']
```

# 其它

## Input标签type为number类型返回的数据为String

---

在input中不论你指定的是不是number类型，获取到的数据都是String类型，不过在vue中可以使用v-model.number将数据转为number类型。

## break,continue和return的用法及区别

---

1. break：是立即结束语句，并跳出语句，进行下个语句执行。
2. continue：是停止当前语句，并从头执行该语句。

3. return：停止函数。
4. 使用的语句环境不一样，break和continue是用在循环或switch语句中，return是用在函数语句中。

## find(), findIndex(),indexof的区别

---

find()方法和findIndex()分别用于找出数组第一个符合条件的元素和元素下标，不适用于字符串；

indexOf(value, index)用于检测字符串或数组中某一元素的首次出现的位置，index规定开始检测的位置；





