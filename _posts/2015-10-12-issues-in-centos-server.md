--
layout: post
title: CentOS Server 问题汇总
categories: Coding
tags:
  - linux
  - centos
date: 2015-10-12 00:00:00
---

# 1. 关于VNC中出现的`Error: could not run xauth`

If the SELinux configuration is set to enforcing then we need to make sure the home directories are set in the correct context:

{% highlight shell %}
egrep -e '^SELINUX=' /etc/selinux/config
SELINUX=enforcing
{% endhighlight %}

Taking a look at the SELinux settings for the home directories (use Z with the ls command):

{% highlight shell %}
ls -aslZ /home/
total 36
drwxr-xr-x. root    root    system_u:object_r:home_root_t:s0 .
drwxr-xr-x. root    root    system_u:object_r:root_t:s0      ..
drwx------. 55 unconfined_u:object_r:home_root_t:s0 jason users  4096 Jan 20 14:22 jason
{% endhighlight %}

The context for my home directory (jason) should be unconfined_u:object_r:user_home_dir_t:s0 and not unconfined_u:object_r:home_root_t:s0 as it is a home directory and not part of the root file system per se.

# 2. LDAP管理

login->beetle: x.x.45.210:636(SSL)  
cn=admin,dc=vlsi5,dc=vlsi,dc=zju,dc=edu,dc=cn  
pw->Gemini7!30  
test connection成功的话fetch DNs

# 3. 新建用户后(adduser或用ldap新建) 

在/home下

{% highlight shell %}
restorecon $USER -R
ls -Z
{% endhighlight %}

查看是否user_home_dir

# 4. 故障排除：
`iptables -S` 查看端口开的情况
`system-config-firewall` 设置端口，防火墙等
