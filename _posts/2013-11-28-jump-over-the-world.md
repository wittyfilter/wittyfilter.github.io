---
layout: post
title: 如何科学上网
categories: Technique
tags:
  - GFW
  - Internet
date: 2013-11-28 00:00:00
---
记录一下平时怎么科 学 上 网

### 1. goagent

这个不必多说，用的人太多了...... 项目主页在[https://code.google.com/p/goagent/](https://code.google.com/p/goagent/)

Wiki里面有安装和使用的介绍，非常详细。

### 2. ssh+proxy

首先，你要有个SSH帐号，可以到[www.cjb.net](http://www.cjb.net)申请一个。

有了帐号以后，linux下如果有ssh客户端的话，直接

	sudo ssh -D 127.0.0.1:7070 username@216.194.70.6

windows下可以使用MyEnTunnel，注意启用动态SOCKS，本地端口填7070(非SSH端口22)。

存在的问题是，如果太久没用这个ssh账号，就会被回收。另外连接有点不稳定。

接下来就是浏览器的扩展了。

<!-- more -->

#### Firefox

火狐带有autoproxy，在规则列表里面选择gfwList，默认代理选择"ssh -D"

#### Chrome

在应用商店里面下载Proxy SwitchySharp扩展，链接是[https://chrome.google.com/webstore/detail/proxy-switchysharp/dpplabbmogkhghncfbfdeeokoefdjegm](https://chrome.google.com/webstore/detail/proxy-switchysharp/dpplabbmogkhghncfbfdeeokoefdjegm)。  
若商店不可用，请在[https://code.google.com/p/switchysharp/downloads/list](https://code.google.com/p/switchysharp/downloads/list)下载crx文件安装。

打开以后新建情景模式，比如“SSH”，SOCKS代理地址127.0.0.1，端口7070，选择SOCKS v5，保存。

切换规则中启用切换规则，有俄网:mobilefree.ru以及在线规则googlecode.com，模式匹配均为正则表达式，情景模式选择刚刚的“SSH”。在线规则列表为：http://autoproxy-gfwlist.googlecode.com/svn/trunk/gfwlist.txt，自动更新时间15分钟，同样为“SSH”情景模式。勾选“AutoProxy兼容列表”。保存，更新列表。

当然也可以使用lovessh.com的在线恢复备份。在选项中选择导入/导出，在线恢复备份地址为：https://www.lovessh.com/sw.bak，然后回到切换规则->更新列表。

### 3. IPv6

IPv6已经有很多网站可以上了，比如谷歌的所有服务(G+, Analytics...)，Wiki百科等等。学校内网就是这样，但是此种方法能上的网站太少。

推荐一个IPv6的代理 [proxy](http://yegle.net/glype/) ，作者可是个牛人。

### 4. Android手机

fqrouter绝对是个神器，至少对于我的nexus设备来说。

[下载地址1](http://www.coolapk.com/apk/fq.router2)
[下载地址2](https://s3-ap-southeast-1.amazonaws.com/fqrouter/fqrouter-latest.apk)

另外HOLA加速器也不错，在Google Play可以找到。除了加速网络外，还可以访问一些国外Social Network。

### 5. 其他

免费版ssh用不爽的话，可以去买SSH代理(壕我们做朋友吧！)  
[MySSH](http://www.myssh.cc/buy.html)

另外上述方式想看u2b视频好像有时候不太给力，那么请看看这个网站，或许有帮助  
[U2B代理](http://www.u2bdaili.com/)  (目前貌似已经失效)

### 参考：

[沐熙工作室](http://www.muxi.me/web/210.html)  
[yqjun](https://yqjun.wordpress.com)  
[lovessh.com](https://www.lovessh.com/) (这里有免费ssh帐号每日可用，推荐一下)