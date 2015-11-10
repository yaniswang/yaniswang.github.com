---
layout: post
category : frontend
title : Promise黑魔法
tags : [promise, nodejs, javascript, generator, es6, es7, async]
---

回调恶魔
======================

在目前Javascript技术背景下，当碰到大量异步代码时，会非常头痛。

目前有以下几种手段来解决异步回调问题：

1. 传统异步回调
2. Promise
3. ES6 Generator
4. ES7 async

远古
-------------------------

对于异步回调，相信大家都不会陌生，称之为洋葱式回调地狱！就是我们要一起消灭的恶魔！

封建
-------------------------

经过地狱的折磨后，我们终于迎来了一丝曙光，Promise诞生了。

在经过很长一段时间的磨合后，目前Promise成功登顶，顺利成为ES6标准，也将会是未来的一个绝对发展方向。

但是说到底，Promise本质上还是回调，仅仅是利用Promise把多个回调串起来。

现代
-------------------------

时光进入ES6时代，我们迎来了Generator，在TJ大神的神迹下，Generator被发扬光大。

    co(function*(){
        return yield sleep(200);
    }).then(function(){
        
    });

利用Generator，我们终于可以告别洋葱式回调地狱了！我们为之欢呼雀跃！

但是……

Generator最初设计并不是用来解决异步回调问题的，被TJ意外中利用来解决回调地狱问题了。

最终，TJ大神也放弃了Generator这个孩子，因为未来带着光环的王子马上就要诞生了。

未来
-------------------------

时光再次穿梭，我们进入了ES7时代。

在这个时代里，我们发明了async await！终于有了一个名正言顺的官方解决方案！

    var func = async function(){
       return await sleep(200); 
    }

    func().then(function(){
        
    });

这个方案算是真正的集大成之解决方案，利用async+Promise完美的解决了回调地狱的问题。

虽然未来还是未来，但是眼下却可以利用co来做过渡，因为co4.0版本已经支持yield返回Promise对象。

如果你的yield返回值均是Promise对象，将来如果要升级到ES7方案，仅需做2件事：

1. co替换成async标识
2. yield替换成await

闭上眼睛……哇！完美运行！

Promise黑魔法
==========================

一切感觉都是那么的美好，但是，但是……

当你的场景中需要调用大量异步接口时，你的代码会像这样：

    function*(){
        var app = new App();
        yield app.a();
        yield app.b();
        yield app.c();
        yield app.d();
        ...
        return yield a.e();
    }

又是一阵头痛！

是不是觉得满屏的yield，眼都绿了？手也敲酸了……

假如能这么写多么的美好啊，开始做梦中……

    function*(){
        var app = new App();
        return yield app.a().b().then(function(ret){
            console.log(ret);
        }).c().d().e();
    }

Duang！是谁敲我的头？

我是超级黑魔法师，让我来拯救你！

现在我们来介绍下本篇文章的主角：[PromiseClass](https://www.npmjs.com/package/promiseclass)

PromiseClass
------------------------

![PromiseClass](https://raw.githubusercontent.com/yaniswang/PromiseClass/master/logo.png)

[![Build Status](https://img.shields.io/travis/yaniswang/PromiseClass.svg)](https://travis-ci.org/yaniswang/PromiseClass) [![Coverage Status](https://img.shields.io/coveralls/yaniswang/PromiseClass.svg)](https://coveralls.io/github/yaniswang/PromiseClass?branch=master) [![NPM version](https://img.shields.io/npm/v/promiseclass.svg?style=flat)](https://www.npmjs.com/package/promiseclass) [![License](https://img.shields.io/npm/l/promiseclass.svg?style=flat)](https://www.npmjs.com/package/promiseclass) [![NPM count](https://img.shields.io/npm/dm/promiseclass.svg?style=flat)](https://www.npmjs.com/package/promiseclass) [![NPM count](https://img.shields.io/npm/dt/promiseclass.svg?style=flat)](https://www.npmjs.com/package/promiseclass)

拿起PromiseClass，让我们一起去消灭回调恶魔！

基于PromiseClass构建的类，所有的方法均会被wrap成Promise风格。

Demo如下：

    var App = PromiseClass.create({
        // 构造函数
        constructor(n){
            console.log(n);
        },
        // 同步方法
        syncMethod1(n){
            return n;
        },
        // 异步方法
        asyncMethod2(n, done){
            setTimeout(function(){
                done(null, n);
            }, n)
        }
    });
    var app = new App('1')
    app.syncMethod1(1).then(function(ret){
        console.log(ret);
    }).asyncMethod2(2).then(function(ret){
        console.log(ret);
    });

这个黑魔法是不是非常神奇？

我们可以无限的循环链式调用下去，并且随时可以用then或者catch来拿到返回值和错误信息。

处理完返回值后，我们可以继续调用方法。

我们甚至还可以和回调混合使用，想怎么玩就怎么玩：

    var app = new App('1')
    app.method2(2, function(error, ret){
        console.log(ret);
        return 3;
    }).then(function(ret){
        console.log(ret);
    });

还不够过瘾？因为我们的返回值都是Promise，所以我们也完美支持在Generator或ES7 async场景下使用：

ES6 Generator:

    co(function*(){
        var app = new App('1')
        var ret1 = yield app.method1(1).then(function(ret){
            return 2;
        });
        return yield app.method2(2);
    });

ES7 async:

    (async function(){
        var app = new App('1')
        var ret1 = await app.method1(1).then(function(ret){
            return 2;
        });
        return await app.method2(2);
    })();

先别激动，我们还有高级黑魔法！

    var App = PromiseClass.create({
        *method1(n){
            return yield this.method2(n);
        },
        method2(n, done){
            setTimeout(function(){
                done(null, n);
            }, n)
        }
    });
    var app = new App('1')
    app.method1(1).then(function*(ret){
        console.log(ret);
        return yield this.method2(2);
    });

当你的类方法或者then回调本身就需要做复杂的异步操作时，就可以将函数定义为Generator。（将来可以很方便的支持ES7 async）

在PromiseClass的黑魔法世界中，我们想怎么玩就怎么玩。

标准异步回调，链式Promise，Generator，ES7 async……一个都不能少！

合适的场景选择最适合你的组合方案。

怎么获取PromiseClass？
----------------------

> npm install promiseclass

> 源代码：[https://github.com/yaniswang/PromiseClass](https://github.com/yaniswang/PromiseClass)

兼容性
----------------------

很抱歉，由于我长的太帅，目前仅支持Node 4.x以上版本

当然，理论上Chrome浏览器可以完美的将我的黑魔法施展出来，但是，想想还有N个浏览器不支持，还是洗洗睡吧……