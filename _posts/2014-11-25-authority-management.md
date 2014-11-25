---
layout: post
category: Linux
title: Linux权限管理
tagline: by zmz
tags: [Linux, 权限管理, ACL, SetUID, SetGID, Sticky BIT, chattr]
---

今天小z带大家一起学习**权限管理**。在之前我们已经知道了普通的文件权限：

    -rw-rw-r--.  1 zmz  zmz     0 Nov 22 04:24 abc

普通的权限包括r（read，读）、w（write，写）和x（执行）。而且，对于文件的所有者、所属组和其他人的权限是不同的。如上面的abc文件，对于所有者和所属组的权限时是读和写，而对于其他用户则只有读的权限。

然而，这只是最普通的文件权限管理方式。这些方式还远不够用。所以，Linux提供了更加复杂和灵活的文件权限管理方式来满足不同情景的需求。大家准备好了吗？让我们一起开始**权限管理**的征程吧。

<!--more-->

#ACL权限

ACL（Access Control List）,是指访问控制列表，它的作用时解决身份不足的问题。ACL会忽略已有的所有者、所属组和其他用户的权限限制，给某一用户分配权限。

ACL权限需要分区的支持，如果某块分区不支持ACL权限，则该分布上的所有文件都不能设置ACL权限。所以，要想设置ACL权限，首先应该设置分区允许ACL权限。

##ACL开启

###查看分区ACL权限是否开启：dumpe2fs

    [root@localhost ~]# dumpe2fs -h /dev/sda3
    ...
    Default mount options:    user_xattr acl


运行以上命令，如果出现上述user_xattr acl选项，则说明分区支持ACL权限（默认ACL权限已经开启）。

###临时开启分区ACL权限
如果ACL权限没有开启，我们可以使用以下命令临时开启ACL权限（让根分区重新挂载）：

    mount -o remount,acl

###配置文件：/etc/fstab
当然也可以修改配置文件，但这种方式不安全，这里就不叙述了。


##查看与设定ACL权限

###getfacl 文件名
+ 功能：查看文件ACL权限

###setfacl 选项 文件名
+ 功能：设置ACL权限
+ -m：设定ACL权限

这里我们以root用户登录，做以下实验：

    [root@localhost ~]# mkdir /project
    [root@localhost ~]# ls -ld /project
    drwxr-xr-x. 2 root root 4096 Nov 19 23:59 /project
    [root@localhost ~]# useradd ssy
    [root@localhost ~]# groupadd tgroup
    [root@localhost /]# setfacl -m u:ssy:rwx /project
    [root@localhost /]# setfacl -m g:tgroup:rwx /project
    [root@localhost /]# ls -ld /project
    drwxrwxr-x+ 2 root root 4096 Nov 22 04:47 /project
    [root@localhost /]# getfacl /project
    getfacl: Removing leading '/' from absolute path names
    # file: project
    # owner: root
    # group: root
    user::rwx
    user:ssy:rwx
    group::r-x
    group:tgroup:rwx
    mask::rwx
    other::r-x


上述代码中，我们在根目录下建立/project目录，此时的权限为："drwxr-xr-x."，然后小z建立了ssy用户和tgroup组，然后使用setfacl命令分别给ssy和tgroup赋予对/project目录的读、写和执行的权利，此时/project的权限为：" drwxrwxr-x+"，最后的'.'变成'+'，标志该文件拥有了ACL权限。

利用getfacl可以查看ACL权限的详细信息，如上述代码所示，可以看出ssy用户对/project目录拥有rwx权限，tgroup组对/project拥有rwx权限。

普通的权限管理方法可以为文件的所有者、所属组和其他用户三种类型的用户指定权限，但利用ACL，就可以方便的向个别用户或者个别组赋予对文件的读、写和执行的权利。

是不是很犀利？！（(*^__^*) 嘻嘻……）。但还没有完呢，ACL权限还稍稍有点限制，且看下面的内容。


