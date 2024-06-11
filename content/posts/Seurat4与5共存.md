---
title: Seurat4 与 5 共存
tags:
  - "R"
  - "Seurat"
slug: j2v9y4b7
date: 2024-06-11T18:10:35+08:00
---

这两天在看单细胞测序的文章，也想着进行一波小复现（跑一下作者的代码），但是这些文章的代码是基于 Seurat v3 版本的，而现在默认用的是 v5 版本，有很多的函数是不一样的，于是搞了一个 Seuratv3 与 v5 共存。

<!--more-->

先建一个文件夹来存放 v3 版本：

```bash
mkdir ~/seurat3
```

然后把它添加到 R 的包安装路径里面去：

```r
.libPaths(c("~/seurat3", .libPaths()))
```

再往这个文件夹里面装指定的 3.1.5 版本 seurat：

```r
remotes::install_version("Seurat", "3.1.5")
```

这个时候加载 seurat 就是 v3 版本的：

```r
library(Seurat)
```
```r
> packageVersion("Seurat")
[1] ‘3.1.5’
```

此后 R 每次启动默认当然还是 v5 版本，不过只要在需要 v3 版本的时候把路径添加进去再加载包就行了。