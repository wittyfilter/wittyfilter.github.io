---
title: Python爬虫制作微博词云
layout: post
date: '2018-09-18 09:37:25'
tags:
- python
- word cloud
categories:
- Coding
---

受到[Liuzhujun同学的博客](https://foofish.net/python-word-cloud.html)启发，想探究一下自己的微博究竟写了些什么东西？基于原博客的代码，有些地方稍作改动。以滤波器本人微博为例，结果是这样的：
![]({{ site.baseurl }}/public/images/wordcloud.jpg)

1. `featurecode`这个域似乎没有用了。有用的域只剩下`uid`和`container_id`。

2. 修改了raw html预处理的正则表达。使之成为：

	```python
	pattern1 = re.compile(r'<.*?>|&gt;|&quot;|转发微博|网页链接|Repost|(分享|查看|刚刚).*(图片|专辑|单曲|歌曲|照片|视频)')
	pattern2 = re.compile(r'/@[^\s]+/|/+@[^\n]+')
	pattern3 = re.compile(r'[(（].*@[^\s]+[）)]|[？?]|、|[！!]')
	text = re.sub(pattern3, '', re.sub(pattern2, '', re.sub(pattern1, '', raw_html)))
	```

	作为重度分享患者，pattern1和3去掉各种：来自@网易云音乐，来自@虾米音乐，刚刚在IG上上传了xxx图片/视频，分享了xxx的歌曲xxx，等等垃圾信息。而pattern2去掉了过长的转发时转发内容的垃圾信息，例如：@aa:123//@bb:234//@cc:345
3. 修改Jieba的Stop Words。这里整理了来自[Baiduguide](http://www.baiduguide.com/baidu-stopwords/)和[xpo6](http://xpo6.com/wp-content/uploads/2015/01/stop-word-list.txt)的中英文Stop Words，合成一个文件，可以在[这里](https://gist.github.com/wittyfilter/27e0b6bbd496ac43ed1a6cb18dc2dd17)下载。

完整代码如下，其中Prerequisite为: 
* jieba
* matplotlib
* pyparsing
* requests
* wordcloud

<script src="https://gist.github.com/wittyfilter/d0cff90a513b14f7eeed5006aad4a11f.js"></script>