---
layout: post
title: "EC2 平台 SaltStack 部署手记"
modified: 2014-10-04 12:59:25 +0800
tags: [devops,aws]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true 
---

> Salt, a new approach to infrastructure management, is easy enough to get running in minutes, scalable enough to manage tens of thousands of servers, and fast enough to communicate with those servers in seconds.

--- 

## Ubuntu 平台安装 SaltStack
准备安装环境：
{% highlight bash %}
sudo apt-get install python-software-properties
sudo add-apt-repository -y ppa:saltstack/salt
sudo apt-get updates
{% endhighlight %}

安装相关组件：
{% highlight bash %}
sudo apt-get install salt-master # 安装 Master
sudo apt-get install salt-minion # 安装 Minion
sudo apt-get install salt-syndic # 安装 Syndic（可选）
{% endhighlight %}


## 配置 Salt Minion
{% highlight bash %}
cp /etc/salt/minion{,.original}
sed -i '12,12imaster: salt.example.org' /etc/salt/minion

cat >> /etc/hosts <<'EOF'

192.168.2.1 salt.example.org
EOF
{% endhighlight %}

## 配置 Salt Master 的防火墙
对于EC2上的部署来说，防火墙包括两个方面：操作系统的防火墙（iptables）和 AWS 的 Securigy Groups 设置

#### 配置 iptables

因为 Salt Master 的默认端口是`4045`和`4046`，因此需要配置这两个端口的规则：
{% highlight bash%}
iptables -A INPUT -p tcp -m multiport --dports 4505,4506 -m state --state NEW -j ACCEPT
{% endhighlight %}

另，常用`iptables`命令行：
{% highlight bash %}
iptables -L -n # 查看防火墙配置
iptables-save > iptables.up.rules # 备份防火墙配置
iptables-restore < iptables.up.rules # 从文间中恢复防火墙配置
iptables -F # 关闭所有防火墙规则
{% endhighlight %}

#### 配置 Securigy Groups

具体来说，就是登录 EC2 的控制台，选中你的 instance，在详细面板中，选择并修改 Securigy groups 。最终配置结果如下：

![Securigy Groups Edit]({{ site.url }}/images/blog/Securigy_Group_Edit .jpg)
{: .pull-center}
详细设置，请参考官方文档 [Securigy Groups] 。


## 配置 Master 的 file_root 

参考自：http://docs.saltstack.com/en/latest/ref/file_server/file_roots.html 

## 题外话
在测试 Salt-Master 的时候，用 `ps -ef | grep salt` 命令查看，发现竟然有9个 salt-master 进程，查看手册发现了这么一段话：

> When the master daemon starts, it is expected behaviour to see multiple salt-master processes, even if 'worker_threads' is set to '1'. At a minimum, a controlling process will start along with a Publisher, an EventPublisher, and a number of MWorker processes will be started. The number of MWorker processes is tuneable by the 'worker_threads' configuration value while the others are not.

详情参考 salt 手册的 [master] 章节。


## 参考资料
---
1. Salt 官方手册的如下章节：
	* [Ubuntu Install]
	* [master]
	* [configuration]
	* [file roots]
2. [系统自动化配置和管理工具 SaltStack]
3. [运维自动化之salt学习笔记]
4. [Salt 防火墙配置]
5. [Salt 客戶端安裝]

[configuration]:http://docs.saltstack.com/en/latest/ref/configuration/index.html 
[Ubuntu Install]:http://docs.saltstack.com/en/latest/topics/installation/ubuntu.html
[master]:http://docs.saltstack.com/en/latest/ref/configuration/master.html
[file roots]:http://docs.saltstack.com/en/latest/ref/file_server/file_roots.html
[Securigy Groups]:http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html
[系统自动化配置和管理工具 SaltStack]:http://www.vpsee.com/2013/08/a-system-configuration-management-and-orchestration-tool-saltstack/
[运维自动化之salt学习笔记]:http://blog.halfss.com/blog/2013/05/22/yun-wei-zi-dong-hua-zhi-saltxue-xi-bi-ji/
[Salt 防火墙配置]:http://netkiller.github.io/linux/management/saltstack/index.html#idp95712800
[Salt 客戶端安裝]:http://netkiller.github.io/linux/management/saltstack/index.html#idp95710048

