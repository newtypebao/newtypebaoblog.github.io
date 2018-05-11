---
layout: post
title: for in 和 each 
date: 2009-08-16 
tag: javascript
---

前阵子在使用extjs，在写javascript的时候，遇到了需要全选全不选的功能,那就是遍历一个tree中所有node的情况。

当时用了一个for in的循环，类似于这样：
``` javascript
for (n in root.childNodes)

{

if(n) {
    var ui = n.getUI();
    if(ui && ui.setChecked) {
     ui.setChecked(true);

     }

}

}
```
 

当时确实遍历出来了所有node，但是同时也把node下面的function也遍历出来了。root.childNodes虽然返回的是个集合，但是这个集合对象本身也是有自己的method的。也就是说用for in循环的话，会把一个js对象的内容遍历出来，而且还会把那个对象所有的funtion（Methods）也遍历出来。后来改用了each的方法就正常了，仅仅只把root下面的'nodes'给遍历了出来。

ps:firefox真是比ie强很多，开始我用for in那种方法的时候在firefox里虽然报错，但是还是能显示效果，

ie就直接挂了。。

