# JavaScript 对象

---

JavaScript 中的所有事物都是对象：字符串、数值、数组、函数...

此外，JavaScript 允许自定义对象。

JavaScript 提供多个内建对象，比如 String、Date、Array 等等。 对象只是带有属性和方法的特殊数据类型。

- 布尔型可以是一个对象。
- 数字型可以是一个对象。
- 字符串也可以是一个对象
- 日期是一个对象
- 数学和正则表达式也是对象
- 数组是一个对象
- 甚至函数也可以是对象

## 访问对象的属性

---

属性是与对象相关的值。

访问对象属性的语法是：

```js
objectName.propertyName
```

这个例子使用了 String 对象的 length 属性来获得字符串的长度：

```js
var message="Hello World!";
var x=message.length;
```

在以上代码执行后，x 的值将是：

```
12
```

## 访问对象的方法

---

方法是能够在对象上执行的动作。

您可以通过以下语法来调用方法：

```
objectName.methodName()
```

这个例子使用了 String 对象的 toUpperCase() 方法来将文本转换为大写：

```js
var message="Hello world!";
var x=message.toUpperCase();
```

在以上代码执行后，x 的值将是：

```
HELLO WORLD!
```

## 创建 JavaScript 对象

---

通过 JavaScript，您能够定义并创建自己的对象。

创建新对象有两种不同的方法：

- 使用 Object 定义并创建对象的实例。
- 使用函数来定义对象，然后创建新的对象实例。

### 使用 Object

---

在 JavaScript 中，几乎所有的对象都是 Object 类型的实例，它们都会从 Object.prototype 继承属性和方法。

Object 构造函数创建一个对象包装器。

Object 构造函数，会根据给定的参数创建对象，具体有以下情况：

- 如果给定值是 null 或 undefined，将会创建并返回一个空对象。
- 如果传进去的是一个基本类型的值，则会构造其包装类型的对象。
- 如果传进去的是引用类型的值，仍然会返回这个值，经他们复制的变量保有和源对象相同的引用地址。
- 当以非构造函数形式被调用时，Object 的行为等同于 new Object()。

语法格式：

```js
// 以构造函数形式来调用
new Object([value])
```

value 可以是任何值。

以下实例使用 Object 生成布尔对象：

```js
// 等价于 o = new Boolean(true);
var o = new Object(true);
```

这个例子创建了对象的一个新实例，并向其添加了四个属性：

```js
person=new Object();
person.firstname="John";
person.lastname="Doe";
person.age=50;
person.eyecolor="blue";
```

也可以使用对象字面量来创建对象，语法格式如下：

```
{ name1 : value1, name2 : value2,...nameN : valueN }
```

其实就是大括号里面创建 **name:value** 对，然后 **name:value** 对之间以逗号 **,** 隔开。

```js
person={firstname:"John",lastname:"Doe",age:50,eyecolor:"blue"};	
```

JavaScript 对象就是一个 **name:value** 集合。

### 使用对象构造器

---

本例使用函数来构造对象：

```js
function person(firstname,lastname,age,eyecolor){
	this.firstname=firstname;
	this.lastname=lastname;
	this.age=age;
    this.eyecolor=eyecolor;
}
```

在JavaScript中，this通常指向的是我们正在执行的函数本身，或者是指向该函数所属的对象（运行时）

一旦您有了对象构造器，就可以创建新的对象实例，就像这样：

```js
var myFather=new person("John","Doe",50,"blue");
var myMother=new person("Sally","Rally",48,"green");
```

您可以通过为对象赋值，向已有对象添加新属性：

假设 person 对象已存在 - 您可以为其添加这些新属性：firstname、lastname、age 以及 eyecolor：

```js
person.firstname="John";
person.lastname="Doe";
person.age=30;
person.eyecolor="blue";

x=person.firstname;
```

在以上代码执行后，x 的值将是：

```
John
```

### 把方法添加到 JavaScript 对象

---

方法只不过是附加在对象上的函数。

在构造器函数内部定义对象的方法：

