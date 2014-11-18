---
layout: post
category: Linux
title: Linux用户和用户组管理(2):用户管理相关文件
tagline: by zmz
tags: [Linux, 用户, 用户组, 家目录, mail, skel]
---

最近实验室事情较少，所以小z有更多的时间来学习Linux。接下来我们学习Linux中的用户管理和用户组管理，本章共包含4小节内容，本小节为第2小节：用户管理相关文件。希望对大家有所帮助^_^

<!--more-->

##用户的家目录

添加用户时，家目录自动生成。

+ 普通用户：/home/用户名/，权限为700(提示符：$)
+ 超级用户：/root/,权限为550(提示符：#)

把普通用户的UID改为0，则变为超级用户，家目录不变。

##用户的邮箱
+ 位置：/var/spool/mail/用户名/
    + /var/: Linux下，可变数据的存储位置

##用户模板目录

+ 位置：/etc/skel/
+ 作用：当我们添加一个用户时，系统会自动的为新用户添加家目录，但家目录并不是空的，里面有一些隐藏文件，这些隐藏文件就是从/etc/skel/拷贝过来的。

***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/user_group_manage.jpg)