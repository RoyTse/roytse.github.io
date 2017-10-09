---
layout: post
title: 关于OS-X安装pwntools的那些坑
date: 2017-07-23 
categories: software  
---


## 介绍


　　`pwntools`是一个CTF框架和漏洞利用开发库，用Python开发，由rapid设计，旨在让使用者简单快速的编写exploit。

　　pwntools对Ubuntu 12.04和14.04的支持最好，但是绝大多数的功能也支持Debian, Arch, FreeBSD, OSX, 等等。安装pwntools前需要安装环境。
***	
　　之前就安装过一次，后来电脑接给别人了，所以没安装成功就gg了。今天又来踩坑，使用pip brew等快捷安装简单无脑安装方式都gg了。所以，只有查询了很多资料，先后进了pwntools的官网和pcat的博客。两相结合最后勉强完成了安装，但是有些功能并不能实现。


## 安装过程


### 安装Binutils

　　使用`homebrew`可以快速的安装。

    $ brew install https://raw.githubusercontent.com/Gallopsled/pwntools-binutils/master/osx/binutils-$ARCH.rb

　　`$ARCH`需要替换成自己电脑的架构。osx可以在终端中输入/arch查看，然后直接替换掉即可。例如我的是i386，所以直接替换掉。如下：

    $ brew install https://raw.githubusercontent.com/Gallopsled/pwntools-binutils/master/osx/binutils-i386.rb
　　

### 安装pwntools


　　安装`pwntools`推荐不要使用pip等方式，总会出奇奇怪怪的问题，按照pcat大佬的方式，直接在github克隆然后运行安装脚本，在终端中输入以下命令。
	
	git clone https://github.com/Gallopsled/pwntools
	cd pwntools
	python setup.py install

　　以上执行后就能安装好，虽然中途应该会出很多错，而且有些功能安装失败，但是好歹安装上了。对吧？至少能安装上了。。。解决方案的话，暂无。
　　
### 测试pwntools

　　以上执行后就能安装好，虽然中途应该会出很多错，而且有些功能安装失败，但是好歹安装上了。对吧？至少能安装上了。。。解决方案的话，暂无。执行以下命令来检测是否成功：

	import pwn  
	>>> pwn.asm("xor eax,eax")  
	'1\xc0'  

　　如果执行结果和上面相同，则说明安装成功，pwn模块现在可以使用了。仅以此记录一下，挣扎了一个下午时间来安装的经历。


转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [关于OS-X安装pwntools的那些坑](http://RoyTse.github.io/2017/07/关于OS_X安装pwntools的那些坑/)  
