---
layout: post
category : frontend
title : IE全系列版本奇怪的BUG
tags : [IE, bug]
---

近期在修一个公司故障单的时候，发现了一个IE全系列版本都有的神奇BUG。

用户上传一张奇怪文件名的图片文件，文件名类似如下：```test.jpg``，左边那个小点是键盘上数字1左边那个符号。

这张图片插入到富文本编辑器后，会变成如下代码：

	<img alt="`test.jpg" src="xxx" width="200" height="200">
	
这样的代码有问题吗？看上去似乎没有问题。

但是公司使用的是tinyMCE编辑器，在IE浏览器下获取HTML代码的逻辑相对比较复杂，但是核心代码如下：

	var content = body.innerHTML;
	var node = body.cloneNode(false);
	node.innerHTML = content;
	
这个代码实现的效果就是把body的HTML代码读出，然后克隆出一份，并把HTML代码赋值给克隆出的对象。我们不要去关心为什么要这样做，为了把问题简单化，可以简化成如下代码：

	body.innerHTML = body.innerHTML;
	
自己拉出来代码，自己再吃回去，就嗝屁了……（要是人干这事，还真就嗝屁了）

那IE拉出来什么样的代码呢？

	<IMG alt=`test.jpg src="xxx" width="200" height="200">
	
这样的代码可能大家见怪不怪了，IE吐出来的代码不是大写就是少了引号，很正常么……

但是奇怪的事情发生了，大家可以把上面的代码写在一个HTML文件里，在任意一个版本的IE浏览器运行一下。神奇的事情发生了，IE竟然不认这段代码了。IE认为这段代码是不符合规范的，直接当成文本输出了。

经过多种场景测试后，最终发现IE全系列浏览器都是把`` ` ``这个符号当作是引号来处理，即是说如下代码都是能正常工作的：

	<img alt=`asfas` width=`200` height=`200`>

神奇吧？如果IE是这样的工作逻辑，那么上面出现的故障就能前后解释通了。

IE的innerHTML在吐代码时，发现这个字符串是以`` ` ``开头的，因此吐出时就直接把两边的双引号省略了。而最终把这个代码写回到innerHTML时，又校验出alt属性只有开头的`` ` ``，却没有结尾，因此直接判定为非法HTML代码，并作为文本输出。

这个问题实际上还是比较容易碰到的，只要属性值以`` ` ``开头，把IE的innerHTML读出来，并写回到浏览器中，就会出现这个现象。

对于这个问题，解决方案只能在读出IE的innerHTML值时进行修补工作，将所有无引号的属性值强制加引号，处理成如下：

	<IMG alt="`test.jpg" src="xxx" width="200" height="200">
	
经过处理的代码重写回innerHTML，IE终于认了。至此，万恶的IE又摸上了一笔……