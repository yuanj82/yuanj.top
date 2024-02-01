---
title: Debian 编译安装最新版 R
tags:
  - "Linux"
  - "Debian"
  - "R"
slug: f5a4q5l4
date: 2023-12-29T18:54:30+08:00
---

Debian 默认自带的 R 版本太低了，所以需要自己编译安装最新版的 R。

<!--more-->

这里查了很多资料，说的多少都会有些问题，于是我自己根据安装过程总结了下。

先安装所需依赖：

```bash
sudo apt install build-essential libcurl4-openssl-dev libssl-dev zlib1g-dev libbz2-dev libreadline-dev libpcre2-dev liblzma-dev libncurses5-dev libxml2-dev libcairo2-dev libxt-dev gfortran
```

这里补充了一个 `gfortran` 依赖，没有它没法编译成功，当然，cmake 这些也是必需的。

下载最新版 R 的源代码包：

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/CRAN/src/base/R-4/R-4.3.2.tar.gz
```

编译安装：

```bash
./configure
sudo make
sudo make install
```

也没什么需要注意的，只要把依赖都装好了，后面的都是很顺其自然的。

---

**2024/02/01 更新**

安装 Rstudio 后发现不能正常使用，报错`error while loading shared libraries: libR.so: cannot open shared object file: No such file or directory`，经过大佬指点，将编译过程中的`./configure`换成`./configure --enable-R-shlib`即可。