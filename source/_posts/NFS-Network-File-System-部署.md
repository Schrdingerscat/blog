---
title: NFS(Network File System)部署
date: 2023-12-04 21:01:22
tags: linux
description: 在Ubuntu18.04搭建nfs服务器

---
# 在Ubuntu18.04搭建nfs服务器

## 安装nfs服务器

在Ubuntu安装nfs服务器比较简单，只需要在命令行执行下面这个命令即可：

``` bash
sudo apt install nfs-kernel-server
```

## 创建nfs服务器共享目录

创建一个目录用于nfs服务器将文件共享给客户端，这个目录将会写入到nfs配置文件中：

``` bash
sudo mkdir /nfsroot
```

## 修改nfs服务器配置文件

打开nfs服务器配置文件/etc/exports，指定nfs服务器共享目录及其属性，内容如下：

``` 
/nfsroot  *(rw,sync,no_root_squash)
```

解析：
/nfsroot：指定/nfsroot为nfs服务器的共享目录
*：允许所有的网段访问，也可以使用具体的IP
rw：挂接此目录的客户端对该共享目录具有读写权限
sync：资料同步写入内存和硬盘
no_root_squash：root用户具有对根目录的完全管理访问权限
no_subtree_check：不检查父目录的权限

## 重启nfs服务器

执行下面两个命令其中一个可以重启nfs服务器：

``` bash
sudo service nfs-kernel-server restart
```

或者

``` bash
sudo /etc/init.d/nfs-kernel-server restart
```

到此，Ubuntu安装nfs服务器的过程就完成了，可以执行下面这个命令查看nfs服务器的共享目录：

``` bash
showmount -e localhost
```

三、客户端访问nfs服务器共享目录
这一小节使用nfs客户端是嵌入式开发板系统自带的，一般嵌入式开发板移植了Linux系统后，都自带有nfs客户端，如果确实需要在Ubuntu安装，可以执行下面的命令安装：

``` bash
sudo apt install nfs-common
```

## 在nfs客户端挂载服务器共享目录

下面是在客户端挂载服务器共享目录的命令，在嵌入式开发板或者Ubuntu的nfs客户端挂载都可以使用，需要将192.168.2.183换成自己的nfs服务器ip：

``` bash
mount -t nfs -o nolock 192.168.2.183:/nfsroot /nfsroot
```



> -t：挂载的文件系统类型
> -o nolock：不要文件锁
> 192.168.xxx.xxx:/nfsroot：nfs服务器ip:服务器共享目录
> nfsroot：客户端已存在的目录



需要注意的是，在嵌入式开发板必须加上-o nolock，不然会出现下面错误，因为 nfs mount 默认选项包括文件锁，依赖于portmap提供的动态端口分配功能。

## 在nfs客户端卸载服务器共享目录

在nfs客户端执行下面命令，可以查询挂载的·nfs·服务器目录

``` bash
mount
```

在nfs客户端执行下面命令，可以卸载的·nfs·服务器目录

``` bash
umount /nfsroot
```

# 固定 NFS 端口

NFS 本身是没有提供信息传输的协议和功能的，它使用的是 RPC （Remote Procedure Call）协议。

通过rpcinfo命令可以查看 NFS 相关的端口：

``` bash
linux@linux-virtual-machine:~$ rpcinfo -p localhost
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100005    1   udp  42754  mountd
    100005    1   tcp  41379  mountd
    100005    2   udp  35253  mountd
    100005    2   tcp  38577  mountd
    100005    3   udp  54781  mountd
    100005    3   tcp  38547  mountd
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049
    100003    3   udp   2049  nfs
    100227    3   udp   2049
    100021    1   udp  33454  nlockmgr
    100021    3   udp  33454  nlockmgr
    100021    4   udp  33454  nlockmgr
    100021    1   tcp  39595  nlockmgr
    100021    3   tcp  39595  nlockmgr
    100021    4   tcp  39595  nlockmgr
```

以上这些端口中，2049 和 111 端口是固定端口，mountd 和 nlockmgr 对应的端口是随机分配的。正常情况下，应该还有 status服务占用端口，不知道为啥老王的系统上没有。算了，这不重要。

mountd、nlockmgr、和status服务默认情况下是随机分配端口的，但这样搞不好配置防火墙或者端口映射啥的。

修改/etc/default/nfs-common文件，配置status服务端口为40000：

``` bash
STATDOPTS="--port 40000"
```

修改/etc/default/nfs-kernel-server文件，配置mountd服务端口为40001：

``` bash
RPCMOUNTDOPTS="--manage-gids -p 40001"
```

创建/etc/modprobe.d/options.conf文件，添加如下内容，配置nlockmgr端口为40002：\

``` bash
options lockd nlm_udpport=40002 nlm_tcpport=40002
```

然后在/etc/modules文件中添加lockd：

``` yaml
# /etc/modules: kernel modules to load at boot time.
#

# This file contains the names of kernel modules that should be loaded
# at boot time, one per line. Lines beginning with "#" are ignored.
lockd
```

重启 PC 使配置生效。重启后，查看配置效果：

``` bash
linux@linux-virtual-machine:~$ rpcinfo -p localhost
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100005    1   udp  40001  mountd
    100005    1   tcp  40001  mountd
    100005    2   udp  40001  mountd
    100005    2   tcp  40001  mountd
    100005    3   udp  40001  mountd
    100005    3   tcp  40001  mountd
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049
    100003    3   udp   2049  nfs
    100227    3   udp   2049
    100021    1   udp  40002  nlockmgr
    100021    3   udp  40002  nlockmgr
    100021    4   udp  40002  nlockmgr
    100021    1   tcp  40002  nlockmgr
    100021    3   tcp  40002  nlockmgr
    100021    4   tcp  40002  nlockmgr
```