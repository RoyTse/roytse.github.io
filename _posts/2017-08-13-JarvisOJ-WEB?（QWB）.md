---
layout: post
title: JarvisOJ-WEB?（QWB）
date: 2017-08-12
categories: Jarvis-OJ-Web
---

## WEB?(QWB)

界面打开是这样子的。一个简单的`text`框与check按钮。失败提示wrong password。

![1](/images/posts/JarvisOJ/1.png)

好吧，应该是注入吧，提示是这么简单的题，是WEB吗？

简单那就admin'#之类的Fuzzing试试。最后发现无论什么都是返回Wrong Password。有点崩溃。上扫描器，看有没有隐藏文件，或者代码泄露。什么都没有。

## 转机

一头雾水，差点放弃，不过界面代码还没看的（毕竟有些简单ctf的flag直接扔在F12的注释里面）。好吧，那我F12看界面代码行了吧？查看之后，发现有点意思。

![1](/images/posts/JarvisOJ/2.png)

除了这个文件，全是来自其他网站引用的，所以这个存在本站的文件应该是突破点。那就美化代码后分析分析咯。分析流程如下：

![1](/images/posts/JarvisOJ/3.png)
![1](/images/posts/JarvisOJ/4.png)
![1](/images/posts/JarvisOJ/5.png)

通过搜索错误提示`wrong password`回溯到一个函数，发现有个`checkpass`的函数，如图一所示，于是追进去，发现第二张图的情况，继续追直到第三张。分析一下：

* 首先将传入的25位的e放入t中
* 然后将t的每一位分别与o里面的每一位相乘后加起来的值作为i
* 将得到的25个i与r每一位比较
* 如果最终比较相等，则返回!0，否则返回0。

所以思路清晰了，我们能控制的是e，而且要返回非0，所以就让e满足这些条件即可。

![1](/images/posts/JarvisOJ/6.png)

通过[云算子](http://www.yunsuanzi.com/matrixcomputations/solvelinearsystems.html)在线求解这个矩阵的逆矩阵，得到e的25位分别是什么。发现如下：

![1](/images/posts/JarvisOJ/7.png)

因为字符范围在`33-127`之间，很像是ascii码，所以把它作为ascii码转换一下，得到了flag。

## 总结

一道Web能出成类似于Crypto的我也是很醉。第一次遇到这么清奇的Web，以后做题还是不管三七二十一直接先上目录扫描器再说，同时看看界面代码，可能这样会比较快。


转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [JarvisOJ-WEB?（QWB）](http://RoyTse.github.io/2017/08/JarvisOJ-WEB?（QWB）/)  