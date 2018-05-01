---
layout: post
title: Tomcat配置 
date: 2008-11-21 
tag: java
---

今天在家配tomcat，以前的配法都是网上说的最多的一种配法:
TOMCAT_HOME/conf下的server.xml文件，修改如下：
在<Host></Host>之间加入：
``` xml
</Context>
<Context path="/myjsp" debug="0" docBase="e:/myjsp" reloadable="true">
</Context>
</Host>
```
这里的path="/myjsp"就是我们就配置的虚拟目录了，以后在地址栏中输入http://localhost:8080/myjsp即可。而docBase="e:/myjsp" 则是机器本地路径，他们通过这个语句形成一个映射关系，其它照抄。 
这样的配法确实比较标准。但是其实还有一种更好的配法噢~
我们需要做的就是在$TOMCAT_HOME$confCatalinalocalhost下建立一个xml文件（Catalinalocalhost 如果没有的话，可以自己手动创建）文件名不限（但是要和里面path的名字要一样。。比如文件名叫 test.xml），里面的内容如下：
``` xml
<Context path="/myjsp" reloadable="true" docBase="E:\My Project\myjsp" workDir="E:\My Project\\myjsp\work" />
```
docBase指定项目文件所在目录

path指定web application发布的别名

reloadable指定是否实时同步(发布)项目文件

debug指定是否允许debug

workDir为临时文件/缓存位置


这样做的好处是要增加新的虚拟目录只要增加一个新的文件，例如：xxx.xml。比较便于管理。
