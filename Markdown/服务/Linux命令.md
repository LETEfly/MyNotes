## 磁盘挂载

---

### 查看系统中磁盘信息

```shell
fdisk -l
```

![img](https://gitee.com/letefly/NoteImages/raw/master/img/2023/03/3f51253b2a12446ca609de937abb2004.png)

### 系统启动时自动挂载磁盘

```shell
#修改fstab
vi /etc/fstab
#在其中添加一行后保存退出，注意要确保/data在目录中真实存在，ext4为被挂载的磁盘名称
/dev/vdb /data ext4 defaults 0 0
#将/etc/fstab的所有内容重新加载
mount -a
```

