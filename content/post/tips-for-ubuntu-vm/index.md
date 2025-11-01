+++
author = "Found Not George"
date = "2024-10-05"
draft = false
slug = "tips-for-ubuntu-vm"
title = "Ubuntu Server 22.04 避坑和配置指南"
description = "配置 Ubuntu 虚拟机我踩过的坑和一些提示，可用于借鉴"
tags = [
    "ubuntu",
    "linux",
    "虚拟机",
]
categories = [
    "笔记", 
    "操作系统大杂烩",
]

image = "https://raw.gitcode.com/areacloser/images-for-hugo-blog/raw/main/DM-20241006112629-001.png"
+++

## 前言

先展示一下成果 ~

![neofetch结果](https://raw.gitcode.com/areacloser/images-for-hugo-blog/raw/main/nf.png)
_Neofetch一下_

![lxmusic运作图](https://raw.gitcode.com/areacloser/images-for-hugo-blog/raw/main/lx.png)
_LX Music_

仔细算算，我花在这个虚拟机上的时间已经超过了15个小时，真可谓道阻且长！

话不多说，下面就是正文了

## 开始之前

### 系统信息

我使用的宿主机是Windows7系统，硬件比较古早，这里只展示部分信息：

| 项目       | 值                   |
| :-------- | :------------------- |
| CPU       | Intel i5-2400        |
| 内存       | 8 GB                 |
| 显示适配器  | Intel(R) HD Graphics |

### 软件安装

#### 安装之前的准备

在最最开始前，必不可少的是打开CPU功能中的虚拟化功能（Intel VT-x 或 Hyper-V），不然虚拟机的功能无法正常实现。不过好像这个功能大部分计算机是默认开启的（比如我就读高中的班级电脑），可是我家电脑却不默认开启。

想要开启它，须要进入BIOS设置界面，常规做法是在开机出现Logo时按下F2键，但我家电脑要按Del键，这由主板型号而定。具体可参考[这篇文章](https://zhuanlan.zhihu.com/p/34223088)。

#### SSH & VNC 连接软件

虚拟机自带的用户界面实在难以入眼，加之后续需要建立VNC连接，找一个合适的远程连接软件是必要的。

大部分人SSH连接都会选择使用 XShell， 但它有两个缺点：

1. 不支持VNC连接
2. 终端配色有时会出问题（如运行Neovim时）

因而此处我推荐使用 MobaXterm 作为替代，它虽然没有汉化，但能完美解决 XShell 的问题。

#### 虚拟机软件

市面上常用的虚拟机软件有两个：`VirtualBox` 和 `VMWare`。

如果各位和我一样是Windows7的钉子户的话，那么请务必 不 要 使用`VirtualBox`！虽然它免费开源，但它出的问题不是我们能够解决的（不支持Windows7）。至于收费的`VMWare`，我想大家应当能各显神通，找到合适的学习版。不过使用Windows7的同学们又要注意了，`VMWare`在 Workstation 15 之后的版本后都不再支持Windows7，所以请不要找更高的版本了！

#### 镜像文件

官网的下载速度十分令人堪忧，建议使用[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/jammy/ubuntu-22.04.5-live-server-amd64.iso)来获取系统镜像。

## CLI界面

### 安装系统

具体教程可参考一下几篇教程，都写得很详细：

- [VMware 虚拟机图文安装和配置 Ubuntu Server 22.04 LTS 教程 - 博客园](https://www.cnblogs.com/calvinit/p/16350378.html)
- [vmware中安装 ubuntu 22.04 live-server - 博客园](https://www.cnblogs.com/abc1069/p/16296050.html)
- [vmware 安装 ubuntu server 22.04.2 LTS记录 - 知乎](https://zhuanlan.zhihu.com/p/627023160)


这里基本按着文章里的做就可以了，但有两点我要提醒：

1. 配置网络连接时建议先用DHCP（后面可以改回固定IP），不然可能会导致IP冲突。
2. 配置磁盘空间时一定要至少分配40G的空间！不然后续安装图形化界面时会空间不足！这是我亲身经历的，它让我被迫一切从头开始，损失惨重！并且建议将所有的剩余空间（free-space）都用掉，反正留着也没用，不如都分配给根目录（/）

### 配置SSH连接

在进行SSH连接前，要先配置端口转发。同样，推荐一些文章教程：

#### 端口转发

- [VMware做NAT端口转发(全网最详细步骤) - CSDN](https://blog.csdn.net/weixin_49047967/article/details/132555042)
- [VMware虚拟机配置端口转发（端口映射） - 博客园](https://www.cnblogs.com/testgo/p/10368999.html)

然后是SSH配置：

#### SSH 配置

- [Ubuntu 22.04.3 Server 安装sshd和开启root账号ssh远程 - CSDN](https://blog.csdn.net/gmaaa123/article/details/135162819)
- [如何在 Ubuntu 上启用 SSH（适用于 20.04、22.04）？ - 阿里云](https://developer.aliyun.com/article/1488008)

同样，我有一点要提醒，在SSH连接时，有可能出现`Error503`问题，即建立SSH连接后无法进入命令行界面。虽然其中的原因尚不明确，但是更换端口转发时的IP地址然后重新连接就能解决问题。

### 更换地区和语言

正常情况下，刚安装好的Ubuntu的默认语言是英语，即`locale`是`en`。更换为中文不是必需的，但我仍然建议大家换掉，因为这会影响后续图形化的语言配置。当然，如果对自己的英语水平有足够的信心，就当我没说。

想要更换语言，需要在命令行中执行以下操作[^fn1]：

```bash
sudo apt-get install language-pack-zh-hans
sudo update-locale LANG=zh_CN.UTF-8
```

最后重启系统即可。

## GUI 界面

由于最开始安装的是Server版，所以此时的Ubuntu是没有图形化界面的，但是我们可以为其安装一个（甚至多个）桌面环境！而想要多快好省地完成这一重任，我们就要请出下面这个神器：

TMOE（天萌管理器）

![2moe头像](https://raw.gitcode.com/areacloser/images-for-hugo-blog/raw/main/2moe.png)
_没找到TMOE的图标就用二萌的头像代替了_

### 题外话

说到这里，我不得不提一下TMOE的作者2moe。TA虽然基本不再更新管理器，但一直在gitee社区活跃出现，而且十分负责地答疑解惑，确实非常有责任心！

（在线催更TMOE2025）

### 进入TMOE之前

在开始图形化之前，墙裂建议各位先切换为普通用户，如果一直使用root用户来安装软件会遇到许多不便（很多软件会因安全问题而停止运行）

### 进入TMOE

#### 安装桌面
Ubuntu虚拟机应该是默认自带`curl`下载器的，如果没有请使用以下命令安装`curl`：

```bash
sudo apt update
sudo apt install -y curl
```

然后键入以下命令安装TMOE管理器[^fn2]：

```bash
bash -c "$(curl -L gitee.com/mo2/linux/raw/2/2)"
```

接下来就是漫长的安装过程了，这里按照提示走就行，记得把下载源设置为gitee，不然速度会更酸爽。桌面环境我选择了Xfce，因为它够轻量，而且可定制性很强。还有一点，设置VNC时最好使用`TigerVNC`它的优化和功能都比`TightVNC`要好。

还有一点，在配置完VNC后，记得回到虚拟机处再配置相应的端口转发。

#### 体验强大功能

到这里时，桌面应该就能在VNC中正常显示了，但可以看到它仍处于百废待兴的状态：

- VNC显示比例不合适
- 界面比较丑陋
- 默认浏览器打不开
- 应用十分稀少

而这些，TMOE中早已有了对策————所有这些问题都可以在上面一站式解决！（其实还需要一点场外援助）

下面就可以自由探索TMOE并美化自己的终端和桌面了！在这里我提供一些思路供参考：

- 去[xfce-looks网站](https://www.xfce-look.org/browse/)上下载主题和图标包并使用TMOE本地主题安装器来安装（推荐Orchis-Dark主题和Papirus-Dark图标包）
- 安装Chromium或Edge-dev浏览器
- 安装Terminology终端模拟器并[将其设置为默认终端](https://developer.baidu.com/article/detail.html?id=3116569)
- 安装Bingle以获取必应每日精选壁纸（有WE的去安装Steam更好）
- 安装Tmoe-Zsh强大Shell

## 更多

### 一些问题&解决方案

#### 自动死机

这是我遇到的一个奇怪的问题。在桌面环境闲置一段时间后整个虚拟机会被自动挂起（suspend），即SSH和VNC乃至虚拟机界面全都卡死无法操作。在我眼中这跟死机没有任何不同，但之前在手机上使用PRoot容器配置桌面环境时从未出现过这个问题

我尝试过禁用虚拟机的挂起功能，也曾尝试修改`/etc/systemd/logind.conf`{: .filepath}[^fn3]，还查看过Xfce设置中的电源选项，但都无功而返。

最后禁用了系统休眠服务就解决了，方法如下[^fn4]：

1. 在终端中输入命令：

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

2. 查看服务是否禁用成功：

```bash
sudo systemctl status sleep.target suspend.target hibernate.target hybrid-sleep.target
```

#### 终端字体显示乱码

在安装Tmoe-Zsh（尤其是使用Powerlevel10k主题）后终端可能会出现乱码，像这样：

![UXterm截图](https://raw.gitcode.com/areacloser/images-for-hugo-blog/raw/main/uxterm.png)
_X11的自带终端是真飞舞_

这时我们就需要找到一款合适的字体来显示其中的符号了，那就是————[Nerd Fonts](https://github.com/ryanoasis/nerd-fonts/releases)！

这里推荐几款不错的字体，只要下载并安装它们，你的终端就会变得炫酷无比！

- [Inconsolata](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.2.1/Inconsolata.zip)

![Inconsolata截图](https://vikingz.me/static/1fbf553fca61382abd4eb7f33cd214e2/799d3/in.png)
_Inconsolata截图_

- [JetBrains Mono](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.2.1/JetBrainsMono.zip)

![JetBrains Mono截图](https://vikingz.me/static/57aee86eafc6be8c0f4c6a53c25d07be/acd90/jetbrian.png)
_JetBrains Mono截图，说实话这个字体文件实在太大了_

- [FiraCode](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.2.1/FiraCode.zip)

![FiraCode截图](https://vikingz.me/static/1fd8d120322043e3b6e6accf2d771353/bbd4b/ligatures.png)
_FiraCode截图_

#### root情况下Electron应用打不开

这个解决方法比较简单，只需要在启动参数最后面加上`--no-sandbox`就可以了，不过浏览器会在顶部弹出警告栏，让人心烦。

### 未完成的事情

#### 配置编辑器

由于Ubuntu软件源比较落后，很多程序用`apt`都下不了最新版本，比如Neovim，通用稳定版都出到0.10了，Ubuntu还在倒腾0.6的版本。无奈我只能使用Unstable不稳定源，结果就是电脑上Neovim每天都更新而且bug奇多。

![我的Neovim现状](https://raw.gitcode.com/areacloser/images-for-hugo-blog/raw/main/nivm.png)
_已经彻底废了_

至于Emacs，我之前在手机上也曾尝试过配置，但仍以失败告终，现在它在我桌面上只是个摆设了。

还有VSCode，我认为我的电脑没有这个能力运行。

#### 本地搭建个人网站

正如你所看到的，这个网站是经由Github Actions搭建，以及Github Pages部署和托管而成的，我曾尝试将工作环境转移到这台虚拟机上，但种种问题使得我的计划不了了之：

- 本地Ruby包依赖管理器Bundler与Github不匹配，但受制于网络，无法自动获取对应版本的Bundler
- Obsidian编辑器在虚拟机上无法正常工作
- 无法正常输入中文（见下一节）

#### 设置中文输入法

网上关于配置中文输入法是无论是iBus框架还是Fcitx框架，都需要在“语言支持”选项中切换语言为汉语，但不知为何我的语言选项中汉语被禁用了，但又处于已安装状态，这导致我不能在虚拟机内输入中文而必须要在外部创建一个文本交换文件，用着真的很不方便。

![语言支持截屏](https://raw.gitcode.com/areacloser/images-for-hugo-blog/raw/main/lang.png)
_这是为什么呢_

#### 配置开机自启动脚本

> 起因：每次登录进Ubuntu是桌面环境都是默认关闭的，需要手动输入startvnc命令才能开始VNC连接，如果提前连接MobaXterm会卡死，因此想让虚拟机一开机就启动桌面环境。

许多开机自启动的教程都是像这样：

- [Ubuntu22.04 rc-local 配置开机自启动脚本 - 博客园](https://www.cnblogs.com/king-dom/p/17371747.html)
- [ubuntu22.04 添加开机启动脚本 - 博客园](https://www.cnblogs.com/ALice1024/p/17302426.html)

其基本思路不外乎修改或创建系统服务，但是在Ubuntu虚拟机上这行不通。修改`rc-local.service`服务后，我的虚拟机卡在开机界面进不去，自己创建的服务又无法正常工作（事实上桌面会直接卡死）。

最后只能使用`.bashrc`，好像也没什么用（我的默认终端是Zsh，可能是要修改`.zshrc`）。

## 后记

明天叕要开学了，假期过得真是快呀。还有很多的东西要学，还有很多的东西想学，不知何时能够再更一次？

本来还想发一期WxPython学习笔记的，现在只好作罢。

总之，下次再会吧！

## 脚注

[^fn1]: [ubuntu 22.04 server设置中文 - 知乎](https://zhuanlan.zhihu.com/p/641482808)
[^fn2]: [Tmoe-linux：在Android 和Windows10上一键安装 GNU/Linux容器 - 我的旅游记录](https://haohe.fun/2021/04/tmoe-linux%EF%BC%9A%E5%9C%A8android-%E5%92%8Cwindows10%E4%B8%8A%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85-gnu-linux%E5%AE%B9%E5%99%A8)
[^fn3]: [ubuntu使用－ubuntu22.04关闭自动挂起功能 - 博客园](https://www.cnblogs.com/nangongergou/p/18042535)
[^fn4]: [Ubuntu防止休眠和挂起（笔记） - CSDN](https://blog.csdn.net/VictoriaLy/article/details/134684502)
