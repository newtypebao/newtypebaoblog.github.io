---
layout: post
title: 关于通过jcifs无法登陆centos7中SMB服务的问题 
date: 2018-06-19
tag: smb
---

最近客户需要将自己部署在IDC机房的系统迁移到阿里云上，由于之前的系统是前几年开发的，当时使用 是Centos6系统，考虑到系统中主要使用到的是jdk\java\smb这些常用的服务，而这些服务在最新的CentOS中运行的还是比较稳定的，因此本次迁移到阿里云上采用的是Centos7。

在系统迁移实施过程中，大部分进行的还是比较顺利的，比如原来用的是tomcat6+jdk1.6(这两个版本到各自的官网上都已经找不到了。。-_-!),从原服务器上搬到centos7上运行都还算正常。

只有应用中出现的一个问题比较奇怪：
由于程序没有做过任何变动，部署到新环境中涉及到通过smb向远程文件服务器写文件的时候，总是报用户名和密码不对,像下面这个报错：
>Caused by: jcifs.smb.SmbAuthException: Logon failure: unknown user name or bad password.
        at jcifs.smb.SmbTransport.checkStatus(SmbTransport.java:515)
        at jcifs.smb.SmbTransport.send(SmbTransport.java:629)

这个比较奇怪，期初我怀疑这个是我建的smb服务用户名密码和应用设置的不匹配，但是通过再三比对，确定使用的smb用户名和密码是没问题的。但这个错误依旧是那么明确unknown user name or bad password。。。

通过搜索了网上资源，目前是在tomcat中调整了jvm的可选参数： *jcifs.smb.lmCompatibility=3*

重新测试后，表现正常。其原因，目前推测还是centos6和centos7中的smb服务的版本不一致导致，具体还没了解清楚。

之前baidu上搜索了很久才在一些英语资料上找到的解决方法，特此总结，以供有相似经历的人分享。
