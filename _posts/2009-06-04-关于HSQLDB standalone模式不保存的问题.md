---
layout: post
title: 关于HSQLDB standalone模式不保存的问题 
date: 2009-06-04 
tag: hsqldb
---

今天在某些需要下，使用了HSQLDB1.80 standalone模式，奇怪的是代码里使用的create table 、insert之类的语句再程序结束之后并未永久的保留在数据库里，第二次运行程序select的时候之前create的table和insert的数据都不存在。

比如：

``` java
public void testHsqlDb() throws Exception
 {
  Class.forName("org.hsqldb.jdbcDriver" );
  Connection c = DriverManager.getConnection("jdbc:hsqldb:file:testdb", "sa", "");
  Statement statement=c.createStatement(); 
  statement.execute("CREATE TABLE Test ( Id INTEGER PRIMARY KEY,Name VARCHAR(50))");
  statement.execute("insert into test values('003','ccc')");  
  ResultSet rs=statement.executeQuery("select name from test where id='003'");
  rs.next();
  String result=rs.getString("Name");
  statement.close();
  c.commit();
  System.out.println(result);   //此处可以查出结果，但是在程序第二次运行的时候数据就没有了
  System.out.println("testHsqlDb !!!");
  c.close();

 }
```

经过数次的调试发现都是如此，最后发现这样一段资料：

HSQLDB is optimized for read performance. The paramerter WRITE_DELAY tells HSQLDB to delay an update or insert statement by X msecs. This can lead to actual data-loss when the database is "turned off" without sending an appropriate shutdonw command (all data inserted after the last write to disk is lost). This often causes problems when the database is accessed within unit tests. In certain situations, it makes sense to use this delay, but for our purposes: **turn it off!!**

So...

Set the write_delay to 0 by adding this line to the startup script file:

SET WRITE_DELAY 0 MILLIS

 

于是在那个standalone模式时生成的xxx.script的文件最后找到了这行改成了

SET WRITE_DELAY 0 MILLIS

resolved~

