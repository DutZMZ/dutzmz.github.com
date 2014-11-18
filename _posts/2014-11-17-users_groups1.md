---
layout: post
category: Linux
title: Linux用户和用户组管理(1):用户配置文件
tagline: by zmz
tags: [Linux, 用户管理, 用户组管理]
---

最近实验室事情较少，所以小z有更多的时间来学习Linux。接下来我们学习Linux中的用户管理和用户组管理，本章共包含4小节内容，本小节为第1小节：用户配置文件。希望对大家有所帮助^_^

<!--more-->

##用户信息文件——/etc/passwd

+ 服务器安全性越高，则用户权限等级制度和服务器操作就越严格。
+ 在Linux中主要是通过用户配置文件来查看和修改用户信息。
+ 查看帮助信息：man 5 passwd

{% highlight shell %}
[zmz@localhost ~]$ vim /etc/passwd
{% endhighlight %}

打开该文件，其中每一行为一个用户的信息，共包含7个字段，以":"分割：

{% highlight shell %}
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
{% endhighlight %}
	
+ 第一字段：用户名
+ 第二字段：x，密码标志（有密码），标志密码真实的放在/etc/shadow下，该文件的权限为：

{% highlight shell %}
[zmz@localhost ~]$ ls -l /etc/shadow
----------. 1 root root 1056 Nov 16 23:47 /etc/shadow
{% endhighlight %}
	
+ 第三字段：UID（用户ID）
    + 0：超级用户
    + 1-499： 系统用户（伪用户），不能删除，不能登录
    + 500-65535：普通用户
    
 **假如把某一用户的UID改为0，则该用户就变为root用户了**


+ 第四字段：GID（用户初始组ID）
    + 初始组：每个组有且只有一个初始组，一般情况下用户名和其初始组同名，建议不要修改初始组名称。
    + 附加组：用户组可以加入其他的用户组，并拥有这些组的权限，附加组可以有多个
    
+ 第五字段：用户说明
+ 第六字段：家目录(宿主目录)
    + 普通用户：/home/username/
    + 超级用户：/root/
+ 第七字段：登录之后的Shell(命令解释器)
    + /bin/Shell:标准Shell（root和普通用户）
    + /sbin/nologin

##用户配置文件——影子文件:/etc/shadow
我们可以看一下该文件的权限：

{% highlight shell %}
[zmz@localhost ~]$ ll /etc/shadow
----------. 1 root root 1056 Nov 16 23:47 /etc/shadow
{% endhighlight %}

可以看到，/etc/shadow文件的权限为000，当然root用户依然可以打开该文件，文件内容：

{% highlight shell %}
root:$6$JYKQ1oDUwTfLjj1a$srpDAsXpbnLXDV.VlVotznmspZlXW.AFwFlFrBGXG0.dhaTfdfj13vO1hOaIWckTSJEzVtvWdazEsbCuxfkMe0:16341:0:99999:7:::
bin:*:15980:0:99999:7:::
daemon:*:15980:0:99999:7:::
{% endhighlight %}

每行9个字段，还是以":"分割。

+ 第一字段：用户名
+ 第二字段：加密密码
    + 经过SHA512加密后的密码
    + !!或者*，表示没有密码，则不能登录
    
+ 第三字段：密码最后一次修改的日期
    + 时间戳，1970.1.1为标准，每过一天，加一。
+ 第四字段：两次修改必须满足的时间间隔
+ 第五字段：密码有效期
+ 第六字段：密码到期之前警告时间
+ 第七字段：密码到期之后的宽限期限
    + 0 ： 代表密码过期后，立即失效
    + -1: 永不失效
+ 第八字段：帐号失效时间(时间戳)
+ 第九字段：保留
+ 时间戳换算：

{% highlight shell %}
[zmz@localhost ~]$ date -d "1970-01-01 16341 days"
Sun Sep 28 00:00:00 CST 2014
{% endhighlight %}

##用户配置文件——组信息文件

###组信息文件：/etc/group
该文件的内容为：

{% highlight shell %}
root:x:0:
bin:x:1:bin,daemon
daemon:x:2:bin,daemon
sys:x:3:bin,adm
{% endhighlight %}

每一行四个字段，

+ 第一字段：组名
+ 第二字段：x，组密码标志，密码放在/etc/gshadow里面，很少使用
+ 第三字段：GID
+ 第四段：组中附加用户

###组密码文件：/etc/gshadow

+ 第一字段：组名
+ 第二字段：组密码
+ 第三字段：组管理员用户名
+ 第四段：组中附加用户

***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/user_group_manage.jpg)