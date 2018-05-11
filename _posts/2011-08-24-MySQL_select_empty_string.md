---
layout: post
title: MySQL中关于查询条件中的字符串空格问题 
date: 2011-08-24 
tag: mysql
---

假设当前mysql数据库中有个表：sysuser

有个字段是：sysUseName

字段中有条记录的内容是：robin

请看下面这两个SQL语句：

SELECT * from sysuser s  where s.sysUseName = 'robin'

SELECT * from sysuser s  where s.sysUseName = 'robin<span style="color:red">空格</span>'

SELECT * from sysuser s  where   s.sysUseName  =  'RoBin<span style="color:red">空格</span>'

这三条语句均可以查询出那条记录。

官方文档上说是MySQL校对规则属于PADSPACE，对CHAR和VARCHAR值进行比较都忽略尾部空格，和服务器配置以及MySQL版本都没关系。

MySQL安装目录下有个doc目录(Windows)，里面的 Data Types -> String Types -> The CHAR and VARCHAR Types 这一小节有对这个问题的说明以及相关的例子(版本不一样可能章节的编号可能有变动，还是看标题吧): All MySQL collations are of type PADSPACE. This means that all CHAR and VARCHAR values in MySQL are compared without regard to any trailing spaces.    

这样带来的问题是：我如何需要精确匹配robin这个内容？假设有一个登陆功能，我希望用户输入‘robin’可以登陆，但是输入‘robin空格’却不能登录，该如何实现。

解决办法如下：

<span style="color:red">SELECT * from sysuser s  where   s.sysUseName  = BINARY 'robin '</span>
(robin后有空格）

BINARY不是函数，是类型转换运算符，它用来强制它后面的字符串为一个二进制字符串，可以理解为在字符串比较的时候区分大小写，精确匹配。

另外，因为有的MySQL特别是4.*以前的对于中文检索会有不准确的问题，可以在检索的时候加上binary。

