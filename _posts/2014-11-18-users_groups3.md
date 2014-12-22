---
layout: post
category: Linux
title: Linux用户和用户组管理(3):用户管理命令
tagline: by zmz
tags: [Linux, 用户管理]
---

最近实验室事情较少，所以小z有更多的时间来学习Linux。接下来我们学习Linux中的用户管理和用户组管理，本章共包含4小节内容，本小节为第3小节：用户管理命令。希望对大家有所帮助^_^

<!--more-->

##用户管理命令：useradd

+ 功能：添加用户
+ -u: UID 手工指定用户的UID号，不建议使用
+ -d: directory，家目录，手工指定家目录
+ -c：comment，用户说明
+ -g：组名
+ -G：组名，指定用户的附加组
+ -s：手工指定用户的登录shell，默认是/bin/bash

{% highlight shell %}
useradd -u 666 -G root,bin -c "hello,world" -d /liming -s /bin/bash liming
{% endhighlight %}

useradd默认添加用户时，那些参数时哪里来的呢？？这些参数来自以下两个配置文件：/etc/default/useradd和/etc/login.defs。

###用户默认值文件：/etc/default/useradd

{% highlight shell %}
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
{% endhighlight %}

+ HOME=/home, 用户家目录
+ INACTIVE=-1，密码过期宽限天数（shadow文件的第七字段）
+ EXPIRE=, 密码失效时间（shadow第八字段）
+ SHELL=/bin/bash
+ SKEL=/etc/skel,模板目录
+ CREATE_MAIL_SPOOL=yes,是否建立邮箱

###/etc/login.defs
+ PASS_MAX_DAYS，密码有效期
+ PASS_MIN_DAYS， 密码修改间隔
+ PASS_MIN_LEN 5, 密码最小位
+ PASS_WARN_AGE 密码到期警告
+ UID_MIN 500,用户最小ID
+ UID_MAX 60000，用户最大ID
+ ENCRYPT_METHOD SHA512，加密模式

##用户管理命令：passwd

+ 为用户增加密码，普通用户只能修改自己的密码，格式：passwd
+ -S：查询用户密码的密码状态，仅root可用，即查看shadow文件中用户的密码信息

{% highlight shell %}
[zmz@localhost ~]$ sudo passwd -S zmz
[sudo] password for zmz: 
zmz PS 2014-11-16 0 99999 7 -1 (Password set, SHA512 crypt.)
{% endhighlight %}
	
+ -l：lock，暂时锁定用户，此时会在/etc/shadow文件中用户对应的密码前加入“!!”。
+ -u：解锁用户
+ --stdin:从标准输入接收密码

{% highlight shell %}
echo "123" | passwd --stdin lamp
{% endhighlight %}	
    
**管道符\|的作用：将前面的命令输出作为后一个命令的输入**
    
##用户管理命令：usermod和chage

###usermod:
+ 功能：user modify, 修改已经存在的账户的信息
+ -u: 修改用户的UID号
+ -c：comment，修改用户说明
+ -G：修改用户的附加组
+ -L：锁定用户
+ -U：解锁用户

{% highlight shell %}
[zmz@localhost ~]$ sudo usermod -c "Love songshuyan" zmz
[sudo] password for zmz: 
[zmz@localhost ~]$ sudo grep zmz /etc/passwd
zmz:x:500:500:Love     songshuyan:/home/zmz:/bin/bash
{% endhighlight %}

    
###chage
+ 功能：修改用户的密码状态
+ -l：查询用户的密码状态
+ -d：修改密码最后一次更改时间
+ -m：...
+ -M:...
+ -W:...
+ -l:...
+ -E:...

##用户管理命令：userdel和su

###userdel
+ 功能：删除用户
+ -r：同时删除用户的家目录

###id
+ 功能：查询用户的UID，GID以及所属组

###su
+ 功能：切换用户
+ 格式：su - root
+ -：减号不能省略，切换用户的同时，将环境变量同时切换
+ -c：仅使用其他账户执行一条命令，而不切换用户身份

{% highlight shell %}
[zmz@localhost ~]$ su - root -c "useradd user1"
{% endhighlight %}	

***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/user_group_manage.jpg)