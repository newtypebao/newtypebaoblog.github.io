---
layout: post
title: Struts+Spring+ibatis基础实现 
date: 2011-06-08 
tag: java
---

现在公司中做的新项目使用Struts+Spring+ibatis作为大致框架，我之前没有用过，趁着边学边做之际把一些流程记录一下，同时也整理一下流程，以便以后自己如果要构架软件结构的话可以用上。

一。构建struts框架

我使用的是myeclipse。新建一个web project,导入一下jar包：commons-logging-1.0.4.jar、freemarker-2.3.8.jar、ognl-2.6.11.jar、struts2-core-2.0.11.jar、xwork-2.0.4.jar

配置web.xml:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns="http://java.sun.com/xml/ns/javaee"
 xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
 xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
 id="WebApp_ID" version="2.5">
 

 <filter>
  <filter-name>struts2</filter-name>
  <filter-class>
   org.apache.struts2.dispatcher.FilterDispatcher
  </filter-class>
 </filter>

 <filter-mapping>
  <filter-name>struts2</filter-name>
  <url-pattern>*.action</url-pattern>
 </filter-mapping>

 <welcome-file-list>
  <welcome-file>index.html</welcome-file>
  <welcome-file>index.htm</welcome-file>
  <welcome-file>index.jsp</welcome-file>
  <welcome-file>default.html</welcome-file>
  <welcome-file>default.htm</welcome-file>
  <welcome-file>default.jsp</welcome-file>
 </welcome-file-list>
</web-app>
```
 

写第一个Action:HelloWorldAction.java :

``` java
package cn.hxex.struts.helloworld.action;

import java.util.Date;

import com.opensymphony.xwork2.ActionSupport;

@SuppressWarnings("serial")
public class HelloWorldAction extends ActionSupport {
 public static final String MESSAGE = "Struts 2 Hello World Tutorial!";

 public String execute() throws Exception {
  setMessage(MESSAGE);
  return SUCCESS;
 }

 private String message;

 public void setMessage(String message) {
  this.message = message;
 }

 public String getMessage() {
  return message;
 }

 public String getCurrentTime() {
  return new Date().toString();
 }

}
```

对应的页面：helloworld.jsp:
``` html
<%@ taglib prefix="s" uri="/struts-tags"%>
<html>
 <head>
  <title>Struts 2 Hello World Application!</title>
 </head>
 <body>
  <h2><s:property value="message" /></h2>
  <p>Current date and time is: <b><s:property value="currentTime" /></b>
 </body>
</html>
```
对应的struts.xml配置文件：

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
    "http://struts.apache.org/dtds/struts-2.0.dtd">

<struts>

 <constant name="struts.devMode" value="true" />

 <package name="helloworld" namespace="/helloworld"
  extends="struts-default">

  <action name="HelloWorld"
   class="cn.hxex.struts.helloworld.action.HelloWorldAction">
   <result>/pages/HelloWorld.jsp</result>
  </action>

  <!-- Add actions here -->
 </package>
 
 </struts>
```

这样，访问http://localhost:8088/strutsdemo/helloworld/HelloWorld.action

大致可以运行一个简单的action了

 

二.Spring配置

首先，在web.xml里配置里加入spring的初始化配置

在lib文件夹加入asm.jar、cglib-2.1.3.jar

``` xml
<listener>
  <listener-class>cn.hxex.struts.helloworld.listener.ContextLoaderListener</listener-class>
 </listener>

<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>classpath:applicationContext-beans.xml</param-value>
 </context-param>
```
 

对应的applicationContext-beans.xml：
``` xml
<beans>
 <import resource="applicationContext-property.xml"/>
 <import resource="applicationContext-database.xml"/>
 <import resource="applicationContext-dao.xml"/>
 <import resource="applicationContext-service.xml"/>
</beans>
```
 

applicationContext-property.xml:

定义了引入的property文件
``` xml
<beans>
  <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
  <property name="locations">
   <list>
    <value>classpath:jdbc.properties</value>
   </list>
  </property>
 </bean>
</beans>
```
jdbc.properties：

定义了数据库的连接方式
```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.maxActive=10
jdbc.maxIdle=1
jdbc.maxWait=10000
jdbc.initSize=1
jdbc.username=root
jdbc.password=111111
jdbc.readolnypassword=111111

jdbc.write.url=jdbc\:mysql\://localhost\:3306/test?user\=root&password\=111111&useUnicode\=true&characterEncoding\=utf8
jdbc.readonly.url=jdbc\:mysql\://localhost\:3306/test?user\=root&password\=111111&useUnicode\=true&characterEncoding\=utf8
```

applicationContext-database.xml:

使用spring定义了一下ibatis中需要的配置，ibatis的特点是读写分开，因此有writeDataSource和readDataSource。
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN"
    "http://www.springframework.org/dtd/spring-beans.dtd">

