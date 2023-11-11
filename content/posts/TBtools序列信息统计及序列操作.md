---
title: "TBtools 序列信息统计及序列操作"
date: 2023-08-21T20:02:39+08:00
tags: [生物信息学,TBtools]
slug: 76asdja
---

使用 TBtools 的 Fasta Stats 和 Sequence Manipulate 工具帮助我们迅速地统计信息及序列操作，简单介绍一下操作。

<!--more-->

## Fasta Stats

该工具可获取 Fasta 序列的以下信息：
- Total_Len（序列总长）
- Total_Seq_Num（染色体数）
- Total_N _Counts（未测通的碱基数）
- Total_LowCase_Counts（重复序列的标志）
- Total_GC_content（GC 含量）
- Minimum Len（最小序列长度）
- Maximum Len（最大序列长度）
- Mean Len（平均序列长度）
- Median Len（序列中位数长度）
- N50

打开工具，输入序列并设置输出目录即可查看信息

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/20230821200732.png)

输出目录下会有一个 excel 文件，显示序列长度与描述

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/20230821200840.png)

## Sequence Manipulate

该工具可以对序列进行以下操作（可勾选多个组合使用）：
- Reverse（反向）
- Complement（互补）
- RNA（序列对应的 RNA 序列）
- UpperCase（大写）
- LowerCase（小写）
- Only IDs（只显示序列 ID）
- Only Seqs（只显示序列信息）
- Seq in one Line（序列显示在一行）
- Bases per Line（每一行的碱基数，设置需要取消勾选 [Seq in one Line]）

直接粘贴序列，选择操作选项即可

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/20230821200952.png)