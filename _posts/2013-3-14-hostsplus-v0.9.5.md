---
layout: post
category : frontend
title : hostsPlus v0.9.5发布 & 最佳实践
tags : [hosts, mac, linux, windows, air]
---

![hostsPlus](/images/hostsplus.png)

经过一段时间需求的积累及开发，hostsPlus v0.9.5版本终于面世了。

这个版本相对于之前的版本，功能和实用性上得到了很大提高。

v0.9.5更新日志
=====================

添加：

1. 添加搜索和替换功能，并支持正则搜索
2. 添加全局备份和恢复功能，方便重装系统时备份数据
3. 添加远程Hosts功能，以支持公司内全局范围的Hosts共享
4. 添加CDN一致性检测功能，发布时可轻松检测全球生效情况
5. 添加由于hosts权限问题而写入失败的提醒
6. Mac版本添加DNS切换支持

调整：

1. codeMirror升级为v3.1

重点功能介绍
=====================

1. 搜索和替换

    这个功能一直有非常多的用户反馈过，这次终于实现了，最大的缺陷解决了。

    搜索：Ctrl + F ， 替换：Ctrl + H

2. 备份和恢复

    之前有用户反馈重装系统时很不方便，现在有了备份和恢复功能，重装时可以方便的备份数据了

3. 远程Hosts功能

    利用此功能，可以一定程度上实现hosts共享，比如公司某些公用hosts绑定的共享

4. CDN一致性检测

    利用此功能可以对多个CDN节点进行内容一致性检测，主要用于提高发布阶段的数据检验效率

5. 对Mac全支持

    此次实现了对Mac系统的全支持，包括DNS切换功能，强烈推荐Mac的同学们安装使用

最佳实践分享
========================

这一章节介绍我所在公司的最佳实践应用场景。

首先了解一下以下信息：

1. AENV是公司的环境管理平台，用来自动化部署应用环境
2. AENV提供了所有应用的公用测试环境，用来做公用测试，以及解决某个应用测试时的依赖问题
3. AENV提供了了一个测试用DNS，利用此DNS可以访问到所有公用测试环境，相当于在本地hosts中绑定了所有的公用绑定
4. AENV提供了一个API接口，可以获得某个用户下所有项目环境的hosts绑定

hostsPlus使用方式：

1. 利用hostsPlus可以方便的切换`本地DNS`和`测试DNS`
2. 利用hostsPlus的远程Hosts功能可以获取开发者相关的所有项目环境hosts分组绑定，并保持随时同步

基于以上两个功能，要想访问测试环境，只需要切换为`测试DNS`，并选择对应的项目环境Hosts绑定即可。

仅需四次鼠标点击，即可完成全部的绑定工作，极大的提高了开发测试效率。

基于远程Hosts的自动同步功能，更能做到项目Hosts绑定和环境管理系统的随时同步，环境一绑定，hostsPlus右键菜单中即可看到对应的环境名。

无论是开发、测试还是产品人员，都可以傻瓜式的在多个测试环境间切换，不用为复杂的环境绑定而苦恼。

Download - 下载
========================

版本号：v0.9.5

**Windows：**[hostsplus.v0.9.5.exe](http://hostsplus.googlecode.com/files/hostsplus.v0.9.5.exe)

**Mac：**[hostsplus.v0.9.5.mac.zip](http://hostsplus.googlecode.com/files/hostsplus.v0.9.5.mac.zip)

**Linux i386：**[hostsplus.v0.9.5.linux_i386.zip](http://hostsplus.googlecode.com/files/hostsplus.v0.9.5.linux_i386.zip)

**Linux a64：**[hostsplus.v0.9.5.linux_a64.zip](http://hostsplus.googlecode.com/files/hostsplus.v0.9.5.linux_a64.zip) (64位版)

**源码：**[https://github.com/yaniswang/hostsPlus](https://github.com/yaniswang/hostsPlus)

Windows安装方法
-------------------------

直接双击EXE文件运行

Mac安装方法
-------------------------

解压缩安装包后，执行以下命令：

    ./install_mac.sh
    
执行过程中，会提示输入root密码（如果你的账户密码为空，这个过程可能会无法完成）。


Linux安装方法
-------------------------

解压缩安装包后，进入到安装包路径下，执行以下命令：

    sudo chmod 777 *
    ./install_linux.sh

install_linux.sh脚本内容如下：

    sudo ln -s /usr/lib/i386-linux-gnu/libgnome-keyring.so.0.2.0 /usr/lib/libgnome-keyring.so.0
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

