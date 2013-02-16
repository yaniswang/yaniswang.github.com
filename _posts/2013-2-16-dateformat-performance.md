---
layout: post
category : frontend
title : JS日期格式化函数性能探底
tags : [date, format, performance]
---

最近开发的软件中需要用到日志功能，其中有一个重要功能是显示日期和时间。于是网上搜了一把，搜到大量的日期格式化函数，不过比较了下，感觉代码都不够优雅，而且性能都不给力。

对线上一些代码进行了评测，结果如下：

测试代码如下，分别对格式化函数进行50万次计算：

    var start = new Date().getTime();
    var date = new Date();
    for(var i = 0;i<500000;i++){
        date.format1('yyyy-MM-dd hh:mm:ss');
    }
    console.log(new Date().getTime() - start);

函数1：

    // 对Date的扩展，将 Date 转化为指定格式的String
    // 月(M)、日(d)、小时(h)、分(m)、秒(s)、季度(q) 可以用 1-2 个占位符， 
    // 年(y)可以用 1-4 个占位符，毫秒(S)只能用 1 个占位符(是 1-3 位的数字) 
    // 例子： 
    // (new Date()).Format("yyyy-MM-dd hh:mm:ss.S") ==> 2006-07-02 08:09:04.423 
    // (new Date()).Format("yyyy-M-d h:m:s.S")      ==> 2006-7-2 8:9:4.18 
    Date.prototype.format1 = function (fmt) { //author: meizz 
        var o = {
            "M+": this.getMonth() + 1, //月份 
            "d+": this.getDate(), //日 
            "h+": this.getHours(), //小时 
            "m+": this.getMinutes(), //分 
            "s+": this.getSeconds(), //秒 
            "q+": Math.floor((this.getMonth() + 3) / 3), //季度 
            "S": this.getMilliseconds() //毫秒 
        };
        if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
        for (var k in o)
        if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
        return fmt;
    }

测试三次：

    成绩1：6657毫秒
    成绩2：6739毫秒
    成绩3：6747毫秒
    平均：6714毫秒

函数2：

    /** * 对Date的扩展，将 Date 转化为指定格式的String * 月(M)、日(d)、12小时(h)、24小时(H)、分(m)、秒(s)、周(E)、季度(q)
        可以用 1-2 个占位符 * 年(y)可以用 1-4 个占位符，毫秒(S)只能用 1 个占位符(是 1-3 位的数字) * eg: * (new
        Date()).pattern("yyyy-MM-dd hh:mm:ss.S")==> 2006-07-02 08:09:04.423      
     * (new Date()).pattern("yyyy-MM-dd E HH:mm:ss") ==> 2009-03-10 二 20:09:04      
     * (new Date()).pattern("yyyy-MM-dd EE hh:mm:ss") ==> 2009-03-10 周二 08:09:04      
     * (new Date()).pattern("yyyy-MM-dd EEE hh:mm:ss") ==> 2009-03-10 星期二 08:09:04      
     * (new Date()).pattern("yyyy-M-d h:m:s.S") ==> 2006-7-2 8:9:4.18      
     */  
    Date.prototype.format2=function(fmt) {         
        var o = {         
        "M+" : this.getMonth()+1, //月份         
        "d+" : this.getDate(), //日         
        "h+" : this.getHours()%12 == 0 ? 12 : this.getHours()%12, //小时         
        "H+" : this.getHours(), //小时         
        "m+" : this.getMinutes(), //分         
        "s+" : this.getSeconds(), //秒         
        "q+" : Math.floor((this.getMonth()+3)/3), //季度         
        "S" : this.getMilliseconds() //毫秒         
        };         
        var week = {         
        "0" : "/u65e5",         
        "1" : "/u4e00",         
        "2" : "/u4e8c",         
        "3" : "/u4e09",         
        "4" : "/u56db",         
        "5" : "/u4e94",         
        "6" : "/u516d"        
        };         
        if(/(y+)/.test(fmt)){         
            fmt=fmt.replace(RegExp.$1, (this.getFullYear()+"").substr(4 - RegExp.$1.length));         
        }         
        if(/(E+)/.test(fmt)){         
            fmt=fmt.replace(RegExp.$1, ((RegExp.$1.length>1) ? (RegExp.$1.length>2 ? "/u661f/u671f" : "/u5468") : "")+week[this.getDay()+""]);         
        }         
        for(var k in o){         
            if(new RegExp("("+ k +")").test(fmt)){         
                fmt = fmt.replace(RegExp.$1, (RegExp.$1.length==1) ? (o[k]) : (("00"+ o[k]).substr((""+ o[k]).length)));         
            }         
        }         
        return fmt;         
    } 

测试三次：

    成绩1：7334毫秒
    成绩2：7497毫秒
    成绩3：7498毫秒
    平均：7443毫秒

本着完美主义的态度，自己重新造了个更好的轮子，分享给需要的同学们，代码如下：

    /** 
     * 对日期进行格式化， 
     * @param date 要格式化的日期 
     * @param format 进行格式化的模式字符串
     *     支持的模式字母有： 
     *     y:年, 
     *     M:年中的月份(1-12), 
     *     d:月份中的天(1-31), 
     *     h:小时(0-23), 
     *     m:分(0-59), 
     *     s:秒(0-59), 
     *     S:毫秒(0-999),
     *     q:季度(1-4)
     * @return String
     * @author yanis.wang@gmail.com
     */
    function DateFormat(date, format) {
        if(format === undefined){
            format = date;
            date = new Date();
        }
        var map = {
            "M": date.getMonth() + 1, //月份 
            "d": date.getDate(), //日 
            "h": date.getHours(), //小时 
            "m": date.getMinutes(), //分 
            "s": date.getSeconds(), //秒 
            "q": Math.floor((date.getMonth() + 3) / 3), //季度 
            "S": date.getMilliseconds() //毫秒 
        };
        format = format.replace(/([yMdhmsqS])+/g, function(all, t){
            var v = map[t];
            if(v !== undefined){
                if(all.length > 1){
                    v = '0' + v;
                    v = v.substr(v.length-2);
                }
                return v;
            }
            else if(t === 'y'){
                return (date.getFullYear() + '').substr(4 - all.length);
            }
            return all;
        });
        return format;
    }

使用方法：

    dateFormat('yyyy-MM-dd hh:mm:ss');
    dateFormat(new Date(), 'yyyy-MM-dd hh:mm:ss');

测试三次：

    成绩1：2903毫秒
    成绩2：2900毫秒
    成绩3：2896毫秒
    平均：2899毫秒

经过改造的函数，整体上性能提升明显，从6714毫秒提升到2899毫秒，减少了3815毫秒，整体降到原43%的时间，性能提升一倍以上。并且从原形注入方式改为静态函数方式，更优雅大方。