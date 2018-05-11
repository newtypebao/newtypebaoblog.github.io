---
layout: post
title: Extjs 获取grid正显示的内容（非store中的） 
date: 2011-03-28 
tag: javascript
---

现有一个grid对应的store中已有数据
列中有设置renderer
也就是说显示在grid的的数据并不一定是store的原始数据，有可能是经过处理显示的。
那么如何能得到这个grid获取到grid中所有（正在显示）的数据？

我想有两种办法：

##### 第一种方法： #####

首先获取到gridview(grid界面上显示的东西一般都是在这个东西里面)，grid.getView()。

然后调用view.getCell(row, col) 这个将得到一个HtmlElement对象，然后获取到这个对象的innerText属性就是grid上显示的那个值了。

完整方法如下：
``` javascript
grid.getView().getCell(row,col).innerText;
```

如果要遍历的话只要把store中的列数和行数遍历下,取出对应的cell的内容即可.

不过这样有个缺点就是innerText这个属性似乎是IE才有，FF没有着个属性，FF里对应的一个属性是textContent.而且严格来说这个textContent也不完全等价于IE中的innerText.

 

##### 第二种方法： #####

获取到那个grid对应store中的值，然后用renderer中定义的方法再render一次。

首先获取到gird在store中对应的数据：yourGrid.store.data.items[yourRowIndex].data.yourField

然后将这个数据用对应的renderer重新render一下，完整如下：
``` javascript
grid.colModel.lookup[yourColIndex].renderer([yourGrid.store.data.items[yourRowIndex].data.yourField) 
```
得到的结果就是render后的值。

如果要遍历的话只要把store中的列数和行数遍历下,然后调用相关在colmodel中定义的renderer即可.

