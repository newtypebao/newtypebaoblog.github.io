---
layout: post
title: 【转】https下HttpServletResponse.sendRedirect跳转到http的解决方法
date: 2018-07-19
tag: Servlet
---
### 问题描述
最近全站上线SSL，架构如下：


>               |
>               |(https)
>               |
>              SLB
>               |
>      Load Banlancer (Nginx)                外网
>---------------|----------------------------------
>    /          |       \                    内网
>   /(http)     |(http)  \(http)
>Container1  Container2  Container3

近日项目中使用了阿里的SLB,在外网使用SSL，然后使用反向代理到具体的Web Container，内网依然使用HTTP进行传输，这样可以节省一些资源，效率也比较高。不过这样做的时候发现所有的 HttpServletResponse.sendRedirect 方法使用相对地址的时候都会跳转到80端口，而不是443，经过查找了一些网上的资源，综合发现下面的几种解决方案。目前项目中暂时使用第一个解决方案（绝对路径），供有需要的同学们参考：

### 解决方案
跳转的时候url使用绝对地址，而不是相对地址
关键代码如下：

``` 
String url = "/login";
// 将相对地址转换成https的绝对地址
HttpServletRequest request = (HttpServletRequest) req;
HttpServletResponse response = (HttpServletResponse)resp;
String scheme = request.getScheme();
String serverName = request.getServerName();
int port = request.getServerPort();
String contextPath = request.getContextPath();
String servletPath = request.getServletPath();
String queryString = request.getQueryString();

StringBuilder sbd = new StringBuilder();
// 强制使用https
sbd.append("https").append("://").append(serverName)
if(port != 80 && port != 443) {
    sbd.append(":").append(port);
}
if(contextPath != null) {
    sbd.append(contextPath);
}
if(servletPath != null) {
    sbd.append(servletPath);
}
sbd.append(url);
if(queryString != null) {
    sbd.append(queryString);
}

// 绝对地址
response.sendRedirect(sbd.toString());

```
可以的参考Spring Security的 org.springframework.security.web.util.RedirectUrlBuilder 代码。

### 创建一个强制转换的过滤器
将上面的代码封装成一个ResponseWrapper，覆盖到sendRedirect方法中，然后在过滤器中进行调用，核心代码如下：
RedirectResponseWrapper.java
```
public class RedirectResponseWrapper extends HttpServletResponseWrapper {

    private final HttpServletRequest request;

    public RedirectResponseWrapper(final HttpServletRequest inRequest,
            final HttpServletResponse response) {
        super(response);
        this.request = inRequest;
    }

    @Override
    public void sendRedirect(final String pLocation) throws IOException {
        if (StringUtils.isBlank(pLocation)) {
            super.sendRedirect(pLocation);
            return;
        }

        try {
            final URI uri = new URI(pLocation);
            if (uri.getScheme() != null) {
                super.sendRedirect(pLocation);
                return;
            }
        } catch (URISyntaxException ex) {
            super.sendRedirect(pLocation);
        }

        // !!! FIX Scheme !!!
        String scheme = request.getScheme();
        String serverName = request.getServerName();
        int port = request.getServerPort();
        String contextPath = request.getContextPath();
        String servletPath = request.getServletPath();
        String queryString = request.getQueryString();

        StringBuilder sbd = new StringBuilder();
        // 强制使用https
        sbd.append("https").append("://").append(serverName)
        if(port != 80 && port != 443) {
            sbd.append(":").append(port);
        }
        if(contextPath != null) {
            sbd.append(contextPath);
        }
        if(servletPath != null) {
            sbd.append(servletPath);
        }
        sbd.append(url);
        if(queryString != null) {
            sbd.append(queryString);
        }

        super.sendRedirect(sbd.toString());
    }
}
```

创建一个过滤器，并在web.xml中启用
```
public class AbsoluteSendRedirectFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        RedirectResponseWrapper redirectResponseWrapper = new RedirectResponseWrapper(request, response);
        filterChain.doFilter(request, redirectResponseWrapper);
    }
}
```

web.xml
```
<filter>
    <filter-name>AbsoluteSendRedirectFilter</filter-name>  
    <filter-class>com.rensanning.core.filter.AbsoluteSendRedirectFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>AbsoluteSendRedirectFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 将Spring MVC中viewResolver的redirectHttp10Compatible属性设置为false
如果使用Spring的话，可以将viewResolver的redirectHttp10Compatible属性设置为false，代码如下：
```
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">  
  <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />  
  <property name="prefix" value="/" />  
  <property name="suffix" value=".jsp" />  
  <property name="redirectHttp10Compatible" value="false" />  
</bean>  
```

### 在Nginx中设置X-Forwarded-Proto
上面的方法使用了一种强制的方式，但真正传输到后端容器中的依然是HTTP协议，Spring Security等使用 ServletRequest#isSecure() 方法判断是否是SSL环境，可以使用Nginx进行反向代理的时候，设置X-Forwarded-Proto，关键设置如下：
```
location / {
    proxy_next_upstream http_502 http_504 error timeout invalid_header;
    proxy_set_header Host  $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_pass http://tdt_server;
    proxy_redirect off;
}
```

将Host由$host修改为$http_host，区别是后者包含端口号，而前者不包含。
还需要后端的容器能够识别，在Tomcat中可以使用 RemoteIpValve 进行设置：
```
<Valve className="org.apache.catalina.valves.RemoteIpValve"
    internalProxies="192\.168\.0\.10|192\.168\.0\.11"
    remoteIpHeader="x-forwarded-for"
    proxiesHeader="x-forwarded-by"
    protocolHeader="x-forwarded-proto" />
```
Resin4.0中可以在resin.xml中通过 resin:SetRequestSecure 设置：
```
<web-app xmlns="http://caucho.com/ns/resin"
         xmlns:resin="urn:java:com.caucho.resin">
    <resin:SetRequestSecure>
        <resin:IfHeader name="X-Forwarded-Proto" value="https" />
    </resin:SetRequestSecure>
</web-app>
```
在Resin4.0中如果想针对特定的地址使用http，可以使用下面的设置：
```
<web-app xmlns="http://caucho.com/ns/resin"
         xmlns:resin="urn:java:com.caucho.resin">
    <resin:Redirect regexp="^/yyy/" target="http://myhost.com/yyy/">
        <resin:IfSecure value="true"/>
    </resin:Redirect>
</web-app>
```
### 在Nginx中使用rewrite
还有一种简单的方式，直接在Nginx中将所有80的请求自动设置成301跳转，设置如下：
```
server {
    listen 80;
    server_name example.com;

    location / {
        rewrite ^(.*) https://$server_name$1 permanent;
    }
}
server {
    listen 443;
    server_name example.com;

    ssl on;
    ssl_certificate   cert.pem;
    ssl_certificate_key  cert.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_next_upstream http_502 http_504 error timeout invalid_header;
        proxy_set_header Host  $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_pass http://tdt_server;
        proxy_redirect off;
    }
}
```
经过这样设置，遇到跳转的时候等于需要跳转两次，先是从https跳转到http，然后因为nginx设置的原因，又从http跳转到https，虽然解决方法粗暴了一些，但是能够忽略后端不同的Java Web Container的差异。
