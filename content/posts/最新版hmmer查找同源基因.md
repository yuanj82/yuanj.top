---
title: 最新版 HMMER 查找同源基因
date: 2023-10-10T19:40:49+08:00
slug: hmmd234csda
tags: [生物信息学,HMMER]
---

pfam 数据库更新了，原有的下载 hmm 模型与种子文件的方法需要进行一些改变。

<!--more-->

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/202310101943037.png)

pfam 官网已经通知，将数据迁移到 [interpro](https://www.ebi.ac.uk/interpro/) 了，所以之前下载 hmm 模型的方法已经不适应了，需要进行一些改变。

打开 [interpro](https://www.ebi.ac.uk/interpro/) 网站，首页往下翻，找到 pfam

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/202310101946555.png)

搜索结构域

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/202310101946291.png)

进入结构域的详情页面，点击左栏的 Alignment，选择 seed 然后下载种子文件，下载后是一个压缩包，解压后是 SEED.ann 文件，与之前旧版数据库里的 PFseed_xxxx.txt 是一样的。

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/202310101950962.png)

接着再点击左栏的 Curation，下载 hmm 模型文件。

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/202310101952002.png)

之后的 HMMER 的使用就和之前一样，没什么变化了。