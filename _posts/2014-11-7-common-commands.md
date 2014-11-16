---
layout: post
category: Linux
title: Linux常用命令
tagline: by zmz
tags: [Linux]
---

Linux虽然复杂难学，但是常用的命令其实并不多。本节小z带大家一起学习Linux的常用命令，希望对大家有所帮助，如果是大牛，完全不用看本节内容。

<!--more-->

***

##命令格式
通常，Linux的命令格式为：命令 [选项] [参数]
命令格式举例：

    ls -al /etc
	
其中[选项]和[参数]是可选的（方括号表示可选）;
在上面的命令种，ls是命令的名字，-al是执行该命令时的选项， /etc 表示该命令的参数，即该命令执行的对象。这条命令的作用时以长格式显示目录/etc下的所有子目录和文件信息，包括隐藏文件。

在Linux种，名称以英文"."开头的目录或者文件，是隐藏的。

***

##目录查看命令ls（list）
功能：列出目录下的子目录和文件信息

    [zmz@localhost tem]$ ls
    a  z

以上命令是在tem目录下执行的，此时ls会列出tem目录下的子目录和文件，隐藏文件不显示。如果想显示隐藏目录，则需要添加-a参数：

    [zmz@localhost tem]$ ls -a
    .  ..  a  .b  z
	
此时多出三个隐藏文件（和目录），其中.表示当前目录，..表示上一级目录。但此时，我们无法直观的区别文件和目录，以及无法确定其具体信息，如果想查看具体信息，则需增加-l（
ong）参数：

    [zmz@localhost tem]$ ls -al
    total 12
    drwxrwxr-x.  3 zmz zmz 4096 Nov 16 03:50 .
    drwx------. 44 zmz zmz 4096 Nov 16 03:48 ..
    -rw-rw-r--.  1 zmz zmz    0 Nov 16 03:48 a
    -rw-rw-r--.  1 zmz zmz    0 Nov 16 03:50 .b
    drwxrwxr-x.  2 zmz zmz 4096 Nov 16 03:48 z
	
此时，我们就可以查看到每个子目录和文件的详细信息。在结果种，第一行：total 12表示该目录的大小，单位为K。以下每一行对应一个子目录或者文件：

    -rw-rw-r--.  1 zmz zmz    0 Nov 16 03:48 a
	
这一行表示文件a的详细信息。


+ 第一个字段：drwxr-xr-x
    + 第一个字符（文件类型）：d表示目录，l表示软连接，-表示软连接
    + rwx: u，所有者的权限，r表示可读，w表示可写，x表示可执行
    + r-x:g，所属组的权限，r表示可读，-表示没有写权限，x表示可执行
    + r-x:  o，其他人的权限，r表示可读，-表示没有写权限，x表示可执行
+ 第二个字段：1，文件硬链接数或目录子目录数
+ 第三个字段：zmz（第一个），表示所有者
+ 第四个字段：zmz（第二个），表示所有者的所属组
+ 第五个字段： 0 ， 表示大小（默认单位：B）
+ 第六个字段：Nov，文件创建月份
+ 第七个字段，16，创建日期
+ 第八个字段：03:4，表示修改时间
+ 第九个字段：a：表示文件或者目录名。

如果想知道文件的大小信息，还可以加上-h（human）参数，则结果会以更加人性化的方式展示：

    [zmz@localhost tem]$ ls -alh
    total 12K
    drwxrwxr-x.  3 zmz zmz 4.0K Nov 16 03:50 .
    drwx------. 44 zmz zmz 4.0K Nov 16 03:48 ..
    -rw-rw-r--.  1 zmz zmz    0 Nov 16 03:48 a
    -rw-rw-r--.  1 zmz zmz    0 Nov 16 03:50 .b
    drwxrwxr-x.  2 zmz zmz 4.0K Nov 16 03:48 z
	
从结果可以看出，此时文件和目录大小会加上单位。

以上ls命令都没有加参数，则ls操作的对象为当前所在目录tem，如果想查看某一特定目录下的子目录或者文件，则可以加上绝对路径，例如查看目录/etc下的子目录和文件：

    [zmz@localhost tem]$ ls -alh /etc
	
如果想查看当前目录本身的信息，则需要-d参数：

    [zmz@localhost tem]$ ls -dhl /etc
    drwxr-xr-x. 119 root root 12K Nov 16 03:31 /etc
	
上述命令显示目录/etc的信息。

***

##目录处理命令

