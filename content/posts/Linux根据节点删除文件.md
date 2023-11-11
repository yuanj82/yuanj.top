---
title: Linux 使用 inode(i 节点号）删除文件
date: 2023-09-05T08:10:24+08:00
slug: dsjkf82md
tags: [Linux]
---

今天用超算的时候发生一个错误，从 NCBI 上下载的数据格式有问题，解压出来的文件不对劲，而且解压出来一些乱码文件，rm 删不掉，最后使用节点删除的办法解决问题。

<!--more-->

使用 find 命令查看节点

```bash
find -i
```

也可以使用 ls

```bash
ls -i
```

查询节点删除文件，解决问题

```bash
find -inum [节点] -delete
```

也可以使用命令

```bash
find . -inum [节点] -exec rm {} \;
```
