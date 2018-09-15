---
layout: post
title: Tsinghua项目手册
tags:
  - project
categories:
  - Technique
date: 2018-09-08 00:00:00
---

# 小车控制

## 1. 登陆

```bash
$ ssh root@host1
```
pw: ***

## 2. 环境设置

```zsh
$ source /opt/ros/kinetic/setup.bash
$ cd firmware/autolabor2.5/launch
$ ./lidar_nav_sh.sh &
```
## 3. 小车控制

```zsh
$ rostopic pub cmd_vel geometry_msgs/Twist -r 2 -- '[1,0,0]' '[0,0,3.5]'
```
其中 -r 参数代表了指令发送的频率（Hz单位，例子中为2Hz），第一个[x, y, z] 代表了位移，第二个[u, v, w]代表了旋转。

或者可以将指令写到一个文件中，请参考command_run.sh和command_yz.sh

# TX1控制

## 1. 登陆

```zsh
$ ssh nvidia@host2
```
pw: nvidia

登陆后开启X11VNC进行调试
```zsh
$ ./x11vncscript
```
VNC请访问*host2*，pw: ***

## 2. ORB SLAM

```zsh
$ cd ~/ORB_SLAM2/build
$ make
$ ./slamscript
```
在出现Loading ORB Vocabulary以后，optitrack可以开始准备录制。
结束后，请在应用窗口双击```Esc```退出程序，将在build文件夹下保存两个轨迹文档。

## 3. 方波调试

```zsh
$ cd ~/Dependency/jetsonTX1GPIO
$ g++ test_gpio.cpp jetsonGPIO.c --std=c++11 -o testgpio
$ sudo ./testgpio k
```
k为频率（Hz）