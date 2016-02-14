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

### 1. Mouseless Copy

cmd-f -> tab

### 2. Split Panes

cmd-d (vertically) / cmd-shift-d (horizontally)

navigate among split panes: cmd-opt-arrow

### 3. System Wide Hotkey

Preferences > Keys. Enable “Show/Hide iTerm2 with a system-wide hotkey”

### 4. Save Mark/Jump to Mark

cmd-shift-M / cmd-shift-J

### 5. Autocomplete

cmd-;

### 6. Paste History

cmd-shift-H

### 7. Instant Replay

cmd-opt-B / Esc

这个功能比较给力，可以看到之前特定时刻的显示信息，比如特定时间点 top 的显示。

### 8. Full Screen

cmd-Enter

### 9. Exposé Tabs

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

# 5. 快捷键
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
- 查看文件简介 command + i（动态窗口+option，多个+ctrl）
- 查看显示选项 command + j
- 查找 command + f
- 弹出 command + e
- 打开文件command + o
- 清倒废纸篓shift+command + delete（不提醒+option）
- 快速启动itunes  f8
- 转到计算机 command+shift + c
- 转到个人主目录 command+shift + h
- 转到 iCloud command+shift + i
- 转到应用程序目录 command+shift + a
- 转到个人收藏 command+shift + f
- 添加到个人收藏 command + t
- 转到目录 command+shift + g
- 连接到服务器 command + k
- 切换不同的显示方式 command + 1234 
- 把多个文件归类到一个文件夹中 ctrl+command + n

## 窗口

- 新窗口 command + n
- 关闭当前程序窗口 command + w（所有+option）
- 隐藏当前程序 command + h（隐藏其他+option）或 option + click (Desktop, Dock item, ...)
- 完全退出程序 command + q
- 最小化command + m（所有+option）
- 适合屏幕 option + click zoom button 
- 移动一个非当前窗口 command + 拖拉窗口

## 图标

- 选择图标名字的首字母 Letter key
- 选择下一个(字母顺序) Tab
- 添加图标到选择的项目 shift + click
- 选择相邻的图标 (列表显示) shift + click
- 选择不相邻的图标 (列表显示) command + click
- 编辑图标名字 Return


## 系统

- 删除后一个字符 fn + delete
- 全屏截图到桌面 shift+command + 3（到剪贴板+control）
- 区域截图到桌面 shift+command + 4（到剪贴板+control）
- 前一个输入法 command + 空格（反向+alt）
- 关机 ctrl+command+option + eject
- 重启 command+ctrl + eject
- 休眠 command+option + eject
- 注销电脑shift+command + q（不提醒+option）
- 在Finder中显示dock项目 command + click
- 隐藏/显示Dock command+option + d
- 查看活动的用户程序 command + Tab（反向+shift）

启动中

- 启动到安全模式 shift（在开机声音后）
- 关闭打开的窗口 shift (登录过程中)
- 从光盘启动 c
- 选择启动磁盘 option
- 启动到verbose模式 command + v
- 启动为单用户模式 command + s
- 打开固件 command+shift + o + f
- 火线连接方式 t
