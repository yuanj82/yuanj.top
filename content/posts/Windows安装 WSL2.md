---
title: Windows10 & 11 安装 WSL2
tags:
  - "Windows"
slug: y6w6f1s5
date: 2023-05-20 14:05:25
---

WSL2 现在已经是我离不开的工具了，实在是太好用了 ... 但安装方法与官网的略微不同，本文记录本人亲身实践的最好的安装方法。

<!--more-->

## 什么是 WSL？

适用于 Linux 的 Windows 子系统可让开发人员按原样运行 GNU/Linux 环境 - 包括大多数命令行工具、实用工具和应用程序 - 且不会产生传统虚拟机或双启动设置开销

简单理解，WSL 为用户提供了一个基本完整的 GNU/Linux 环境，用户无需安装虚拟机或者双系统即可使用 GNU/Linux 中的软件

WSL1 是最初发行的版本，但是比较鸡肋，I/O 性能很差，并且没有 Linux 内核，所以有很多 Linux 程序都无法云效，比如 Docker，WSL1 更像是基于 Windows 内核的一个翻译层，实际上工作的还是 Windows 而不是 Linux

WSL2 是 WSL1 的升级版，提供了更好的文件系统性能和更完全的 Linux 系统内核支持，WSL2 使用虚拟化技术在轻量级虚拟机 (VM) 中运行 Linux 内核，同时保留了 WSL1 的操作体验，可以把通过 WSL2 启动的 Linux 系统认为是虚拟机中的一个 Linux 系统，因此，相对于通过用户模式和内核模式组件构成兼容性底层来运行 Linux 的 WSL1 来说，WSL2 的 Linux 系统更完整，功能更完善。例如，WSL1 不支持 Docker，而 WSL2 可以以原生的方式运行 Docker

## 检查 Windows 版本

打开 Windows 设置——系统——关于——操作系统内部版本

需要内部版本为 18362 或更高，版本号为 1903 或更高，也就是说 LTSC2019（1809）是无法使用 WSL2 的

## WSL2 的安装

以管理员身份打开 Powershell ，分别运行下列两行代码

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

等待功能启用完成后重启电脑，重启完成之后再打开 Powershell，运行下列代码

```powershell
wsl --update
```

将 WSL 更新至最新版

## 安装发行版

点击 [此处](https://www.microsoft.com/store/apps/9PN20MSR04DW) 打开微软商店安装 Ubuntu22.04LTS，当然，也可以安装其他的发行版

安装完成之后打开 Ubuntu22.04LTS，输入用户名和密码

注意：密码需要输入两次，且输入时不会显示密码

## 配置（可选）

### 换源

由于大多数 Linux 发行版的默认软件仓库在国外，所以我们需要将软件源更换为国内的以提高下载速度，以 Ubuntu 为例

输入下列命令使用中科大软件源

```bash
sudo sed -i 's@//.*archive.ubuntu.com@//mirrors.ustc.edu.cn@g' /etc/apt/sources.list
```

更多细节和其他发行版配置可以查看 [中科大软件源配置](https://mirrors.ustc.edu.cn/help/index.html) 和 [清华软件源使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)

### 更新系统

换源完成后输入下列命令更新系统和软件包

```bash
sudo apt upate && sudo apt upgrade
```

## Ubuntu 常用命令

Ubuntu 中安装和卸载软件包使用的是 apt 软件包管理器，下面是一些常用的命令

```bash
## 查看一些可更新的包
sudo apt update

## 升级安装包
sudo apt upgrade

## 安装指定软件包
sudo apt install <package_name>

## 卸载指定软件包
sudo apt remove <package_name>

## 查找软件包
apt search <package_name>

## 列出可更新的软件包
apt list --upgradeable

## 清理不再使用的依赖和库文件
sudo apt autoremove
```

关于 conda 的安装可以参考此文章 [《Linux 下安装 conda》](https://www.yuanj.top/2023/05/01/linux/20230501/)
