# 部署Vue服务

涉及范畴：Jenkins，Linux，Nginx

## 在Jenkins上新增项目

在Jenkins上构建一个自由风格的软件项目（点击新建任务），如果是同一个项目建议先建一个文件夹，然后再在文件夹里面新建项目（点击新建Item）

![image-20221012094556467](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012094556467.png)

## 在Jenkins上配置项目

首先填写描述以及设置旧构建的保留时间与个数

![image-20221012094819795](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012094819795.png)

配置代码的git源，到时候Jenkins会自动将代码拉取下来

![image-20221012095151124](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012095151124.png)

指定Node运行版本，用于代码的打包

![image-20221012095418374](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012095418374.png)

在Jenkins里面运行指定的构建命令

![image-20221012095518236](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012095518236.png)

```shell
#!/bin/bash
npm install
npm run build
tar -cvf  dist.tar.gz dist
```

构建后将压缩包发送到远程的Linux上，并执行相应的操作

![image-20221012095759574](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012095759574.png)

操作内容：进入到/home/xslab-project/文件夹下，解压dist.tar.gz，将原来的web文件加删除，再将解压好的dist文件夹命名为web，在将config.json复制进去（这个是存放后端地址的，上传后的代码不包含这个，所以这里得手动加进去才能正常运行），然后将压缩包改名，留存。

```shell
#!/bin/bash
tname=`date +%Y-%m-%d%H%M%S`
cd /home/xslab-project/
tar -xvf dist.tar.gz 
rm -rf web 
mv dist web
cp ./config/config.json ./web
mv dist.tar.gz dist_${tname}.tar.gz
find /home/xslab-project/ -mtime +1 -name "*.tar.gz" -exec rm -rf {} \;
```

注意，这里的远程登录用的是私钥登录（不知道能不能用密码）

![image-20221012100040444](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012100040444.png)

最后点击保存

## Nginx配置

到/etc/nginx/conf.d/路径下新增一个conf文件

```shell
cd /etc/nginx/conf.d/
vim xsleb.conf
```

修改文件并保存

```
server {
  listen 7300;
  root /home/xslab-project/web/;
  
  location / {
    index  index.html index.htm;
    try_files $uri $uri/ /index.html;
  }
  
  location ~* ^/mlf {
    proxy_pass http://localhost:7301;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $http_host;
	proxy_send_timeout 180s;
	proxy_read_timeout 180s;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header REMOTE-HOST $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

重启Nginx

```shell
./nginx -s reload
```



## 确保项目正常构建与部署

在Jenkins面板中点击立即构建，然后构建历史中就会新增一个构建任务

![image-20221012101822602](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012101822602.png)

然后可以点击进入到构建任务里面去查看控制台的输出，如果图标显示的是绿色，那么就说明构建成功了，否则可以根据控制台的输出信息进行排查；

![image-20221012102040301](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012102040301.png)

也可以返回到项目的面板上查看工作空间的内容

![image-20221012102508233](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012102508233.png)

构建完成后就要看有没有正常部署到Linux上

通过Xshell和XFTP进行远程访问，查看是不是将代码正确部署到Linux上去

![image-20221012102946737](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221012102946737.png)

---

# 部署Node服务

涉及范畴：Jenkins，Linux，Docker

## 在Jenkins上配置项目

在Jenkins上的新增和上述的部署Vue服务一致，因此略过。

配置上也大致相同，除了构建时执行的命令

```shell
#!/bin/bash
tar -cvf  node.tar.gz *
```

以及构建后在Linux执行的命令

```shell
#!/bin/bash
tname=`date +%Y-%m-%d%H%M%S`
cd /home/xslab-project/
mkdir node/nodeDist
tar -xvf nodeDist.tar.gz -C node/nodeDist
docker-compose down
rm -rf node/src
mv node/nodeDist node/src
docker-compose up -d
mv node.tar.gz node_${tname}.tar.gz
find /home/xslab-project/ -mtime +1 -name "*.tar.gz" -exec rm -rf {} \;
```

大致流程说明：首先在Jenkins上拉取代码，然后直接将代码打包发送到Linux上。进入指定目录下，这里由于业务需求，该目录下存在前端服务的文件夹和后端服务的文件夹，而后端服务的文件夹中又区分代码跟数据库信息。将代码解压放到后端服务文件夹中，然后关闭dokcer-compose，将新旧代码进行替换，接着再启动dokcer-compose部署容器，启动服务，最后备份数据。

注意：没有必要在Jenkins上编译，因为编译只是把代码进行压缩，并没有把依赖也一并编译进去，传到Linux上还是要安装依赖。又因为这次用的linux并没有安装Node，导致在Linux上也无法安装依赖。 所以，从Jenkins到Linux再到Docker容器都只需要传递源码即可，安装依赖可以让dokcer-compose来执行。

## dokcer-compose配置

```yaml
version: "3"
services:
  mlf-xslab-node-service:
    image: node:14.15.1
    container_name: mlf-xslab-node-service
    privileged: true
    restart: always
    ports:
      - "7301:7301"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - "./node:/app"
    working_dir: /app
    command:
      - /bin/bash
      - -c
      - |
        cd ./src
        npm install
        node index.js

```

说明：首先语言版本是3，然后新增一个容器服务，镜像是node:14.15.1，配置端口映射以及文件入口，以及配置后终端执行的命令：进入src文件夹，安装依赖，运行代码。

## 确保项目正常构建与部署

再jenkins上的监控排查这里不多赘述，重点说明再Linux的监控和排查

首先通过XFTP或Xshell查看代码是否正确部署；然后执行`docker ps`查看容器服务是否启动，如果没有启动和可能是Jenkins配置的构建后在Linux执行的命令有问题，如果启动了就执行`docker logs mlf-xslab-node-service`查看docker运行的日志，特别是一些命令有没有执行，有没有执行到运行代码(node index.js)。

---

# Docker离线部署kkfileView服务

[kkfileView官方地址](http://kkfileview.keking.cn/zh-cn/docs/production.html)

## 获取镜像

方法一：访问gitee下载gz包

[kkFileView 发行版](https://gitee.com/kekingcn/file-online-preview/releases)

方法二：在docker联网的情况下通过docker下载

```bash
docker pull keking/kkfileview
```

下载后将其导出

```bash
docker save -o [IMAGE_NAME].tar [IAMGE:VERSION]
```

## 导入镜像

```bash
docker load < [IMAGE_NAME].tar
```

## 部署容器

```bash
#新增容器并启动
docker run -p 8012:8012 --name kkfileview keking/kkfileview --restart=always
#查看是否正在运行
docksr ps
#如果正在运行查看日志运行时是否报错
docker logs -ft kkfileview
```

## 遇到的问题

在虚拟机外访问的时候，word、excel文件预览不了：原因可能是虚拟的防火墙没有关，关闭防火墙再试试，如果不行跟踪日志看看。

```bash
#暂时关闭防火墙
systemctl stop firewalld
#永久关闭防火墙
systemctl disable firewalld
#开启防火墙
systemctl enable firewalld
```

---

