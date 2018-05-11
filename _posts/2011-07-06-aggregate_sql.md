---
layout: post
title: 关于数据分类汇总的sql  
date: 2011-07-06 
tag: studying
---

 Name |	Type | Price
	- | :-:   | :-: 
  A   | type1 | 1
  A   | type1 | 2
  A   | type2 | 3
  B   | type1 | 4
  B   | type2 | 5
  B   | type2 | 6
  C   | type1 | 7
  C   | type1 | 7
  C   | type2 | 8

想要使用sql分类汇总成如下格式数据

也就是说数据就是每种Name的都有几种Type 并且每条数据都有个price
汇总的数据是每种Name 每种Type 的price求和

  Name | SumType1Price | SumType2Price (如果有type3的话那就type3price)
	- | :-:   | :-: 
    A  |  3 (1+2)   |  3
    B  |  4         |  11(5+6)   
    C  |  14(7+7)   |  8   

**解答：**
``` sql
select Name,sum(if(Type='type1',Price,0)),
sum(if(Type='type2',Price,0)),sum(if(Type='type3',Price,0))
 from tt group by Name
```
 

这里补充一下如果需要汇总每种type的count

实现方法也是使用sum,并不是使用count

count(if(Type='type2',*,0))     --错误
count(if(Type='type2',1,0))    --错误

**count的话也是用sum
sum(if(Type='type2',1,0))，也就是符合条件的行算1，不符合条件的行算0**
