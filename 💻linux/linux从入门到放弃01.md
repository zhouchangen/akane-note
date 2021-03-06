---
title: linux从入门到放弃——01linux家族的过去
date: 2017-8-6 13:42:00
categories: Linux
comments: true
---

# linux从入门到放弃——01linux家族的过去

我又开了一个坑，这一次我打算做一系列有关于linux的栏目，不能说是教程，只是写下我学习ubuntu的一个过程和心得，大概的内容我想好了，我会把我怎么学ubuntu的过程一点点写下了，遇到了哪

些问题，然后为什么要学linux，以及linux有什么用，我有点喜欢IT历史，所以文章的内容可能会讲一下历史。大家都用过微软家的windows操作系统吧，还有没用过也听过的苹果家的OS系统，那么问

题来了，这个linux系统是什么？很多人可能没有听说过，所以也就不会想着去学习，毕竟windows用的挺好的人不会去折腾这些东西。恩，在这里说一下，如果你是想要成为一个程序员的话，就可以

来看看这些教程，了解一下linux。如果你是想尝鲜用用其他系统也可以来看看，说不定看了几篇后你就决定放弃然后卸载了。然后我们先来说说linux是什么，不是说ubuntu吗？不，还是得先介绍一下

linux历史，了解了linux有利于后面的学习。

<img src="images/linux/qi.jpeg" width="300px" align="center"/>



Linux是一套免费、开源的类Unix的做服务器的操作系统，是一个基于POSIX和UNIX的多用户、多任务、支持多线程和多CPU的操作系统，它的特点就是：稳定、安全、处理多并发。相比起windows

的话会安全很多，可能有人会说了，我装个360或者电脑管家也很安全啊，这个正是因为linux的安全其实很少黑客会去入侵linux系统，反而因为windows没有那么安全，所以导致了被大量黑客入侵，

这个为什么说安全后面再介绍。它能运行主要的UNIX工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。

那这个unix又是什么，可能对历史有点了解的人都知道其实以前的计算机是非常巨大的，而最先开始的就是unix系统，不像我们现在身边的设备那么小，在那个时候很少有人能够用得起计算机，一般

都是一些大型的公司或者研究所，像我们现在的普通人真的是想都不敢想。而在1973的时候出现了以后改变时间的好消息，就是unix源代码开源了。这时候就有很多公司觉得以后unix绝对是未来，所

以都跑去研究了起来。像著名的伯克利分校搞了BSD，这个伯克利分校里面也是有很多牛逼的人物。IBM公司的AIX系统，sun公司的solaris系统，hp的hp unix。到后面出现了一个叫minix的操作系

统，这些都是基于unix开源而来的。那么这linux到现在都没讲到，又是怎么来着呢？

<img src="images/linux/linus.jpeg" width="300px" align="center"/>



1991年的时候，在当时有个在芬兰读书的小伙子，叫做linus Torvalds（林纳斯·托瓦兹，瑞典籍芬兰人，瑞典发音有点类似[林new克思]，但是英语有点类似[利尼克思]）开始在一台386sx兼容微机上学

习minix操作系统，这个人的故事也特别牛逼，他从小就开始接触计算机了，那时候他跟他外公学到了很多的东西当时他的外公是有名的教数学的教授。当时在1991年4月linus就开始动手想编制自己的

操作系统。说牛人就是牛人，在1991 年4 月13 日的时候他就在comp.os.minix 上发布说自己已经成功地将bash移植到了minix 上，而且已经爱不释手、不能离开这个shell软件了，这个时候论坛上就

炸了，他的学长学姐什么的还有很多人都觉得很好所以就开始动手加各种功能，其遵守的是GPL协议，linus就把他开源了。

<img src="images/linux/lin.png" align="center">



然后1991年7月3日，第一个与Linux有关的消息是在comp.os.minix上发布的（但是那时候还不叫Llinux，当时linus的脑子里想的可能是FREAX（怪诞的、怪物、异想天开的意思）。说起来linux这个名

字也由很多由来，说法不一，比较有名的几个说法是，大家觉得这东西实在太好了，所以就由你的名字来命名吧，但是这个牛人就比较低调说，说不行不行。其实也是情有可原，因为linux是由linus发

起的，准确的来说其实linux不是由他一个人做出来的。那这个时候怎么办，就换一个。要不叫linux，大家一看觉得行，就这样定了。其中这个x有混合的意思。也有说是指：linux is not unix。就是说

linux不是unix，它有自己的特点，在当时还有一些大佬特定写了《Unix痛恨者手册》就是用来专门批评unix的，我觉得如果有时间的话我想买一本来看看。