```js
function person(firstname,lastname,age,eyecolor)
{
    this.firstname=firstname;
    this.lastname=lastname;
    this.age=age;
    this.eyecolor=eyecolor;

    this.changeName=changeName;
    function changeName(name)
    {
        this.lastname=name;
    }
}
```

changeName() 函数 name 的值赋给 person 的 lastname 属性。

# JavaScript prototype（原型对象）

---

所有的 JavaScript 对象都会从一个 prototype（原型对象）中继承属性和方法。

在前面的章节中我们学会了如何使用对象的构造器（constructor）：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
}
 
var myFather = new Person("John", "Doe", 50, "blue");
var myMother = new Person("Sally", "Rally", 48, "green");
```

我们也知道在一个已存在构造器的对象中是不能添加新的属性：

```js
Person.nationality = "English";
```

Person.nationality 的结果是undefined

要添加一个新的属性需要在在构造器函数中添加：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
  this.nationality = "English";
}
```

## prototype 继承

---

所有的 JavaScript 对象都会从一个 prototype（原型对象）中继承属性和方法：

- `Date` 对象从 `Date.prototype` 继承。
- `Array` 对象从 `Array.prototype` 继承。
- `Person` 对象从 `Person.prototype` 继承。

所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例。

JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

`Date` 对象, `Array` 对象, 以及 `Person` 对象从 `Object.prototype` 继承。

## 添加属性和方法

---

有的时候我们想要在所有已经存在的对象添加新的属性或方法。

另外，有时候我们想要在对象的构造函数中添加属性或方法。

使用 prototype 属性就可以给对象的构造函数添加新的属性：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
}
 
Person.prototype.nationality = "English";
```

当然我们也可以使用 prototype 属性就可以给对象的构造函数添加新的方法：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
}
 
Person.prototype.name = function() {
  return this.firstName + " " + this.lastName;
};
```

# JavaScript Number 对象

---

JavaScript 只有一种数字类型。

可以使用也可以不使用小数点来书写数字。

JavaScript 数字可以使用也可以不使用小数点来书写：

```js
var pi=3.14;  // 使用小数点
var x=34;    // 不使用小数点
```

极大或极小的数字可通过科学（指数）计数法来写：

```js
var y=123e5;  // 12300000
var z=123e-5;  // 0.00123
```

## 所有 JavaScript 数字均为 64 位

---

JavaScript 不是类型语言。与许多其他编程语言不同，JavaScript 不定义不同类型的数字，比如整数、短、长、浮点等等。

在JavaScript中，数字不分为整数类型和浮点型类型，所有的数字都是由 浮点型类型。JavaScript 采用 IEEE754 标准定义的 64 位浮点格式表示数字，它能表示最大值（Number.MAX_VALUE）为 **±1.7976931348623157e+308**，最小值（Number.MIN_VALUE）为 **±5e-324**。

此格式用 64 位存储数值，其中 0 到 51 存储数字（片段），52 到 62 存储指数，63 位存储符号：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/490px-General_floating_point_frac.svg.png)

| 值 (aka Fraction/Mantissa) | 指数（Exponent）  | 符号（Sign） |
| :------------------------- | :---------------- | :----------- |
| 52 bits (0 - 51)           | 11 bits (52 - 62) | 1 bit (63)   |

