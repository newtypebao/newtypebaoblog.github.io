---
layout: post
title: 关于EXT DateField 的问题 
date: 2009-02-02 
tag: javascript
---

在学习使用ext 2.0（曾经也换成2.2过）中遇到这样一个问题： 
在一个Ext.grid.GridPanel显示一些数据，使用的store类型为Ext.data.GroupingStore，数据来源使用的是
proxy : new Ext.data.HttpProxy({  url : get_url  })  返回的是一个JSON字符串，
GridPanel中有一列需要显示时间，ColumnModel定义为：
``` javascript
{
                header : "创建时间",
                dataIndex : 'CREATETIME',             
                type:'date',                              //定义类型为日期类型
                renderer: Ext.util.Format.dateRenderer('Y/m/d H:i:s'),        //定义日期的格式
                width : 215,
                align : 'right'
            }
```
store定义如下：
``` javascript
__store = new Ext.data.GroupingStore({               
                proxy : new Ext.data.HttpProxy({
                            url : url     //定义数据来源
                        }),
                reader : new Ext.data.JsonReader({
                    root : 'BILLS',
                    totalProperty : '__totalCount',
                    fields : [                    
                            {name:"CREATETIME",type:"date",dateFormat:"Y-n-j H:i:s"}]
                    }),
                // turn on remote sorting
                sortInfo:{field: 'BILLTYPE', direction: "ASC"}
            });
     __store.setDefaultSort('CREATETIME', 'desc');
```
现在默认在GridPanel里加载的所有数据，现在需求要做一个筛选的功能，因此又定义了一个metaform，在里面定义了一些field，其中有DateField，奇怪的问题出现了：
加载数据后，DateField的时间不正确，鼠标点一下DateField的输入框，再点一下其他输入框，DateField中的时间会改变，每点一下就会变一次。清理下浏览器缓存或者重启下服务器都不能解决此问题。

经过不断的调试排错，总结经验如下一点：
1. 将store的类型换成Ext.data.SimpleStore，使用固定的数组作为数据源，使用DateField正常，但是此方法不可行，因为不使用proxy，而使用固定数据我们是不可接受的~
2. 默认如果不加载数据源，（store里是空着的情况下），使用DateField正常，如果之前store里已经加载了数据那么DateField就会出现上述奇怪的问题。
3. store里field的类型要与GridPanel里ColumnModel定义的类型一致，如果一处定义为date一处定义为String，那grid上可能会显示为：#### ## ###

现在问题已经解决，方法如下：
不使用extjs中的date类型，在GroupingStore中直接使用字符串的方式解析：
``` javascript
reader : new Ext.data.JsonReader({
                    root : 'BILLS',
                    totalProperty : '__totalCount',
                    fields : [
                    "CREATETIME",                 //{name:"CREATETIME",type:"date",dateFormat:"Y-n-j H:i:s"},
                            ]                       
                    })

ColumnModel里也以字符串的方式绑定：
{
                header : "创建时间",
                dataIndex : 'CREATETIME',
                //type:'date',
                //renderer: Ext.util.Format.dateRenderer('Y/m/d H:i:s'),
                width : 215,
                align : 'right'
            }
```
问题解决
此时返回的日期格式大致如下：2009-02-01T16:00:00Z