##ACL权限——最大有效权限

###最大有效权限

在上一小节中，我们使用getfacl查看文件的ACL权限信息，其中的mask是什么意思呢？原来，mask是用来指定文件最大有效权限的。如果给用户赋予了ACL权限，该ACL权限和mask权限“相与”才得到用户的真正权限。

设定文件的mask权限：

    [root@localhost /]# setfacl -m m:rx /project
    [root@localhost /]# getfacl /project
    getfacl: Removing leading '/' from absolute path names
    # file: project
    # owner: root
    # group: root
    user::rwx
    user:ssy:rwx			#effective:r-x
    group::r-x
    group:tgroup:rwx		#effective:r-x
    mask::r-x
    other::r-x

在上一小节的基础上，我们修改了/project的mask权限为rx，然后在查看/project的ACL权限信息，可以看到，虽然ssy和tgroup的权限为rwx，但是实际有效权限为r-x。

###setfacl的其他选项
+ -x：删除指定用户或组的ACL权限，例子：


    [root@localhost /]# setfacl -x g:tgroup /project
    [root@localhost /]# getfacl /project
    getfacl: Removing leading '/' from absolute path names
    # file: project
    # owner: root
    # group: root
    user::rwx
    user:ssy:rwx
    group::r-x
    mask::rwx
    other::r-x

+ -b：删除所有的ACL权限
+ -R：针对父目录，对于其**现有的文件和子目录**，递归设置父目录的ACL权限(注意-R的位置)


    [root@localhost ~]# setfacl -m u:ssy:rx -R /project

+ -d：设定默认的ACL权限，如果给父目录设定了默认ACL权限，那么父目录中所有**新建**的子文件都会继承父目录的ACL权限：


    setfacl -m d:u:用户名:权限 文件名


***

#2.文件特殊权限

##SetUID

+ 简介

SetUID是一种特殊的权限，该权限有以下四点特征：

1. **只有可执行的二进制程序才能设定SUID权限**
2. **命令执行者要对该程序拥有x（执行）权限**
3. 命令执行者在执行该程序时，临时获得该程序文件的所有者身份
4. SetUID权限只能在该程序执行过程中有效，也就是说身份改变只在程序执行过程中有效

对于以上4点，大家肯定有很多疑问，没关系。我们先来看一个例子。

我们都知道，passwd命令用来修改用户的密码，密码真实存在的文件是/etc/shadow文件。让我们先看一下该文件的权限：

    [root@localhost ~]# ls -l /etc/shadow
    ----------. 1 root root 1177 Nov 20 00:00 /etc/shadow

除了超级用户外，其他所有人都没有操作该文件的权限，那我们是如何修改密码的呢？？

接下来，我们再来看一下passwd这条命令的权限：

    [root@localhost ~]# ls -l /usr/bin/passwd
    -rwsr-xr-x. 1 root root 30768 Feb 22  2012 /usr/bin/passwd

passwd这条命令的所有者为root，可执行权限标志为s(若为S，则为报错)，表明这条命令拥有SUID权限。也就是说，passwd这条命令拥有SetUID的权限。passwd是可执行二进制程序，并且普通用户对passwd拥有执行（x）权限，所以当普通用户执行passwd时会暂时获得root身份，这样也就可以修改密码了（命令执行完成后，又变为普通用户）。

这就时SetUID权限的作用，它可以让命令执行者暂时的变为命令的所有者，从而执行命令。

+ 设定SetUID的方法（4代表SetUID）
    + chmod 4755 文件名
    + chmod u+s 文件名
+ 取消SetUID的方法
    + chmod 755 文件名
    + chmod u-s 文件名

+ **SetUID非常危险，应谨少用甚至不用**

+ 总结：如果一个命令拥有SetUID权限，则普通用户在执行该命令时，会暂时变为该命令的拥有者，即灵魂附体，执行完毕后，恢复普通用户身份。



