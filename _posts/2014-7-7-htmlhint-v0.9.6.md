---
layout: post
category : frontend
title : HTMLHint v0.9.6 发布
tags : [htmlhint, javascript, html, analysis, ide, build]
---

![HTMLHint logo](https://raw.github.com/yaniswang/HTMLHint/master/logo.png)

HTMLHint是一款基于JS开发的静态扫描组件，支持所有浏览器和Nodejs平台，可以集成到IDE环境或编译系统中。

支持非常多的扫描规则，可灵活搭配使用。

**官方网站：**[http://htmlhint.com/](http://htmlhint.com/)

HTMLHint特色
=================

1. 跨平台支持，支持所有浏览器及Nodejs
2. 全单元测试覆盖，travis自动持续集成，质量有保障
3. 插件体系灵活，规则添加方便

v0.9.6 更新日志
==================

1. 添加规则: [attr-no-duplication](https://github.com/yaniswang/HTMLHint/wiki/attr-no-duplication)
2. 添加规则: [space-tab-mixed-disabled](https://github.com/yaniswang/HTMLHint/wiki/space-tab-mixed-disabled)
3. 添加规则: [id-class-ad-disabled](https://github.com/yaniswang/HTMLHint/wiki/id-class-ad-disabled)
4. 添加规则: [href-abs-or-rel](https://github.com/yaniswang/HTMLHint/wiki/href-abs-or-rel)
5. 添加规则: [attr-unsafe-chars](https://github.com/yaniswang/HTMLHint/wiki/attr-unsafe-chars)
6. 默认规则添加: attr-no-duplication
7. 添加行内规则集支持
8. 添加测试用例: Set false to rule
9. 添加功能点: 当规则集为空`{}`时，使用默认规则

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
        'spec-char-escape': true,
        'attr-no-duplication': true
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