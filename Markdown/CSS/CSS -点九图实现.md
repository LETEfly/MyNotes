## 什么是点九图

点九”是andriod平台的应用软件开发里的一种特殊的图片形式，文件扩展名为：`.9.png`。其实相当于把一张png图分成了9个部分（九宫格），分别为4个角，4条边，以及一个中间区域。如下图，在图片整体拉伸时，可以保持①③⑥⑧不变，保证圆角等细节，②⑦横向拉伸，④⑤纵向拉伸，避免了普通拉伸的模糊失真。

![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/v2-517187a521d718a41102e74c086c1aa0_720w.webp)

最简单的例子，微信对话框。

你不可能罗列出所有用户打字的多少，因此也不可能预先定义对话框的尺寸，对于下图这种基础对话框，

![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/v2-c8e87202eda351043b50b1ac4215160e_720w.webp)

简单粗暴的拉伸只能得到下图

![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/v2-53d0892e6f0ff759281cfef6e88eee3c_720w.webp)

而使用点九图后，拉伸的就优雅多了：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/v2-a8e99a638da1a43ea2051a26d67714b0_720w.webp)

## Web端点九图的实现方式

> **border-image-slice**：规定图像边框的向内偏移
>
> - 默认值：100%
> - 传承性：no
> - 版本：css3
> - JavaScript语法：object.style.borderImageSlice="50% 50%"
>
> **语法：**：border-image-slice: number|%|fill;

该属性规定图像的上、右、下、左侧边缘的向内偏移，图像被分割为九个区域：四个角、四条边以及一个中间区域。除非使用了关键词 fill，否则中间的图像部分会被丢弃。如果省略第四个数值/百分比，则与第二个值相同。如果省略第三个值，则与第一个值相同。如果省略第二个值，则与第一个值相同。

例如以下这张图，在PS中，它的尺寸和各部分已经被预先设定好：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/v2-4688dbc167dcb333e3a32c5f161f6ab6_720w.webp)

像点九图一样，他被平均划分为9个27*27的小方格

```vue
<template>
	<div class="box"></div>
</template>
<style>
    .box{
        /*规定边框的粗细、形态、颜色*/
        border:27px solid #000;
        /*规定引入图片的地址*/
        border-image-source:url(border.png);
        /*规定上右下左四个内偏移区域27px处分割*/
        border-image-slice:27 27 27 27;
        /*覆盖border:27px，直接指定边框图片宽度。这里把四个边都设定成27px*/
        border-image-width:27px 27px 27px 27px;
        /*让边框背景延伸到盒子外*/
        border-image-outset:27px 27px 27px 27px;
        border-image-repeat:repeat;
    }
</style>
```

这样子对图片进行随意拉伸就不会出现变形了

![image-20230304205839819](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/image-20230304205839819.png)