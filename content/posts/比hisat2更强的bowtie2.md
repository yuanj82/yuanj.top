---
title: 比 Hisat2 更强的 bowtie2
tags:
  - "Genomics"
  - "Bioinformatics"
slug: e3v5q7k1
date: 2024-03-06T15:21:34+08:00
---

最近在处理一组转录组数据时遇到一个问题，就是这个数据的参考基因组很大，有 10GB 多，Hisat2 总是会报错，于是找到该组数据的原文献，看到材料与方法里使用的是 bowtie2，所以就用 bowtie2 比对，就没有发生错误了。

<!--more-->

Hisat2 报错：

```bash
terminate called after throwing an instance of 'std::bad_alloc'
  what():  std::bad_alloc
(ERR): hisat2-align died with signal 6 (ABRT) (core dumped)
terminate called after throwing an instance of 'std::bad_alloc'
  what():  std::bad_alloc
(ERR): hisat2-align died with signal 6 (ABRT) (core dumped)
terminate called after throwing an instance of 'std::bad_alloc'
  what():  std::bad_alloc
(ERR): hisat2-align died with signal 6 (ABRT) (core dumped)
terminate called after throwing an instance of 'std::bad_alloc'
  what():  std::bad_alloc
(ERR): hisat2-align died with signal 6 (ABRT) (core dumped)
terminate called after throwing an instance of 'std::bad_alloc'
  what():  std::bad_alloc
```

给我整懵了 ... 我这是超算平台，应该不会出现内存不足的问题吧？我一般用超算的 login 节点，于是速度看了一遍超算使用文档后将脚本任务提交到超算的 gpu 节点：

```bash
srun -p debug -w gk[11-15] -N 2 -n 12 -t 20 --gres=gpu:2 hisat.sh
```

出去一趟回来后发现还是报同样的错误，于是我猜测，应该是基因组太大，hisat2 承受不住？找到原文献看了一下，作者材料与方法中提到，使用的是 bowtie2，所以我也就用 bowtie2 试了一下，结果异常顺利！

安装 bowtie2：

```bash
conda install bioconda::bowtie2
```

https://benlangmead.github.io/aws-indexes/bowtie 有一些官方提供的索引，有水稻的就很不错，但是依旧只是很少数物种的，所以还是自己构建索引：

```bash
nohup bowtie2-build Paeoniaostii.fa Paeoniaostii &
```

随后将 reads 比对到参考基因组：

```bash
#!/bin/bash
for i in {78..82}
do
    bowtie2 -p 6 -3 5 --local -x Paeoniaostii -1 SRR179997${i}_1.fastq.gz -2  SRR179997${i}_2.fastq.gz -S Paeoniaostii_${i}.sam
done
```

成功解决问题~

然后仔细查了一下这两个软件的优缺点，以下内容转载自 [简书](https://www.jianshu.com/p/601469194b5e)：

**bowtie&bowtie2**

bowtie 出现在上古时期（就是很久远的意思了），那个时候测序行业的发展还不成熟，序列长度普遍在 50bp 以下，因此 bowtie 的出现就是为了满足长度在 50bp 以下的 reads 的比对。官方称其可以把短的 DNA 序列（35bp）快速的比对到人类基因组上。

而 bowtie2 的出现则弥补了 bowtie 的短板，bowtie2 擅长比对 50-100bp 长的 reads，长度甚至长达 1000。它适合比对那些比较长的基因组，如哺乳动物基因组。

bowtie 和 bowtie2，是两个不同类型的比对工具，bowtie2 并非是 bowtie 的升级。尺有所长寸有所短，bowtie 适合长度在 50b 长度以内的 reads 比对，而 bowtie2 适合 50-100b，甚至更长的 reads 比对。但是这两个都属 DNA-seq 比对工具

**Tophat 和 Tophat2**

Tophat/Tophat2 工具本身不能进行比对，它是通过调用 bowtie/bowtie2 进行比对的。划重点，bowtie2 不是 bowtie 的升级版，但是 Tophat2 是 Tophat2 的升级版。因此 Tophat 只可以调用 bowtie，而 Tophat2 不仅可以调用 bowtie2（默认）还可以更改设置调用 bowtie。

Tophat/Tophat2 调用 bowtie/bowtie2 后，会首先使用 bowtie/bowtie2 对序列进行比对，对于那些没有比对上的，会考虑其跨外显子的可能性，将 reads 劈开重新比对。

如果你去 bowtie/bowtie2/Tophat 的官网仔细观察，你会发现，bowtie 和 bowtie2 各自有自己的官网，有专属于自己的介绍。而 Tohat 就不同了，它只有一个，仅仅是在 2012 年 4 月 9 日 Tophat 发布了 2.0.0 版本，宣布支持 bowtie2 的比对。而我们通常也将支持 bowtie2 版本的 Tophat 称之为 Tophat2。

此外，如果你够无聊，你在它们的主页上下扒拉扒拉，你会发现无论是 bowtie 还是 bowtie2 在 2019 年仍然是有更新的。但是 Tophat 到了 2016 年 2 月便停止了更新……

**Hisat2**

Tophat2 的原作者们也不知道是出于什么考虑，不再更新 Tophat2，转而开发了一个新的比对工具 Hisat2，更是推荐人们使用 Hisat2，声称其速度更快，内存占用率更小，准确率更高。

此外，Hisat2 不仅支持 RNA-seq 的比对还支持 DNA-seq 比对，唯一需要做的就是加上一个参数--no-spliced-alignment。但是就目前来看，大部分人都是使用 Hisat2 做 RNA-seq，没人使用它做 DNA-seq。
