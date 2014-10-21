---
layout: post
category : frontend
title : Nodejs连接mysql时碰到的坑
tags : [nodejs, mysql]
---

公司中基于Nodejs开发的一套系统，在运行大半年中，偶尔发现脚本被延后8小时，始终摸不着头绪。

最近几天突然想到仔细查下日志，结果发现在半夜时出现了一次mysql连接超时，之后的8小时一直超时着。

之前使用的代码如下：

    var db = mysql.createConnection({
      host     : '...',
      user     : '...',
      password : '...',
    });
    db.query('...');

这种默认的方式是没有对连接超时进行处理的，必需要自己捕获超时事件，并执行对应的响应：

    conn.on('error', function (err) {
        console.log('db error', err);
        if (err.code === 'PROTOCOL_CONNECTION_LOST') {
            handleError();
        } else {
            throw err;
        }
    });

网上查了下资料，如果使用pool方式，内部已经实现超时自动重连：

    var pool = mysql.createPool({
      host     : '...',
      user     : '...',
      password : '...',
    });

    pool.getConnection(function (err, conn) {
        conn.query('...');
    });

不过根据官方的文档，pool还有一种使用方法：

    var pool = mysql.createPool({
      host     : '...',
      user     : '...',
      password : '...',
    });

    pool.query('...');

那么这两种方式有什么区别呢？

我们扒一下官方的源代码：

    Pool.prototype.query = function (sql, values, cb) {
        var query = Connection.createQuery(sql, values, cb);

        if (!(typeof sql === 'object' && 'typeCast' in sql)) {
            query.typeCast = this.config.connectionConfig.typeCast;
        }

        if (this.config.connectionConfig.trace) {
            // Long stack trace support
            query._callSite = new Error;
        }

        this.getConnection(function (err, conn) {
            if (err) {
                var cb = query._callback;
                cb && cb(err);
                return;
            }

            // Release connection based off event
            query.once('end', function() {
                conn.release();
            });

            conn.query(query);
        });

        return query;
    };

从这个源代码上可以看出，pool.query仅仅是封装了一层，并且每次调用都会调用getConnection和release。

写了个代码简单测试了下，30000次循环的情况下，pool.query在时间消耗上是getConnection的5倍。

因此，对于需要频繁调用query的情况，强烈建议使用getConnection方式。

如果你也在用Nodejs连mysql，希望这篇文章可以让你少走一点弯路。