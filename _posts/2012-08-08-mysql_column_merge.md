---
layout: post
title: mysql多记录单字段合并 
date: 2012-08-08 
tag: mysql
---

在开发中我们很多的时候都存在这样的问题。将表的几列合并到一行显示。
例如：

ID | NAME | CITY 
- | :-: | :-: 
1 | 张三| 上海 
2 | 李四 | 北京 
3 | 王五 | 上海
4 | 赵六 | 广州

我们想查询每个城市有哪些人 显示方式如:

- | :-: | :-: 
上海	| 张三,王五
北京	| 李四
广州 | 赵六

group_concat 必须与 group by 一起使用 <br/>
完整句法如下：
``` sql
GROUP_CONCAT([DISTINCT] expr [,expr ...]  
[ORDER BY {unsigned_integer | col_name | formula} [ASC | DESC] [,col ...]]  
[SEPARATOR str_val])
```
可以看到我们在group_concat 函数中可以使用distinct，order by等以前使用
而separator则是显示使用什么样的链接字符。
对于以上查询我们可以
``` sql
select CITY, GROUP_CONCAT( `NAME` separator ','  ) from testtable group by CITY
```
如果里面出现张三有两条上海的记录我们连接只想查询这样的结果呢
我们可以使用distinct的函数 使用方法如下。
``` sql
select name, GROUP_CONCAT( distinct  testtable.`NAME`  separator ','  ) from  testtable  group by  testtable.`CITY`  
```
如果我们想按城市排序呢，我们可以使用order  by
``` sql
select name, GROUP_CONCAT( distinct  testtable .`NAME` order by CITY separator ','  ) from  testtable  group by  testtable.`CITY` 
```