<beans>

 <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
  <property name="locations">
   <list>
    <value>classpath:jdbc.properties</value>
   </list>
  </property>
 </bean>
 
 <bean id="writeDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
  <property name="driverClassName"><value>${jdbc.driverClassName}</value></property>
  <property name="url"><value>${jdbc.write.url}</value></property>
  <property name="username"><value>${jdbc.username}</value></property>
  <property name="password"><value>${jdbc.password}</value></property>
  <property name="maxActive"><value>${jdbc.maxActive}</value></property>
  <property name="maxWait"><value>${jdbc.maxWait}</value></property>
  <property name="maxIdle"><value>${jdbc.maxIdle}</value></property>
  <property name="initialSize"><value>${jdbc.initSize}</value></property>
  <property name="removeAbandoned"><value>true</value></property>
 </bean>

 <bean id="readDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
  <property name="driverClassName"><value>${jdbc.driverClassName}</value></property>
  <property name="url"><value>${jdbc.readonly.url}</value></property>
  <property name="username"><value>${jdbc.username}</value></property>
  <property name="password"><value>${jdbc.password}</value></property>
  <property name="maxActive"><value>${jdbc.maxActive}</value></property>
  <property name="maxWait"><value>${jdbc.maxWait}</value></property>
  <property name="maxIdle"><value>${jdbc.maxIdle}</value></property>
  <property name="initialSize"><value>${jdbc.initSize}</value></property>
  <property name="removeAbandoned"><value>true</value></property>
 </bean>
 
 <!-- SqlMap setup for iBATIS Database Layer -->
    <bean id="sqlMapClient4Write" class="org.springframework.orm.ibatis.SqlMapClientFactoryBean">
        <property name="configLocation">
            <value>classpath:/cn/hxex/struts/helloworld/dao/impl/maps/sql-write-map-config.xml</value>
        </property>
        <property name="dataSource">
         <ref bean="writeDataSource"/>
        </property>
    </bean>
    
    <bean id="sqlMapClient4Read" class="org.springframework.orm.ibatis.SqlMapClientFactoryBean">
        <property name="configLocation">
            <value>classpath:/cn/hxex/struts/helloworld/dao/impl/maps/sql-read-map-config.xml</value>
        </property>
        <property name="dataSource">
         <ref bean="readDataSource"/>
        </property>
    </bean>
 
 <!-- Transaction manager for a single JDBC DataSource -->
    <bean id="writeTxManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource">
            <ref local="writeDataSource"/>
        </property>
    </bean>
 
 <bean id="readTxManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource">
            <ref local="readDataSource"/>
        </property>
    </bean>
 
 <bean id="writeTxInterceptor" class="org.springframework.transaction.interceptor.TransactionInterceptor">
  <property name="transactionManager">
   <ref bean="writeTxManager"/>
  </property>
  <property name="transactionAttributes">
   <props>
    <prop key="*">PROPAGATION_REQUIRED,-Exception</prop>
   </props>
  </property>
 </bean>
 
 <bean id="readTxInterceptor" class="org.springframework.transaction.interceptor.TransactionInterceptor">
  <property name="transactionManager">
   <ref bean="readTxManager"/>
  </property>
  <property name="transactionAttributes">
   <props>
    <prop key="*">PROPAGATION_REQUIRED,readOnly</prop>
   </props>
  </property>
 </bean>
 
 <bean id="txBeanNameProxyCreator" class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
  <property name="interceptorNames">
   <list>
    <value>writeTxInterceptor</value>
    <value>readTxInterceptor</value>
   </list>
  </property>
  <property name="beanNames">
   <list>
    <value>*Service</value>
   </list>
  </property>
 </bean>
</beans>
```
 

applicationContext-dao.xml:

DAO的定义
``` xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE beans PUBLIC
    "-//SPRING//DTD BEAN//EN"
    "http://www.springframework.org/dtd/spring-beans.dtd">

<beans default-autowire="no" default-lazy-init="false"
 default-dependency-check="none">

 <bean id="userReadDao" class="cn.hxex.struts.helloworld.dao.read.impl.UserReadDaoImpl">
  <property name="ibatisSqlMapClient">
   <ref bean="sqlMapClient4Read" />
  </property>
  <property name="myDataSource">
   <ref bean="readDataSource" />
  </property>
 </bean>

</beans>
```
 

applicationContext-service.xml:

业务逻辑service，里面需要调用数据库操作
``` xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE beans PUBLIC
    "-//SPRING//DTD BEAN//EN"
    "http://www.springframework.org/dtd/spring-beans.dtd">

<beans
  default-autowire="no"
  default-lazy-init="false"
  default-dependency-check="none"
>

  <bean id="userService" class="cn.hxex.struts.helloworld.service.UserService">
    <property name="userReadDao">
      <ref bean="userReadDao"/>
    </property>
  </bean>
  
 

  <!--
     To include additional bean definitions for Spring in the generated
     application context file, add a file to your XDoclet merge directory
     called spring-beans.xml that contains the <bean></bean> markup.
  -->

