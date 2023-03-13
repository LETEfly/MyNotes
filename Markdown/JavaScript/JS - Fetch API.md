## 了解fetch

> - Fetch API 提供了一个**获取资源的接口**（包括跨域请求），用于取代传统的XMLHttpRequest的，在 JavaScript 脚本里面发出 HTTP 请求。
>
> - 目前还没有被所有浏览器支持，如果考虑低版本浏览器的问题的话，手动引入[fetch.js](https://github.com/github/fetch/blob/master/fetch.js)即可兼容。 	
> - Fetch API是基于**Promise**的设计，返回的是Promise对象，它是为了取代传统xhr的不合理的写法而生的。

没有fetch时我们获取异步资源的方式，例如：发送一个get请求，首先实例化一个`XMLHttpResquest`对象，注册`httpRequest.readyState`，它改变时会回调函数`xhr.onreadystatechange`。readyState共有5个可能的值：

- **UNSENT (未打开)**	open()方法还未被调用;
- **OPENED  (未发送)**	send()方法还未被调用;
- **HEADERS_RECEIVED (已获取响应头)**	send()方法已经被调用, 响应头和响应状态已经返回;
- **LOADING (正在下载响应体)**	响应体下载中; responseText中已经获取了部分数据;
- **DONE (请求完成)**	整个请求过程已经完毕.

```js
var xhr = new XMLHttpResquest();
xhr.onreadystatechange = function(){
	//该回调函数会被依次调用4次
	console.log(xhr.resdyState);
	   //请求已完成
	if(xhr.readyState===4){
 		//http状态为200
       if(xhr.status===200){
         //打印响应来的数据
         console.log(xhr.response);
        //JSON.parse()将JSON格式的字符串转化为JSON对象
         var data = JSON.parse(xhr.response);
         //打印得到的JSON对象
         console.log(data);
       }
 	}
}
//请求的网址
var url = '网址';
//该方法为初始化请求,第一个参数是请求的方法,比如GET,POST,PUT,第二个参数是请求的url,第三个参数为true表示发送异步请求
xhr.open('GET',url,true);

//设置http请求头
httpRequest.setRequestHeader("Content-Type","application/json");

//发出请求,参数为要发送的body体,如果是GET方法的话，一般无需发送body,设为空就可以
httpRequest.send(null);
```

使用fetch后我们获取异步资源的方式:

```js
//请求的网址
var url = '网址';;
//发起get请求
var promise = fetch(url).then(function(response) {

   //response.status表示响应的http状态码
   if(response.status === 200){
     //json是返回的response提供的一个方法,会把返回的json字符串反序列化成对象,也被包装成一个Promise了
     return response.json();
   }else{
     return {}
   }
});
    
promise = promise.then(function(data){
  //响应的内容
	console.log(data);
}).catch(function(err){
	console.log(err);
})
```

## XMLHttpResquest与Fetch相比较

- fetch()使用 Promise，不使用回调函数，因此大大简化了写法，写起来更简洁。
- fetch()采用模块化设计，API 分散在多个对象上（Response 对象、Request 对象、Headers 对象），更合理一些；相比之下，XMLHttpRequest 的 API 设计并不是很好，输入、输出、状态都在同一个接口管理，容易写出非常混乱的代码。
- fetch()通过**数据流（Stream对象）**处理数据，可以分块读取，有利于提高网站性能表现，减少内存占用，对于请求大文件或者网速慢的场景相当有用。XMLHTTPRequest对象不支持数据流，所有的数据必须放在缓存里，不支持分块读取，必须等待全部拿到后，再一次性吐出来。

## Fetch的语法

```js
fetch(url).then(...).catch(...)
```

示例：

```js
fetch('网址')
	// fetch()接收到的response是一个 Stream 对象
	// response.json()是一个异步操作，取出所有内容，并将其转为 JSON 对象
  .then(response => response.json()) 
  .then(json => console.log(json))//获取到的json数据
  .catch(err => console.log('Request Failed', err)); 

// 等价于以下写法
async function getJSON() {
  let url = '网址';
  try {
    let response = await fetch(url);
    return await response.json();
  } catch (error) {
    console.log('Request Failed', error);
  }
}
console.log(getJSON());	// 获取到的json数据

```

## Fetch的Response对象

### 同步属性

- `fetch()`请求成功以后，得到的是一个 `Response` 对象。它对应服务器的 `HTTP `回应。
- `Response` 包含的数据通过 `Stream` 接口异步读取，但是它还包含一些同步属性，对应 HTTP 回应的标头信息（Headers），可以立即读取。

**示例：**

```js
async function getfetchText() {
  let response = await fetch('网址');
  console.log(response.status); // 获取http状态码 //200
  console.log(response.statusText);	// 获取http回应的状态信息
}
getfetchText()
```

**标头信息的属性有：**

- response.ok：返回一个布尔值，表示请求是否成功
例如：true对应 HTTP 请求的状态码 200 到 299，false对应其他的状态码。

 - response.status：返回一个数字，表示 HTTP 回应的状态码
例如：200，表示成功请求

 - response.statusText属性返回一个字符串，表示 HTTP 回应的状态信息
例如：请求成功以后，服务器返回"OK"

 - response.url：返回请求的 URL。
如果： URL 存在跳转，该属性返回的是最终 URL。

 - response.redirected：返回一个布尔值，表示请求是否发生过跳转。

 - response.type：返回请求的类型。可能的值如下：
basic：普通请求，即同源请求。
cors：跨域请求。
error：网络错误，主要用于 Service Worker。

### 判断请求是否成功发出

第一种方法：

- fetch()发出请求以后，只有网络错误或者无法连接时，fetch()才会报错，其他情况都不会报错，而是认为请求成功。
- 只有通过Response.status属性，得到HTTP 回应的真实[状态码](https://so.csdn.net/so/search?q=状态码&spm=1001.2101.3001.7020)，才能判断请求是否成功

**示例：**

```js
async function getfetchText() {
  let response = await fetch('网址');
  if (response.status >= 200 && response.status < 300) {
    return await response.text();
  } else {
    throw new Error(response.statusText);
  }
}
```

第二种方法：

判断`response.ok`是否为true

**示例：**

```js
if (response.ok) {
  // 请求成功
  console.log('请求成功')
} else {
  // 请求失败
  console.log('请求失败')
}
```

### 操作标头

- Response 对象还有Response.headers属性，指向一个 Headers 对象，对应 HTTP 回应的所有标头。
- Headers 对象可以使用for…of循环进行遍历

**示例：**

```js
const response = await fetch(url);

for (let [key, value] of response.headers) { 
  console.log(`${key} : ${value}`);  
}

// 或者

for (let [key, value] of response.headers.entries()) { 
//response.heasers.entries()方法返回一个遍历器，可以依次遍历所有键值对([key,value])
  console.log(`${key} : ${value}`);  
}
```

**用来操作标头的方法有：**

下面的有些方法可以修改标头，那是因为继承自 Headers 接口。对于 HTTP回应来说，修改标头意义不大，况且很多标头是只读的，浏览器不允许修改。比较常用的也就是`response.headers.get()`

```js
const response = await fetch(url);
//根据指定的键名，返回键值。
response.headers.get()
//返回一个布尔值，表示是否包含某个标头。
response.headers.has()
//将指定的键名设置为新的键值，如果该键名不存在则会添加。
response.headers.set()
//添加标头。
response.headers.append()
//删除标头。
response.headers.delete()
//返回一个遍历器，可以依次遍历所有键名。
response.headers.keys()
//返回一个遍历器，可以依次遍历所有键值。
response.headers.values()
//返回一个遍历器，可以依次遍历所有键值对（[key, value]）。
response.headers.entries()
//依次遍历标头，每个标头都会执行一次参数函数。
response.headers.forEach()
```

### 读取Response对象内容的方法

```js
const response = await fetch(url);
//得到文本字符串，用于获取文本数据，比如 HTML 文件。
response.text()
//得到 JSON 对象。
response.json()
//得到二进制 Blob 对象，例如读取图片文件，显示在网页上。
response.blob()
//得到 FormData 表单对象，主要用在 Service Worker 里面，拦截用户提交的表单，修改某些数据以后，再提交给服务器。
response.formData()
//得到二进制 ArrayBuffer 对象，主要用于获取流媒体文件。
response.arrayBuffer()
```

### 创建副本（clone）

**Stream 对象只能读取一次**，读取完就没了,这意味着五个读取方法，只能使用一个，否则会报错。

**示例：**

```js
// 先使用了response.text()，就把 Stream 读完了。
// 后面再调用response.json()，就没有内容可读了，所以报错。
let text =  await response.text();
let json =  await response.json();  // 报错
```

Response 对象提供`Response.clone()`方法，创建Response对象的副本，实现多次读取。

**示例：**

```js
const response1 = await fetch('图片地址');
// response.clone()复制了一份 Response 对象，然后将同一张图片读取了两次
const response2 = response1.clone();

const myBlob1 = await response1.blob();
const myBlob2 = await response2.blob();

image1.src = URL.createObjectURL(myBlob1);
image2.src = URL.createObjectURL(myBlob2);
```

### 底层接口

`Response.body`是 Response 对象暴露出的底层接口，返回一个 `ReadableStream` 对象，供用户操作

例如：**用来分块读取内容，显示下载的进度**

```js
const response = await fetch('图片地址');
// response.body.getReader()方法返回一个遍历器
const reader = response.body.getReader();

while(true) {
  // 这个遍历器的read()方法每次返回一个对象，表示本次读取的内容块
  const {done, value} = await reader.read();
  // done属性是一个布尔值，用来判断有没有读完
  if (done) {
    break;
  }
  // value属性是一个 arrayBuffer 数组，表示内容块的内容，而value.length属性是当前块的大小
  console.log(`Received ${value.length} bytes`)
}
```

## 定制HTTP请求

fetch()的第一个参数是 URL，还可以接受第二个参数optionObj，作为配置对象，定制发出的 HTTP 请求。

HTTP 请求的方法、标头、数据体都在这个`optionObj`对象里面设置

```js
fetch(url,optionObj)
```

`fetch()`请求的底层用的是 `Request()` 对象的接口，参数完全一样，因此上面的 API 也是 `Request()`的 API

**fetch()的第二个参数的完整API如下：**

```js
const response = fetch(url, {
  //请求方式
  method: "GET",
  //定制http请求的标头
  headers: {
    "Content-Type": "text/plain;charset=UTF-8"
  },
  //post请求的数据体，因为此时为get请求，故为undefined
  body: undefined,
  referrer: "about:client",
  //用于设定fetch请求的referer标头
  referrerPolicy: "no-referrer-when-downgrade",
  //指定请求模式，此时为cors表示支持跨域请求
  mode: "cors", 
  //发送cookie
  credentials: "same-origin",
  //指定如何处理缓存
  cache: "default",
  redirect: "follow",
  integrity: "",
  keepalive: false,
  signal: undefined 
});
```

**参数详解：**

```json
method：HTTP 请求的方法，POST、DELETE、PUT都在这个属性设置。
headers：一个对象，用来定制 HTTP 请求的标头。
body：POST 请求的数据体。
cache：指定如何处理缓存。可能的取值如下：
/*
	default：默认值，先在缓存里面寻找匹配的请求。
	no-store：直接请求远程服务器，并且不更新缓存。
	reload：直接请求远程服务器，并且更新缓存。
	no-cache：将服务器资源跟本地缓存进行比较，有新的版本才使用服务器资源，否则使用缓存。
	force-cache：缓存优先，只有不存在缓存的情况下，才请求远程服务器。
	only-if-cached：只检查缓存，如果缓存里面不存在，将返回504错误。
*/
mode: 指定请求的模式。可能的取值如下：
/*
	cors：默认值，允许跨域请求。
	same-origin：只允许同源请求。
	no-cors：请求方法只限于 GET、POST 和 HEAD，并且只能使用有限的几个简单标头，不能添加跨域的复杂标头，相当于提交表单所能发出的请求。
*/
credentials：指定是否发送 Cookie。可能的取值如下：
/*
	same-origin：默认值，同源请求时发送 Cookie，跨域请求时不发送。
	include：不管同源请求，还是跨域请求，一律发送 Cookie。
	omit：一律不发送。
*/
signal：指定一个 AbortSignal 实例，用于取消fetch()请求

keepalive：用于页面卸载时，告诉浏览器在后台保持连接，继续发送数据。
/*
一个典型的场景就是，用户离开网页时，脚本向服务器提交一些用户行为的统计信息。
这时，如果不用keepalive属性，数据可能无法发送，因为浏览器已经把页面卸载了。
*/
redirect: 指定 HTTP 跳转的处理方法。可能的取值如下：
/*
	follow：默认值，fetch()跟随 HTTP 跳转。
	error：如果发生跳转，fetch()就报错。
	manual：fetch()不跟随 HTTP 跳转，但是response.url属性会指向新的 URL，response.redirected属性会变为true，由开发者自己决定后续如何处理跳转。
*/
integrity：指定一个哈希值，用于检查 HTTP 回应传回的数据是否等于这个预先设定的哈希值。
/*
比如，下载文件时，检查文件的 SHA-256 哈希值是否相符，确保没有被篡改
fetch('http://site.com/file', {
  integrity: 'sha256-abcdef'
});
*/
referrer: 用于设定fetch请求的referer标头。
/*
这个属性可以为任意字符串，也可以设为空字符串（即不发送referer标头）。
*/
referrerPolicy: 用于设定Referer标头的规则。可能的取值如下：
/*
	no-referrer-when-downgrade：默认值，总是发送Referer标头，除非从 HTTPS 页面请求 HTTP 资源时不发送。
	no-referrer：不发送Referer标头。
	origin：Referer标头只包含域名，不包含完整的路径。
	origin-when-cross-origin：同源请求Referer标头包含完整的路径，跨域请求只包含域名。
	same-origin：跨域请求不发送Referer，同源请求发送。
	strict-origin：Referer标头只包含域名，HTTPS 页面请求 HTTP 资源时不发送Referer标头。
	strict-origin-when-cross-origin：同源请求时Referer标头包含完整路径，跨域请求时只包含域名，HTTPS 页面请求 HTTP 资源时不发送该标头。
	unsafe-url：不管什么情况，总是发送Referer标头。
*/
```

## 取消fetch请求

`fetch()`请求发送后，如果中途想要取消，需要使用`AbortController`对象

```js
//创建一个AbortController实例
let controller = new AbortController();
fetch(url, {
  signal: controller.signal
});
//给controller.signal绑定监听事件，controller.signal的值改变则会触发abort事件
controller.signal.addEventListener('abort',
  () => console.log('abort!')
);
// controller.abort()方法用于发出取消信号。这时会触发abort事件，这个事件可以监听
controller.abort(); // 取消
// 可以通过controller.signal.aborted属性判断取消信号是否已经发出
console.log(controller.signal.aborted); // true
```

**实例：**

```js
//创建实例
let controller = new AbortController();
//设置定时器
setTimeout(() => controller.abort(), 1000);

try {
  let response = await fetch('路径', {
    signal: controller.signal
  });
} catch(err) {
  if (err.name == 'AbortError') {
    console.log('Aborted!');
  } else {
    throw err;
  }
}
```