### mkdir
+ 功能：make directory，创建目录
+ 命令格式：mkdir 目录名
+ mkdir linux：在当前目录下创建名为linux的子目录
+ mkdir dir1/dir2:此时，如果子目录dir1不存在，则会报错。因为默认无法递归创建目录，除非加上-p参数。
+ mkdir -p dir1/dir2:递归创建目录，如果dir1不存在，则创建dir1.

###cd
+ 功能：change directory,改变当前目录
+ 格式：cd 目录名
+ cd ..:表示进入上一级目录

###pwd
+ 功能：print working directory,显示当前目录的绝对路径

###rmdir
+ 功能：remove directory，删除空目录，注意，是空目录。如果目录不为空，则无法删除。

###rm
+ 功能：删除文件或者目录（目录可以不为空），rm -rf tem
+ -r:删除目录
+ -f：force，强制执行

###cp：
+ 功能：copy，复制文件或者目录，cp -rp tem tem1
+ -r：复制目录
+ -p：property，复制的同时保持目录或者文件的属性

###mv
+ 功能：move，剪切或者重命名
+ 格式：mv  [源目录或者文件] [目标文件或者目录]

###clear
功能：清屏，实际上就是让光标所在行变为当前窗口的第一行

***

##文件处理命令

###touch
+ 功能：创建空文件
+ touch a：创建名为a的文件

###cat
+ 功能：查看文件内容
+ cat -n a
+ -n：显示行号

###tac
+ 功能：倒着显示文件内容
+ tac -n a

###more
+ 功能：分页显示文件的内容，常用于查看帮助文档
+ 空格或f：翻页
+ enter：换行
+ q：退出

###less
+ 功能：分页显示文件内容，但可以向上翻页。
+ 支持搜索功能：/string,查找string。
+ q：退出

###head
+ 功能：显示文件前几行，默认为10行。
+ head -n num file:显示文件的前num行

###tail
+ 功能：显示文件的最后几行，
+ -n： 行数
+ -f：实时显示文件的变化

###ln
+ 功能：生成链接文件
+ 格式：ln -s [源文件] [目标文件]
+ -s 创建软链接
    + 软链接文件：类似与windows下的快捷方式，文件权限：lrwxrwxrwx
   + 硬链接文件：可以同步更新。
   + 当原文件被删除时，软链接找不到文件，失效。而硬链接不受影响。
   + 硬链接文件的i节点和原文件的i节点相同
   + 硬链接不能跨越分区，也不能针对目录使用

***

##权限管理命令

###chmod
+ 功能：change the permission mode of files or directories,改变文件或者目录的权限。
+ 格式：chmod [{ugoa}{+-=}{rwx}]
+ u、g、o、a：分别表示所有者、所属组、其他、所有
+ +、-、=：分别表示增加权限、删除权限、赋值权限。
+ r、w、x：读、写、执行
+ 执行权限：文件所有者和root
+ -R：递归的操作目录下的所有子目录和文件


    [zmz@localhost tem]$ chmod g+w a
	
	
该命令向所属组增加对文件a的写权限

+ 另一种方式：权限数字，r:4,w:2,x:1

    
    [zmz@localhost tem]$ ls -l
    total 16
    -rw-r-----. 1 zmz zmz   51 Nov 16 04:50 a
	drwxrwxr-x. 3 zmz zmz 4096 Nov 16 04:28 dir1
	drwxrwxr-x. 2 zmz zmz 4096 Nov 16 04:25 linux
	drwxrwxr-x. 2 zmz zmz 4096 Nov 16 03:48 z
	
+ 6=4+2,即读和写的权利，该权利对应所有者
+ 4，即读的权利，该权利对应所属组
+ 0，没有任何权利，该数字对应其他人。
这种方式更加简单，直接。

###chown
+ 功能：change file owner，改变文件的所有者
+ 格式：chown 用户 文件或者目录
+ 权限：只有root用户可以修改文件的所有者

###chgrp
+ 功能：改变文件或者目录的所属组


##检索命令

###find

+ 格式：find [范围] [条件]
+ 功能：在某个范围内，查询满足条件的文件和目录
+ 精确匹配：

    [zmz@localhost tem]$ find /etc -name init
    
在/etc下，查找名为init的文件或者目录

+ 模糊匹配：

    [zmz@localhost tem]$ find /etc -name \*init\*


    + ‘\*’可以匹配任意字符串，所以上述命令的功能就是在/etc下查找名称种所有包含init的文件或者目录
    + ‘？’可以匹配任意单个字符

