# Node版本管理工具（NVM）

NVM（Nodejs Version Management） 是一个 Node 的版本管理工具，可以进行 Node 版本的切换、安装、查看等。

开发中可能接手多个项目，项目的 Node 版本不同，切换版本很麻烦，我们可以使用 NVM 这个工具来进行 Node 的版本控制。

## 下载地址

```bash
https://github.com/coreybutler/nvm-windows/releases
```

## 常用命令

```bash
#查找本电脑上所有的node版本
##- nvm list 查看已经安装的版本
##- nvm list installed 查看已经安装的版本
##- nvm list available 查看网络可以安装的版本
nvm list 

#根据node的版本号安装node
nvm install <version>

#卸载指定的版本node
nvm uninstall <version>

#切换使用指定的版本node
nvm use <version>

#列出所有版本
nvm ls 

#显示node的当前版本
nvm current

#查看nvm的当前的版本
nvm version 

#给不同的版本号添加别名
nvm alias <name> <version>

#删除已定义的别名
nvm unalias <name> 

#在当前版本node环境下，重新全局安装指定版本号的npm包
nvm reinstall-packages <version> 

#打开nodejs控制
nvm on

#关闭nodejs控制
nvm off 

#查看设置与代理
nvm proxy

#设置或者查看setting.txt中的node_mirror，如果不设置的默认是 https://nodejs.org/dist/
nvm node_mirror [url] 

#设置或者查看setting.txt中的npm_mirror,如果不设置的话默认的是： https://github.com/npm/npm/archive/.
nvm npm_mirror [url]

#切换指定的node版本和位数
nvm use [version] [arch] 
 
#设置和查看root路径
nvm root [path] 

```

