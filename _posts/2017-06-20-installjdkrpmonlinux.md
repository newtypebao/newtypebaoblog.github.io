---
layout: post
title: (转)Linux下安装JDK-（rpm版)  
date: 2017-06-20 
tag: java
---

1、下载JDK

> 路径：[http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)
> 
> 下载jdk：
> 
> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">#cd /tmp  </span>
> 2.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">#wget "http://download.oracle.com/otn-pub/java/jdk/7u25-b15/jdk-7u25-linux-x64.rpm?AuthParam=1372142112_983985b072250462338f52e48b37d8b9"  </span>
> 
> </div>

<span style="line-height: 26px; color: rgb(51, 51, 51); font-family: Arial; font-size: 14.4px;">2、安装</span>

> ①复制到/usr/java/路径下
> 
> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">#mkdir /usr/java/  </span>
> 2.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">#cp jdk-7u25-linux-x64.rpm /usr/java/  </span>
> 
> </div>
> 
> ②添加可执行权限，并安装
> 
> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;"># cd /usr/java/  </span>
> 2.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;"># chmod +x jdk-7u25-linux-x64.rpm   </span>
> 3.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">#rpm -ivh jdk-7u25-linux-x64.rpm  </span>
> 
> </div>
> 
> ③执行结果：

> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">[root@localhost java]# rpm -ivh jdk-7u25-linux-x64.rpm   </span>
> 2.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">Preparing...                ########################################### [100%]  </span>
> 3.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">   1:jdk                    ########################################### [100%]  </span>
> 4.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">Unpacking JAR files...  </span>
> 5.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">    rt.jar...  </span>
> 6.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">    jsse.jar...  </span>
> 7.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">    charsets.jar...  </span>
> 8.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">    tools.jar...  </span>
> 9.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">    localedata.jar...  </span>
> 
> </div>

<span style="line-height: 26px; color: rgb(51, 51, 51); font-family: Arial; font-size: 14.4px;">3、配置环境变量</span>

> ①进入编辑pro<wbr>file文件
> 
> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">#vim /etc/profile  </span>
> 
> </div>
> 
> ②在pro<wbr>file文件最后追加入如下内容：
>     export  JAVA_HOME=/usr/java/jdk1.7.0_25
>     export  CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
>     export  PATH=$PATH:$JAVA_HOME/bin
> 
> ③保存并退出，执行如下

> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">#source /etc/profile  </span>
> 
> </div>

4、查看java版本

> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">[root@localhost java]# java -version  </span>
> 2.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">java version "1.7.0_25"  </span>
> 3.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">Java(TM) SE Runtime Environment (build 1.7.0_25-b15)  </span>
> 4.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">Java HotSpot(TM) 64-Bit Server VM (build 23.25-b01, mixed mode)  </span>
> 
> </div>

5、如果原先存在老的jdk版本或者默认的jdk版本，上面的jdk信息可能不是最新版本，则需要<span style="line-height: 19px; font-family: Verdana, Arial, Helvetica, sans-serif; font-size: 13px; background-color: rgb(254, 254, 242);">更alternatives，选择一下jdk版本</span>

> <span style="line-height: 19px; font-family: Verdana, Arial, Helvetica, sans-serif; font-size: 13px; background-color: rgb(254, 254, 242);"></span>
> 
> <div>
> 
> <div style="line-height: 21px; padding-left: 45px;">
> 
> <div style="line-height: normal; padding: 3px 8px 10px 10px; font-size: 9px; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; color: silver; background-color: rgb(248, 248, 248); border-left: 3px solid rgb(108, 226, 108);">**[plain]** [view plain](http://blog.csdn.net/sonnet123/article/details/9169741# "view plain")[copy](http://blog.csdn.net/sonnet123/article/details/9169741# "copy")</div>
> 
> </div>
> 
> 1.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">安装 java alternative ---jdk  </span>
> 2.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;"># update-alternatives --install /usr/bin/java java /usr/java/jdk1.7.0_25/bin/java 60  </span>
> 3.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;">配置JDK：  </span>
> 4.  <span style="line-height: 21px; margin: 0px; padding: 0px; border: none; color: black; background-color: inherit;"># update-alternatives --config java  </span>
> 
> </div>
> 
> <span style="line-height: 19px; font-family: Verdana, Arial, Helvetica, sans-serif; font-size: 13px; background-color: rgb(254, 254, 242);">//按 Enter 来保存当前选择[+]，或键入选择号码：?</span>
