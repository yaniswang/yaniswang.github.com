---
layout: post
category : frontend
title : hostsPlus v1.0.0 正式发布
tags : [hosts, mac, windows, air]
---

写在最前面
=========================

hostsPlus是款非常实用的开发小工具，能够很方便的管理hosts绑定，提高开发效率。

此软件从诞生到现在已经走了快2年时光，经过不断的完善，目前在阿里巴巴的团队中已经获得了相当不错的使用率。

好东西要拿出来分享，在v1.0.0发布之际，分享给大家。

希望本款小工具，能够给大家带来工作效率的提升。

什么是hostsPlus？
=========================

![hostsplus logo](/images/hostsplus_logo.png)

hostsPlus是一款强大的hosts增强管理软件，可以方便高效的管理您的hosts绑定，是您开发的得力助手。

具有如下功能：

1. 多文件Hosts方案管理
2. 单Hosts文件中分组管理
3. 远程Hosts (可以定时同步远程的hosts内容)
4. 切换DNS (利用架设DNS服务器，可以实现公共绑定的管理)
5. CDN一致性检测
6. 主题支持
7. 跨平台兼容：Windows,Mac

软件截图：

![hostsplus screenshot](/images/hostsplus_screenshot1.png)

![hostsplus screenshot](/images/hostsplus_screenshot2.png)

v1.0.0更新日志
=====================

添加：

1. 添加自动探测活动网卡名字功能，能自适应兼容Windows和Mac系统
2. 添加方案保存菜单
3. 添加字体大小修改功能

更多更新日志请查看：[hostsPlus更新日志](https://github.com/yaniswang/hostsPlus/blob/master/CHANGE.md)

分组使用说明
===============================

新建分组
-----------------------------

以如下格式设置可新建分组(快捷键: Ctrl + G)：

    # =组ID=
    或
    # = 组ID =
    或
    # ======组ID======
    
前后的等号可以是1个或者多个，具体视使用者偏好。

单个hosts文件中，多个组ID必需是唯一不重复的。

小提示：可以先把光标定位在分组首行，按Ctrl + G快捷键，就会在当前行上方新建一个分组。

互斥分组
-----------------------------

互斥分组即是多个组之间是相互排斥的，开启一个组，就必需关闭另外一个或多个组。

假如有如下环境：开发环境、测试环境、预发环境、生产环境。这几个环境的hosts绑定不可能同时生效，其中一个生效时，另外几个必需要处于关闭状态。

在这之前，我们切换开发环境，都是手工在hosts文件中通过注释的方式切换。现在，有了hostsPlus，你可以高效的完成这个过程。

那么，如何建立互斥分组呢？请看示例：

    # =====组1(dev)=====
    # =====组2(dev)=====
    # =====组3(dev)=====

括号里为组name，相同name为互斥关系。

当打开组1时，另外两个组就会自动切换为关闭状态。

依赖分组
----------------------------------------------

依赖分组是某一个分组被开启时，被依赖的分组也会同时被开启。

直接看示例：

    # =====组1=====
    # =====组2(?组1)=====
    
括号里开始处加问号，代表依赖某个组。示例为组2依赖组1，当组2开启时，组1自动开启。

组合使用
----------------------------------------------

互斥和依赖可以组合起来使用，上例子：

    # =====组1=====
    # =====组2=====
    # =====组3(dev)=====
    # =====组4(dev)(?组1)(?组2)=====
    
组3和组4为互斥关系，组4依赖组1和组2。

快捷键列表
===============================

 * Ctrl + Tab : 切换hosts方案
 * Ctrl + Q : 切换启用状态
 * Ctrl + / : 切换注释状态
 * Ctrl + G : 新建分组
 * Ctrl + A : 全选
 * Ctrl + C : 复制
 * Ctrl + X : 剪切
 * Ctrl + V : 粘贴
 * Ctrl + Z : 撤销
 * Ctrl + Y : 恢复
 * Ctrl + S : 保存hosts编辑结果
 * Ctrl + F : 搜索
 * F3 : 搜索下一个
 * Shift + F3 : 搜索上一个
 * Ctrl + H : 替换
 * Ctrl + Shift + H : 全部替换
 * ESC : 隐藏到系统栏
 * F1 : 打开使用指南
 * F11 : 切换全屏
 
注：Mac系统下所有Ctrl替换为Command键。

DEMO示例
=============================

以下是示例文件，大家可以直接把以下内容复制到编辑区域中保存(注意备份旧数据，或者在新建方案中测试)，体验下分组、互斥及依赖关系。

    # 这里是顶部公用区，不属于任何分组
    127.0.0.1   localhost
    # ==================== 公共绑定 ====================
    127.0.0.1   www.google.com
    # ==================== 本地(style) ====================
    127.0.0.2   www.google.com
    # ==================== 预发(style)(?公共绑定) ====================
    127.0.0.3   www.google.com

Download - 下载
========================

版本号：v1.0.0

**Windows：**[hostsplus-v1.0.0.win.zip](https://github.com/yaniswang/hostsPlus/releases/download/v1.0.0/hostsplus-v1.0.0.win.zip)

**Mac：**[hostsplus-v1.0.0.mac.zip](https://github.com/yaniswang/hostsPlus/releases/download/v1.0.0/hostsplus-v1.0.0.mac.zip)

**源码：**[https://github.com/yaniswang/hostsPlus](https://github.com/yaniswang/hostsPlus)