+ -iname: 不区分大小写
+ -size： 根据文件大小来搜索：+n，表示>n, -n , 表示<n, n表示等于，单位：数据块（512B）
+ -user: 根据所有者检索
+ -group:
+ -amin: 访问时间：-amin -6, 6分钟内被访问的
+ -cmin：文件属性change
+ -mmin：文件内容modify
+ -type： 文件类型，f：文件，d：目录，l：软链接文件
+ -a: 两个条件同时满足
+ -o: 两个条件满足一个就可以

    [zmz@localhost tem]$ find /home/zmz -user zmz -name a

+ 特例：find /etc -name init -exec ls -l {} \;
查询结果会用ls命令显示

##尽量少用find命令，因为会消耗很多系统资源##

###locate
+ 功能：查询文件
+ 格式：locate 文件名
+ locate是从一个数据库种进行查找，所以速度快，但对于新建的文件，可能存在更新延迟.
+ updatedb:更新数据库，（但/tmp不在更新范围内）
+ locate -i filename:不区分大小写

###which
+ 功能：查询命令所在的位置
+ 格式：which 命令


###wherhis
+ 功能：搜索命令所在目录以及帮助文档路径

###grep
+ 功能：在文件中搜寻字符串匹配的行并输出：
+ 格式：grep -iv [指定字符串] [文件]
+ -i：不区分大小写
+ -v：排除指定字符串

***

##帮助命令

###man
+ 功能：查询命令的帮助文档
+ 格式：man 命令名

###whatis
+ 功能：获取命令的简要信息
+ 格式：whatis 命令名称

###help
+ 功能：获得shell内置命令的帮助信息

###apropos 配置文件名
+ 功能：得到配置文件的简要信息
+ 格式：apropos 配置文件名

***

##关机命令

###shutdown:
+例子：shutdown -h now: 现在关机（halt）
+ -r: 重启
+ -c:取消上一条关机命令

###halt

###poweroff

###init 0

***

##重启命令：

###reboot

###init 6

###logout：退出当前登录

###runlevel：查看系统运行级别

***

##压缩命令

###gzip [filename]:
+ 压缩格式: .gz 
+ 压缩（只能压缩文件,不能压缩目录，不保留源文件）

###gunzip : 解压缩， 或者 gzip -d 

###tar: 
+ 打包目录
+ 格式： tar -cvf Japan.tar Japan
    + -c: 打包
    + -x: 解包
    + -v：显示详细信息
    + -f：指定文件名
    
+ tar -zcf Japan.tar.gz Japan(打包并压缩)

###zip
+ 压缩文件或者目录
+ -r ： 压缩目录

###unzip
+ 解压缩


###bzip2
+ 压缩文件
+ -k ： 保留源文件
+ tar -cjf Japan.tar.bz2 Japan

###bunzip2
+ 解压缩
+ -k：是否保持压缩包

***

##用户管理命令

###useradd
+ 功能：添加用户

###passwd 
+ 功能：设置用户密码

###who
+ 功能：查看用户登录信息，显示：用户名 登录终端
+ 
###w
+ 显示详细的登陆信息

###uptime
+ 查看系统启动了多长时间

***

##网络命令
###write
+ 功能：给用户发消息，Ctrl + D保存结束
+ 格式：write 用户名

###wall
+ 功能：write all，给所有用户发广播信息（自己也能收到）

###mail
+ 功能：查看发送邮件
+ mail 用户名：用户可以不在线买
+ mail：接收

###ping
+ 功能：测试网络连通性    
+ -c:ping 次数

###ifconfig
+ 功能： 查看和设置网络配置

###traceroute
+ 功能：显示数据包到主机的路径

###last
+ 功能：列出当前与过去登入系统的用户信息

###lastlog
+ 功能：列出一个给定用户或者所有用户的最后一次登录的信息

###netstat
+ 功能：显示网络相关信息
+ -t:TCP协议
+ -u：UDP协议
+ -l：监听
+ -r：路由
+ -n：显示IP地址和端口号

###mount
+ 功能：挂载U盘、光盘、移动硬盘
+ 例子：

    sudo mount -t ntfs-3g /dev/sda2 win

###umount
+ 功能：卸载文件系统

    
***

参考：[兄弟连2014年新版Linux视频教程](http://bbs.lampbrother.net/read-htm-tid-161465.html)

![vim logo](/img/vim.jpg)