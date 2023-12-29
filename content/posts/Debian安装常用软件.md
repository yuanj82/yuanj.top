---
title: Debian 安装常用软件
tags:
  - "Linux"
  - "Debian"
slug: r3q5q3t1
date: 2023-12-29T18:38:45+08:00
---

记录一下 Debian 上安装常用软件的一些命令。

<!--more-->

## Wine 应用

参照 [deepin-wine](https://github.com/zq1997/deepin-wine) 的文档来安装 wine 应用。

添加仓库：

```bash
wget -O- https://deepin-wine.i-m.dev/setup.sh | sh
```

然后直接安装

```bash
sudo apt-get install com.qq.weixin.deepin  #安装微信
sudo apt-get install com.qq.weixin.work.deepin  #安装企业微信
```

全部 wine 软件包列表：https://deepin-wine.i-m.dev/

## 安装包安装

在官网下载安装包，使用 dpkg 安装：

QQ：https://im.qq.com/linuxqq/index.shtml

WPS：https://www.wps.cn/product/wpslinux

腾讯会议：https://meeting.tencent.com/download/

洛雪音乐助手：https://github.com/lyswhut/lx-music-desktop/releases

Obsidian：https://obsidian.md/download

## chrome

先安装 wget：

```bash
sudo apt update && sudo apt install wget
sudo apt install apt-transport-https lsb-release ca-certificates
```

添加 chrome 的存储库：

```bash
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor -o /usr/share/keyrings/google-archive-keyring.gpg
```

安装 chrome 存储库：

```bash
echo "deb [signed-by=/usr/share/keyrings/google-archive-keyring.gpg] http://dl.google.com/linux/chrome/deb/ stable main" | sudo tee /etc/apt/sources.list.d/google-chrome.list
```

安装 chrome：

```bash
sudo apt update
sudo apt install google-chrome-stable
```

## VScode

安装依赖项：

```bash
sudo apt update
sudo apt install software-properties-common apt-transport-https curl
```

导入微软 GPG 密钥：

```bash
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

添加 VScode 存储库：

```bash
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
```

安装 VScode：

```bash
sudo apt update
sudo apt install code
```

## flameshot 截图

```bash
sudo apt install flameshot
```

设置中配置命令的快捷键：`flameshot gui`

## TBtools

先安装 JAVA：

```bash
sudo apt install default-jre
```

下载 TBtools 的 jar 文件：

```bash
git clone https://github.com/CJ-Chen/TBtools-II.git
```

给 Linux.sh 添加可执行权限：

```bash
sudo chmod +x Linux.sh
```

运行 TBtools：

```bash
./Linux.sh
```

退出后会有个 Java 进程一直在后台，搜索 PID 后 Kill 就好。

## Gromacs GPU 版

下载源代码包：

```bash
wget https://ftp.gromacs.org/gromacs/gromacs-2023.3.tar.gz
```

编译安装：

```bash
tar xfz gromacs-2023.3.tar.gz
cd gromacs-2023.3
mkdir build
cd build
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON
make
make check
sudo make install
```

最后修改 bash 配置：

```bash
echo "source /usr/local/gromacs/bin/GMXRC" >> ~/.bashrc
source ~/.bashrc
```