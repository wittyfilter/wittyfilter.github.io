---
layout: post
title: 浙大VPN在OS X下的配置方法
tags:
  - VPN
  - ZJU
categories:
  - Technique
date: 2015-12-10 22:26:22
---

#### 1 新建VPN
![]({{ site.baseurl }}/public/images/vpn1.jpg)

#### 2 相关设置

填上服务器名称和用户名,服务器可以是lns.zju.edu.cn，或者10.5.1.7
![]({{ site.baseurl }}/public/images/vpn2.jpg)

#### 3 鉴定设置

在Authentication Setting中填上你的密码
![]({{ site.baseurl }}/public/images/vpn3.jpg)

#### 4 高级选项

在”Advanced...“中的”Options“将”Send all traffic over VPN connection“打上勾
![]({{ site.baseurl }}/public/images/vpn4.jpg)

<!-- more -->

#### 5 破解IPSec共享密钥，下面介绍2种方法。

##### 方法1

需要在`/etc/ppp/options`文件里输入下面两行字


	plugin L2TP.ppp
	l2tpnoipsec


##### 方法2

前提是可以打开校内网，进入[信息化服务网站](http://zuits.zju.edu.cn)，在用户自助服务区点击“VPN接入”，下载[ZJU VPN.pkg]({{ site.baseurl }}/public/downloads/ZJU VPN.pkg)（解决IPSec共享密钥丢失软件包）即可。

运行"应用程序"里的L2TP，选择要设置的VPN，然后Apply

这里特别说明下:

1. 先去掉L2TP Ipsec的钩，然后试试能不能连上
2. 如果可以连上，再尝试钩上ZJU Router，看能不能连上，如果不能就不要钩了
3. 如果上述程序出错无法打开，请尝试将步骤1中新建的vpn删除，再重新做一遍
![]({{ site.baseurl }}/public/images/vpn5.jpg)




 


