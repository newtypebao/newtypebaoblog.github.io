---
layout: post
title: python备份mysql
date: 2011-08-10 
tag: mysql
---

总体思想就是使用python调用cmd的 mysqldump 加上当前的时间作为备份文件名 实现备份

日志方面还有待改善 最好的效果是如果报错能将cmd的错误信息重定向到报错文件中

以下是具体脚本：
``` python
#coding:utf-8

import os
import sys
import re
from time import strftime, localtime
from datetime import timedelta, date
import calendar
import logging
import pdb

###设置-----备份的目的目录
backupDir=r'F:\dbbak2\test2'

### 设置日志文件存放路径
backupLogFile=r'F:\dbbak3\test3\backupinfo.log'

### 设置MYSQL bin目录
mysqlDir=r'D:\Program Files\MySQL\MySQL Server 5.0\bin'


def datetimestr():
    '''
    get datetime string
    date format="YYYYMMDDHHMMSS"
    '''
    return year+mon+day+hour+min+sec

logger=logging.getLogger('tulipbackup')
logger.setLevel(logging.DEBUG)
if not(os.path.exists(os.path.dirname(backupLogFile))):
     os.makedirs((os.path.dirname(backupLogFile))) 
fh = logging.FileHandler (backupLogFile)
fh.setLevel(logging.DEBUG)
formatter = logging.Formatter("%(asctime)s - %(name)s - %(levelname)s - %(message)s")  
fh.setFormatter(formatter)  
logger.addHandler(fh)

try:
 mysqlBackupCmd='"'+mysqlDir+r'\mysqldump.exe"'

 year = strftime("%Y",localtime())
 mon  = strftime("%m",localtime())
 day  = strftime("%d",localtime())
 hour = strftime("%H",localtime())
 min  = strftime("%M",localtime())
 sec  = strftime("%S",localtime())
 if not(os.path.exists(backupDir)):
  os.makedirs(backupDir)
 if os.system(mysqlBackupCmd+r'  -u root -p111111 tulipflow3> '+backupDir+r'\\'+datetimestr()+r'.sql')==0:
  logger.info('backup finish!')
 else:
  logger.info('backup failed!')
except Exception,detail:
 logger.info('backup failed!')
        logger.error(detail)
```