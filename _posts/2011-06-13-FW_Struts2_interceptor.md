---
layout: post
title: 【转】Struts2内建拦截器的简要介绍 
date: 2011-06-13 
tag: java
---

alias：实现在不同请求中相似参数别名的转换。

autowiring：这是个自动装配的拦截器，主要用于当Struts2和Spring整合时，Struts2可以使用自动装配的方式来访问Spring容器中的Bean。

chain：构建一个Action链，使当前Action可以访问前一个Action的属性，一般和<result type="chain" .../>一起使用。

conversionError：这是一个负责处理类型转换错误的拦截器，它负责将类型转换错误从ActionContext中取出，并转换成Action的FieldError错误。

createSession：该拦截器负责创建一个HttpSession对象，主要用于那些需要有HttpSession对象才能正常工作的拦截器中。

debugging：当使用Struts2的开发模式时，这个拦截器会提供更多的调试信息。

execAndWait：后台执行Action，负责将等待画面发送给用户。

exception：这个拦截器负责处理异常，它将异常映射为结果。

fileUpload：这个拦截器主要用于文件上传，它负责解析表单中文件域的内容。

i18n：这是支持国际化的拦截器，它负责把所选的语言、区域放入用户Session中。

logger：这是一个负责日志记录的拦截器，主要是输出Action的名字。

model-driven：这是一个用于模型驱动的拦截器，当某个Action类实现了ModelDriven接口时，它负责把getModel()方法的结果堆入ValueStack中。

scoped-model-driven：如果一个Action实现了一个ScopedModelDriven接口，该拦截器负责从指定生存范围中找出指定的Modol，并将通过setModel方法将该Model传给Action实例。
params：这是最基本的一个拦截器，它负责解析HTTP请求中的参数，并将参数值设置成Action对应的属性值。

prepare：如果action实现了Preparable接口，将会调用该拦截器的prepare()方法。

static-params：这个拦截器负责将xml中<action>标签下<param>标签中的参数传入action。

scope：这是范围转换拦截器，它可以将Action状态信息保存到HttpSession范围，或者保存到ServletContext范围内。

servlet-config：如果某个Action需要直接访问Servlet API，就是通过这个拦截器实现的。
注意：尽量避免在Action中直接访问Servlet API，这样会导致Action与Servlet的高耦合。

roles：这是一个JAAS（Java Authentication and Authorization Service，Java授权和认证服务）拦截器，只有当浏览者取得合适的授权后，才可以调用被该拦截器拦截的Action。

timer：这个拦截器负责输出Action的执行时间，这个拦截器在分析该Action的性能瓶颈时比较有用。

token：这个拦截器主要用于阻止重复提交，它检查传到Action中的token，从而防止多次提交。

token-session：这个拦截器的作用与前一个基本类似，只是它把token保存在HttpSession中。

validation：通过执行在xxxAction-validation.xml中定义的校验器，从而完成数据校验。

workflow：这个拦截器负责调用Action类中的validate方法，如果校验失败，则返回input的逻辑视图。
大部分时候，开发者无需手动控制这些拦截器，因为struts-default.xml文件中已经配置了这些拦截器，只要我们定义的包继承了系统的struts-default包，就可以直接使用这些拦截器。

