---
layout: post
title: Apache Tomcat Versions 
date: 2018-07-13
tag: Tomcat
---

Apache Tomcat® is an open source software implementation of the Java Servlet and JavaServer Pages technologies. Different versions of Apache Tomcat are available for different versions of the Servlet and JSP specifications. The mapping between the specifications and the respective Apache Tomcat versions is:

|Servlet Spec  |  JSP Spec | EL Spec | WebSocket Spec | JASPIC Spec | Apache Tomcat Version | Latest Released Version | Supported Java Versions |
| ------------- |:-------------:| -----:|
|4.0 |2.3 | 3.0 | 1.1 | 1.1 | 9.0.x | 9.0.10 | 8 and later |
|3.1 | 2.3 | 3.0 | 1.1 | 1.1 | 8.5.x | 8.5.32 | 7 and later |
|3.1 | 2.3 | 3.0 | 1.1 | N/A | 8.0.x (superseded) | 8.0.53 (superseded) | 7 and later |
|3.0 | 2.2 | 2.2 | 1.1 | N/A | 7.0.x | 7.0.90 | 6 and later (7 and later for WebSocket) |
|2.5 | 2.1 | 2.1 | N/A | N/A | 6.0.x (archived) | 6.0.53 (archived) | 5 and later |
|2.4 | 2.0 | N/A | N/A | N/A | 5.5.x (archived) | 5.5.36 (archived) | 1.4 and later |
|2.3 | 1.2 | N/A | N/A | N/A | 4.1.x (archived) | 4.1.40 (archived) | 1.3 and later |
|2.2 | 1.1 | N/A | N/A | N/A | 3.3.x (archived) | 3.3.2 (archived) | 1.1 and later |

Each version of Tomcat is supported for any stable Java release that meets the requirements of the final column in the table above.

reference:http://tomcat.apache.org/whichversion.html