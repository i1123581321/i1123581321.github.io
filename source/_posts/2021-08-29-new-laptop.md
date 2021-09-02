---
title: 记录一次主力机的迁移工作
mathjax: false
documentclass: ctexart
classoption: UTF8
geometry:
  - margin=1in
  - a4paper
date: 2021-08-29 23:38:30
tags:
- "life"
categories: ["life"]
---

事情的起因是现在的主力机已经用了四年多了，除去电池拉跨的问题，不可扩展的内存和硬盘也为后续的使用带来了重重困难，因此决定换一台新的电脑作为主力机。也在此记录一下整体的迁移过程。

## 事前准备

打开包装，确认了电脑和电源适配器和三包凭证都在，ABCD 面没什么划痕，屏幕完好。强迫症作祟总之先把电脑上贴的 AMD 标抠了。插电开机。

重装系统，安装普通的家庭版。检查了下屏幕坏点，键盘触控板以及硬盘通电时间。新系统没有驱动，从官网下载了驱动手动安装，这里比较失策的一点是没有准备以太网线，把驱动全装了一遍才识别出无线网卡来。成功联网后激活系统，开始更新，登录 MS 账户，打开 wd 的内核隔离。

## 基础软件

首先安装一些基本使用需要的软件。

安装 7z（program files），最好用的解压缩软件，没有之一。安装 clash for windows（user/local），科学上网必备。安装 FDM（user/appdata/local），用于下载软件。新版 Windows 自带 chromium 内核的 edge，只需要更新一下即可。从 MS 官网安装 office2016。

在 MS 商店安装 translucentTB（美化），snipaste（截屏软件），QQ 桌面版。安装 MS 商店的 QQ 是因为不管是桌面 QQ 还是 TIM 都有 QQProtect 服务。

安装 iTunes（program files），音乐管理。安装 calibre（program files x86），电子书管理。安装 potplayer（program files），k-lite codec（program files x86），lavfilter（program files x86），madvr（user/local），并根据 vcb-studio 的教程设置好播放器。

安装 everything（user/local），用于文件索引。安装 paint.net（program files），用于简单的图片操作。安装 resilio sync（user/appdata/roaming）。安装 screen to gif（user/local），用于录屏，暂时没有安装 OBS 的需求。

安装 stellarium（program files），星空软件。

安装 typora（user/appdata/local/programs），目前最好用的 MD 写作软件。

安装 crystal disk info（user/local），检测硬盘状态。安装 rapid ee（user/local），管理环境变量。安装 space sniffer（user/local），监测硬盘空间。

安装 utorrent（user/local），PT/BT 下载软件。

安装腾讯会议。

至此基本可以作为日常机使用。

## 编程环境配置

使用 rapid ee 时刻管理环境变量。

安装 powershell7（program files）以及 Cascadia code 字体，Cascadia code 作为主要的等宽字体使用。

安装 windows terminal，vscode（user/appdata/local/programs）。安装 git（program files）。

安装 msvc（program files x86），主要是为了可能会依赖 MSVC 的其他软件，没有考虑安装 MingW 是因为如果有 C/CPP 编程需求就直接 WSL 了。安装 miniconda3（user/local），用于 python 以及 ml 需要的环境。安装 jdk11，gradle，maven（user/local），主要是 Java 环境和对应的环境管理工具。

安装 nodejs（program files），目前是为了博客更新（hexo）。安装 dotnet（program files）以及 powertoys（program files）。

安装 pandoc（user/local），主要用于各种格式转 pdf。

安装 texlive（user/local），latex 写作。

安装 jetbrains toolbox（user/appdata/local），然后安装 idea 和 pycharm 免费版。

安装 virtual box，然后安装 windows 虚拟机，把一些不常用的国产软件（网易云/百度云盘/……）丢进去。

按照 MS doc 的教程安装 WSL2。

## 总结

总的来讲过程中没什么波澜，大部分软件官网/github 下载安装包后一路 next 完事，最麻烦的大概是通过网络安装的 texlive，国内网络环境足足下载了两个半小时，还是直接下载完整安装包比较好。剩下的大概就是些换源之类的琐碎小事，好在 vscode 的设置可以同步，jb 家的 ide 也没多少要改的。至此整个迁移工作基本完成，历时约三天，数据转移过去以后就可以无缝衔接了。