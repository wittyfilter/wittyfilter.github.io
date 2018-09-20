---
title: Learning Linux
layout: post
categories: Coding
tags:
- linux
date: '2014-09-18 08:00:00 +0800'
---

# 1. Linux Storage Stack Diagram

[Linux Storage Stack Diagram](https://www.thomas-krenn.com/en/wiki/Linux_Storage_Stack_Diagram)
![](https://www.thomas-krenn.com/de/wikiDE/images/b/ba/Linux-storage-stack-diagram_v4.0.png)

# 2. Vim Cheat Sheet

[http://imgur.com/gallery/YLInLlY](http://imgur.com/gallery/YLInLlY)
![](http://i.imgur.com/YLInLlY.png)

# 3. 更改GRUB的默认启动？
这一修改对于双系统的电脑来说还是蛮重要的。
修改`/etc/default/grub`中`GRUB_DEFAULT`这一项的值，如果grub中你想要默认启动的系统是第n项，那么就设置`GRUB_DEFAULT=n-1`，然后执行
```zsh 
$ sudo grub-update
```