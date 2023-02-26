## 深度选择器  /deep/ 

需求：在某一组件中想要修改全局组件的样式（全局样式），但又不想直接修改全局组件的样式，而是仅在当前组件引用时更改，如：一组件要更改element对话框的内容（.el-dialog .el-dialog__body）的高度，但又不能影响到其它组件的正常使用。这种时候就需要用到/deep/.

```css
/deep/ .el-dialog .el-dialog__body{
  max-height: 71vh;
}
```

注意是在scope里更改。

## background-clip

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

## 