---
title: Ubuntu 安装 ar8161 网卡驱动
layout: post
date: 2012-11-07T04:02:08Z
tags: [old]
---

7月份时候，下决心花了3.5K银子装了一台能入流的主机，兴冲冲的安装上了[Ubuntu Server 12.04 LTS], 却发现因为主板上的网卡芯片（AR8161）太新，驱动还没测试稳定，尚未加入内核。还好搜到了解决的办法：编译[compat-wireless]的源码包。当时网上相关的文档还比较少，现在已经好找多了。算是翻译把。

#### 在无网络的情况下，安装编译环境 build-essential   

参考上一篇文章[Ubuntu 平台离线安装软件包]

#### 编译安装 alx

下载并解压compat-wireless的源码包, 进入解压后的目录：

``` bash
wget -c http://linuxwireless.org/download/compat-wireless-2.6/compat-wireless-2012-07-03-pc.tar.bz2
tar xvf compat-wireless-2012-07-03-pc.tar.bz2
cd compat-wireless-2012-07-03-pc
```

配置网卡型号：

``` bash
./scripts/driver-select alx
```

编译安装:

``` bash
sudo make && make install
```

如果编译安装过程没有报错的话，最后会提示几个操作加载内核模块，图省事的话直接重启就行了。

检查安装结果:

``` bash
ifconfig -a
```

看见 eth0 了吗？ ：-）

[Ubuntu Server 12.04 LTS]:http://www.ubuntu.com/download/server
[compat-wireless]:http://linuxwireless.org/download/compat-wireless-2.6
[Ubuntu 平台离线安装软件包]:/blog/note/Ubuntu-offline-install-package