##SetGID

###SetGID针对二进制文件的作用

1. **只有可执行的二进制程序才能设定SetGID权限**
2. **命令执行者要对该程序拥有x（执行）权限**
3. 普通用户执行拥有SetGID权限的命令时，组身份获升级为该命令的所属组
4. SetGID权限只能在该程序执行过程中有效，也就是说组身份改变只在程序执行过程中有效

这种情况下，SetGID权限与SetUID权限非常相似，只是当普通用户执行具有SetGID权限的命令时，组身份会暂时变为该命令的所属组，请看以下例子：

    [root@localhost ~]# ll /usr/bin/locate
    -rwx--s--x. 1 root slocate 38464 Oct 10  2012 /usr/bin/locate
    [root@localhost ~]# ll /var/lib/mlocate/mlocate.db
    -rw-r-----. 1 root slocate 3393004 Nov 19 04:41 /var/lib/mlocate/mlocate.db

locate文件用来快速的查找文件，其所属组权限的执行位为s，标志locate命令拥有SetGID权限。locate命令实际操作的文件是/var/lib/mlocate/mlocate.db，该文件的所属组拥有读权限。当普通用户执行locate命令时，组身份变为slocate，也就可以读mlocate.db文件了。


###针对目录的SetGID权限

与SetUID不同，SetGID权限还可以针对目录，此时：

1. 普通用户必须对此目录拥有r和x权限，才能进入此目录
2. 普通用户在此目录中的有效组会变为此目录的所属组
3. 若普通用户对此目录拥有w权限，新建的文件的默认所属组是这个目录的所属组

+ 设定SGID
    + chmod 2755 文件名
    + chmod g+s 文件名

+ 取消SGID
    + chmod 755 文件名
    + chmod g-s 文件名
    
##Sticky BIT（粘着位权限）

1. 粘着位权限目前只针对**目录**有关
2. 普通用户对该目录拥有w和x权限
3. 如果没有黏着位，并且用户对目录拥有w权限，则该用户可以删掉该目录下的所有文件；但该目录有了黏着位权限，则只有root用户可以删除所有文件，普通用户只能删除自己建立的文件。但普通用户可以修改别人的文件。


    [zmz@localhost ~]$ ll -d /tmp
    drwxrwxrwt. 44 root root 4096 Nov 22 02:43 /tmp

+ 设置粘着位权限
    + chmod 1755 目录名
    + chmod o+t 目录名
+ 取消粘着位权限：
    + chmod 777 目录名
    + chmod o-t 目录名

***

#3.文件系统属性chattr权限

##chattr
+ 功能：改变文件的属性，为了防止误操作。
+ 格式：chattr [+-=][选项] 文件或目录名
+ i属性:如果对文件赋予i属性，则不能对该文件进行删除、改名和修改；如果对目录赋予i属性，那么只能修改目录下的文件的数据，但不允许新建和删除文件，即不能对该目录进行修改。**针对root用户也有效**
+ a属性：如果对文件赋予a属性，则只能对文件进行增加数据，但不能删除也不能修改数据；如果对目录赋予a属性，那么只允许在目录下建立和修改文件，但不允许删除。**针对root用户也有效**

##lsattr 
+ 格式：lsattr 选型 文件名
+ -a：查看所有文件和目录的属性
+ -d：若是目录，则列出目录本身的属性

***

#4.系统命令sudo权限

+ root把本来只能超级用户执行的命令赋予普通用户来执行。
+ sudo的操作对象是系统命令

##sudo使用

###visudo

+ 修改/etc/sudoers文件
+ root ALL=（ALL） ALL
用户名 被管理主机的地址=（可使用的身份） 授权命令
+ sc ALL=/sbin/shutdown -r now
如果在/etc/sudoers文件中增加该行，则说明用户sc可以执行重启命令。

###sudo -l
查看root赋予的权利

***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/user_group_manage.jpg)