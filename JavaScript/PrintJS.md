# Print.js 

> 一个微小的JavaScript库，用于帮助从Web打印。
>
> 该文档对应版本：v1.5.0

[官网地址](https://printjs.crabbly.com) | [仓库地址](https://github.com/crabbly/print.js)

## 打印PDF文件

Print.js的主要目的是帮助我们在应用程序中直接打印PDF文件，而无需离开界面，也不用嵌入。适用于用户无需打开或下载PDF文件的情况，他们只需直接打印即可。

例如，用户在获取服务器生成的报告时，这些报告以PDF的形式返回给用户。这时候如果用户想要直接打印而不想下载也不想打开，那可以通过Print.js来实现这一操作。

> PDF文件的访问路径必须与应用程序所在路径处于相同的域中。Print.js在打印文件之前使用iframe加载文件，因此受同源策略的限制。这是为了防止跨站点脚本（XSS）攻击。

**例子**

```html
<!--添加按钮以打印本地服务器上的PDF文件-->
<button type="button" onclick="printJS('docs/printjs.pdf')">
    Print PDF
</button>

<!--在加载大型文件的时候，由于加载时间较长，可以向用户显示加载信息-->
<button type="button" onclick="printJS({printable:'docs/xx_large_printjs.pdf', type:'pdf', showModal:true})">
    Print PDF with Message
</button>
```

## 打印HTML页面

有时候我们只想要打印HTML页面的部分选定的内容，但是实现起来是比较麻烦的。而如果使用Print.js，我们就可以将要打印的元素id传递给它进行打印。元素可以是任何标签，只要它具有唯一标识的id。Print.js将会以非常接近屏幕外观的效果进行打印。

**例子**

```html
 <!--添加按钮以打印HTML表单-->
<form method="post" action="#" id="printJS-form">
    ...
</form>
<button type="button" onclick="printJS('printJS-form', 'html')">
    Print Form
</button>

<!--向页面添加页眉-->
<button type="button" onclick="printJS({ printable: 'printJS-form', type: 'html', header: 'PrintJS - Form Element Selection' })">
    Print Form with Header
</button>

```

## 图片打印

通过传递图像url，Print.js可以用于快速打印页面上的任何图像。当屏幕上有多个图像时，可以使用低分辨率的图像进行显示，在用户尝试打印所选图像时，再将高分辨率url传递给print.js。

**例子**

```js
//添加页眉信息并打印图片
printJS({printable: 'images/print-01-highres.jpg', type: 'image', header: 'My cool image header'})

//打印多张图片
printJS({
  printable: ['images/print-01-highres.jpg', 'images/print-02-highres.jpg', 'images/print-03-highres.jpg'],
  type: 'image',
  header: 'Multiple Images',
  imageStyle: 'width:50%;margin-bottom:20px;'
})
```

## JSON数据打印

以一种简单高效的方式打印JS 对象形式的动态数据或者数组。

```js
//这些数据可能来源于后端
const someJSONdata = [
    {
       name: 'John Doe',
       email: 'john@doe.com',
       phone: '111-111-1111'
    },
    {
       name: 'Barry Allen',
       email: 'barry@flash.com',
       phone: '222-222-2222'
    },
    {
       name: 'Cool Dude',
       email: 'cool@dude.com',
       phone: '333-333-3333'
    }
]
//利用Print.js将上面的数据以表格的方式进行打印
printJS({printable: someJSONdata, properties: ['name', 'email', 'phone'], type: 'json'})
//可以给表格(grid)加一些自定义样式
printJS({
	    printable: someJSONdata,
	    properties: ['name', 'email', 'phone'],
	    type: 'json',
	    gridHeaderStyle: 'color: red;  border: 2px solid #3971A5;',
	    gridStyle: 'border: 2px solid #3971A5;'
	})
//可以加入HTML的头部内容
printJS({
		printable: someJSONdata,
		type: 'json',
		properties: ['name', 'email', 'phone'],
		header: '<h3 class="custom-h3">My custom header</h3>',
		style: '.custom-h3 { color: red; }'
	  })
```

## 下载和安装

### npm

```bash
npm install print-js --save
```

### yarn

```bash
yarn add print-js
```

使用npm或者yarn安装时，通过以下命令进行引入

```js
import print from 'print-js'
```

### cdn

```
https://printjs-4de6.kxcdn.com/print.min.js
https://printjs-4de6.kxcdn.com/print.min.css
```

## 参数配置

| 参数               | 默认值                                              | 说明                                                         |
| ------------------ | --------------------------------------------------- | ------------------------------------------------------------ |
| printable          | null                                                | PDF或图像：URL地址<br />HTML：元素id<br />JSON：数组         |
| type               | 'pdf'                                               | 打印的类型，可选值：pdf、html、image、json、raw-html         |
| header             | null                                                | 用于HTML、图像或JSON打印的可选标头。它将放在页面顶部。此属性接收文本或HTML的字符串 |
| headerStyle        | 'font-weight: 300;'                                 | 标头样式文本                                                 |
| maxWidth           | 800                                                 | 用于HTML、图像或JSON打印时设置文档的最大宽度（单位：px），在打印HTML时建议与打印内容的宽度保持一致，**不然会被压缩** |
| css                | null                                                | 可传入单个css文件地址URL地址，或者以字符串数组形式传入多个css文件URL地址 |
| style              | null                                                | 传入自定义样式的字符串，作用于被打印的HTML标签上             |
| scanStyles         | true                                                | 设置为false时，原本的css样式将会失效，这时候可以使用css参数来引入自定义的样式效果 |
| targetStyle        | null                                                | 在打印HTML元素时，只让某些样式生效。该参数允许你传入要生效的样式数组。如['padding-bottom'，‘border-top’] |
| targetStyles       | null                                                | 和targetStyle类似，不过它会处理所有相关的样式，比如['border', 'padding']将会包括‘padding-top’、‘border-top’、‘padding-left’、‘border-left’……<br />也可以传递['*']来处理所有样式 |
| ignoreElements     | [ ]                                                 | 打印HTML时忽略打印的内容，以字符串数组形式传入元素的id       |
| properties         | null                                                | 打印JSON时传入的表头名称，要和JSON中的键值对的键保持一致。   |
| gridHeaderStyle    | 'font-weight: bold;'                                | 打印JSON时，表头的自定义样式                                 |
| gridStyle          | 'border: 1px solid lightgray; margin-bottom: -1px;' | 打印JSON时，表格的自定义样式                                 |
| repeatTableHeader  | true                                                | 打印JSON时，若为false表头仅显示在第一页，否则在表格的其它页也会显示表头 |
| showModal          | null                                                | 若设为true将会显示加载动画和加载提示信息(modalMessage)       |
| modalMessage       | 'Retrieving Document...'                            | 加载提示信息文本                                             |
| onLoadingStart     | null                                                | 加载PDF时要执行的函数                                        |
| onLoadingEnd       | null                                                | 加载PDF后要执行的函数                                        |
| documentTitle      | 'Document'                                          | 打印HTML、图像或JSON时显示的文档标题                         |
| fallbackPrintable  | null                                                | 在打印PDF时，如果浏览器不兼容（检查浏览器兼容性表），PrintJS将在新的选项卡中打开pdf。可以传入另一个pdf文档来代替“printable”中的文档。适用于JS被禁用或者无效的情况。 |
| onPdfOpen          | null                                                | 在打印PDF时，如果浏览器不兼容（检查浏览器兼容性表），PrintJS将在新的选项卡中打开pdf。可以在此处传递回调函数，发生浏览器不兼容的情况时将执行回调函数。同时适用于某些情况下，你希望处理打印流、更新用户界面等 |
| onPrintDialogClose | null                                                | 浏览器打印对话框关闭后执行回调函数                           |
| onError            | error => throw error                                | 发生错误时要执行的回调函数                                   |
| base64             | false                                               | 打印作为base64数据传递的PDF文档时使用                        |

## 浏览器兼容

|        | Google Chrome | Safari | Firefox | Edge | Opera | Internet Explorer |
| ------ | ------------- | ------ | ------- | ---- | ----- | ----------------- |
| PDF    | 是            | 是     | 是      | 是   | 是    | 否                |
| HTML   | 是            | 是     | 是      | 是   | 是    | 是                |
| Images | 是            | 是     | 是      | 是   | 是    | 否                |
| JSON   | 是            | 是     | 是      | 是   | 是    | 是                |

