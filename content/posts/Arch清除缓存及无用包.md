---
title: Arch 清除缓存及无用包
tags:
  - "Linux"
  - "Arch"
slug: o1r3l6y0
date: 2023-11-15T19:53:29+08:00
---

使用 Arch 时间长了，难免会产生一些无用包或者缓存，记录一些清理相关垃圾命令。

<!--more-->

```bash
sudo pacman -R 包名：该命令将只删除包，保留其全部已经安装的依赖关系
sudo pacman -Rs 包名：在删除包的同时，删除其所有没有被其他已安装软件包使用的依赖关系
sudo pacman -Rsc 包名：在删除包的同时，删除所有依赖这个软件包的程序
sudo pacman -Rd 包名：在删除包时不检查依赖
```

清除所有无用包：

```bash
sudo pacman -Qtdq | sudo pacman -Rns -
```

清除缓存：

```bash
sudo pacman -Scc 
```