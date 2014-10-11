---
layout: post
category : frontend
title : IE window.name跨域方案陷阱
tags : [ie, window.name, 跨域]
---


window.name跨域方案一直是前端解决很多特殊业务场景的降龙宝刀，但是IE下，大家可能要小心陷阱了。

经典的跨域流程如下：

`a.com/test.html`中内嵌iframe一个目标页`b.com/target.html`，目标页中完成`window.name`的改写，然后跳转回`a.com/temp.html`

一般情况下认为，`a.com/temp.html`可以是不存在的一个页面，返回404状态码也不影响跨域。

父页面可以通过`iframe.contentWindow.name`可以获取到返回的字符串。

但是在IE下有个细节是有差异的。

假如`a.com/temp.html`这个404页面返回的content-length大小小于512字节，IE会认为这个404页面不够权威，从而会显示成如下的页面：

![ie404](/images/ie404.png)

此时，右键查看URL地址，可以发现已经跳转到：`res://ieframe.dll/http_404.htm#http://a.com/temp.html`

从URL上来看，明显已经跨域了，从而造成在父页面读取不了内页的name值。

那么，我们如何才能和`window.name`快乐的玩呢？

这里给出几个建议：

1. 优先使用postMessage，由于不兼容postMessage的浏览器铁定是完美支持window.name的，因此两套方案结合完全不用担心浏览器突然砍我们一刀
2. 如果非要全浏览器兼容`iframe+window.name`，`a.com/temp.html`弄个0字节或者把404的内容增加到512字节以上，就能解决了
3. 当前面间跳转的`window.name`跨域，暂不存在陷阱

由于W3C标准中关于`window.name`特性并没有详细描述，从来没哪个浏览器禁止这个特性，所以这个特性被大量网站使用。

只要这个特性不会引发严重的安全风险，相信浏览器厂商或W3C也不会去变更一个旧的API。

W3C大部分的草案都是向下兼容的，浏览器不兼容的调整几乎都是修补严重安全漏洞。

如果能使用第1套方案的尽量都用第1套方案，浏览器厂家禁用`window.name`的可能性也并不等于零。