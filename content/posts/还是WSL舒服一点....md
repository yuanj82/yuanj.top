---
title: 还是 WSL 舒服一点 ...
tags:
  - "Windows"
  - "Linux"
slug: m5e5d8j8
date: 2024-06-11T16:27:49+08:00
---

用了一段时间的 scoop，实在是忍不了了，还是灰溜溜地开了 WSL😢

<!--more-->

scoop 整体感觉是不错的，但是还有一些问题：

- 下载的时候经常断开连接，比如昨天下载 Rstudio，是从亚马逊的 S3 下载的，一直下载不动 ... 今天下载就又可以了，这个问题很多软件都有。
- 更新的时候似乎没法检查软件是否正在运行？比如我一直用 bash，是属于 git 的，但是更新的时候会先更新，更不了就报错了，为什么不能先检查应用是否正在运行？
- scoop 的命令似乎对 git bash 的支持并不好，比如我要通过 bash 更新所有软件`scoop update -k *`，它会先检查 buckets 的更新，然后目标定向为当前目录下的所有文件夹 ...
- 有些软件自己会更新，容易导致和 scoop 版本不匹配

![](https://images.yuanj.top/202406111640910.png)

而且，bash 在 Windows Terminal 里是很不错很美观的，但是在 vscode 里面就似乎不是很舒服了 ... 

所以，我改用 scoop 来安装一些开源的简单的软件如 telegram、zotero 等等，然后开发环境还是老老实实用 WSL2。以前 WSL2 上装的是 Debian，但是软件包一直不咋更新，版本容易落后，所以换成了 Arch，舒服多了，Debian 上装 R 包经常容易出现缺库的现象，我本以为 Arch 也一样的，但是没有，居然很少缺库，只需要装几个重要的包就基本足够了，实在是舒服啊~

```bash
sudo pacman -S cmake gdal geos proj gcc-fortran
```

pymol 还需要安装 qt：

```bash
sudo pacman -S qt5-base python-pyqt5 xorg
```

图形界面有点问题，需要修复一下：

```bash
sudo rm -r /tmp/.X11-unix
ln -s /mnt/wslg/.X11-unix /tmp/.X11-unix
```

再在 .zshrc 写入：

```bash
export LIBGL_ALWAYS_INDIRECT=1
```

`source ~/.zshrc`即可。

本想不用 aur 的，后来发现有俩包还真就只能用 aur🤣也就只能用上了，R 包 colormap 这种需要 V8 库，之前在 Debian 上一条命令`sudo apt install libv8-dev`立马就装好了，但是 Arch 上只能从 aur 下载，然后编译了将近两个小时 ...

```bash
yay -S udunits
yay -S v8-r
```

换上 WSL2 之后一切都舒服了~

以前还会在 .wslconfig 限制 WSL2 所能调用的内存和核心：

```txt
[wsl2]
# memory=4GB
# processors=2
# swap=8GB
# autoProxy=true

[experimental]
sparseVhd=true
autoMemoryReclaim=dropcache
```

但是现在发现好像不需要了，最新版的 WSL2 据说可以自动回收内存了，我也感受到了，在经历了编译时占用 CPU 98%和 15 GB 内存后，编译结束 CPU 立刻降下去了，内存也降到 4GB 一直停着了，也算是可以了。

![](https://images.yuanj.top/202406111650829.png)

WSL2 主要还可以用 pymol、gromacs、deeptools 等等的软件，Windows 就不行，超算又不方便，所以自己的还是好一些。于是一切又熟悉又舒适了，游戏也删掉了~ 至于性能损失，损失就损失了吧 ...

跑了一下 acATAC-seq 流程，占用大概稳定在 13GB 左右，我的内存是 32GB 的，总体还是不错的。