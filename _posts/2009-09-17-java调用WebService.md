---
layout: post
title: java调用WebService 
date: 2009-09-17 
tag: java
---

最近项目上的需要，试验一下WebService的相关功能，用XFire制作了一个WebService的project在TOMCAT上，然后用Eclipse装了个XFire的插件，自动可以通过wsdl的文件生成客户端代码。但是发现还是不怎么方便，生成出来的相关类n个，每个发布的方法都会生成2个相关的类，如果发布一个方法是返回一个String[ ]类型的，那么生成一个叫ArrayOfString的类。感觉上反正是自动的也挺不错，但是还是希望能能不能有一些简单的方法能去调用。想自己写一个方便的方法，第一个参数是wsdl地址，第二个参数是调用的方法名，第三个参数是方法所需的参数。查了些资料，发现还是有这样方便的代码的。

import java.util.Vector;

import org.apache.axis.client.Service;
import org.apache.axis.client.Call;
import javax.xml.namespace.QName;


public class HelloWorldClient {
  
   
 /**  
 * @param args  
 */  
 public static void main(String[] args) {   
  long time=System.currentTimeMillis();
  try {
  String endpoint="wsdl的地址";
  Service service = new Service();
  Call call = (Call)service.createCall();
  
  call.setTargetEndpointAddress(new java.net.URL(endpoint));
  call.setOperationName(new
  QName("wsdl的地址","调用的方法名"));
//  call.addParameter("str", org.apache.axis.Constants.XSD_STRING,
//  ParameterMode.IN);
//  call.addParameter("pwd",org.apache.axis.Constants.XSD_STRING,
//  ParameterMode.IN);
  call.setReturnType(org.apache.axis.encoding.<font color=orange>XMLType.SOAP_VECTOR</font>);
  call.setUseSOAPAction(true);
  call.setSOAPActionURI("wsdl的地址/方法名");
  <font color=orange>Vector</font> day = (<font color=orange>Vector</font>)call.invoke(new Object[] {});
  System.out.println(day.size());
  } catch (Exception e) {
  System.err.println(e.toString());
  }
  System.out.println("Total time is: "+(System.currentTimeMillis()-time));

 }   
}

橙色部分是需要注意一下的，如果方法返回的是字符串那么在returnType里面设置为<font color=orange>XMLType.XSD_STRING</font>。此处我的webservive提供的方法返回的是个String[] 因此我将此书设为了<font color=orange>XMLType.SOAP_VECTOR</font>。同时下面返回出来的结果也需要相应的类型转换一下。字符串的话转成String就好了，而数组就转成Vector。自定义类型的数组我还没试过，不知道是啥后果。继续摸索中。。。

