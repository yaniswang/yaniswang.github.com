---
layout: post
category : frontend
title : Markdown Reader v1.0.5发布
tags : [markdown, chrome]
---


随着Github的流行，有个技术也火起来了，那就是[Markdown语法](http://zh.wikipedia.org/zh/Markdown)。

Markdown语法写起来清爽便利，但是纯文本阅读起来体验还是不够好。

这里，给大家分享一款我两年前开发的Chrome插件：[Markdown Reader](https://chrome.google.com/webstore/detail/gpoigdifkoadgajcincpilkjmejcaanc)

目前此插件装机量已经达到了2466，用户反应非常不错：

![0](/images/md0.png)

应很多网友的反馈，本次v1.0.5版本增加了表格扩展的支持。

软件特点
=========================

本款插件有以下特点：

1. 漂亮的样式渲染，看起文档更舒服
2. 代码块增加代码高亮和行号功能，提高码农工作效率
3. 监测文件修改，并自动重新加载并渲染markdown，使保存的修改立即生效
4. 右侧大纲跟随功能，方便章节间快速导航
5. 回到顶部功能，方便快速回到顶部
6. 支持表格扩展

本插件可以使用在以下两种场景：

1. 阅读Markdown文件
2. 编辑Markdown文件时自动刷新，预览文档内容

软件截图
=========================

**主体功能**

![1](/images/md1.png)

**代码高亮**

![2](/images/md2.png)

**大纲跟随**

![3](/images/md3.png)

**回到顶部**

![4](/images/md4.png)

**表格支持**

![5](/images/md5.png)

使用教程
==========================

1. 在Chrome官方市场中安装：[Markdown Reader](https://chrome.google.com/webstore/detail/gpoigdifkoadgajcincpilkjmejcaanc)
2. 安装完成后，地址栏输入：[chrome://chrome/extensions](chrome://chrome/extensions)（或者 工具->扩展程序）
3. 开启Markdown Reader的“允许访问文件网址”选项，不开启这个选项只能渲染http开头的md文件
4. 拖动以md扩展名的文件到 Chrome浏览器中，漂亮的文档界面就展现在我们面前了

小技巧
=========================

如果将md文件关联到chrome浏览器，将会提升至更好的体验。

方法如下：

1. 双击md文件，会出现：`打开方式`窗口
    ![6](/images/md6.png)
2. 选择用Google Chrome打开，并且打勾：`始终使用选择的程序打开这种文件`

在这之后，双击md文件，就可以看到漂亮的文档了。