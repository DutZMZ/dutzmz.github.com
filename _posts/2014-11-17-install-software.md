---
layout: post
category: Linux
title: Linux软件包管理
tagline: by zmz
tags: [Linux, Vim]
---

对于习惯了windows傻瓜式安装和卸载程序的小白来说，Linux的软件管理显得十分高大上，也显得很困难，很有无从下手的感觉。本小节是小z同学在学习Linux（CentOS）软件包管理时的笔记，希望对大家有所帮助^_^


<!--more-->

***

##简介

###软件分类

在windows下，一般软件包都会包含一个.exe的安装程序，但.exe程序在Linux下是无法运行的。CentOS的程序安装包分为以下两种：

+ 源码包：顾名思义，就是可以看到“源代码”的软件安装包。源码包的安装过程要经过配置、编译和安装等过程。
    + 优点：
        + 自由定制所需功能：因为可以看到源代码，所以可以根据自己的需求修改源代码，实现功能的定制。前提是你足够强大，可以看懂源代码
        + 源码包在安装前需要经过本地编译，安装完成后更适合自己的系统，更加稳定，效率更高
        + 卸载方便：直接删除安装目录即可
    + 缺点：
        + 安装步骤繁琐，容易出现拼写错误
        + 编译时间长，安装时间长
        + 编译过程种一旦报错，很难解决
        
有一种特殊的源码包，称为“脚本安装包”，是把源码包的安装步骤用脚本语言实现，使得源码包的安装过程大大简化，但该类型的软件包较少。

+ RPM包
RPM包，即二进制包、系统安装包，类似与windows中的.exe
安装程序。
    + 优点：
        + RPM包管理简单，有相应的命令实现安装、卸载、升级和查询
        + 安装速度快
    + 缺点：
        + 不能看到源代码
        + 功能定制不灵活
        + RPM包安装最大的缺点是包之间依赖性，即安装A，需要安装B，而安装B还需要安装C。有时甚至会形成循环以来！！

从以上分析可以看出，源码包和RPM包各有利弊，但主流选择是RPM包安装，所有的rpm包在安装镜像的packages目录下都可以找到。

***

##RPM命令管理——包命名与依赖性

###RPM包命名规则

    httpd-2.2.15-15.e16.centos.1.i686.rpm
    
以上是一个**包全名**的例子。各个字段的含义如下：
+ httpd：软件包名，即软件的名字
+ 2.2.15：软件包的版本号
+ 15：软件包的发布次数
+ e16.centos：该软件包适合的linux平台
+ i686：该软件包适合的硬件平台

###RPM包的依赖性

+ 树形依赖：A => B => C
+ 环形以来：A => B => C => D
+ 模块依赖：安装A时，需要B的某个函数库。对于软件的模块依赖，可以通过[www.rpmfind.net](http://www.rpmfind.net)查询

***

##RPM命令管理——安装、升级、卸载

+ 包全名：如果操作的包尚未安装，需要使用包全名
+ 包名：即软件名，如果软件包已经安装在本地，则此时使用包名即可。

+ RPM安装
    + 命令：rpm -ivh 包全名
    + -i: install,安装
    + -v：verbose，显示安装信息
    + -h：hash，显示安装进度
    + --nodeps：不检测依赖性，不建议使用
    
+ RPM升级：
    + 命令：rpm -Uvh 包全名
    + -U：update 升级

+ RPM卸载
    + 命令：rpm -e 包名
    + -e：erase， 卸载
很多时候，卸载软件都出现错误：

    error: Failed dependencies:
    
因为要卸载的软件是被其他软件依赖。

***

##RPM命令管理——查询

###rpm -qai 包名
+ -q：query，查询
+ -q：all，查询所有已安装包
+ -i:information，查询详细信息

###rpm -qip 包全名
+ -p:查询为安装包的信息

###rpm -ql 包名
+ 查询包中文件的安装位置



###rpm -ql 包名
+ -l：list

###rpm -qf 系统文件名
+ -f：查询系统文件属于哪个包

###rpm -qR 包名
+ -R：requires，查询依赖性

***

##RPM命令管理——校验和文件管理

###rpm -V 已安装的包名

+ 验证系统文件是否被修改（verify）

{% highlight shell %}
[zmz@localhost ~]$ rpm -V httpd
S.5....T.  c /etc/httpd/conf/httpd.conf
..?......    /usr/sbin/suexec
{% endhighlight %}


+ S:大小是否被修改
+ M；类型是否被修改
+ 5：MD5校验和
+ L：路径
+ U：文件所有者
+ G：所属组
+ T：修改时间
+ c：普通文件
+ g：ghost，鬼文件，指不该被包含的文件

###rpm包的文件提取

+ rpm2cpio：将rmp包转为cpio格式
+ cipo：标准工具，用于创建软件档案或者从软件档案种提取文件

    rpm2cpio 包全名 | cpio -idv 文件绝对路径
    
    
***

##RPM包管理——yum在线管理：IP地址配置和网络yum源

###IP地址配置和网络yum源
+ setup：配置网络IP地址，Redhat系统专有命令
+ 配置好IP后，并没有立即生效，需要将/etc/sysconfig/network-scripts/ifcfg-eth0 中：ONBOOT="yes"
+ service network restart

###网络yum源
+ 配置文件：/etc/yum.repos.d/CentOS.Base.repo

***

##RPM包管理——yum在线管理——yum命令

###yum list
+ 参看远程所有可用的软件列表

###yum search 关键字
+ 查询与关键字相关的包

###yum -y install 包名
+ 在线安装包
+ -y： 自动输入yes

###yum -y update 包名
+ 更新
+ 注意，如果不加包名，此时会全部更新，包括系统内核，从而导致系统无法启动！！！！！！！！！！

###yum -y remove 包名
+ 卸载

###rpm grouplist
+ 查询软件组

###rpm groupinstall "软件组名"
+ 安装软件组

***

##RPM包管理——yum在线管理——光盘yum源搭建
+ 挂载光盘
+ 让网络yum源失效（对yum源文件重命名,.bak）
+ 让光盘yum源生效
    + enable=1
    + baseurl = file:///mnt/cdrom

##源码包与RPM包的区别

###RPM包的位置：
+ /etc:配置文件
+ /usr/bin：执行命令
+ /usr/lib:函数库
+ /usr/share/doc:使用手册
+ /usr/share/man:帮助文档
+  service 包名 start：启动

###源码包位置：/usr/local/软件名
+ 绝对路径启动：/etc/rc.d/init.d/httpd start
+ 源码包只能使用绝对路径启动

***

###源码包安装
+ ./configure:软件配置与环境检查，将结果写入Makefile，后续使用
    + ./configure --prefix=/usr/local/apache2
+ make：编译，（make clean：清楚make产生的临时文件）
+ make install：安装

###源码包卸载
+ 删除安装目录即可

***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/install-software.jpg)