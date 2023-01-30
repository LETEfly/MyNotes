## Docker简介

![img](https://gitee.com/letefly/NoteImages/raw/master/img/docker01.png)

> Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从 Apache2.0 协议开源。
>
> Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。
>
> 容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。
>
> Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版） 和 EE（Enterprise Edition: 企业版），我们用社区版就可以了。

### 相关链接

Docker 官网：[https://www.docker.com](https://www.docker.com/)

Github Docker 源码：https://github.com/docker/docker-ce

### Docker的应用场景

- Web 应用的自动化打包和发布。
- 自动化测试和持续集成、发布。
- 在服务型环境中部署和调整数据库或其他的后台应用。
- 从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境。

### Docker 的优点

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

#### 1.快速，一致地交付您的应用程序

Docker 允许开发人员使用您提供的应用程序或服务的本地容器在标准化环境中工作，从而简化了开发的生命周期。

容器非常适合持续集成和持续交付（CI / CD）工作流程，请考虑以下示例方案：

- 您的开发人员在本地编写代码，并使用 Docker 容器与同事共享他们的工作。
- 他们使用 Docker 将其应用程序推送到测试环境中，并执行自动或手动测试。
- 当开发人员发现错误时，他们可以在开发环境中对其进行修复，然后将其重新部署到测试环境中，以进行测试和验证。
- 测试完成后，将修补程序推送给生产环境，就像将更新的镜像推送到生产环境一样简单。

#### 2、响应式部署和扩展

Docker 是基于容器的平台，允许高度可移植的工作负载。Docker 容器可以在开发人员的本机上，数据中心的物理或虚拟机上，云服务上或混合环境中运行。

Docker 的可移植性和轻量级的特性，还可以使您轻松地完成动态管理的工作负担，并根据业务需求指示，实时扩展或拆除应用程序和服务。

#### 3、在同一硬件上运行更多工作负载

Docker 轻巧快速。它为基于虚拟机管理程序的虚拟机提供了可行、经济、高效的替代方案，因此您可以利用更多的计算能力来实现业务目标。Docker 非常适合于高密度环境以及中小型部署，而您可以用更少的资源做更多的事情。

## Docker安装

### Win10 Docker 安装

Docker 并非是一个通用的容器工具，它依赖于已存在并运行的 Linux 内核环境。

Docker 实质上是在已经运行的 Linux 下制造了一个隔离的文件环境，因此它执行的效率几乎等同于所部署的 Linux 主机。

因此，Docker 必须部署在 Linux 内核的系统上。如果其他系统想部署 Docker 就必须安装一个虚拟 Linux 环境。

![img](https://gitee.com/letefly/NoteImages/raw/master/img/CV09QJMI2fb7L2k0.png)

在 Windows 上部署 Docker 的方法都是先安装一个虚拟机，并在安装 Linux 系统的的虚拟机中运行 Docker。

Docker Desktop 是 Docker 在 Windows 10 和 macOS 操作系统上的官方安装方式，这个方法依然属于先在虚拟机中安装 Linux 然后再安装 Docker 的方法。

Docker Desktop 官方下载地址： https://docs.docker.com/desktop/install/windows-install/

**注意：**此方法仅适用于 Windows 10 操作系统专业版、企业版、教育版和部分家庭版！

#### 安装 Hyper-V

Hyper-V 是微软开发的虚拟机，类似于 VMWare 或 VirtualBox，仅适用于 Windows 10。这是 Docker Desktop for Windows 所使用的虚拟机。

但是，这个虚拟机一旦启用，QEMU、VirtualBox 或 VMWare Workstation 15 及以下版本将无法使用！如果你必须在电脑上使用其他虚拟机（例如开发 Android 应用必须使用的模拟器），请不要使用 Hyper-V！



#### 开启 Hyper-V

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513668234-4363-20171206211136409-1609350099.png)

程序和功能

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513668234-4368-20171206211345066-1430601107.png)

启用或关闭Windows功能

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513668234-9748-20171206211435534-1499766232.png)

