---
layout: post
category : frontend
title : referrer安全协议解析
tags : [meta, referrer, security, w3c]
---

相信很多人知道，当一个用户从A页面浏览到B页面，可以从`header`或者`document.referrer`中读取到A页面的URL地址。

但是，你是否知道出于安全考虑，从HTTPS页面跳到HTTP页面，`referrer`值是空的？

那么，我们的问题来了。

GOOGLE搜索引擎的页面几乎全是HTTPS协议的，我们如何知道哪些流量是从GOOGLE过来的呢？

这里请出我们的主角：referrer安全协议 ([http://www.w3.org/TR/referrer-policy/](http://www.w3.org/TR/referrer-policy/))

referrer安全协议
======================

示例代码：

    <meta name="referrer" content="origin">

1. Default: 默认设置，只有https转http时不发送`referrer`
2. Never：永远不发送`referrer`，无论是不是https
3. Always：永远发送`referrer`，无论是不是https
4. Origin：永远发送`referrer`，但是只发域名部分

我们举google页面的例子，打开google搜索页面后，可以看到如下代码：

    <meta content="origin" id="mref" name="referrer">

这说明GOOGLE是允许在某些支持此特性的浏览器下，获取到域名部分的来源信息。

比如，我们可以看到GOOGLE搜索页面点击后的referrer：

    https://www.google.com/

经测试，目前可以明确以下两个浏览器是支持这个特性的：

1. Chrome
2. Safari