![img](https://gitee.com/letefly/NoteImages/raw/master/img/Float_mantissa_exponent.png)

## 精度

整数（不使用小数点或指数计数法）最多为 15 位。

```js
var x = 999999999999999;   // x 为 999999999999999
var y = 9999999999999999;  // y 为 10000000000000000
```

小数的最大位数是 17，但是浮点运算并不总是 100% 准确：

```js
var x = 0.2+0.1; // 输出结果为 0.30000000000000004
```

## 八进制和十六进制

如果前缀为 0，则 JavaScript 会把数值常量解释为八进制数，如果前缀为 0 和 "x"，则解释为十六进制数。

```js
var y = 0377;
var z = 0xFF;
```

 绝不要在数字前面写零，除非您需要进行八进制转换。 

默认情况下，JavaScript 数字为十进制显示。

但是你可以使用 toString() 方法 输出16进制、8进制、2进制。

```js
var myNumber=128;
myNumber.toString(16);   // 返回 80
myNumber.toString(8);    // 返回 200
myNumber.toString(2);    // 返回 10000000
```

## 无穷大（Infinity）

当数字运算结果超过了JavaScript所能表示的数字上限（溢出），结果为一个特殊的无穷大（infinity）值，在JavaScript中以Infinity表示。同样地，当负数的值超过了JavaScript所能表示的负数范围，结果为负无穷大，在JavaScript中以-Infinity表示。无穷大值的行为特性和我们所期望的是一致的：基于它们的加、减、乘和除运算结果还是无穷大（当然还保留它们的正负号）。

```js
myNumber=2;
while (myNumber!=Infinity)
{
    myNumber=myNumber*myNumber; // 重复计算直到 myNumber 等于 Infinity
}
```

除以0也产生了无限:

```js
var x = 2/0;
var y = -2/0;
```

## NaN - 非数字值

NaN 属性是代表非数字值的特殊值。该属性用于指示某个值不是数字。可以把 Number 对象设置为该值，来指示其不是数字值。

你可以使用 isNaN() 全局函数来判断一个值是否是 NaN 值。

```js
var x = 1000 / "Apple";
isNaN(x); // 返回 true
var y = 100 / "1000";
isNaN(y); // 返回 false
```

除以0是无穷大，无穷大是一个数字:

```js
var x = 1000 / 0;
isNaN(x); // 返回 false
```

## 数字可以是数字或者对象

数字可以私有数据进行初始化，就像 x = 123;

JavaScript 数字对象初始化数据， var y = new Number(123);

```js
var x = 123;
var y = new Number(123);
typeof(x) // 返回 Number
typeof(y) // 返回 Object
(x === y) // 为 false，因为 x 是一个数字，y 是一个对象
```

## Number 属性

| 属性                     | 描述                                                  |
| :----------------------- | :---------------------------------------------------- |
| Number.MAX_VALUE         | 最大值                                                |
| Number.MIN_VALUE         | 最小值                                                |
| Number.NaN               | 非数字                                                |
| Number.NEGATIVE_INFINITY | 负无穷，在溢出时返回                                  |
| Number.POSITIVE_INFINITY | 正无穷，在溢出时返回                                  |
| Number.EPSILON           | 表示 1 和比最接近 1 且大于 1 的最小 Number 之间的差别 |
| Number.MIN_SAFE_INTEGER  | 最小安全整数。                                        |
| Number.MAX_SAFE_INTEGER  | 最大安全整数。                                        |

## 数字方法

| 方法                   | 描述                                                         |
| :--------------------- | :----------------------------------------------------------- |
| Number.parseFloat()    | 将字符串转换成浮点数，和全局方法 [parseFloat()](https://www.runoob.com/jsref/jsref-parsefloat.html) 作用一致。 |
| Number.parseInt()      | 将字符串转换成整型数字，和全局方法 [parseInt()](https://www.runoob.com/jsref/jsref-parseint.html) 作用一致。 |
| Number.isFinite()      | 判断传递的参数是否为有限数字。                               |
| Number.isInteger()     | 判断传递的参数是否为整数。                                   |
| Number.isNaN()         | 判断传递的参数是否为 isNaN()。                               |
| Number.isSafeInteger() | 判断传递的参数是否为安全整数。                               |

## 数字类型原型上的一些方法

| 方法            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| toExponential() | 返回一个数字的指数形式的字符串，如：1.23e+2                  |
| toFixed()       | 返回指定小数位数的表示形式。`var a=123; b=a.toFixed(2); // b="123.00"` |
| toPrecision()   | 返回一个指定精度的数字。如下例子中，a=123 中，3会由于精度限制消失:`var a=123; b=a.toPrecision(2); // b="1.2e+2"` |

