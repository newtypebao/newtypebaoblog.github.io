---
layout: post
title: CentOS7 中安装rstatd服务
date: 2018-04-28 
tag: network
---

近日，由于项目组要对系统做压力测试，用的工具是loadrunner，由于在压测如果需要监控服务器信息，需要在服务器上装一些软件（rstatd）。通过网上搜索了一些资料，几乎都是描述CentOS6的，个别描述 CentOS7的文章大部分也是差不多的方法,大致步骤是在[sourceforge](https://sourceforge.net/projects/rstatd/)的网站上下载，然后：
>tar zxvf rpc.rstatd-4.0.1.tar.gz
>cd rpc.rstatd-4.0.1<br/>
>./configure   //配置<br/>
>make          //编译<br/>
>make install  //安装<br/>

如果第一次执行rpc.rstatd报错，检查portmap服务：
>cd /etc/init.d<br/>
>./portmap start<br/>

还有的文章中说到还要安装xinetd、rup之类的服务。

事实上这种方法至少从我亲身实践的时候并不可行。总结了下主要是由于几个原因：

1. sourceforge中rstatd这个项目最后更新的时间是2013年5月，也就是说那个时候是针对CentOS7以前的版本设计的，我尝试过CentOS7中部署，但无法正常工作。

2. portmap这个服务在centos7中并不存在，取而代之的是叫rpcbind的服务。

综上，在CentOS7中如果需要部署rstatd的话，其实很简单，直接使用yum自动安装一个叫ruser-server的服务即可（我用阿里自带的yum源就已包含）。

>yum install rusers-server即可。当然也可以去找到相关的tar.gz人工培植、编译、安装。

>systemctl status rstatd <span style="white-space:pre;"></span> //查看rstatd的状态

>systemctl start rstatd    //启动rstatd服务

其中rpcbind服务在centos7中默认一般是开着的，在centos6中也是同样的可以用yum安装ruser-server,但是如果默认rpcbind没开,可能会发生rstatd起不来的情况，那么需要service rpcbind start把rpcbind服务先启动一下，之后rstatd就可以工作了。

可以通过rpcinfo -p的命令来验证服务是否正常启动，正常的话会显示有portmapper和rstatd的服务：

>rpcinfo -p

一般来说命令结果显示如下，有rstatd和portmapper这两个服务的话，就可以正常监控了：<br/>

>   program vers proto   port  service <br/>
>   100000    4   tcp    111  portmapper<br/>
>   100000    3   tcp    111  portmapper<br/>
>   100000    2   tcp    111  portmapper<br/>
>   100000    4   udp    111  portmapper<br/>
>   100000    3   udp    111  portmapper<br/>
>   100000    2   udp    111  portmapper<br/>
>   100001    3   udp    930  rstatd<br/>
>   100001    2   udp    930  rstatd<br/>
>   100001    1   udp    930  rstatd<br/>



