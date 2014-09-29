---
layout: post
category : frontend
title : 拥抱Generator，告别回调
tags : [es6, generator, nodejs]
---


这篇文章的主题是拥抱Generator，告别异步回调，让我们用几个简单易懂的Demo来彻底解剖ES6的Generator。

并和fibers比较下原理上的差异，让我们一起来深入学习ES6的Generator。

什么是Generator？
======================

我们先看第1个DEMO：

    function* test(p){
        console.log(p); // 1
        var a = yield p + 1;
        console.log(a); // 3
    }

    var g = test(1);
    var ret;
    ret = g.next();
    console.log(ret); // { value: 2, done: false }
    ret = g.next(ret.value + 1);
    console.log(ret); // { value: undefined, done: true }

1. yield关键字可以让当前函数暂停执行并保存现场，并跳出到调用此函数的代码处继续执行。
2. 可以利用函数执行时的返回句柄的next方法回到之前暂停处继续执行
3. next执行的返回值的value即是yield关键字后面部分的表达式结果
4. 下一个next的唯一参数值可以作为yield的整体返回值，并赋值给a变量

看下执行顺序就能比较清楚Generator是怎么工作的了

![generator](/images/generator.png)

但是，为什么要设计这样的执行方式呢？

一次又一次的`next()`能干啥用呢？

Generator能为我们带来什么？

下面转入正题，正式看看Generator式的代码能干些啥？

Generator能做什么？
=======================

实际上类似协同程序的概念已经在相当多的语言中已经实现，例如Lua(coroutine), Python(Generator)。

Generator和标准的coroutine还是有区别的，Generator执行结束后会将控制权交还给调用者。

Generator实际上是一种特殊的迭代器，不过nodejs下主流的场景是将异步回调变成同步模式。

