---
layout: post
category : frontend
title : Github如何在Pull Merge前把数据拉成临时分支
tags : [github, pull request, test]
---

Github用多了以后，想到一个问题：

对于一个开源项目，如何测试别人pull request过来的代码？

有没有可能把pull request的代码临时性的合并到一个临时分支？如果测试没问题了，正式同意merge，测试不通过则打回。

最近在为csslint贡献代码的时候，正好发现travis可以在pull request时自动触发测试，想到肯定有这样的功能，于是打开了travis日志，真的发现了以下代码：

	$ git clone --depth=100 --quiet git://github.com/stubbornella/csslint.git stubbornella/csslint
	$ cd stubbornella/csslint
	$ git fetch origin +refs/pull/328/merge:

把上面这个代码写成一个本地的脚本，就可以方便的对别人pull request的代码进行详细的测试。