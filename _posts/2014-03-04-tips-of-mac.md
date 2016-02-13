---
layout: post
title: MAC 使用技巧
categories: Knowledge
tags:
  - osx
  - iterm
  - mac
date: 2014-03-04 00:00:00
---

# 1. iTerm 2

###1. Mouseless Copy

cmd-f -> tab

####2. Split Panes

cmd-d (vertically) / cmd-shift-d (horizontally)

navigate among split panes: cmd-opt-arrow

###3. System Wide Hotkey

Preferences > Keys. Enable “Show/Hide iTerm2 with a system-wide hotkey”

###4. Save Mark/Jump to Mark

cmd-shift-M / cmd-shift-J

###5. Autocomplete

cmd-;

###6. Paste History

cmd-shift-H

###7. Instant Replay

cmd-opt-B / Esc

这个功能比较给力，可以看到之前特定时刻的显示信息，比如特定时间点 top 的显示。

###8. Full Screen

cmd-Enter

###9. Exposé Tabs

cmd-opt-E

和 Chrome 的 Tab Overview (cmd-ctrl-T) 类似，标签页缩略图

# 2. 服务的自启动及立即启动

以mongodb为例，其他服务不知道是不是类似

To have launchd start mongodb at login:
{% highlight shell %} 
ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
{% endhighlight %}
Then to load mongodb now:
{% highlight shell %} 
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
{% endhighlight %}

# 3. 锁定屏幕
应用程序—>实用工具—> 钥匙串访问  
打开偏好设置->通用  
勾选“在菜单栏中显示钥匙串状态”，然后在你需要离开时就可以直接锁定屏幕了

# 4.调整声音
系统会发出“嘟嘟”的声音，按住shift键就没了  
同时按shift+option， 可以4分1格调节（同样适用于键盘背光和屏幕亮度）

# 5.把多个文件归类到一个文件夹中
选中你想要的文件 按cntrol+command+n

# 6.杂
快速重命名：选中回车  
当打开一个程序的多个窗口，如finder，如果要同时最小化， 按住Option，然后按其中一个窗口上的最小化按钮就ok了，要同时全部还原就按住option点dock图标就好了～



# 7. 快捷键
## 表示法：
⇧ = shift
⌃ = control
⌥ = option / alt
Home=fn + ◄
End=fn + ►
Page Up=fn + ▲
Page Down=fn + ▼

## Finder
- 查看路径 command + 点击窗口标题
- 隐藏/显示栏 command + b
- safari刷新页面command + r
- 复制 command + c 或 option + 拖拉
- 原位复制 command + d
- 创建替身 command + l 或 command+option + 拖拉
- 显示原身位置 command + r
- 粘贴 command + v
- 查看文件简介 command + i（动态窗口+option）
- 查看显示选项 command + j
- 查找 command + f
- 弹出 command + e
- 注销电脑shift+command + q（不提醒+option）
- 打开文件command + o
- 清倒废纸篓shift+command + delete（不提醒+option）
- 快速启动itunes  f8
- 关机 ctrl+command+option + eject
- 重启 command+ctrl + eject
- 休眠 command+option + eject
- 转到计算机 command+shift + c
- 转到个人主目录 command+shift + h
- 转到 iCloud command+shift + i
- 转到应用程序目录 command+shift + a
- 转到个人收藏 command+shift + f
- 添加到个人收藏 command + t
- 转到目录 command+shift + g
- 连接到服务器 command + k
- 切换不同的显示方式 command + 1234 




对齐项目 Command + 拖拉
在单独的窗口中打开目录 Command + 双击
打开项目 Command + Down Arrow
关闭目录 (转到上层目录) Command + Up Arrow
打开目录 (列表显示) Option + Right Arrow
关闭目录 (列表显示) Option + Left Arrow
打开选择的目录中的所有目录 (列表显示) Command + Option + Right Arrow
关闭选择的目录中的所有目录 (列表显示) Command + Option + Left Arrow
删除项目 Command + Delete


## 窗口
- 新窗口 command + n
- 关闭当前程序窗口 command + w（所有+option）
- 隐藏当前程序 command + h（隐藏其他+option）或 option + click (Desktop, Dock item, ...)
- 完全退出程序 command + q
- 最小化command + m（所有+option）
- 适合屏幕 option + click zoom button 
- 移动一个非当前窗口 command + 拖拉窗口



删除后一个字符（普通键盘的Delete)=fn + delete
截图保存整个屏幕到桌面=shift + command + 3
保存整个屏幕到剪贴板=control + shift + command + 3
截取指定屏幕区域到桌面=shift + command + 4
前一个输入法=command + 空格
下一个输入法=alt + command + 空格
强制重新启动 Command + Ctrl + 电源 key
启动到安全模式 Shift (在开机声音后)
打开登录窗口 Shift (当屏幕变成蓝色以后)
并闭打开的窗口 Shift (登录过程中)
从光盘启动 c
选择启动磁盘 Option
启动到 verbose 模式 Command + v
启动为单用户模式 Command + s
打开固件 Command + Shift + o + f

火线连接方式 t


动作 快捷键

选择下一图标 Arrow keys
选择图标名字的首字母 Letter key
选择下一个(字母顺序) Tab
添加图标到选择的项目 Shift + click
选择相邻的图标 (列表显示) Shift + click
选择不相邻的图标 (列表显示) Command + click
编辑图标名字 Return






dock快捷键

动作 快捷键

在 Finder 中显示项目 Command + click dock item
切换dock (全键盘操作) Ctrl + d
导航 (全键盘操作) Arrow Left, Arrow Right or Tab, Shift + Tab
打开项目 Space, Return, Enter
隐藏/显示Dock Command + Option + d

用户进程快捷键

动作 动作

查看活动的用户程序 Command + Tab
往回查看活动的用户程序 Command + Shift + Tab

对话框快捷键

动作 快捷键

选择下一区域 Tab
选择默认按钮 Return or Enter
关闭提示 Esc or Command + period
选择上层或者下层目录(保存/打开) Up Arrow, Down Arrow
向上滚动 (列表) Page up
向下滚动 (列表) Page down

全键盘操作

动作 快捷键

打开全键盘操作 Ctrl + F1
提示以及窗口任意控制 Ctrl + F7
高亮下一控制 Tab

高亮下一控制 (文本框) Ctrl + Tab
高亮下一窗口 Command + `
高亮在列表, 标签组或菜单中的项目 Arrow keys
移动滚动条 Arrow keys
高亮文本框相邻的控制 Ctrl + Arrow keys

选择高亮项目 Space bar
选择默认按钮 Return or Enter
点击取消按钮 Esc
不选择项目关闭菜单 Esc
反转高亮移动的顺序 Shift + "key"
菜单条 Ctrl + F2
Dock Ctrl + F3
浏览窗口 Ctrl + F4 (Ctrl + Shift + F4)
工具栏 Ctrl + F5
实用程序窗口 Ctrl + F6

查看多个文件占用的容量 ctrl + command + i
把多个文件归类到一个文件夹中：选中你想要的文件 按control+command+n
同时按shift+option， 可以4分1格调节（这个同样适用键盘背光和屏幕亮度）
