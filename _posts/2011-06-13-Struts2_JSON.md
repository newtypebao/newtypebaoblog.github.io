---
layout: post
title: 关于在Struts2.1.8中使用JSON 
date: 2011-06-13 
tag: java
---

今天需要做个功能，简单来说就是界面上需要通过ajax调用一个action，然后将返回的值显示在界面上。

考虑到json方式比较方便（尤其是解析），所以决定通过使用json格式来ajax.

搜索了一下struts刚好有个json的plugin：“jsonplugin”网上找到对应工程下载

http://code.google.com/p/jsonplugin/downloads/list

目前有两个：

Struts 2 JSON Plugin 0.34 - Struts 2.1.x Compatible

Struts 2 JSON Plugin 0.32 - Struts 2.0.x Compatible

由于我的工程用的是struts2-core-2.1.8.1.jar，因此我下载了JSON Plugin 0.34

顺便介绍下使用jsonplugin的配置：

首先将jar导入工程，接着是struts的配置：

（必须继承json-default、json-default继承自struts-default）

``` xml
<package name="testajax" extends="json-default"
  namespace="/testajax">

<action name="example"
   class="com.newtypebao.ExampleAction">
   <result name="success" type="json" />
  </action>

</package>
```

其中type="json"就是那个jar里定义的，大家有空可以看下里面的配置学习下。

之后只需在action中返回SUCCESS:

``` java
@Override
 protected String excute() throws Exception {
  return SUCCESS;
 }
```

然后再工程中调用这个action返回的将会是出现下载文件对话框，原因是JSON插件将HTTP响应（Response）的MIME类型设为“application/json”。里面的内容就是这个action序列化的结果。

例如你的action里有

``` java
private String test1;

private String test2;
```

(get set 略。。)

那么把返回文件下载下来，用记事本打开，内容如下： 
{"test1":"aaaaaa","test2":"bbbbbbbbbb"} 
还可以在javabean的属性get方法上面加上一些annotation
@JSON(name="newName")json中的名称
@JSON(serialize=false) 属性不被加入json
@JSON(format="yyyy-MM-dd") 格式化日期

 

 

这里要说一个问题

就是struts2.1.8貌似并不适合用上面介绍的JSON Plugin 0.34，如果用的话将会出现

java.lang.ClassNotFoundException: com.opensymphony.xwork2.util.TextUtils
需要导入Struts2.18导入struts2-json-plugin-2.1.8.1.jar 就ok了。

