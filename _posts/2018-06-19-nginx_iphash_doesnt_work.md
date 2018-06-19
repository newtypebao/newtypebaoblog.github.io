---
layout: post
title: 关于nginx中iphash不生效的解决方案 
date: 2018-06-19
tag: nginx
---

最近客户需要将自己部署在阿里云上的系统从可用区A迁移到可用区E,所有服务器人工重新搭建（不愿意走镜像-_-!)，系统总体的结构是:SLB->WEB SERVER(nginx)->APP SERVER(TOMCAT),项目实施过程中配置nginx时遇到一个很奇怪的问题：

外网访问者每次从外网SLB访问系统时，总是会路由到某2个固定的后端ECS上（后端总共有十多台ECS)。
这个比较奇怪，因为理论上根据配置，nginx会通过iphash的规则，根据不同的外网ip转发到后端服务器。然后后台总共有10多台服务器，用了100个外网地址测试，还是固定的那么2个后端ECS，感觉明显是有问题的。然后，开始了排查之旅。。

首先先从阿里角度排查，怀疑是否有可能是SLB里需要什么特别的配置，比如没有把实际的客户端IP放进来？查阅了阿里的资料发现如下：

`四层负载均衡（TCP协议）服务可以直接在后端ECS上获取客户端的真实IP地址，无需进行额外的配置。`<br/>
`七层负载均衡（HTTP/HTTPS协议）服务需要对应用服务器进行配置，然后使用X-Forwarded-For的方式获取客户端的真实IP地址。`<br/>
`真实的客户端IP会被负载均衡放在HTTP头部的X-Forwareded-For字段，格式如下：`<br/>
`X-Forwarded-For: 用户真实IP, 代理服务器1-IP， 代理服务器2-IP，...`<br/>
`当使用此方式获取客户端真实IP时，获取的第一个地址就是客户端真实IP。`

由于原SLB是四层的（TCP协议），新环境的SLb是七层的（HTTPS协议），客户要用7层的，那么我尝试在nginx配置中告知Nginx真实客户端IP从哪个请求头获取，在server中增加配置：<br/>
`real_ip_header X-Forwarded-For;`

重新测试后，发现效果还是老样子，无改善。继续。。发现nginx access日志中大量的访问ip大都是在一个地址段，100.x.x.x,继续查阅阿里资料，了解到这些访问的来源都是由负载均衡系统发起的，地址段为100.64.0.0/10。因此，继续在server中增加配置：<br/>
`set_real_ip_from 100.64.0.0/10;`<br/>
`real_ip_recursive on; `<br/>

顺便说明一下这两个配置的作用：<br/>
set_real_ip_from192.168.0.0/16：告知Nginx哪些是反向代理IP，即排除后剩下的就是真实客户端IP，支持配置具体IP地址、CIDR地址； <br/>
real_ip_recursive on：是否递归解析，当real_ip_recursive配置为off时，Nginx会把real_ip_header指定的请求头中的最后一个IP作为真实客户端IP；当real_ip_recursive配置为on时，Nginx会递归解析real_ip_header指定的请求头，最后一个不匹配set_real_ip_from的IP作为真实客户端IP。 

重新测试后，表现正常。

之前baidu上搜索都无法直接定位到这个情况以及原因，特此总结，以供有相似经历的人分享。
