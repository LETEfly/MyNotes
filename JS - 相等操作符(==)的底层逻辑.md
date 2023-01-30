相等操作符是比较两个值是否相等，**在比较前会将两个值转换为相同的类型，‘==’的两边都有可能被转换**，最终的比较方式跟全等操作符（===）一致，相等操作符满足交换律。

| 类型      | Undefined | Null | Number | String | boolean | Object     |
| :-------- | :-------- | :--- | :----- | :----- | :------ | :--------- |
| Undefined | true      | true | false  | false  | false   | Isfalsy(B) |
| Null      | true      | true | false  | false  | false   | Isfalsy(B) |

上面的表格的意思就是两两互相比较，Object、String、Number、Boolean根据以下顺序进行转换为相同类型后再进行比较。

![image-20221215182431772](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221215182431772.png)

```js
[] == true //结果为false
//运算逻辑等价于
Number([]) === Number(true) 
```

一般而言，根据 ECMAScript 规范，所有的对象都与 `undefined` 和 `null` 不相等。但是大部分浏览器允许非常窄的一类对象（即，所有页面中的 `document.all` 对象），在某些情况下，充当效仿 `undefined` 的角色。相等操作符就是在这样的一个背景下。因此，`IsFalsy(A)` 方法的值为 true ，当且仅当 A 效仿 `undefined`。在其他所有情况下，一个对象都不会等于 `undefined` 或 `null`。

```js
var num = 0;
var obj = new String("0");
var str = "0";
var b = false;

console.log(num == num); // true
console.log(obj == obj); // true
console.log(str == str); // true

console.log(num == obj); // true
console.log(num == str); // true
console.log(obj == str); // true
console.log(null == undefined); // true

// 两者都是false  除了极少数的情况
console.log(obj == null); 
console.log(obj == undefined);
```

除了一个类型，每个值也有一个固有的布尔值，通常称为truthy或falsy。
关于`falsy` ,`truthy` 可以这样理解：

```js
if (value) {
  // value is truthy
}
else {
  // value is falsy
  // it could be false, 0, '', null, undefined or NaN
}
```

**有些开发者认为，最好永远都不要使用相等操作符。全等操作符的结果更容易预测，并且因为没有隐式转换，全等比较的操作会更快。**