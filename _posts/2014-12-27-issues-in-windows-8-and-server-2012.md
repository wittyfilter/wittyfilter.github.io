---
layout: post
title: Windows8/WindowsServer2012 问题汇总
categories: Coding
tags:
  - windows
  - IE
  - windows server
  - osx
date: 2014-12-27 00:00:00
---

# 1. IE内置管理员无法使用

Click Start>Run
Type `gpedit.msc` and click "OK"
The Group Policy Editor will open
找到“计算机配置”里面的“Windows设置”，打开“安全设置” -> “本地策略” -> “安全选项”，在右边查找一项策略：“用户帐户控制: 用于内置管理员账户的管理员批准模式”。启用这个策略。

# 2. Mac远程windows及windowsserver2012

Enable RDP security layer in Group Policy on the machine:

Verify that the firewall allows remote desktop connections with RDP (Port 3389)
Click Start>Run
Type `gpedit.msc` and click "OK"
The Group Policy Editor will open
In the left hand side bar, expand Computer Configuration>Administrative Templates>Windows Components>Remote Desktop Services>Remote Desktop Session Host
Select "Security"
Change "Require use of specific security layer for remote desktop (RDP) connection" to Enabled" and select "RDP" in the Options pane.
Change "Require user authentication for remote connections by using Network Level Authentication" to "Disabled."
Close Group Policy Editor and reboot the machine for changes to take effect.