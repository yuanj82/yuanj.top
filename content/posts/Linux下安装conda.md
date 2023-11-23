---
title: Linux 下安装 conda
tags:
  - "Linux"
  - "Conda"
slug: z9w5y9m7
date: 2023-05-01 22:59:01
---

做生信的话，conda 应该是必要的软件之一吧？记录一下安装过程以及源的配置。

<!--more-->

## 什么是 conda？

Conda 是适用于任何语言的软件包、依赖项和环境管理系统--包括 Python，R，Ruby，Lua，Scala，Java，JavaScript，C / C ++，FORTRAN 等

Conda 是在 Windows、macOS 和 Linux 上运行的开源软件包管理系统和环境管理系统。Conda 可以快速安装、运行和更新软件包及其依赖项。Conda 可以轻松地在本地计算机上的环境中创建，保存，加载和切换。它是为 Python 程序创建的，但可以打包和分发适用于任何语言的软件

Conda 作为软件包管理器，可以帮助您查找和安装软件包。如果您需要一个能够使用不同版本 Python 的软件包，则无需切换到其他环境管理器，因为 conda 也是环境管理器。仅需几个命令，您就可以设置一个完全独立的环境来运行不同版本的 Python，同时继续在正常环境中运行喜欢的 Python 版本

在默认配置下，conda 可以安装和管理来自 repo.anaconda.com 仓库的 7,500 多个软件包，该仓库由 Anaconda 生成，审查和维护

Conda 也可以与 Travis CI 和 AppVeyor 等持续集成系统结合使用，以提供对代码的频繁，自动化测试

所有版本的 Anaconda， Miniconda 和 Anaconda 存储库均包含 conda 软件包和环境管理器 。Conda 也被包含在 Anaconda Enterprise 中，该公司为 Python，R，Node.js，Java 和其他应用程序堆栈提供现场企业包和环境管理。Conda 还可以在社区频道 conda- forge 上获取 。当然，也可以在 PyPI 中获取 Conda，但是通过这种方法可能不是最新的

## 下载安装包

使用下列命令来下载 miniconda3 的安装包

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py38_4.12.0-Linux-x86_64.sh
```

如果系统提示找不到命令“wget”，那么使用下列命令安装 wget

```bash
#Debian 系，如 Ubuntu、Debian、deepin、Linuxmint
sudo apt install wget

#CentOS 系，如 CentOS 7
sudo yum install wget

#Fedora 系统
sudo dnf install wget

#Arch 系，如 manjaro、Arch
sudo pacman -S wget 
```

## 安装 miniconda3

下载完成之后，使用下列命令安装 miniconda3

```bash
bash Miniconda3-py38_4.12.0-Linux-x86_64.sh
```

安装过程中会遇到下列几个问题

```bash
#需要阅读开源协议，一直按回车键即可
Welcome to Miniconda3 4.7.12

In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
>>> 
===================================
Miniconda End User License Agreement
```

```bash
#是否接受开源协议，输入 yes
do you accept the license terms? [yes|no] [no] 
>>> Please answer 'yes' or 'no':' 
>>>
```

```bash
#指定安装位置，按回车使用默认位置即可
## Miniconda3 will now be installed into this location: 
## /root/miniconda3 
## 
## - Press ENTER to confirm the location 
## - Press CTRL-C to abort the installation 
## - Or specify a different location below 
[/home/USER_NAME//miniconda3] >>>
```

```bash
#安装完成，是否进行初始化，输入 yes
installation finished. Do you wish the installer to initialize Miniconda3 by running conda init? [yes|no] [no] 
>>> yes
```

## 更换清华源

由于 conda 默认的源在国外，经常会出现网络问题而导致下载失败，所以我们需要换源

可以参照清华大学开源镜像站的说明 https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/

安装完成之后输入下列命令编辑 conda 源

```bash
sudo nano ~/.condarc
```

同样，如果系统提示找不到命令“nano”，那么使用下列命令安装 wget

```bash
#Debian 系，如 Ubuntu、Debian、deepin、Linuxmint
sudo apt install nano

#CentOS 系，如 CentOS 7
sudo yum install nano

#Fedora 系统
sudo dnf install nano

#Arch 系，如 manjaro、Arch
sudo pacman -S nano
```

在编辑窗口中粘贴下列内容

```bash
channels:
  - conda-forge
  - bioconda
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
auto_activate_base: false
```

按 Ctrl+S 保存后按 Ctrl+X 退出

## 创建虚拟环境

使用下列命令

```bash
conda create -n env1 python=3.7

#-n 后面是虚拟环境的名称，自己指定
#python=3.7 说明创建一个 3.7 版本的 python 环境，根据自己的需要可以更换
```

等待一小会之后会列出创建此环境所需的软件包，输入 y 确定即可

## 安装软件

先激活虚拟环境

```bash
conda activate env1
#以上一步创建的环境为例
```

如果我要搭建一个生信分析环境，那么需要安装 hmmer、samtools 等工具，激活环境后使用 conda install 命令安装

```bash
conda install hmmer samtools hisat2
```

同样，列出所需软件包之后输入 y 回车确定

## 退出环境

使用完成之后需要退出环境

```bash
conda deactivate
```