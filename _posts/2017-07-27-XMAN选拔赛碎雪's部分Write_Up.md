---
layout: post
title: XMAN选拔赛碎雪's部分Write_Up
date: 2017-07-23 
categories: CTF_WP  
---


## 介绍

### variacover

	<meta charset="utf-8">
	<?php
	error_reporting(0);
	if (empty($_GET['b'])) {
	    show_source(__FILE__);
	    die();
	}else{
	    include('flag.php');
	$a = "www.XMAN.com";
	$b = $_GET['b'];
	@parse_str($b);
	if ($a[0] != 'QNKCDZO' && md5($a[0]) == md5('QNKCDZO')) {
	    echo $flag;
	}else{
	exit('你的答案不对0.0');
	}
	}
	
	
	?>
上来就给了一段页面源代码，简单粗暴。代码审计一下逻辑，发现b如果为空就输出源码，然后中断程序。否则包含flag.php。所以我们的思路很明确，输出flag.php即可。

`Parse_str`会把`b`里面的字符串解析为变量。要求是`a[0]`的md5为`QNKCDZO`，但是它自己不能为这个。百度一下这个关键词，发现一串数字的MD5和这个相同。b=a[0]=这个数字就能满足条件，满足条件输出了FLAG，没满足输出的是你的答案不对。百度一下，你就知道。

### urldecode

![urlcode1](/images/posts/xmanctf/1.png)

界面提示很明显，传入参数`me`。那么传入看看情况。

![urlcode1](/images/posts/xmanctf/2.png)

页面提示输入XMAN，好吧，那就试试。输入再看看。

![urlcode1](/images/posts/xmanctf/3.png)

所以输入XMAN并没有什么用，只能换个思路了。随手审查一下，发现了问题。

![urlcode1](/images/posts/xmanctf/4.png)

在注释中发现了`hint`，是urldecode???好吧，猜测一下，是不是页面会对参数me传入的值进行`decode`操作。如果`decode`之后是XMAN那就会得到`flag`。找个在线php编程界面`encode`再传入试试，得到`%58%4d%41%4e`。传入之后发现没用，有点奇怪，再`encode`一次试试。果不其然，得到了flag。

![urlcode1](/images/posts/xmanctf/5.png)

### unserialize

![urlcode1](/images/posts/xmanctf/6.png)

又是传参数，传入`code`，那么传入xman试试2333.

![urlcode1](/images/posts/xmanctf/7.png)

得到提示，`flag.php`。好吧，直接访问一下，

![urlcode1](/images/posts/xmanctf/8.png)

知道`flag`确实是在这个文件里，然后得到了hint2，访问`help.php`。

![urlcode1](/images/posts/xmanctf/9.png)

结合题目`unserialize`，猜测这里需要序列化之后作为参数传入，得到`flag`。
最终代码如下：

	<?php
	class FileClass{
	public function __toString(){
	return file_get_contents($this->filename);
	}
	}
	$z=new FileClass();
	$z->filename='flag.php';
	echo serialize($z);
	?>

创建一个文件类，写一个读取文件函数，最后返回文件内容。将这个对象序列化之后传入。就可以得到`flag`。

![urlcode1](/images/posts/xmanctf/10.png)

有点懵逼，读取了文件但是没有`flag`。想了想，看看f12审查下元素，果然是注释。得到`flag`。

### PHP

这道题进入之后什么都没有，完全空白的界面。好吧，可能前段跑路了吧，拿起我的御剑扫一扫。发现一个`index.php~`。

	<?php
	$a=0;
	$b=0;
	$c=0;
	if (isset($_GET['aaa']))
	{
	        $aaa = $_GET['aaa'];
			$aaa=="1"?die("Emmm..."):NULL;
	        switch ($aaa)
	        {
	        case 0:
	        case 1:
	                $a=1;
	                break;
	        }
	}
	$bbb=(array)json_decode(@$_GET['bbb']);
	if(is_array($bbb)){
	    is_numeric(@$bbb["ccc"])?die("Emmm..."):NULL;
	    if(@$bbb["ccc"]){
	        ($bbb["ccc"]>2017)?$b=1:NULL;
	    }
		if(is_array(@$bbb["ddd"])){
	        if(count($bbb["ddd"])!==2 OR !is_array($bbb["ddd"][0])) die("Emmm...");
	        $eee = array_search("XMAN", $bbb["ddd"]);
	        $eee===false?die("Emmm..."):NULL;
	        foreach($bbb["ddd"] as $key=>$val){
	            $val==="XMAN"?die("Emmm..."):NULL;
	        }
	        $c=1;
	}
	}
	if($a && $b && $c){
	    include "flag.php";
	    echo $flag;
	}
	?>

和第一道题差不多，php弱类型，传入参数`aaa`和`bbb`。一个个条件满足下来就行了。

`aaa`传入这样的参数就可以了，`'1'`。不是1但是值又是1。`bbb`传入的要求比较高，要构造一个数组并包括另一个数组。`{"ccc":"2018%202","ddd":[[],0]}`数组中存在`ccc`但是不是数字，但是又要作为数字比2017大。所以`2018 `即可。然后还有一个数组名为`ddd`，但是数组里的东西只有2个，且第一个不为数组，得是对象。`ddd`的`key值`要是XMAN，因为空对象与任何都匹配，然后把`0`作为值也不会等于XMAN，所以完美绕过。传入这些参数最后得到`flag.php`。

### download

这道题怎么说呢，是一个项目平台，弱口令试试`admin\admin`结果就直接进去了。我做的时候，能打开项目，能通过`mkdir`新建文件夹，然后就能编辑文件，然后写了php函数直接读取了`flag`得到结果。不过后来加了权限，没办法复现了。

### Welcome2IRC

![urlcode1](/images/posts/xmanctf/11.png)

提示就是这个，查了一下`IRC`，发现是一个网络聊天协议，还给了一个端口，所以应该是要连上去看看。所以下了一个IRC软件连上去看看。然后就得到了`Flag`。

![urlcode1](/images/posts/xmanctf/12.png)

## Crypto

### Masonic

![urlcode1](/images/posts/xmanctf/13.png)

共济会密码特点很明显，所以百度一下。百度一下猪圈密码然后对照着就行了。

转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [XMAN选拔赛碎雪's部分Write_Up](http://RoyTse.github.io/2017/07/XMAN选拔赛碎雪's部分Write_Up/)  






