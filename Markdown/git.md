## Git 全局设置

```bash
git config --global user.name "赖明浩"
git config --global user.email "2672799965@qq.com"
```

## 创建新版本库

```bash
git clone git@192.168.1.188:lmh/xx.git
cd xx
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

## 已存在的文件夹

```bash
cd existing_folder
git init
git remote add origin git@192.168.1.188:lmh/xx.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

## 已存在的 Git 版本库

```bash
cd existing_repo
git remote rename origin old-origin
git remote add origin git@192.168.1.188:lmh/xx.git
git push -u origin --all
git push -u origin --tags
```

## 迁移仓库地址并保留分支和历史记录

```bash
#1.先克隆老项目的镜像
git clone --mirror old.git
 
#2.移除老项目的地址替换成新项目
git remote set-url --push origin new.git
 
#3.将镜像推到远程，这一步需要输入新的git的账号和密码
git push --mirror  
```

## 设置git push和pull的默认分支

```bash
git branch --set-upstream-to=origin/<远程分支> <本地分支>
#更为简洁的方式是在push时，使用-u参数，-u参数会在push的同时会指定当前分支的默认上游分支。
git push -u origin <远程分支>
```

## Git Hook
