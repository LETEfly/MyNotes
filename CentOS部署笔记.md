# 联网配置

前提条件：新建虚拟机的时候网络选择NAT模式

1. 查看网关

​	点击编辑-虚拟网络编辑器-NAT模式-NAT设置-网关IP，如192.168.19.2

2. 进入以下地址

   ```bash
   cd /etc/sysconfig/network-scripts
   ```

3. 编辑ifcfg-ens33

   ```shell
   vi ifcfg-ens33
   ```

   编辑内容

   ```bash
   TYPE=Ethernet
   PROXY_METHOD=none
   BROWSER_ONLY=no
   BOOTPROTO=dhcp
   DEFROUTE=yes
   IPV4_FAILURE_FATAL=no
   IPV6INIT=yes
   IPV6_AUTOCONF=yes
   IPV6_DEFROUTE=yes
   IPV6_FAILURE_FATAL=no
   IPV6_ADDR_GEN_MODE=stable-privacy
   NAME=ens33
   UUID=d4df1e4-s152-43s5-5fgr951c6a54
   DEVICE=ens33
   #更改与新增的内容
   ONBOOT=YES
   IPADDR=192.168.19.100
   NETMASK=255.255.255.0
   DNS1=114.114.114.114
   DNS2=8.8.8.8
   GATEWAY=192.168.19.2
   ```

4. 重启网络服务并验证

   ```bash
   service network restart 
   ping www.baidu.com
   ```

   

