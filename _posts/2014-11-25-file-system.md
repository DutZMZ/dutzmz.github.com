---
layout: post
category: Linux
title: Linux文件系统
tagline: by zmz
tags: [Linux, 文件系统]
---

本小节时小z学习Linux文件系统时的笔记。由于小z对这一小节的理解不好，所以这里只做简单的介绍。

<!--more-->

***

#分区和文件系统管理

大家都知道，在Windows下，我们可以看到很多盘，比如C盘、D盘、E盘……但是计算机内部默认情况下只有一个硬盘，这些盘是哪来的呢？其实，这些盘是对硬盘的一种逻辑划分，是硬盘的一部分。分区的主要目的是为了便于管理。在Windows下，上面的C、D、E称为盘符。也就是说我们通过盘符去定位分区，通过盘符去访问或者进入分区。

类似的，在Linux下也需要对硬盘进行切分。但切分方式需要满足一些限制。而且，Linux下没有盘符的概念。与盘符相对应的时**挂载点**。挂载点其实就是一个目录。通过将分区与挂载点建立连接，我们就可以通过挂载点访问分区内容了。而上述建立分区与挂载点连接的过程，就称为**挂载**。

我们先来了解一下分区类型和文件系统。

##分区类型

分区类型主要包括：住分区和扩展分区。

+ 主分区：最多只有4个
+ 扩展分区：只能有一个，也占主分区的一个名额；扩展分区不能存储数据和格式化，必须再划分成**逻辑分区**才能使用。



##文件系统

Linux使用的ext文件系统。

+ ext2:最大支持16TB的分区和最大2TB的文件
+ ext3:最大支持16TB的分区和最大2TB的文件,带日志功能
+ ext4：向下兼容；最大支持1EB的分区和最大16TB的文件（1EB=1024PB=1024*1024TB）；无限数量子目录；更安全，可靠性更高……


***

#文件系统常用命令

##df命令、du命令、fsck命令、dumpe2fs命令

###df
+ 功能：统计文件系统的空间使用情况

###du
+ 功能：统计目录和文件大小
+ -s：统计总占用量
+ -a：显示每个子文件的磁盘占用量，默认只统计子目录的磁盘占用量
+ -h：使用习惯单位显示大小


    [zmz@localhost ~]$ du -sh tem
    24K	tem

**du命令只统计文件和目录占用的空间
df命令从文件系统的角度，除了统计文件和目录占用的空间外，还统计被系统或者程序占用的空间**

###fsck
+ 功能：文件系统修复命令
+ -a:不用显示用户提示，自动修复文件系统
+ -y：自动修复
+ 尽量不用

###dumpe2fs
+ 功能：查看分区的详细信息
+ 格式：dumpe2fs 分区设备文件名

##挂载命令：mount
+ 功能:进行设备挂载，设备文件名，挂载点（目录）

+ -l：显示卷标名称

{% highlight shell %}
[zmz@localhost ~]$ mount
/dev/sdb3 on / type ext4 (rw)
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
tmpfs on /dev/shm type tmpfs (rw,rootcontext="system_u:object_r:tmpfs_t:s0")
none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
/dev/sda2 on /home/zmz/win type fuseblk (rw,allow_other,blksize=4096)
{% endhighlight%}

+ -a：依赖配置文件/etc/fstab的内容，自动挂载

+ 挂载格式：mount [-t 文件系统] [-L 卷标名] [-o 特殊选项] 设备文件名 挂载点

##挂载U盘和光盘

###挂载光盘
1. 建立挂载点
2. 挂载：mount -t iso9660 /dev/sr0 /mnt/cdrom

###卸载命令
+ 格式：umount 设备文件名或挂载点

###挂载U盘

+ fat32:vfat
+ fat16:fat


    mount -t vfat /dev/sdb1 /mnt/usb

##支持NTFS文件系统

**Linux默认不支持NTFS文件系统**，所以可以使用NTFS-3G的插件来支持NTFS文件系统。

略……



***

#fdisk分区

##fdisk命令分区过程

###fdisk -l:查询系统中可以使用的存储设备
分区的步骤：

1. fdisk分区

    fdisk /dev/sdb
    
2. partprobe
分区完成后，执行命令partprobe，重新读取分区表信息

3. 格式化分区**(扩展分区不可以格式化)**：


    mkfs -t ext4 /dev/sdb1
    
4. 挂载分区


##分区自动挂载与fstab文件修复

我们新建的分区，系统重启后没有自动挂载的。要想实现对分区的自动挂载，需要修改/etc/fstab配置文件。注意：修改完成后，重启时必须保证分区存在，否则会导致系统无法正常启动。

###/etc/fstab文件
+ 第一字段：分区设备文件名或UUID（硬盘通用唯一识别码）,UUID更可靠(dumpe2fs查看UUID)
+ 第二字段：挂载点
+ 第三字段：默认的文件系统
+ 第四字段：权限
+ 第五字段：指定分区是否被dump备份，0代表不备份，1代表每天备份，2代表不定期备份
+ 第六字段：指定分区是否被fsck检测，0代表不检测，其他数字代表检测的优先级，当然1比2高

/etc/fstab是重要的系统配置文件，如果修改错误，会造成系统无法启动，所以，建议修改/etc/fstab之后，执行以下命令，进行自动挂载，没有错误，则说明修改正确：

    mount -a


但是，如果因为修改/etc/fstab文件导致系统崩溃，无法启动，进行如下步骤，以root命令登录，输入密码，重新挂载根分区，修改/etc/fstab文件：（否则无法对/etc/fstab进行写入）

    mount -o remount,rw /
***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/user_group_manage.jpg)