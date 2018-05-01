---
layout: post
title: 前台后台都UTF-8，还是乱码 
date: 2012-06-05 
tag: work diary
---

今天做了个例子大致页面如下:<br/>
`<html>`<br/>
`<head>`<br/>
`<meta http-equiv="Content-Type" content="text/html; charset=utf-8">`<br/>
`<title>例子</title>`<br/>
`</head>`<br/>
`<body>`<br/>
`<form name="ledaddform" id="ledaddform"  action="http://localhost:8088/test/` `report.jsp?type=中文"  method="post">`<br/>
`<div style="text-align:center;"><button type="submit">提交</button></div>`<br/>
`</form>`<br/>
`</body>`<br/>
`</html>`<br/>

也就是说我在这个页面里有个submit按钮，直接传了一个带中文字的链接参数到了服务端。

之后提交到后台后的页面大致如下：<br/>
`<%@ page language="java" contentType="text/html; charset=UTF-8"`
` pageEncoding="utf-8"%>`

`<%`

`String type=new String(request.getParameter("type"));   //获取前面传过来的type参数`

`%>`

结果这里的type是乱码。

看了许多网上的解决方案 做主要的方案是说，这里需要转码。

例如这样：

`String type= new String(requeset.getParameter("type").getBytes("ISO-8859-1"),"GBK");`

也做了用于设置编码的filter：

`request.setCharacterEncoding("utf-8");`

但是测试下来还是不能正常获取。获取到的不是？？？就是乱码。

经过无数测试和查询资料，最后试了这个方法：

在前台那个中文字符的地方套了层转码的方法,这样：`<%=URLEncoder.encode(new String("中文"))%>`

结果在后台requeset.getParameter获取参数的时候，居然不需要转码，直接获取到了中文。

具体原理还不是很了解。

