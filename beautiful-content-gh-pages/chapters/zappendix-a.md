反剽窃的艺术
===

剽窃
---

### 聚合网站的危害

当我开始意识到我需要与聚合网站作战的起因是，我的博客在Google中的索引在不断地下降。如我在《[博客反爬虫 策略一——根据User Agent](https://www.phodal.com/blog/nginx-disable-crawlers-by-user-agent/)》 所看到的索引值一样在下降。

![Anti Bad Bots](./images/anti-bad-bots.jpg)

索引值下降通常不外乎几个简单的原因：

1. Google修改爬虫算法
2. Google认为你作弊了（一些不合法的SEO规则）
3. Google认为你的内容对用户无用 （如重复，太短）
4. 等等

而我们面对的降合网站应该属于第三种结果，他们复制我们的文章，然后Google认为我们作弊了！！！这是什么概念？

**别人抄了你的作业，然后老师说你抄袭！**
**别人抄了你的作业，然后老师说你抄袭！**
**别人抄了你的作业，然后老师说你抄袭！**

长此以远，你在Google中就看不到你的文章了。当时我也遇到了类似的情形，于是我开始反击。从禁止特爬虫到限制RSS的长度，也才有了下面的结果：

![Google index Results](./images/google-index-results.jpg)

但是第二个网站仍然是聚合网站，What's the fuck。对于我来说，这已经是很好的结局，在Google中的第一个结果是自己的文章。对于必应来说也是一个不错是的结果，然而下面仍然是一堆的聚合网站：

![Bing Index Results](./images/bing-index-results.jpg)

而这是聚合网站的索引，在百度、360搜索、搜狗等等的搜索引擎就没有那么好运。


### 与聚合网站斗争

你辛辛苦苦写地东西就这样无情地被盗取了，想想便觉得还是GitHub好，至少可以看到有多少人Fork你的代码。虽然是不同类似的网站，但是作为一个技术人员我们总是应该做出点什么。

首先，我们要理解为什么他们的排名会比我们高——主要是因为他们的Rank高，即他们的网站排名比我们高。毕竟，他们抄袭了那么多的网站，很容易就排到我们前面去了。

他们主要有下面两种行为：

 - 转载不添加链接
 - 原文链接包含rel=nofollow标签 （告诉Google不要前往此链接）

而他们转载文章的基本手法也就是： 

 - RSS转载
 - 爬虫

而对于RSS转载来说，一种很有效的策略就是限定字符的全文输出，并且在RSS的最后加一个原文链接。如下图所示：

![With Origin Links](./images/rss-limit.jpg)

尽管原文链接会被加上rel=nofollow，但是有趣的一点出现了。我们的文章是全文输出，而聚合网站的文章则会变成摘要~~，你懂的。搜索引擎就会知道哪里会有问题。与此同时一种有效的策略就是使用图片——放在自己网站上的图片，而这个图片多数时候也会被复制过去。那么胜利的天平将会向你倾斜。

与此同时，还可以在一些Rank更高的网站上粘贴自己的文章，这是我最近在尝试的。但是这招只对于Google、Bing来说是有效的，对于百度来说，你懂的钱多就行了——从不指望百度可以从SEO中获取一些好处。并且在这些网站上粘贴上原文链接，那么依据Page Rank就会有下面的结果：

![Page Rank for Copy Website](./images/page-rank-copy.jpg)

除此还有防不胜防的爬虫，不断地修改自己的Rule?这就会变成一场无止尽的斗争。

RSS限制
---

Robot复制/RSS复制/人为复制

没有人会看所有的内容~~ -> 限制RSS长度 -> 简介很重要

看了看所谓的垃圾站的一些文章，发现都是原文的，也没有一些特有的元素。有种用RSS订阅过来的感觉，那么显然在我们的文章加一个原文链接的作用可能不是很大。

- 如果对方的rank比你高，你的文章就很容易被Google定为抄袭
- 对方可以在link上加nofollow，Google从理论上是支持这个的，实现有可能会继续往下走。

于是策略上就有两部分:

- 减少原文字数
- 加原文链接

不良影响:

 -  影响RSS阅读用户。

但是从理论上来说，如果用户有兴趣的话，读原文可能是不错的。只能后面再看看，是否真的有影响了。

反爬虫
---

Nginx 
限制IP


于是再度拿来了[goaccess 分析 nginx log](http://www.phodal.com/blog/use-goaccess-analyse-nginx-log/)。


Visitors	| %	| Name	
--------------|-----|--------
58222	|50.71%	|Unknown	
21231	|18.49%	|Safari	
18422	|16.04%	|Chrome	
7049	|6.14%	|Crawlers	

至少有6%是Crawlers，没有办法确认50%的未知是什么情况，这些爬虫有:

- Googlebot
- bingbot
- Googlebot-Mobile
- Baiduspider
- YisouSpider

当然还有一些坏的爬虫:

- AhrefsBot
- Python-urllib/2.6
- MJ12bot/v1.4.5
-  Slurp

先让我们干掉这些Bug。。。

### 用User Agent禁用爬虫

于是在网上找到了一些nginx的配置

```nginx
# Forbid crawlers such as Scrapy
if ($http_user_agent ~* (Scrapy|Curl|HttpClient)) {
    return 403;
}
 
# Disable specify UA and empty UA access
if ($http_user_agent ~ "FeedDemon|JikeSpider|Indy Library|Alexa Toolbar|AskTbFXTV|AhrefsBot|CrawlDaddy|CoolpadWebkit|Java|Feedly|UniversalFeedParser|ApacheBench|Microsoft URL Control|Swiftbot|ZmEu|oBot|jaunty|Python-urllib|lightDeckReports Bot|YYSpider|DigExt|YisouSpider|HttpClient|MJ12bot|heritrix|EasouSpider|Ezooms|^$" ) {
    return 403;
}
 
# Forbid crawlers except GET|HEAD|POST method
if ($request_method !~ ^(GET|HEAD|POST)$) {
    return 403;
}
```

顺手也把curl也禁用掉了，

```bash
curl -I -s www.phodal.com
HTTP/1.1 403 Forbidden
Server: mokcy/0.17.9
Date: Thu, 09 Apr 2015 14:22:42 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 169
Connection: keep-alive
```

当User Agent是上面的爬虫时，返回403。如果是Google的Bot的话:

```bash
curl -I -s -A 'Googlebot' www.phodal.com
HTTP/1.1 200 OK
Server: mokcy/0.17.9
Content-Type: text/html; charset=utf-8
Connection: keep-alive
Vary: Accept-Encoding
Vary: Accept-Language, Cookie
Content-Language: en
X-UA-Compatible: IE=Edge,chrome=1
Date: Thu, 09 Apr 2015 14:39:11 GMT
X-Page-Speed: Powered By Phodal
Cache-Control: max-age=0, no-cache
```

符合预期

SEO
---

copy/paste -> PR比较高的网站 => 添加原文链接

原文链接 -> 你的网站

Nginx 技术手段

微信公众号-原创
---

粉丝经济

与

建立技术社群

难以逾越的沟
---

图片文字 -> 微博 / 公众号

微博 -> 默许 如知乎

技术难度