xxxxxxxxxx *// babel.config.js:* module.exports = {        compact: false,  }js

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513668234-6433-20171206211858191-1177002365.png)

也可以通过命令来启用 Hyper-V ，请右键开始菜单并以管理员身份运行 PowerShell，执行以下命令：

```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

#### 安装 Docker Desktop for Windows

点击 [Get started with Docker Desktop](https://hub.docker.com/?overlay=onboarding)，并下载 Windows 的版本，如果你还没有登录，会要求注册登录：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/5AEB69DA-6912-4B08-BE79-293FBE659894.png)

#### 运行安装文件

双击下载的 Docker for Windows Installer 安装文件，一路 Next，点击 Finish 完成安装。

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513669129-6146-20171206214940331-1428569749.png)

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513668903-9668-20171206220321613-1349447293.png)

安装完成后，Docker 会自动启动。通知栏上会出现个小鲸鱼的图标![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513582421-4552-whale-x-win.png)，这表示 Docker 正在运行。

桌边也会出现三个图标，如下图所示：

我们可以在命令行执行 docker version 来查看版本号，docker run hello-world 来载入测试镜像测试。

如果没启动，你可以在 Windows 搜索 Docker 来启动：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513585082-6751-docker-app-search.png)

启动后，也可以在通知栏上看到小鲸鱼图标：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/1513585123-3777-whale-taskbar-circle.png)

> 如果启动中遇到因 WSL 2 导致地错误，请安装 [WSL 2](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)。

安装之后，可以打开 PowerShell 并运行以下命令检测是否运行成功：

```
docker run hello-world
```

在成功运行之后应该会出现以下信息：

![img](https://gitee.com/letefly/NoteImages/raw/master/img/EmkOezweLQVIwA1T__original.png)

### CentOS7 Docker离线安装

> 参考教程：[CentOS离线安装docker](https://blog.csdn.net/houmenghu/article/details/123690559)

#### 版本要求

Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。

通过 `uname -r `命令查看你当前的内核版本

#### 下载docker

下载自己合适的版本，[下载地址](https://download.docker.com/linux/static/stable/x86_64/)

#### 解压并移动

```bash
#解压，不要解压到/usr/bin/路径下
tar zxf docker-18.06.1-ce.tgz
#解压后得到一个docker文件夹，将其内部所有文件移动到/usr/bin/路径下
mv docker/* /usr/bin/
#删除压缩包与docker文件夹
rm -rf docker*.tgz
rm -rf docker
```

#### 配置docker.service

进入/etc/systemd/system/目录,并创建docker.service文件

- 方法一：将下面的配置文件保存为docker.service文件，然后通过文件传输放到指定路径下；
- 方法二：进入指定路径通过`touch docker.service`创建并输入以下配置信息

```shell
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
  
[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
ExecReload=/bin/kill -s HUP
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
  
[Install]
WantedBy=multi-user.target
```

#### 给docker.service文件添加执行权限

```bash
chmod 777 /etc/systemd/system/docker.service
```

#### 重新加载配置文件

每次有修改docker.service文件时都要重新加载下

```bash
#重载systemd下 xxx.service文件
systemctl daemon-reload
#启动Docker
systemctl start docker
#设置开机自启
systemctl enable docker.service
```

## Docker容器的导入和导出

### docker容器的导出

```bash
docker export [options] container
```

> OPTIONS说明：
> -o表示输出的文件，这里指定了输出的路径，如果没有指定路径，则默认生成到当前文件夹。
> 示例1：docker export -o redis.tar.gz redis 或 docker export redis > redis1.tar.gz
> 说明：将运行中的redis容器导出为redis.tar.gz包
>
> scp [参数] <源地址（用户名@IP地址或主机名）>:<文件路径> <目的地址（用户名 @IP 地址或主机名）>:<文件路径

### docker容器的导入

从tar包导入内容为docker镜像

```bash
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
```

> OPTIONS说明：
> -c :应用docker 指令创建镜像；
> -m :提交时的说明文字
> 示例1：`docker import redis.tar.gz redis:v1`
> 示例2：docker import https://example.com/exampleimage.tgz

## 常用命令

### 拉取镜像

```
docker pull <IMAGE>[:<VERSION>]
```

### 查看镜像

```bash
#方法1
docker images
#方法2
docker image ls
```

### 导入导出镜像

```bash
#导出镜像
docker save -o node:14.15.1.tar node:14.15.1
#导入镜像
docker load < hangge_server.tar
```

### 删除镜像

```bash
#删除指定镜像
docker rmi <IMAGE>
#删除所有镜像
docker rmi  $(docker images -q)
```



### 新增容器

```bash
#-p: 指定端口映射，格式为：主机(宿主)端口:容器端口
#-i: 以交互模式运行容器，通常与 -t 同时使用；
#-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
#-d: 后台运行容器，并返回容器ID；
docker run -p <OUTSIED PORT>:<INSIDE PORT> --name <CONTAINER NAME> <IMAGE>
```

### 查看容器信息

```bash
#查看正在运行的容器信息
docker ps
#查看全部的容器信息
docker ps -a
```

### 启动与关闭容器

```bash
#启动
docker start <CONTAINER>
#关闭
docker stop <CONTAINER>
#强制关闭
docker kill <CONTAINER>
#停止所有
docker stop  $(docker ps -a -q)
```

### 配置容器的自动重启策略

```bash
#--restart=always：配置自启参数，取消改为no；
#新增时配置
docker run <CONTAINER> --restart=always
#已存在时配置
docker update <CONTAINER> --restart=always
```

### 连接正在运行中的容器

```bash
#ctrl+c终止
#--sig-proxy=false：确保CTRL-D或CTRL-C不会关闭容器
docker attach [OPTIONS] <CONTAINER>
```

### 在运行的容器中执行命令

```bash
#访问容器的终端，退出时使用exit；
#-d :分离模式: 在后台运行
#-i :即使没有附加也保持STDIN 打开
#-t :分配一个伪终端
docker exec -it -d <CANTAINER> </bin/bash || bash>
#以交互模式执行容器内的脚本
docker exec -it <CANTAINER> /bin/sh /xx/xxxx.sh
```

### 获取容器的日志

```bash
#-f : 跟踪日志输出
#--since :显示某个开始时间的所有日志
#-t : 显示时间戳
#--tail :仅列出最新N条容器日志
docker logs [OPTIONS] CONTAINER
```

### 删除容器

```bash
#删除指定容器
docker rm <CONTAINER>
#删除全部容器
docker rm ${dockerk images -q}
```

### 查看容器运行日志

```bash
# -f : 跟踪日志输出
# --since :显示某个开始时间的所有日志
# -t : 显示时间戳
# --tail :仅列出最新N条容器日志
docker logs -f --tail=500 <CANTAINER>
```

## 报错处理

### Hardware assisted virtualization and data execution protection must be enabled in the BIOS

![image-20221107174503060](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221107174503060.png)

win10安装docker时报错Hardware assisted virtualization and data execution protection must be enabled in the BIOS，这就很奇怪了，明明是在blos已经启用了虚拟硬件了，怎么还会报错呢？

1. 确认CPU是否开启虚拟化，如果没有要进入BIOS开启

![image-20221107174617164](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221107174617164.png)

2. 打开windows的【启用或关闭windows功能】，查看是否安装Hyper-V,没选的勾选

![image-20221107174816813](https://gitee.com/letefly/NoteImages/raw/master/img/image-20221107174816813.png)

3.执行一下命令并重启

```bash
bcdedit /set hypervisorlaunchtype auto
```

