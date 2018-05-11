---
layout: post
title: 写程序的一点小细节总结(陆续补充ing) 
date: 2010-03-12 
tag: studying
---

1. 如果做个bean里面有List或者Hashtable这样类型的属性，那么这个属性的get的方法不能直接就输出那个属性，比如一个Hashtable,get的时候应该判断一下如果为null的话，输出一个new Hashtable(),不为null的话直接return那个属性即可。因为一般人往这个hashtable里面加东西并不一定都是直接setHashtable(ht)的，一般都是get之后得到那个hashtable的引用，然后ht.put()进去。
2. 配置文件都是用全小写的作为文件名。
3. 作为api，那么方法的参数不应该使用JSONObject或者JSONArray这种很模糊的类型作为参数。这样使用该api的人还需要了解你这个jsonobject里的结构式怎么样的，不合理。
4. hashtable.getKey(xxx).toString()。在不确定getKey出来是否有值的情况下又直接toString()很容易造成NullPointException。
5. 写function的时候，如果有很多参数，一般参数的先后顺序是根据参数的重要性排序的，比如一般key之类的应该排在name之前。
6. 同一段代码用到只要用到两次，就应该想办法把它做成一个函数调用两次，而不是复制粘贴。