---
layout: post
category : frontend
title : hostsPlus v0.9.3发布 - 兼容Windows&Linux&Mac
tags : [adobe, air, js, hosts, Mac, Linux]
---

![hostsPlus](/images/hostsplus.png)

# 软件开发由来

为什么会开发这么一款hosts编辑软件？

网上hosts管理软件并不少，但是为什么要选择重复造轮子？

开发这款软件最初的两个需求点如下：

1. 开发环境切换，但是又不需要整个文件切换，总会有些公用的绑定需要保留
2. 由于公司开发机的IP不是固定IP，测试同学测试时总会碰到IP无法访问的问题

基于这两个需求点，才有了今天这个软件。

经过后期不断的加入新的创意元素，功能越来越强大。

下面介绍一下这款软件能够做哪些事。


# 主要特点

* hosts方案管理
* hosts单文件分组管理
* 重定向到主机名或域名
* DNS切换
* 前端小工具集
* 主题切换
* 跨平台：Windows, Linux, Mac

# 关于分组

这里简单介绍下hosts分组的概念。

传统hosts管理软件都是多文件管理，切换时整个文件进行切换。

而hostsPlus最大的特色就是推出了分组管理的概念，一个文件里可以通过一定的格式，设置多个分组。并且，多个分组间可以设置互斥或者依赖关系。

所谓互斥关系，就是两个分组是相互排斥的。开启其中一个分组，另外一个分组必需要关闭，否则就冲突了。

例如所有互联网公司都有如下几个开发环境：开发环境、测试环境、生产环境，这几个环境之间必然是相互排斥的，选择了其中一个环境，另外几个环境必然就要关闭。

所谓依赖关系，就是某一个分组必需在另外一个分组开启的情况下，才能正常工作。

例如一个网站有非常多的应用，每个应用都会依赖一些公用的绑定。

详细使用指南，可以前往这里查阅：[https://github.com/yaniswang/hostsPlus/wiki/manual](https://github.com/yaniswang/hostsPlus/wiki/manual)


##Download - 下载

版本号：v0.9.3

**Windows：**[windows.v0.9.3.exe](http://hostsplus.googlecode.com/files/windows.v0.9.3.exe)

**Linux i386：**[linux.v0.9.3_i386.zip](http://hostsplus.googlecode.com/files/linux.v0.9.3_i386.zip)

**Linux a64：**[linux.v0.9.3_a64.zip](http://hostsplus.googlecode.com/files/linux.v0.9.3_a64.zip) (64位版)

**Mac：**[mac.v0.9.3.zip](http://hostsplus.googlecode.com/files/mac.v0.9.3.zip)

**源码：**[https://github.com/yaniswang/hostsPlus](https://github.com/yaniswang/hostsPlus)

### Windows安装方法

直接双击EXE文件运行
	
### Linux安装方法

解压缩安装包后，进入到安装包路径下，执行以下命令：

	sudo chmod 777 *
	./install_linux.sh

install_linux.sh脚本内容如下：

	sudo chmod +x ./air_2.6_linux.bin
	sudo ./air_2.6_linux.bin
	sudo gsettings set com.canonical.Unity.Panel systray-whitelist "['all']"
	sudo chmod 777 /etc/hosts
	sudo dpkg -i hostsplus.v0.9.3_a64.deb
	sudo chmod 777 /opt/hostsPlus/share/ext/*
	sudo cp /usr/share/applications/hostsplus.desktop ~/Desktop
	sudo chmod 777 ~/Desktop/hostsplus.desktop
	sudo cp /usr/share/applications/hostsplus.desktop ~/桌面
	sudo chmod 777 ~/桌面/hostsplus.desktop
	/opt/hostsPlus/bin/hostsPlus
	
执行过程中，会提示输入root密码。

安装脚本会做如下事情：

* 安装Air运行时
* 关闭托盘图标白名单(ubuntu 12才需要，关闭后需要注销才生效)
* 放开/etc/hosts文件的写权限
* 安装hostsPlus
* 将快捷方式复制到桌面

### Mac安装方法

解压缩安装包后，执行以下命令：

	./install_mac.sh
	
执行过程中，会提示输入root密码（如果你的账户密码为空，这个过程可能会无法完成）。