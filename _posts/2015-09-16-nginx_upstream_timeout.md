---
layout: post
title: 关于ngnix中upstream timed out (10060）的解决方案 
date: 2015-09-16 
tag: java
---

最近项目中个别功能中涉及大数据量的查询无法下载的情况，架构是ngnix+websphere.<div>首先查询WAS错误日志，发现如下情况：</div><div><div>reason: RC: 10053 &nbsp;An established connection was aborted by the software in your host machine.</div><div>这个似乎是因为was里有什么限制。</div><div>然后经过查看ngnix的错误日志：upstream timed out (10060: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond) while reading response header from upstream<wbr></div><div>后来经过排查，发现是有由于以下几个默认没有配置具体设置导致的：</div><div><div>**&nbsp;proxy_connect_timeout xxx;**</div><div>**&nbsp;proxy_read_timeout xxx;**</div><div>**&nbsp;proxy_send_timeout xxx;**</div></div><div>查阅了相关文档，默认配置是60s,如果有大数据量的查询下载功能（后台查询+生成文件）超过默认超时时间的话，就会产生以上错误。以上这些配置可以直接定义在location里，可以根据实际业务中的情况适当调大即可。（比如整个查询生产过程要2分钟左右，可以考虑将这三个配置都调成180.）</div></div><div>
</div><div>
</div><div><div><font size="2"><span style="line-height: 22.75px;">ps:之前排查时，也曾通过一些异常的关键字百度过，搜出来的答案大多都不尽如人意。有的说要减少反向代理连接时间，可以解决这个问题。proxy_connect_timeout 调成1; 有的说把程序移到另外一台上面,然后NGINX代理那台.</span></font></div><div><font size="2"><span style="line-height: 22.75px;">这些方法现在看来都比较假。。特此总结，以供有相似经历的人分享。</span></font></div></div>