</beans>

sql-read-map-config.xml：

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMapConfig PUBLIC "-//iBATIS.com//DTD SQL Map Config 2.0//EN"
    "http://www.ibatis.com/dtd/sql-map-config-2.dtd">

<sqlMapConfig>

 <settings enhancementEnabled="true" maxTransactions="40"
  maxRequests="32" maxSessions="10" lazyLoadingEnabled="false" />

 <sqlMap resource="cn/hxex/struts/helloworld/dao/impl/maps/User.xml" />
</sqlMapConfig>

 

sql-write-map-config.xml：

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMapConfig PUBLIC "-//iBATIS.com//DTD SQL Map Config 2.0//EN"
    "http://www.ibatis.com/dtd/sql-map-config-2.dtd">

<sqlMapConfig>

 <settings enhancementEnabled="true" maxTransactions="40"
  maxRequests="32" maxSessions="10" lazyLoadingEnabled="false" />

  <sqlMap resource="cn/hxex/struts/helloworld/dao/impl/maps/User.xml" />
</sqlMapConfig>
```
 

 

使用ibatis访问数据库的基类：

cn.hxex.struts.helloworld.dao.read.impl.BaseIbatisReadDaoImpl:
``` java
package cn.hxex.struts.helloworld.dao.read.impl;

import javax.sql.DataSource;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.orm.ibatis.support.SqlMapClientDaoSupport;

import com.ibatis.sqlmap.client.SqlMapClient;

/**
 * Ibatis型读dao基类
 *
 */
public class BaseIbatisReadDaoImpl extends SqlMapClientDaoSupport {

 protected final Log log = LogFactory.getLog(getClass());

 /**
  * @spring.property ref="sqlMapClient4Read"
  * @param sqlMapClient
  *            SqlMapClient
  */
 public void setIbatisSqlMapClient(SqlMapClient sqlMapClient) {
  super.setSqlMapClient(sqlMapClient);
 }

 /**
  * @spring.property ref="readDataSource"
  * @param dataSource
  *            DataSource
  */
 public void setMyDataSource(DataSource dataSource) {
  super.setDataSource(dataSource);
 }

 /**
  * 
  * @param ob
  * @return
  */
 public int getInt(Object ob) {
  if (ob != null)
   return Integer.parseInt(ob.toString());
  else
   return 0;
 }
}
```
 

 

做自己需要的dao时只要扩展这个基类就可以了：
``` java
package cn.hxex.struts.helloworld.dao.read.impl;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import cn.hxex.struts.helloworld.dao.read.intf.IUserReadDao;
import cn.hxex.struts.helloworld.po.User;


/**
 * 
 */
public class UserReadDaoImpl extends BaseIbatisReadDaoImpl implements IUserReadDao
  {

 /**
  * 查询所有的用户
  * 
  * @return List
  */
 public List<User> getAllUser() {
  Map param = new HashMap();

  // 分页用
  // param.put("start", );
  // param.put("maxResult", );
  return (List<User>) super.getSqlMapClientTemplate().queryForList("getAllUsers", param);
 }

}
```

通过spring输入service:(applicationContext-service.xml中曾配置过)
``` java
package cn.hxex.struts.helloworld.service;

import java.util.List;

import cn.hxex.struts.helloworld.dao.read.intf.IUserReadDao;
import cn.hxex.struts.helloworld.po.User;

public class UserService {
 IUserReadDao userReadDao;
 public List<User> getAllUser(){
  return userReadDao.getAllUser();
 }
 public void setUserReadDao(IUserReadDao userReadDao) {
  this.userReadDao = userReadDao;
 }
}
```
在需要的Action中使用还需配置struts.xml:

加入拦截器：
``` xml
<package name="front-default" extends="struts-default">
  <interceptors>
   <interceptor-stack name="defaultWebStack">
    <interceptor-ref name="autowiring" />
    <interceptor-ref name="staticParams" />
    <interceptor-ref name="params" />
   </interceptor-stack>
  </interceptors>
 </package>
```
之后继承自这个package
``` xml
<package name="user" namespace="/user" extends="front-default">
```
或者直接配置在需要的action里
``` xml
<package name="user" namespace="/user" extends="front-default">

  <action name="userQuery_*"
   class="cn.hxex.struts.helloworld.action.UserQueryAction"
   method="{1}">
   <result name="success">/pages/queryuser.jsp</result>
   <result name="edit" type="redirect">/helloworld/HelloWorld.action</result>
   <interceptor-ref name="defaultWebStack"/>
  </action>
  <!-- Add actions here -->
 </package>
```
 

具体拦截器的作用可以参见Struts2内建拦截器的简要介绍

 

之后就可以在需要的Action中自动注入所需服务了.
``` java
public class UserQueryAction extends ActionSupport {

 UserService userService;
 public String execute() throws Exception {
  List<User> result = userService.getAllUser();
  this.setUsers(result);
  return SUCCESS;
 }
```