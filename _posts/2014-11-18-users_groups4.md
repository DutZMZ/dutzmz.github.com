---
layout: post
category: Linux
title: Linux用户和用户组管理(3):用户组管理命令
tagline: by zmz
tags: [Linux, 用户组管理]
---

最近实验室事情较少，所以小z有更多的时间来学习Linux。接下来我们学习Linux中的用户管理和用户组管理，本章共包含4小节内容，本小节为第4小节：用户组管理命令。希望对大家有所帮助^_^

<!--more-->

##用户组管理命令

###groupadd
+ 增加组
+ -g ： GID

###groupmod
+ 修改组的属性
+ -g：修改组ID
+ -n：修改组名

###groupdel
+ 删除组
+ 对于非空组，如果该组有初始用户，则不可以删除，否则可以删除

###gpasswd
+ 功能：把用户添加入组或者从组中删除
+ -a：add，添加用户
+ -d：
+ elete，删除用户
***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/user_group_manage.jpg)