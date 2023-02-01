# PicGo+Gitee+Typora图床搭建与使用

## 背景

我的markdown笔记平常是放在github上托管，以前笔记的图片就放在根目录的image文件下，markdown笔记再通过路径去访问目标图片。然而这么做会导致图片跟笔记的耦合度太高，不方便进行分享或移动。所以需要一个方案把图片和笔记独立开，并且笔记中的图片通过URL进行访问。

涉及技术栈：PicGo 2.3.1+Gitee-uploader1.1.2(PicGo插件)+Node.js≥8+Gitee+Typora1.2.5

> 为什么使用Gitee？
>
> 我的笔记是放在github上的，所以我原先打算放在github上，但是上传到github上的图片没有“科学上网”一般都看不到，所以换了一个比较有好的环境-Gitee，不过也可以考虑使用别的仓库，在安装完PicGo的时候会显示一些默认支持的仓库，如果没有也可以自行百度一下添加其他仓库的方法，一般需要安装插件来支持，Gitee的便是如此。

## 图床搭建

### 下载PicGo以及插件

下载地址：[Molunerfinn/PicGo (github.com)](https://github.com/Molunerfinn/PicGo/releases)，我这边下载的是[PicGo-Setup-2.3.1-x64.exe](https://picgo-1251750343.cos.ap-chengdu.myqcloud.com/2.3.1/PicGo-Setup-2.3.1-x64.exe)版本，如果带有`beta`的为测试版本

![image-20230131013757125](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131013757125.png)

下载安装后即可打开，页面大致如下：

![image-20230131014133383](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131014133383.png)

接下来安装插件，让PicGo支持Gitee的图传，点击插件设置然后输入`Gitee-uploader`，然后点击安装

![image-20230131014327114](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131014327114.png)

这时候图床设置中就会出现Gitee了，我这边因为用不到其他的图传，所以在设置把其他的图床隐藏了

![image-20230131014445511](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131014445511.png)

### 创建Gitee图床仓库

在[Gitee](https://gitee.com/)上创建一个仓库，填写信息然后建议把Readme文件勾选上（方便后面将项目转为开源项目）

![image-20230131011625459](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131011625459.png)

进入及所创建的仓库，点击管理，找到仓库设置-基本信息-是否开源，选为开源（如果仓库没有任何文件是不能勾选开源的），勾选仓库公开须知，点击保存

![image-20230131012036378](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131012036378.png)

### Gitee私人令牌创建

点击网页左上角个人头像，点击“设置”，点击“私人令牌”，点击“生成新令牌”

![image-20230131012756180](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131012756180.png)

输入令牌名称，然后权限仅勾选`projects`即可，接着点击提交

![image-20230131012850121](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131012850121.png)

输入密码验证后生成私人令牌，然后点击复制（建议保存起来），将令牌保存好后再勾选并关闭该页面

![image-20230131013132039](https://gitee.com/letefly/NoteImages/raw/master/img/image-20230131013132039.png)

### 图床配置

![image-20230131021618787](https://gitee.com/letefly/NoteImages/raw/master/img/2023/01/image-20230131021618787.png)

- repo为仓库的地址，如https://gitee.com/userName/repositoryName，的地址就是userName/repositoryName，`userName`是用户名称，`repositoryName`为仓库名称

- branch为分支，默认为`master`

- path为图片在仓库存放的路径，我这边放在仓库根目录的`img`文件夹下

- token为Gitee生成的私人令牌

- customPath为自动配置path路径，在path中存在`$customPath`才会生效

  例如将path设置为：`img/$customPath`

  1. customPath选择年，则实际的path值为img/2019
  2. customPath选择年季，则实际的path值为img/2019/summer
  3. customPath选择年月，则实际的path值为img/2019/01

  `$customPath`为占位符，我这边采用第三种，这样就会将图片上传到img路径下对应年月的文件路径下

- customUrl用于替代`https://gitee.com/:owner/:repo/raw/:path/:filename`，例如：`${customUrl}/path/filename.jpg`

  确保`customUrl`可以访问您的仓库

至此，图床已经搭建完毕，可以试着上传几张图片看看效果。

## Typora调用图传

打开Typora，点击文件-偏好设置-图像，将插入图片时选为“上传图片”，并根据需求勾选应用上述规则，然后选择上传服务为“PicGo(app)”，并选择安装路径

![image-20230131022547339](https://gitee.com/letefly/NoteImages/raw/master/img/2023/01/image-20230131022547339.png)

这样在Typora调用PicGo就实现了，可以点击验证图片上传选项试试效果。

## 参考文档

- [lizhuangs/picgo-plugin-gitee-uploader: picgo uploader for gitee (github.com)](https://github.com/lizhuangs/picgo-plugin-gitee-uploader)

- [Molunerfinn/PicGo: A simple & beautiful tool for pictures uploading built by vue-cli-electron-builder (github.com)](https://github.com/Molunerfinn/PicGo)

- [PicGo+Typora图床自动化 - 简书 (jianshu.com)](https://www.jianshu.com/p/09f5308f72fa)