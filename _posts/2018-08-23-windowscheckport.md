---
layout: post
title: Windows下bat脚本判断(tomcat)端口是否可用
date: 2018-08-23
tag: Windows,bat
---
脚本如下：
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
echo.Checking For local%NUM%Port is Opening...
pause
exit
:en1
echo.Checking For local%NUM%Port is Not Opening...
echo.Starting Tomcat Program...
call "%CATALINA_HOME%"\bin\startup.bat
echo.Starting Succes!
echo.OK!
pause
exit
```

主要命令讲解：

for /f "tokens=3 delims=: " %%a in ('netstat -an')  ： 

根据netstat -an命令的返回结果进行文本的切割，切割的关键字是冒号和空格。将文本中的每行内容根据关键字切割成3列，并且只使用第三列，即端口号信息。通过循环比对目前系统使用中的端口号，监测8080端口是否开启。未开启的话就执行tomcat的启动程序。