---
layout: post
category: Jekyll
title: 基于Jekyll和github的个人博客搭建(初级)
tagline: by zmz
tags: [Jekyll, github]
---

本博客主要是分享小z在搭建个人博客过程中一些的经历。小z是菜鸟一枚，在搭建博客的过程中走了一些弯路，当然也积累了一些经验，就此写在这里，希望本文对那些想搭建基于Jeykll和github个人博客的人们有所帮助，谢谢。

<!--more-->

## 1. [Jekyll](http://jekyllcn.com/)

[Jekyll](http://jekyllcn.com/)是用来生成静态网页的一个工具。我们可以使用[markdown](http://baike.baidu.com/view/2311114.htm)语言来书写博客，但最终我们的博客是要通过浏览器来阅读的，那博客是怎么变为网页的呢？这就是Jekyll完成的任务。但是Jekyll会有自己的目录结构，下边我们会提到。好了，对于Jekyll大家了解这么多足足足以^_^

##2. [Github](https://github.com/)和Git

[Github](https://github.com/)是一个著名的开源代码库和分布式版本控制系统，其作者就是大名鼎鼎的[Linus Torvalds](http://baike.baidu.com/view/117611.htm?fromtitle=Linus+Torvalds&fromid=9336769&type=syn)——Linux操作系统的创始人。大多数人可能不懂“版本控制”，其实大家无需了解这一**程序员世界**的名词，只知道我们的网站存放在Github上就可以了。此外，存放在Github上的东西别人是可以免费下载的，除非你花钱，创建一个私人库，否则就是公共的，这就是开源的理念。

### 2.1 申请Github帐号

Github帐号的申请过程这里就不说了，登陆[Github官网](https://github.com/)申请就可以了。我想大家都能搞得定（什么？？你不会？？好吧，孩子，洗洗睡吧。）

### 2.2 安装Git或者Github for windows
虽然可以通过浏览器在Github上进行简单的操作，比如创建和修改文件，但是对于Github的操作远远不止这些，而且，我们在写博客的时候，通常先会在本地书写和调试，等内容和排版都在本地完成后才会提交到网上，所有这些操作都需要使用到一个工具——[Git](http://baike.baidu.com/subview/1531489/12032478.htm?fr=aladdin)。对于Git的简单使用推荐参考[这里](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000),作者对Git进行了简单而又详细地讲解（如果你有钱，而且感觉该Git教程对你有帮助，可以资助作者哦^_^）l。

<font color = "#ff0000">Windows用户的福利--Github for windows</font>
Github发布了windows版本的管理工具--Github for windows，[这里](http://pan.baidu.com/s/1pJlw0Tt)可以下载。由于小z平时在windows玩耍，所以果断选择该利器操作Github。Github for windows图形界面非常简单，而且还提供了Git的Shell窗口，如果是大神，可以选择命令行的方式操作，我等菜鸟还是在图形窗口下吧。安装过程一如既往的简单，双击，等待完成（什么？你又不会？还能不能愉快的玩耍了:)）。

好了，到这里，小z假定你已经有了Github的帐号，并且成功安装了Github for windows工具。我们离成功进了一小步——只要：进步，就是好的。

小z的Github帐号是：**DutZMZ**，下面会以该帐号讲解。

##3.搭建
###3.1 选择主题
要想搭建一个网站，涉及到的知识和技术绝对不是一点半点，HTML、JS、CSS……我们只是想建个博客，写写文字，上面那些技术还是留给屌丝程序员们去搞吧。

幸好Jekyll给我等菜鸟提供了很多[主题模板](http://jekyllthemes.org/)，只需下载下来，改改配置文件就能为我所用（当然，引用别人的东西最好找个地方注明，以表感谢）。

点击[这里](http://jekyllthemes.org/)，选择你喜欢的模板，如果都不喜欢，那就没办法了，小z也没有能力教大家从头到尾构造自己的网站。小z比较喜欢[simpleyyt](http://jekyllthemes.org/themes/simpleyyt/)这个模板，所以用这个模板给大家讲解。

下载完成后，解压，默认文件名为：simpleyyt.github.io-master


所以对于刚才我们下载的simpleyyt模板，解压后的文件夹命名为username.github.com，username为你的账户名。比小z修改后的文件夹名为：dutzmz.github.com

###3.2 建立repository：username.github.com
>在使用Github和Jekyll建立静态博客的过程中，必须要注意的一点是Github Pages分为两种，个人页面和项目页面,每个账号只能建立一个个人页面，个人页面的目的是展示这个账号的各种信息，个人页面的链接为https://username.github.io，在建立个人页面的repository时repo-name一定要是username.github.com，而且推送分支是master。
[原文在这里](http://www.jianshu.com/p/b6f3b03d5c15)

我们要建个人页面，所以根据我们的Github账号决定了个人链接，也决定了我们建立的repository的名称。比如小z的Github的账号为DutZMZ，则应该建立的repository为DutZMZ.github.com,对于推送分支，大家不用了解。登陆Github，建立一个repository，名称为username.github.com，老样子，username换成自己的账号名。小z建立的repository为：dutzmz.github.com. 此时我们的repository还是空的。

###3.3 将模板推送到新建的repository

打开Github for windows，登陆个人账号，然后克隆刚才我们新建的repository到本地，记录本地目录的位置，如小z克隆后的位置为：C:\Users\zmz\Documents\GitHub\dutzmz.github.com，然后将文件夹simpleyyt.github.io-master下的所有内容都拷贝到克隆文件夹：C:\Users\zmz\Documents\GitHub\dutzmz.github.com。

回到Github for windows，同步，就完成了我们的系统搭建。试试个人网址：http://username.github.io,奇迹出现了，我们的博客已经搭建好了，哈哈。

###3.4 写博客，并发布

最后，大家写的博客文件，要放在_post文件夹下，比如：C:\Users\zmz\Documents\GitHub\dutzmz.github.com\_posts下，然后使用Github for windows同步到Github网上就可以了。
**最重要的**

+ 文件名的命名规则为：YYYY-MM-DD-filename,其中YYYY-MM-DD为日期。
+ 每个模板都有自己的博客格式，最好先看看已有的博客是怎么样写的。比如simpleyyt的博客开始必须为一下内容：

{% highlight c %}

---
layout: post
category: android
title: 强化你的Terminal IDE——在android平板/手机上编写C/C++
tagline: by Snail
tags: [android, c++, c]
---
{% endhighlight c %}

其中layout不能改，category是博客的归类，可以修改。title是博客显示在网页上的名字，tagline表明作者，tags是博客内容的关键字，可以有多个，必须使用英文的逗号分隔，并且逗号后紧跟一个空格。

至此，我们完成了博客的搭建过程，当然，如果你有能力，可以对其中的一些内容作修改。这里，小z就不带大家一起做了。谢谢大家。
