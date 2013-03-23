---
layout: post
category : frontend
title : HTMLHint v0.9.1 首个版本发布
tags : [javascript, html, analysis, ide, build]
---

![HTMLHint logo](https://raw.github.com/yaniswang/HTMLHint/master/logo.png)

HTMLHint是一款基于JS开发的静态扫描组件，支持所有浏览器和Nodejs平台，可以集成到IDE环境或编译系统中。

**官方网站：**[http://htmlhint.com/](http://htmlhint.com/)

软件开发背景：
=================

1. 近一段时间一直在负责公司质量方面工作
2. 团队质量情况不是很乐观，本人之前就出过标签未闭合造成的线上故障
3. 业界中JS有JSHint，CSS有CSSLint，但是HTML没有比较靠谱的方案

基于上面一些背景原因，业余时间开发了HTMLHint。在解决公司需求的同时，希望能够为整个前端业界的HTML代码质量提升，提供一些帮助。


HTMLHint特色
=================

1. 跨平台支持，支持所有浏览器及Nodejs
2. 全单元测试覆盖，travis自动持续集成，质量有保障
3. 插件体系灵活，规则添加方便

HTMLHint的安装
=================

1. 浏览器

    下载: [https://raw.github.com/yaniswang/HTMLHint/master/lib/htmlhint.js](https://raw.github.com/yaniswang/HTMLHint/master/lib/htmlhint.js)

2. Nodejs

        npm install htmlhint -g

HTMLHint的使用
=================

接口调用方式:

    HTMLHint.verify(code, rules);


省略rules的情况下，调用以下默认规则集:

    {
        'tagname-lowercase': true,
        'attr-lowercase': true,
        'attr-value-double-quotes': true,
        'doctype-first': true,
        'tag-pair': true,
        'spec-char-escape': true
    }

1. Nodejs

        var HTMLHint  = require("htmlhint").HTMLHint;
        var messages = HTMLHint.verify('<ul><li></ul>', {'tag-pair': true});

2. 浏览器

        <script type="text/javascript" src="htmlhint.js"></script>
        <script type="text/javascript">
            var messages = HTMLHint.verify('<ul><li></ul>', {'tag-pair': true});
        </script>

反馈BUG
=======================

在Github上新建 [new issue](https://github.com/yaniswang/HTMLHint/issues/new)，在其中描述问题或建议。

更希望更多的开发者能够参与进来，共同完善HTMLHint。

更多链接
=======================

1. [所有规则介绍](https://github.com/yaniswang/HTMLHint/wiki/Rules)
2. [开发手册](https://github.com/yaniswang/HTMLHint/wiki/Developer-guide)