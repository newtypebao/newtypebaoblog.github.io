---
layout: post
title: Extjs ComboBox 模糊过滤 
date: 2009-05-23 
tag: javascipt
---

了解Ext的朋友们可能知道，在Ext2.0中的Ext.form.ComboBox有个typeAhead的属性可以使combobox在用户输入的同时根据已绑定的数据中弹出一些提示，但是这些提示并不具备模糊过滤的功能，比如绑定的数据中有个叫newtypebao，当输入n的时候可以出现提示，但是输入b或者bao的时候是不会弹出提示的。

可以通过以下修改使其具有模糊过滤的功能：

``` javascript
	combo = new Ext.form.ComboBox({  

          id:'somecombo',  

          store: combostore,  

          displayField:'state',  

          typeAhead:true,

          width: 180,  

          mode: 'local',  

          //enableKeyEvents: true,

          forceSelection: true

          triggerAction: 'all',  

          emptyText:'Select a state...',  

          selectOnFocus:true,

          renderTo: document.body  

      });    

 

      combo.on('beforequery',function(e){ 

            var combo = e.combo; 

            if(!e.forceAll){ 

                 var value = e.query; 

                combo.store.filterBy(function(record,id){ 

                var text = record.get(combo.displayField); 

                        //用自己的过滤规则,如写正则式 

                return (text.indexOf(value)!=-1);

            });  

                combo.expand();  

                return false; 

        }  

    });  
```