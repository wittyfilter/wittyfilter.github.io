---
layout: post
title: Openbox个人配置
categories: Computer
tags:
  - linux
date: 2013-08-29 00:00:00
---
记录一下自己Openbox配置，with Archlinux

---

### 关于Openbox

openbox是一个轻量级，可定制性高的窗口管理器。  
安装完成后, 应该把默认的配置文件 rc.xml, menu.xml, 和 autostart 和environment复制到`~/.config/openbox`：

{% highlight shell %}
mkdir -p ~/.config/openbox
cp /etc/xdg/openbox/{rc.xml,menu.xml,autostart,environment} ~/.config/openbox
{% endhighlight %}

**注意: 不要用 root 进行以上操作，应使用普通用户**

这四个文件组成了Openbox配置的基础。每一个文件是配置的独立的部分，它们的功能是：

- rc.xml
本文件是配置文件，用于定义键盘快捷键, 主题, 虚拟桌面等。

- menu.xml
 本文件定义了在桌面用鼠标击键时显示的菜单。它定义了程序启动器和快捷方式。

- autostart
本文件在 Openbox 启动时读取。包含了一些需要启动的程序，通常用来定义许多环境变量、启动面板/dock、设置壁纸或者执行其他启动脚本等等。

- environment
本文件被openbox-session启动时调用。它包含了在Openbox上下文中定义的变量。任何你想对 Openbox 本身可见以及从菜单启动的程序需要的变量都放在这里。

如果将Openbox作为单独的窗口管理器使用，则只需要在`~./xinitrc`中添加

    exec openbox-session

startx即可启动Openbox。Openbox中的xdg-autostart需要pyxdg。
如果有桌面环境的话，自带的登录管理器会话选择就比较方便。推荐cdm，基于会话且轻量。

---

### 配置Openbox

<!-- more -->

#### 首选项

配置文件于`~/.config/openbox/rc.xml`，自带有注释。图形化界面的程序可以使用Obconf。

#### 菜单

菜单文件`~/.config/openbox/menu.xml`，可手动编辑，推荐使用mmaker自动生成。

{% highlight shell %}
mmaker -vf Openbox3 #-f选项覆盖之前的菜单
{% endhighlight %}

基于GUI的菜单编辑可以使用obmenu。

#### 自启动

自启动程序脚本位于`~/.config/openbox/autostart`,Openbox也会启动在`/etc/xdg/autostart`中的所有的*.desktop文件。我的自启动脚本：

{% highlight shell %}
xbacklight -set 0
fcitx &
feh --bg-scale /home/simonyang/Pictures/bg.jpg
guake &
tint2 &
xcompmgr -c -C -t-5 -l-5 -r4.2 -o.55 &
volumeicon &
batti &
{% endhighlight %}

xbacklight调整背光，主要是为了省电。

#### 主题和外观

安装openbox-themes可获得丰富的主题，推荐主题numix。可以在[这里](http://www.box-look.org)找到更多主题，然后用Obconf安装。
gtk主题可以通过安装gtk-theme-switch控制，依然推荐numix。具体的主题配置在`~/.gtkrc-2.0`。
图标推荐faenza，可以安装faience-icon-theme获得，然后编辑`~/.gtkrc-2.0`，添加：

    gtk-icon-theme-name = "Faenza"

#### 文件关联

从仓库安装perl-file-mimeinfo，调用mimeopen类似这样:

{% highlight shell %}
mimeopen -d /path/to/file
{% endhighlight %}

会提示用哪个程序来打开 /path/to/file。

#### 音量控制

使用alsamixer可以全局控制。对于快捷键的绑定，如媒体控制功能键，需要修改rc.xml，添加：

    <keybind key="XF86AudioRaiseVolume">
     <action name="Execute">
       <command>amixer set Master 5%+ unmute</command>
     </action>
    </keybind>
    <keybind key="XF86AudioLowerVolume">
     <action name="Execute">
       <command>amixer set Master 5%- unmute</command>
     </action>
    </keybind>
    <keybind key="XF86AudioMute">
     <action name="Execute">
       <command>amixer set Master toggle</command>
     </action>
    </keybind>

---

### 一些应用程序

#### 图片查看

推荐轻量的feh。feh也可以用来设定桌面背景：

{% highlight shell %}
feh --bg-scale /path/to/picture
{% endhighlight %}

#### 面板

推荐tint2，配置可以使用tintwizard程序。最好安装tint2-svn，有更多新特性可以使用。
托盘需要显示电源和音量控制，推荐batti和volumeicon

#### 网络

推荐wicd，systemd自启动，禁用其他网络的systemd，networkmanager貌似不可正常显示通知。

#### 文件管理器

spacefm是个不错的选择，虽然功能上和nautilus与dolphin比起来差太多了。自动挂载windows硬盘请用ntfs-config。

#### 应用程序启动器

推荐dmenu。

#### 其他

要想使得窗口有阴影特效，可以这样：

{% highlight shell %}
xcompmgr -c -C -t-5 -l-5 -r4.2 -o.55 &
{% endhighlight %}

输入法使用fcitx再好不过了。将以下加入到`~/.xprofile`中：

{% highlight shell %}
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
{% endhighlight %}

drop-down终端推荐用guake

---

### 参考

<https://wiki.archlinux.org>