---
layout: post
title: Extjs Store的简单实验使用 
date: 2009-08-16 
tag: javascript
---

大部分人使用Store的时候都是使用url的方式，即从后台获取一个json字符串，载入store.

有时候我们需要做个试验什么的，只需把store写死，而不希望通过url这么麻烦。

方法如下:

``` javascript
var results=[['同意','agree'],
     ['拒绝','disagree'],
     ['全部','all']];
var store = new Ext.data.SimpleStore({
   data:results,
   fields : ['NAME', 'VALUE'] 
```
 

这样就构造了一个字段为'NAME', 'VALUE'，数据为

   NAME    |  VALUE   
	:-:    | :-:   
    同意    |    agree
    拒绝    |    disagree
    全部    |    all

 

这样在做实验的时候就方便多啦~~~
