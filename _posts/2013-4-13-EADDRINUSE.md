---
layout: post
category : frontend
title : request EADDRINUSE解决方案
tags : [tcp, request, EADDRINUSE]
---

最近在使用nodejs发起大量request请求的时候，出现了以下错误信息：

    { [Error: connect EADDRINUSE] code: 'EADDRINUSE', errno: 'EADDRINUSE', syscall: 'connect' } 3

操作系统：Win Server 2003 x64

按照传统思维，EADDRINUSE错误应该是指地址被占用。但是发起request请求，何来地址占用一说？

刚开始一直以为是nodejs本身的BUG，后来找到nodejs官方github上的讨论，才知道这个是操作系统层面的问题。

网上搜了一下，找到几篇介绍TCP握手的文章，个人能力有限，并且也没时间深入进去。

直接把解决方案尝试了一下，方案如下：

Windows系统，添加以下两个注册表项：

    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters]
    "TcpTimedWaitDelay"=dword:0000001e
    "MaxUserPort"=dword:0000ffff

Linux系统：

    echo "30" > /proc/sys/net/ipv4/tcp_fin_timeout

经过测试，以上方案非常靠谱，操作后重启系统，问题完美解决！

如果你也碰到这个问题，可以尝试一下以上解决方案。
