## 属性选择器

---

属性选择器的权重是0010

**写法1 某某[属性]**

```css
/*选择到a标签且有title属性的*/
a[title]{
	background: yellow;
}

a[lang][target]{
	background: yellow;
}
```

**写法2: 某某[属性=属性值]**

```css
/*选择到有某某标签有指定属性且属性值必须一摸一样的也有的多一个空格都不行*/
a[lang="zh"]{
	background: yellow;
}
a[title="this is a link"]{
	background: yellow;
}
/* class名字是item的,且有属性lang且属性值必须一模一样是zh-cn的 */
.item[lang="zh-cn"]{
	background: yellow;
}
/* id是last且有title属性且有class属性,属性值只能是links的变 */
#last[title][class="links"]{
	background: yellow;
}
```

**写法3: 某某[属性^=属性值]**

```css
/* a标签有class属性且属性值是li开头的 */
a[class^="li"]{
	background-color: yellow;
} 

a[title^="this"][lang]{
	background-color: yellow;
} 
```

**写法4 某某[属性$=属性值]**

```CSS
/* a标签有class属性且属性值结尾是t的 */
a[class$="t"]{
	background-color: yellow;
}  

a[href$=".pdf"]{
	background-color: yellow;
}
a[href$=".doc"]{
	background-color: red;
}
a[href$=".png"]{
	background-color: green;
}
```

 **写法5 某某[属性*=属性值]**

```CSS
/* 选择到a标签且有href属性且只要有字母b就可以 */
a[href*="b"]{
	background-color: green;
}
```

**写法6 某某[属性~=属性值]**

```CSS
/* 选择到的是a标签且有class属性,且属性值有完整的itme词的变 */
a[class~="item"]{
	background-color: green;
}
```

**写法7 某某[属性|=属性值]**

```CSS
/* 这个是选择到有a标签,且有属性title,且属性值只有1个是link的或者属性值有多个但是得是link-开头的变 */
a[title|="link"]{
	background-color: green;
}
```

## 深度选择器  /deep/ 

---

需求：在某一组件中想要修改全局组件的样式（全局样式），但又不想直接修改全局组件的样式，而是仅在当前组件引用时更改，如：一组件要更改element对话框的内容（.el-dialog .el-dialog__body）的高度，但又不能影响到其它组件的正常使用。这种时候就需要用到/deep/.

```css
/deep/ .el-dialog .el-dialog__body{
  max-height: 71vh;
}
```

注意是在scope里更改。

## background-clip

---

Clip 的意思为修剪，那么从字面意思上理解，`background-clip` 的意思即是背景裁剪。`background-clip` 的作用就是设置元素的背景（背景图片或颜色）的填充规则。

与 `box-sizing` 的取值非常类似，通常而言，它有 3 个取值：

```css
{
    // 背景延伸到边框外沿（但是在边框之下）
    background-clip: border-box;  
    // 边框下面没有背景，即背景延伸到内边距外沿。
    background-clip: padding-box;
    // 背景裁剪到内容区 (content-box) 外沿。
    background-clip: content-box; 
}
```

## 文字透视与渐变效果

---

`-webkit-background-clip:text;`：让背景裁剪到文本区，即除文本以外的区域是看不到背景的效果的；

`color: transparent;`：让文本显示透明，配合上`-webkit-background-clip:text;`就可以让背景的效果作用到文本上了；

`background`：在背景效果中指定背景颜色或者渐变效果或者图片，结合上面的属性就可以让背景的效果作用到文本上；

```css
//渐变效果
{
   background: linear-gradient(to right, rgb(255, 0, 0) 0%, rgba(0, 0, 255, .8) 50%, rgb(128, 0, 128) 80%);
   -webkit-background-clip: text;
   color: transparent;
 }
```

```css
//透视效果
{
    color: transparent;
    background: url($img) no-repeat center center;
    background-size: cover;
    -webkit-background-clip: text;
}
```

## 隐藏input number的箭头

---

```javascript
.input_number{
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
        -webkit-appearance: none;
    }
    input[type="number"]{
        -moz-appearance: textfield;
    }
}
```

## white-space

---

- **pre**	空白会被浏览器保留。其行为方式类似 HTML 中的 `<pre>` 标签。
- **nowrap**	文本不会换行，文本会在在同一行上继续，直到遇到`<br>`标签为止。
- **pre-wrap**	保留空白符序列，但是正常地进行换行。
- **pre-line**	合并空白符序列，但是保留换行符。
- **inherit**	规定应该从父元素继承 white-space 属性的值。

## 修改自动填充功能的默认样式

---

在谷歌浏览器上面登录页面时，如果之前记住了帐号密码的话，那么可以自由的选择登陆的账号，谷歌浏览器会自动自动填充密码， 而你无需再输入密码 。但是填充之后，默认的输入框样式却被修改了,那么如何去修改呢？

```css
input:-webkit-autofill {
    -webkit-box-shadow: 0 0 0 1000px #464646 inset !important;
    box-shadow: 0 0 0 1000px #464646 inset !important;
    -webkit-text-fill-color: #f0f0f0;
    caret-color: #fff;
｝
```