我们用TJ大神开发的[co模块](https://www.npmjs.org/package/co)来解释Generator的用途：

co是coroutine的缩写，即协同程序。

    var co = require('co');

    co(function* (){
        var now = Date.now();
        yield sleep200ms;
        console.log(Date.now() - now);
    })();

    function sleep200ms(cb) {
        setTimeout(cb, 200);
    }

很神奇吧？在ES6之前js中不可能实现同步的sleep功能。

我们再改写下上面的代码，让sleep函数支持自定义延迟的时间。

    var co = require('co');

    co(function *(){
        var now = Date.now();
        yield sleep(500);
        console.log(Date.now() - now);
    })();

    function sleep(ms){
        return function(cb){
            setTimeout(cb, ms);
        };
    }

基于co，我们就可以写出类似这样的业务代码：

    co(function *(){
        var rs = yield db.query('select url from xxx');
        rs.forEach(rs){
            var content = yield getUrl(rs.url);
            ...
        }
    })();

在这之前，我们只能用洋葱式回调写法：

    db.query('select url from xxx', function(rs){
        rs.forEach(r){
            getUrl(r.url, function(content){
                ...
            });
        }
    });

co还有更多的用法：

数组并发：

    co(function* () {
      var ret = yield [
        get(1),
        get(2)
      ];
      console.log(rs);// [1, 2]
    })();

健值对并发：

    co(function* () {
      var data = yield {
        a: getA(),
        b: getB()
      };
      console.log(data); // { a: 'aaa', b: 'bbb' }
    })();

更多用法不在这里深入。

我们是不是很想知道co内部是如何实现的？我们来次深入解剖吧。

co的实现原理
===========================

我们先来看个简化版的co代码，也算是co的骨架，官方co仅仅是功能更多更强大。

    co(function *( input ) {
        var now = Date.now();
        yield sleep200;
        console.log(Date.now() - now);
    });

    function co(fn){
        var gen = fn();
        next();
        function next(res){
            var ret;
            ret = gen.next(res);
            // 全部结束
            if(ret.done){
                return;
            }
            // 执行回调
            if (typeof ret.value == 'function') {
                ret.value(function(){
                    next.apply(this, arguments);
                });
                return;
            }
            throw 'yield target no supported!';
        }
    }

    function sleep200(cb){
        setTimeout(cb, 200)
    }

上面的代码中核心是Generator的流程控制，以及回调的执行。

通过co来执行代码的性能如何呢？以下是co官方的测试数据：

* 用co前：     570ms
* 用co后：     610ms

从数据上看，性能有一定的降低，但对大部分场景完全可以忽略这种性能损耗。

之前的异步模块如何使用？
===========================

几乎所有的node原生模块，以及大量的npm模块，都可以利用TJ的[thunkify模块](https://www.npmjs.org/package/thunkify)进行封装。

    var co = require('co');
    var thunkify = require('thunkify');
    var request = require('request');
    var get = thunkify(request.get);

    co(function *(){
      var a = yield get('http://google.com');
      var b = yield get('http://yahoo.com');
      var c = yield get('http://cloudup.com');
      console.log(a[0].statusCode);
      console.log(b[0].statusCode);
      console.log(c[0].statusCode);
    })()

    co(function *(){
      var a = get('http://google.com');
      var b = get('http://yahoo.com');
      var c = get('http://cloudup.com');
      var res = yield [a, b, c];
      console.log(res);
    })()

    // Error handling
    co(function *(){
      try {
        var res = yield get('http://badhost.invalid');
        console.log(res);
      } catch(e) {
        console.log(e.code) // ENOTFOUND
     }
    })()

是不是非常方便？是如何实现的呢？

thunkify的实现原理
==========================

异步封装成同步还是非常简单的，我们看下官方的代码：

    function thunkify(fn) {
        assert('function' == typeof fn, 'function required');

        return function() {
            var args = new Array(arguments.length);
            var ctx = this;

            for (var i = 0; i < args.length; ++i) {
                args[i] = arguments[i];
            }

            return function(done) {
                var called;

                args.push(function() {
                    if (called) return;
                    called = true;
                    done.apply(null, arguments);
                });

                try {
                    fn.apply(ctx, args);
                } catch (err) {
                    done(err);
                }
            }
        }
    };

在外层封装了函数，将参数传递给异步函数，并在回调中调用done，以触发next。

可能有心的人已经留意到，无论是co还是thunkify，都增加了防重复执行判断。

由于异步回调可能会触发多次，假如多次触发next，就会造成流程混乱。

和fibers的差异
==========================

co和Nodejs之前的[Fibers模块](https://www.npmjs.org/package/fibers)功能基本上一致。

同样我们先看下fibers是如何将异步变同步的：

    var Fiber = require('fibers');

    Fiber(function() {
        var now = Date.now();
        sleep(500);
        console.log(Date.now() - now);
    }).run();

    function sleep(ms) {
        var fiber = Fiber.current;
        setTimeout(function() {
            fiber.run();
        }, ms);
        Fiber.yield();
    }

是不是感觉和co的代码风格非常神似？

fibers主流程的代码上并没有出现特殊关键字，直观上来看和PHP等各类同步语言比较接近。

我们再来看看fibers是如何将异步结果回传的。

    var Fiber = require('fibers');

    Fiber(function() {
        var now = Date.now();
        var ret = delayTask(500);
        console.log(ret);
        console.log(Date.now() - now);
    }).run();

    function delayTask(ms) {
        var fiber = Fiber.current;
        var ret;
        setTimeout(function() {
            ret = 'ok';
            fiber.run();
        }, ms);
        Fiber.yield();
        return ret;
    }

上面的代码在流程上完全没变，仅仅在`Fiber.yield();`执行添加了`return ret;`。

并且在`fiber.run();`前进行了赋值`ret = 'ok';`;

本质思想上两种方案没本质区别：

1. Generator是利用yield特殊关键字来暂停执行，而fibers是利用`Fiber.yield()`暂停
2. Generator是利用函数返回的Generator句柄来控制函数的继续执行，而fibers是在异步回调中利用`Fiber.current.run()`继续执行。

简单来讲，Generator把句柄暴露给外部，而fibers把句柄暴露给内部。

上面的第2点特性决定了Generator必需把异步代码委托给外部，根据异步返回结果决定流程控制。

而fibers由于是对内暴露，因此必需要在异步回调中恢复父函数的执行。

假如异步回调多的话，就有可能在流程及内部变量使用上出现混乱。

我们再写个DEMO测下co的主流程中的异步特性：

    co(function *( input ) {
        var a;
        setTimeout(function(){
            console.log(a); // 'abc'
        }, 300)
        a = yield delayTask(200);
    })();

    function delayTask(ms){
        return function(cb){
            setTimeout(function(){
                cb(null, 'abc')
            }, ms);
        }
    }

从结果上来看，此处特性和fibers是一致的，yield同样是不影响异步函数的执行的。

Generator在经过co封装之后，就基本上和fibers没差异了。

因此，基于co，我们也可以写出漂亮的同步风格代码。

我可以在哪些地方使用Generator特性？
======================================

根据这个[ECMAScript 6 compatibility table](http://kangax.github.io/compat-table/es6/#Generator expressions)的资料显示，目前已经有如下平台可以支持：

1. Chrome 35+ (about://flags中开启)
2. Firefox 31+ (默认开启)
3. nodejs harmony

如果你站点是内部站点，或者服务器端站点，赶紧用起来吧。

让我们告别蛋疼的回调！