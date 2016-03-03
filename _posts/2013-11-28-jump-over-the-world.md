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

# 1. ss

Shadowsocks是首选啦，目前暂时可用。

# 2. goagent

这个不必多说，一度以来用的人太多了......[项目主页](https://github.com/goagent/goagent)，Wiki里面有安装和使用的介绍，非常详细。

**项目已被和谐，原来有下载的仍可以使用**

# 3. ssh+proxy

一个老办法了。首先，你要有个SSH帐号，例如去[MySSH](http://www.myssh.cc/buy.html)申请一个。有了帐号以后，linux下如果有ssh客户端的话，直接

{% highlight shell %}
sudo ssh -D 127.0.0.1:7070 username@216.194.70.6
{% endhighlight %}

windows下可以使用MyEnTunnel，注意启用动态SOCKS，本地端口填7070(非SSH端口22)。

接下来就是浏览器的扩展了。

<!-- more -->

## Firefox

火狐带有autoproxy，在规则列表里面选择gfwList，默认代理选择"ssh -D"

## Chrome

在应用商店里面下载[Proxy SwitchyOmega扩展](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif?utm_source=chrome-ntp-icon)。

或者[备用下载地址](https://github.com/FelisCatus/SwitchyOmega/releases)，下载crx文件安装。

打开以后新建情景模式，比如“SSH”，SOCKS代理地址127.0.0.1，端口7070，选择SOCKS v5，保存。

切换规则中启用切换规则，有俄网:mobilefree.ru以及在线规则googlecode.com，模式匹配均为正则表达式，情景模式选择刚刚的“SSH”。在线规则列表为：http://autoproxy-gfwlist.googlecode.com/svn/trunk/gfwlist.txt，自动更新时间15分钟，同样为“SSH”情景模式。勾选“AutoProxy兼容列表”。保存，更新列表。

# 4. IPv6

IPv6已经有很多网站可以上了，比如谷歌的所有服务(G+, Analytics...)，Wiki百科等等。学校内网就是这样，但是此种方法能上的网站太少。

# 5. 移动端

Android上吗fqrouter绝对是个神器，然而已经没有更新啦～ 现在去下载shadowsocks和anyconnect都是可以的～iOS上面也是同理啦。
