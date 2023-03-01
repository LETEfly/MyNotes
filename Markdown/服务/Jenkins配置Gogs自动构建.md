## 背景

有项目的代码放在Gogs上托管，然后又通过Jenkins做自动化部署，现在想要在本地代码Push到Gogs上时，Jenkins就执行更新部署。

## 过程原理

- Jenkins的`Gogs Plugin`插件会提供触发打包的API；
- Gogs的仓库设置提供了Web Hook([钩子](https://baike.baidu.com/item/hook/19512231?fr=aladdin))；
- 将Jenkins的API绑定到Gogs Web Hook的推送地址上；
- 这样就能指定Gogs在Push后将事件推送给Jenkins，然后Jenkins去执行项目构建与部署。

## Jenkins Gogs Plugin

在Jenkins的插件管理中可以找到并安装Gogs plugin插件，安装后在具体项目的配置中就可以看到Gogs Webhook的选项，每个选项的具体作用可以点击`?`按钮查看

![image-20230301105921866](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/image-20230301105921866.png)

另外，在构建触发器中会多出一个选项：`Build when a change is pushed to Gogs`(在Gogs发生推送事件的时候执行构建)，该选项需要勾上才能触发构建。

![image-20230301110226596](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/image-20230301110226596.png)

安装并配置好自动构建后，我们需要手动获取API的地址，具体格式如下

```
http(s)://<< jenkins-server >>/gogs-webhook/?job=<< jobname >>
```

注意：如果项目时在某个文件路径下，那在`<< jobname >>`前面还得跟上文件名，如

```
http://192.168.7.201:8888/user/somebody/my-views/view/all/job/某某项目/job/web/
```

以上是某个项目所在的路径，是一个在“某某项目”文件夹下的名为“web”的项目，那么这个项目所对应的API应该是：

```
http://192.168.7.201:8888/gogs-webhook/?job=某某项目/web
```

## Gogs Web Hook

在Gogs的代码仓库中进入到`仓库设置 > 管理Web钩子`，点击添加新的Web钩子，类型选择Gogs，然后将刚刚的推送地址张贴到推送地址中：

![image-20230301112019271](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/image-20230301112019271.png)

然后点击添加钩子，**注意**，此时可能存在报错：`推送 URL 被解析到默认禁用的本地网络地址。`，如图：

![image-20230301112202974](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/image-20230301112202974.png)

这是因为Gogs默认禁止访问本地网络地址，需要去修改`app.ini`的配置文件，添加参数 `LOCAL_NETWORK_ALLOWLIST` 后重启服务。

```ini
#多个地址用逗号隔开，localgogs为本地配置的域名
LOCAL_NETWORK_ALLOWLIST = 192.168.7.201,192.168.1.110,localgogs
```

app.ini文件所在路径：

![image-20230301114913739](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/image-20230301114913739.png)

```ini
#app.ini

...
[security]
INSTALL_LOCK = true
SECRET_KEY   = otqYKm9E34JWVah
LOCAL_NETWORK_ALLOWLIST=192.168.7.201,192.168.1.110,localgogs
```

修改配置，重启容器后再保存即可成功，保存后可以点击测试推送

![image-20230301115354264](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/image-20230301115354264.png)

如果推送成功了，并且jenkins也自动执行构建了，那么就说明成功了。另外，在测试推送的时候也可以点击具体的推送记录查看请求内容与响应内容。
