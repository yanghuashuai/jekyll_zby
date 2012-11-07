---
layout: post
title: Ubuntu 平台离线安装软件包, 以安装 build-essential 为例
---

{{ page.title }}
================

<p class="meta">2012-10-31</p>

**基本原理**  

1.  apt-get install 的 --print-uris 选项可以打印出包依赖列表：
 >**--print-uris**  
 >Instead of fetching the files to install their URIs are printed. Each URI will have the path, the destination file name, the size
 >and the expected md5 hash.

2.  apt-get install 会优先检查本地缓存目录 /var/cache/apt/archives/ ，如果这个目录存在依赖列表中的软件包，且版本一致，则不会再尝试通过网络下载。

**基本操作**   

打印所有依赖包的URI列表。
	sudo apt-get install --print-uris -y build-essential | grep deb	

截取其中的路径和文件名，整理成一个列表文件, 保存为一个文件(例如：uris.lst)：
>/pool/main/g/gcc-4.6/libgomp1_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/m/mpfr4/libmpfr4_3.1.0-3ubuntu2_amd64.deb   
>/pool/main/g/gcc-4.6/libquadmath0_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/m/mpclib/libmpc2_0.9-4_amd64.deb  
>/pool/main/b/binutils/binutils_2.22-6ubuntu1_amd64.deb  
>/pool/main/e/eglibc/libc-dev-bin_2.15-0ubuntu10_amd64.deb  
>/pool/main/l/linux/linux-libc-dev_3.2.0-29.46_amd64.deb  
>/pool/main/e/eglibc/libc6-dev_2.15-0ubuntu10_amd64.deb  
>/pool/main/g/gcc-4.6/cpp-4.6_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/g/gcc-defaults/cpp_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/g/gcc-4.6/gcc-4.6_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/g/gcc-defaults/gcc_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/g/gcc-4.6/libstdc++6-4.6-dev_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/g/gcc-4.6/g++-4.6_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/g/gcc-defaults/g++_4.6.3-1ubuntu5_amd64.deb  
>/pool/main/m/make-dfsg/make_3.81-8.1ubuntu1_amd64.deb  
>/pool/main/d/dpkg/libdpkg-perl_1.16.1.2ubuntu7_all.deb  
>/pool/main/d/dpkg/dpkg-dev_1.16.1.2ubuntu7_all.deb  
>/pool/main/b/build-essential/build-essential_11.5ubuntu2_amd64.deb  
>/pool/main/f/fakeroot/fakeroot_1.18.2-1_amd64.deb  
>/pool/main/liba/libalgorithm-diff-perl/libalgorithm-diff-perl_1.19.02-2_all.deb  
>/pool/main/liba/libalgorithm-diff-xs-perl/libalgorithm-diff-xs-perl_0.04-2build2_amd64.deb  
>/pool/main/liba/libalgorithm-merge-perl/libalgorithm-merge-perl_0.08-2_all.deb  
>/pool/main/m/manpages/manpages-dev_3.35-0.1ubuntu1_all.deb  
>/pool/main/l/linux/linux-libc-dev_3.2.0-29.46_amd64.deb  

将这个uris.lst文件拷贝到一个联网的机器上面，选择一个合适的ubuntu源，下载这些deb包（有多种下载方式, 下面是一个示例脚本）：
	cat uris.lst | while read uri
	do
		wget -c  http://ubuntu.srt.cn/ubuntu${uri}
	done	

将下载的这些deb包拷贝到目标机器的/var/cache/apt/archives目录下面，执行安装命令：
	sudo apt-get install build-essential


完成安装
