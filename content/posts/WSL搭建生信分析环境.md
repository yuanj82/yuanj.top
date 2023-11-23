---
title: WSL 搭建生信分析环境
date: 2023-08-15T20:49:31+08:00
tags:
  - "WSL"
  - "生物信息学"
slug: jdksf7d9
---

在做生信分析的时候，很多情况下都会用到 Linux 环境，而大多数人的电脑都是 Windows 环境，目前 Windows 下使用 Linux 最主流的两种方法就是服务器和虚拟机，但服务器的价格比较高，并且在文件传输等方面不太方便；虚拟机对电脑的要求比较高，并且占用也很大、启动时间长且配置较为麻烦，现如今，在 Windows 上已经有 WSL 了，使用它做生信分析极大地提高了效率，避免了很多问题。

<!--more-->

## WSL 的优点

- 安装、配置方便，Windows10/11 原生集成，可迅速安装配置
- 启动速度快，配合 VScode 可迅速进入工作状态
- 占用低，可以通过 .wslconfig 来限制占用的硬件资源
- 文件传输方便，可以直接打开 Windows 下的文件
- WSL2 已支持图形界面
- 与 Windows 系统互相独立互不干扰
- 可以备份恢复，便于迁移

## WSL 的缺点

- 由于 WSL2 与 Windows 通过网络协议互相访问，导致直接访问 Windows 系统下的文件时速度会下降
- Windows 自带的 openssh 版本低，导致 VScode 会与 WSL 断开连接
- WSL 下使用 Windows 代理时需要配置
- 默认安装在 C 盘，但可以自行下载安装包解压后安装在其他盘

## WSL 的安装

参考我的文章 [Windows10 & 11 安装 WSL2](https://www.hieroglyphs.top/posts/309de741/)

## WSL 的配置

此处默认以 Debian 系为例，我使用的是 Debian11

### 换源

直接参考各大开源软件镜像站的文档即可，推荐中科大的，一行命令即可换源

- [USTC Mirror Help](https://mirrors.ustc.edu.cn/help/)

- [清华开源软件镜像站使用帮助](https://mirrors-i.tuna.tsinghua.edu.cn/help/debian/)

- [阿里巴巴开源镜像站](https://developer.aliyun.com/mirror/)

### 常用软件的安装

**下载器**

```bash
sudo apt-get install wget curl
```

**Git**

```bash
sudo apt-get install git
```

**conda**

参考文章 [Linux 下安装 conda](https://www.hieroglyphs.top/posts/1d0dd329/)

**oh-my-zsh**

更加美观、便捷的终端，使用清华镜像安装（需要先安装 Git）

```bash
git clone https://mirrors.tuna.tsinghua.edu.cn/git/ohmyzsh.git
cd ohmyzsh/tools
REMOTE=https://mirrors.tuna.tsinghua.edu.cn/git/ohmyzsh.git sh install.sh
```

**Nodejs**

Debian 系发行版自带的 Nodjes 版本较低，使用 NodeSource 维护的 PPA 安装指定版本的 Nodejs（需要先安装 curl），修改版本号即可

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get update
sudo apt-get install nodejs
```

**Neofetch**

系统信息查看工具

```bash
sudo apt-get install neofetch
```

输入 neofetch 即可查看系统版本、CPU、内存等信息

**其他**

[Linux 下安装 BUSCO](https://www.hieroglyphs.top/posts/24ed802/)

### 连接 VScode

安装最新版的 [Vscode](https://code.visualstudio.com/)，然后在 VScode 的插件商店搜索并安装名为 WSL 的插件

插件安装完成后在 WSL2 的终端中输入`code .`，等待下载相关文件后 VScode 便会自动打开，后面直接打开 VScode 就会自动启动 WSL2

### 使用 Git 的 openssh

>由于 Windows 自带的 openssh 版本较低，可能会导致 WSL 与 VScode 断开连接，所以我们使用最新版 Git 的 openssh

先使用 [淘宝镜像](https://registry.npmmirror.com/binary.html?path=git-for-windows/) 下载安装最新版的 Git

在 Windows 系统的开始菜单搜索`编辑系统环境变量`，以此点击`环境变量`——系统环境变量里的`Path`——`编辑`——`新建`

将 Git 路径下的`usr\bin`添加进去，例如我的 Git 安装在默认路径，这里就写`C:\Program Files\Git\usr\bin`

然后点击上移，将刚刚添加的 Git 的路径移动到`%SYSTEMROOT%\System32\OpenSSH\`之上，代表优先使用 Git 的 openssh

最后打开 powershell 或 cmd，输入下列命令关闭 WSL2，再次启动 WSL2 即可

```powershell
wsl --shutdown
```

### 限制 WSL2 的内存

进入 wsl，输入下列命令建立 .wslconfig 文件

```bash
vi "$(wslpath "C:\Users\YourUsername\.wslconfig")"
```
把 yourUsername 换成自己的 Windows 用户名 

然后输入下列内容

```bash
[wsl2]
memory=1GB 
processors=1
swap=1GB
```

这些内容分别指定了 WSL2 实例的最大内存为 1GB、占用核心为 1 核心和交换分区为 1GB

保存后打开 power shell，输入 wsl --shutdown 关闭 wsl，再重新进入即可

## 扩展使用

### 在指定目录打开终端

默认情况下，VScode 中终端会在用户根目录下打开，即~目录，但~下有很多软件和系统的配置文件等，会影响我们的视野，我采用的方法是在根目录下建立一个`work`目录，然后 cd 到该目录，再输入`code .`打开 VScode，这样每次打开终端都会再`work`目录下打开，将所需要的文件放进去，会方便很多

### 在 Windows 下查看 Linux 下文件

在终端中输入

```bash
explorer.exe .
```

就会在当前目录打开 Windows 的文件资源管理器，但很多文件无法在 Windows 下直接编辑，没有权限，建议还是使用 VScode 编辑文件

### WSL 直接访问 Windows 下的文件

Windows 下的各个盘全部默认挂载在 WSL`/`目录下的`/mnt`目录中，例如 C 盘的路径为`/mnt/c`，这样我们就可以直接在 WSL 中访问 Windows 下的文件，但需要注意的是，WSL 为 Linux 系统，与 Windows 系统的文件系统不同，例如 Linux 下的文件及文件夹名称大小写不同，不能包含空格，所以这里使用的时候需要注意

### git clone 后没有权限

输入下列命令将克隆的文件与文件夹的所有者改为指定用户

```bash
sudo chown -R username ~/work/xxxx
```

## WSL 的备份与导入

**导出 WSL**

```powershell
wsl --export <Distribution Name> <FileName>
```

**导入 WSL**

```powershell
wsl --import <Distribution Name> <InstallLocation> <FileName>
```

将指定 tar 文件导入和导出为新的发行版 选项包括：

--vhd：指定导入/导出发行版应为 .vhdx 文件，而不是 tar 文件
--version：（仅导入）指定将发行版导入为 WSL 1 还是 WSL 2 发行版

## WSL 的卸载

```powershell
wsl --unregister <DistributionName>
```

## 参考资料

[使用 WSL 在 Windows 上安装 Linux](https://learn.microsoft.com/zh-cn/windows/wsl/install)

[WSL 的基本命令](https://learn.microsoft.com/zh-cn/windows/wsl/basic-commands)