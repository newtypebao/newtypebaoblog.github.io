---
layout: post
title: EXT store 中文排序问题 
date: 2009-05-23 
tag: javascript
---

了解Extjs的朋友们可能会发现Ext.data.Store可以针对绑定的内容排序，但是这个排序的功能对中文的支持似乎不是很好，如果绑定的数据都是中文的话，排序后的数据并不是完全按照拼音的顺序排序的。

需要修改如下：
``` javascript
// 重载 Ext.data.Store.prototype.applySort 函数以修复 DataStore 对汉字排序异常的问题   

// var _applySort = Ext.data.Store.prototype.applySort;   

//如有需要，保存原 applySort 函数的引用     

 Ext.data.Store.prototype.applySort=function(){        //重载 applySort     

         if(this.sortInfo && !this.remoteSort){     

                var s = this.sortInfo, f = s.field;     

                var st = this.fields.get(f).sortType;   

                var fn = function(r1, r2){     

                        var v1 = st(r1.data[f]), v2 = st(r2.data[f]);     

                        // 添加:修复汉字排序异常的Bug  

                        if(typeof(v1) == "string"){ //若为字符串，  

            return v1.localeCompare(v2);//则用 localeCompare 比较汉字字符串, Firefox 与 IE 均支持   

  }  

                // 添加结束    

return v1 > v2 ? 1 : (v1 < v2 ? -1 : 0);   

            }; 

               this.data.sort(s.direction, fn);     

              if(this.snapshot && this.snapshot != this.data){     

                         this.snapshot.sort(s.direction, fn);   

   }

   }

                        };  
```