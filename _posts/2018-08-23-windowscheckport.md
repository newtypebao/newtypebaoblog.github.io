---
layout: post
title: Windows下bat脚本判断(tomcat)端口是否可用
date: 2018-08-23
tag: Windows,bat
---

考虑到目前项目组所使用的windows server服务器上部署的tomcat总是不定期的自动关闭，经过讨论初步准备的解决方案是希望有个脚本能定时扫描系统中tomcat是否开启，如果未开启，则自动启动tomcat.

脚本如下：

注：以上脚本中需要将tomcat的目录设置到windows的环境变量中（CATALINA_HOME）

```
@echo off
echo.Check that the tomcat port is open
set num=8080
for /f "tokens=3 delims=: " %%a in ('netstat -an') do (
echo."%%a"
if "%%a"=="%num%" goto en
)
if not "%%a"=="%num%" goto en1
:en
echo.Checking For local port %NUM% is Opening...
exit
:en1
echo.Checking For local port %NUM% is Not Opening...
echo.Starting Tomcat Program...
call "%CATALINA_HOME%"\bin\startup.bat
echo.Starting Succes!
echo.OK!
exit
```

主要命令说明：

for /f "tokens=3 delims=: " %%a in ('netstat -an')  ： 

根据netstat -an命令的返回结果进行文本的切割，切割的关键字是冒号和空格。将文本中的每行内容根据关键字切割成多列，并且只使用第三列，即端口号信息。通过循环比对目前系统使用中的端口号，监测8080端口是否开启。未开启的话就执行tomcat的启动程序。


然后，将上面的脚本设置为windows的计划任务，根据项目组的实际情况，设定计划任务执行的时间间隔即可。