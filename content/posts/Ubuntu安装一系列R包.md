---
title: Ubuntu 安装一系列 R 包
tags:
  - "Linux"
  - "TUbuntu"
slug: f8t9v3q0
date: 2024-03-10T13:37:59+08:00
---

Ubuntu 安装 R 包的时候总会有一堆的错误，无非就是缺少一些库，使得 R 包或者 R 包依赖的 R 包安装不上，补上库和 R 包就可以了。

<!--more-->

## languageserver

先安装需要的库：

```bash
sudo apt-get install libxml2-dev openssl libssl-dev libcurl4-openssl-dev
```

然后安装：

```r
install.packages('languageserver')
```

## tidyverse

需要的库：

```bash
sudo apt-get install libfreetype6-dev libharfbuzz-dev libfribidi-dev libfreetype6-dev libpng-dev libtiff5-dev libjpeg-dev
```

安装依赖的 R 包：

```r
install.packages("sysfonts")
install.packages("textshaping")
install.packages("ragg")
```

最后安装：

```r
install.packages("tidyverse")
```

## ggplot2/ggrepel

直接安装即可：

```r
install.packages("ggrepel")
install.packages("ggplot2")
```