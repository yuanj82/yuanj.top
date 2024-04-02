---
title: 修复 WPS 伪粗体问题与 PDF 导出错误问题
tags:
  - "Linux"
slug: y9p0e8u7
date: 2024-03-20T22:04:23+08:00
---

发现 WPS 里黑体的字都变成黑块了，而且 PDF 导出也报错，去论坛翻了一下，找到解决方法。

<!--more-->

首先是 PDF 导出错误，Debian 论坛上说 libtiff.so.5 的问题，论坛大佬说 aur 有 libtiff5 ，就先安装一个 libtiff5 试试：

```bash
yay -S libtiff5 
```

问题解决~

然后是粗体黑块问题：

![](https://images.yuanj.top/202403202207036.png)

论坛上查到是 freetyp2 的一个 bug，切换到旧版 freetyp2 就可以了，于是用 pacman 给它降级，先安装 downgrade ：

```bash
sudo pacman -S downgrade  
```

然后降级：

```bash
sudo downgrade freetype2
```

选择 2.12.0 版本，问题又解决~