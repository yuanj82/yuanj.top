---
title: Arch 更换内核
tags:
  - "Arch"
  - "Linux"
slug: r3n6d5o8
date: 2024-03-25T21:22:26+08:00
---

今天看到 Wiki 上说 linux-zen 内核是最适合日常使用的内核，于是乎，便一试，结果内核驱动识别不到显示器 ... 只能又换回原版内核。

<!--more-->

Arch 可以安装多种内核，根据 Wiki 的介绍有：

- Stable — 原版的 Linux 内核以及模块，使用了一些补丁。
https://www.kernel.org/ || linux

- Hardened — 更加注重安全的 Linux 内核，采用一系列 加固补丁 以减少内核和用户空间产生漏洞的风险。和 linux 相比，还启用了一些加固选项，比如用户命名空间（同时通过补丁禁用未授权用户的访问）、审计以及 SELinux
https://github.com/anthraxx/linux-hardened || linux-hardened

- Longterm — 包含了长期支持的 Linux 内核和内核模块。
https://www.kernel.org/ || linux-lts

- Zen Kernel — 一些内核黑客合作的结果，提供了适合日常使用的优秀内核。 更多详情请参见 https://liquorix.net （为 Debian 提供了基于 Zen 内核的二进制文件）.
https://github.com/zen-kernel/zen-kernel || linux-zen

看着群里不少大佬也用 zen 内核，那我何不一试？于是 g ... 然后灰溜溜换回原版内核。

查看当前内核版本：

```bash
uname -r
```

安装原版内核：

```bash
sudo pacman -S linux
```

删掉 zen 内核：

```bash
sudo pacman -Rs linux-zen
```

然后 grub 重新生成一下配置：

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

重启就解决了~