## **方法一**

```css
<style type="text/css">
html {
  filter:grayscale(100%);
  -webkit-filter:grayscale(100%);
  -moz-filter:grayscale(100%);
  -ms-filter:grayscale(100%);
  -o-filter:grayscale(100%);
 filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
  -webkit-filter:grayscale(1)
}
</style>
```

filter是滤镜的意思，filter:gray的意思就是说给页面加上一个灰度的滤镜，所以html里面的所有内容都会变成黑白的了。不过这个滤镜对于chrome和safari浏览器是无效的，所以下面会有一行-webkit-filter: grayscale(100%);这个样式是专属于使用webkit内核的浏览器的，意思和FILTER: gray;差不多，只是写法不同罢了。

## **方法二**

下面这段代码可以把网页变为黑白，将代码加到 CSS 最顶端就可以实现素装，如果网站没有使用 CSS，可以在网页/模板的 HTML 代码和 之间插入：

```css
<style>
 html {
 filter: progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
 -webkit-filter: grayscale(100%);}
</style>
```

有一些网站可能使用这个 css 不能生效，是因为网站没有使用最新的网页标准协议，请将网页最头部的替换为以下代码：

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
```

还有一些网站 FLASH 动画的颜色不能被 CSS 滤镜控制，可以在 FLASH 代码的和之间插入：

```html
<param value="false" name="menu"/>
<param value="opaque" name="wmode"/>
```

## **最后**

给出一段规范的代码，把这段代码加入到网站页面的css里面即可实现页面变成灰色的效果：

```css
html{
    -webkit-filter:grayscale(100%);
    -moz-filter:grayscale(100%);
    -ms-filter:grayscale(100%);
    -o-filter:grayscale(100%);
    filter:grayscale(100%);
    filter:url("data:image/svg+xml;utf8,<svg xmlns=\'http://www.w3.org/2000/svg\'><filter id=\'grayscale\'><feColorMatrix type=\'matrix\' values=\'0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0 0 0 1 0\'/></filter></svg>#grayscale");
    filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1)
}
```