然后1991年的10月5日，林纳斯·托瓦兹在comp.os.minix新闻组上发布消息，正式向外宣布Linux内核的诞生（Freeminix-like kernel sources for 386-AT）。1993年，大约有100余名程序员参与了

Linux内核代码编写/修改工作，其中核心组由5人组成，此时Linux 0.99的代码大约有十万行，用户大约有10万左右。1994年3月，Linux1.0发布，代码量17万行，当时是按照完全自由免费的协议发

布，随后正式采用GPL协议。然后有一些公司就觉得linux是未来的趋势，于是又纷纷来研究linux。可能有人就说了，我听过什么Ubuntu、openSUSE、Fedora、centOS、redhat、debian，怎么没有

这个叫LINUX的，但是因为严格来讲，Linux这个词本身只表示Linux内核，就是其实人们已经习惯了用Linux来形容整个基于Linux内核，并且使用GNU工程各种工具和数据库的操作系统。在1995年1

月，Bob Young创办了RedHat公司（小红帽），对linux做了个桌面，以GNU/Linux为核心，搞出了一种冠以品牌的Linux，即RedHat Linux,称为Linux"发行版"，在市场上出售。这在经营模式上是一种

创举。



linux主要作为linux发行版（称为“distro”）的一部分使用，从桌面系统上开看，主要是分为redhat和debian两大分支，姑且在这里把linux称为一颗树根（root），在1993年中，linux这根开始了两个分

支：Debian和Slackware，在1994年Slackware又分支了SUSE，年底的时候在两个分支基础上出现了我们说的redhat（红帽），此后在1996年在红帽分支上出现了Conectiva，98年红帽上又出现了

Mandrake。在1999年，红帽分支上有了我国的红旗linux。然后在2003-2005年这期间发生了巨大变化。14年下半年的时候，Debian上出现了ubuntu分支，而且发展的很强大。我们后面所看到的

Xubuntu、Kubuntu这些都是基于ubuntu的。

<img src="images/linux/linux祖谱.gif" width="400px" align="center"/>



这里说一下ubuntu（译为吾帮托或乌班图），意思是”人性“、“我的存在是因为大家的存在”，是非洲传统的一种价值观，类似华人社会的“仁爱”思想。ubuntu基于Debian发行版和unity桌面环境，与

Debian的不同在于它每6个月会发布一个新版本,ubuntu在次版本号中如果是偶数就说明是长期支持版本，做服务器当然是选择稳定的了。Ubuntu的目标在于为一般用户提供一个最新的、同时又相当稳

定的主要由自由软件构建而成的操作系统。而且ubuntu社区资源非常丰富，可以方便从社区获得帮助。如果我们要想学习linux的话，其实选择Ubuntu就好了。很多人都在纠结我到底是在用哪个比较

好，这个问题适合问知乎大神。其实哪一个都可以用来学习，如果想学习的话就选择一个然后一直学。有人折腾了很久结果就只是在linux各版本间不断的重装。我这里用的ubuntu，如果按照流行的说

法是说比较适合新手，对新手比较友好，而且论坛，社区这些比较活跃，资源也很丰富，当你遇到一些问题的时候可能很快就能找到答案了。

在今天来说，linux受到了越来越多的人喜欢。



然后这些这么多的版本还是开源的，那么主要编写的人是哪些？在这里所有的发行版本其实都是基于linux写kernel，主要是有个人、松散组织的团队、以及商业机构和志愿者组织编写。一个Linux发行

版包括：linux内核，一些GNU程序库和工具，命令行shell，图形界面的XWindow系统和相应的桌面环境，如KDE和GNOME，并包含数千种从办公套件，编译器，文本编辑器到科学工具的应用软

件。Linux存在着许多不同的Linux版本，但它们都使用了Linux内核。Linux可安装在各种计算机硬件设备中，比如我们的手机、平板电脑、路由器、视频游戏控制台、台式计算机、大型机和超级计算

机。操作系统其实可以简单的分为四个类：PC、智能设备、服务器、嵌入式系统。。而linux主要就是用来服务器开发的。linux的主要方向就分为两个，一个是linux系统管理员，这个需要非常熟悉

linux操作系统。还有一个就是linux程序员，这个需要你会C/C++/Java/PHP这些的一种或者多种，或者做linux软件工程师（PC），linux嵌入式开发（单片机芯片）。



恩，今天就到这里先吧，这一篇没讲到什么内容，就是说一下linux的历史，起码你知道了什么是linux，linux怎么来的，然后linux主要是用来做服务器的。下一篇的话我打算开始介绍linux的安装和使用。感谢你的喜欢和支持。我们下期再见！

