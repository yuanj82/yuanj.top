---
title: 使用 trim Galore 替代 Trimmomatic 进行转录组数据清洗
tags:
  - "Transcriptomics"
  - "Bioinformatics"
slug: y2b9l4g8
date: 2024-03-06T15:09:41+08:00
---

最初第一次使用 Trimmomatic 的时候就很头疼，代码那么长，而且那些参数都很不好懂 ... 但是当时也就凑活着用了，最近读到几篇论文都用 trim Galore 来进行测序数据的清洗，于是试用了一下，确实比起 Trimmomatic 要好一些。

<!--more-->

## Trimmomatic

优势：

- 可使用参数更多，如滑窗剪切，可以直接选择使用内置的接头序列等等；
- 默认可生成 paired 和 unpaired 两种文件，更利于下游分析。

劣势：

- 代码非常长，而且容易写错，最好写在一个脚本里；
- 参数比较难记，像 ILLUMINACLIP 中的几个数字分别代表什么必须要对照说明书才能看懂；
- 运行时间较长；
- 只适用于 illumina 测序得到的数据，不适用与其他测序平台。

## Trim_galore

优势：

- 安装和使用都非常简单；
- 代码较短
- 参数更直观，不用去死记硬背
- 默认下不区分 paired 和 unpaired，运行速度较快

劣势：

- 可调参数较少

总结一句话就是，Trim_galore 更方便、简单直观，适用的平台多，而 Trimmomatic 最大的好处是功能和可调用的参数多。

## trim_galore 参数

参数说明：

- --quality：设定 Phred quality score 阈值，默认为 20
- --phred33：选择-phred33 或者-phred64，表示测序平台使用的 Phred quality score
- --adapter：输入 adapter 序列。也可以不输入，Trim Galore! 会自动寻找可能性最高的平台对应的 adapter。自动搜选的平台三个，也直接显式输入这三种平台，即--illumina、--nextera 和--small_rna
- --stringency：设定可以忍受的前后 adapter 重叠的碱基数，默认为 1（非常苛刻），可以适度放宽，因为后一个 adapter 几乎不可能被测序仪读到
- --length：设定输出 reads 长度阈值，小于设定值会被抛弃
- --paired：对于双端测序结果，一对 reads 中，如果有一个被剔除，那么另一个会被同样抛弃，而不管是否达到标准
- --retain_unpaired：对于双端测序结果，一对 reads 中，如果一个 read 达到标准，但是对应的另一个要被抛弃，达到标准的 read 会被单独保存为一个文件
- --gzip 和--dont_gzip：清洗后的数据 zip 打包或者不打包
- --output_dir：输入目录。需要提前建立目录，否则运行会报错
- -- trim-n : 移除 read 一端的 reads

安装：

```bash
conda install bioconda::trim-galore
```

使用举例：

```bash
#!/bin/bash
for i in {73..84}
do
    trim_galore -q 25 --phred33 --stringency 3 --length 36  --paired SRR109915${i}_1.fastq.gz SRR109915${i}_2.fastq.gz --gzip -o ./clean/
done
```