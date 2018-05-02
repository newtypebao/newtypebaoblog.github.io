---
layout: post
title: 用sql查出某个时间段的字段之和 
date: 2011-06-17 
tag: mysql
---

现有数据库结构如下：

开始时间    |     结束时间   |   价格
- | :-: | :-: 
2010-1-1   |     2010-1-10  |    1
2010-1-11  |     2010-1-20   |    2 
2010-1-21  |     2010-1-30   |    3


先要求用sql查出某个时间段的价格之和
比如输入参数 开始时间：2010-1-5 结束时间：2010-1-25
结果应该是 5号到10号的价钱之和（6） + 11-20号的价钱之和（20） + 21到25号的价钱之和（15）
而不是简单的三条记录的sum

 

**方法如下：**

``` sql
create table test
(
s_dt date,
e_dt date,
price number
);

insert into test values(to_date('20100101','yyyymmdd'),to_date('20100110','yyyymmdd'),1);
insert into test values(to_date('20100111','yyyymmdd'),to_date('20100120','yyyymmdd'),2);
insert into test values(to_date('20100121','yyyymmdd'),to_date('20100130','yyyymmdd'),3);


select sum(case when to_date('20100105','yyyymmdd') between s_dt and e_dt then
  price*(e_dt - to_date('20100105','yyyymmdd')+1)
  when to_date('20100125','yyyymmdd') between s_dt and e_dt then
  price*( to_date('20100125','yyyymmdd') - s_dt +1)
  when to_date('20100105','yyyymmdd') < s_dt and to_date('20100125','yyyymmdd')>e_dt then
  price*(e_dt-s_dt+1)
  end)
  from test;
```
-----------------------------------------------
查询结果：41
