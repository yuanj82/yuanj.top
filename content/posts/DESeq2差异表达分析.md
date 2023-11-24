---
title: DESeq2 差异表达分析
tags:
  - "R"
  - "DESeq2"
slug: i2b2p5m1
date: 2023-11-24T21:56:13+08:00
---

还是之前拿到的转录组数据，只有表达量矩阵是不行的，作为 RNA-seq 下游分析重要的一步，差异表达分析将不同处理下的基因表达数据同对照组做统计分析，筛选出具有显著表达变化的基因集，这一步对后面的研究是很有必要的。

<!--more-->

首先加载 R 包：

```r
library(DESeq2)
```

读入表达矩阵，以基因 ID 为行名：

```r
counts = read.csv(
    'gene2.counts', 
    header = T,  
    sep = '\t', 
    row.names = "Geneid", 
    comment.char = '#', 
    check.name = F
)
```

我这里拿到的表达矩阵的数据格式是这样的：

```txt
# Program:featureCounts v2.0.6; Command:"featureCounts" "-T" "5" "-t" "exon" "-g" "Name" "-a" "Lolium_perenne.gff3" "-o" "gene.counts" "-p" "C0_1" "C0_2" "C0_3" "C50_1" "C50_2" "C50_3" "C500_1" "C500_2" "C500_3" 														
Geneid	Chr	Start	End	Strand	Length	C0_1	C0_2	C0_3	C50_1	C50_2	C50_3	C500_1	C500_2	C500_3
KYUSt_chr1.5-E1	1	53340	53891	+	552	233	146	195	201	189	264	177	326	243
KYUSt_chr1.6-E1	1	54648	55664	-	1017	2	4	8	12	6	3	20	47	17
KYUSt_chr1.7-E1	1	59936	60283	+	348	3	0	6	7	0	0	1	0	0
KYUSt_chr1.8-E1	1	61782	61841	+	60	0	0	0	0	0	0	2	0	0
KYUSt_chr1.8-E2	1	62373	62621	+	249	9	21	81	34	17	8	57	12	9
KYUSt_chr1.9-E1	1	63456	63489	+	34	0	0	0	0	0	0	0	0	0
KYUSt_chr1.9-E2	1	63904	63983	+	80	0	0	0	0	0	0	0	0	0
KYUSt_chr1.9-E3	1	64100	64177	+	78	0	0	0	0	0	0	0	0	0
KYUSt_chr1.9-E4	1	64286	64361	+	76	0	0	0	0	0	0	0	0	0
```

对数据要先进行一些预处理，比如删掉前五行不需要的数据：

```r
counts = counts[,-c(1:5)]
```

我的这个数据里面有很多表达量很低或者都是 0 的，这种数据自然是直接删掉为好，只保留相加大于 10 的数据：

```r
counts = counts[rowSums(counts)>10, ]
```

创建样本信息数据框：

```r
samples = data.frame(
    sampleID = c("C0_1", "C0_2", "C0_3", "C50_1", "C50_2", "C50_3", "C500_1", "C500_2", "C500_3"), 
    sample = c("sample1", "sample1", "sample1", "sample2", "sample2", "sample2", "sample3", "sample3", "sample3")
)
```

按照 sampleID 更改 samples 的行名：

```r
rownames(samples) = samples$sampleID
```

将样本类型列转换为因子型数据，并设定默认排序为 1=sample1, 2=sample2, 3=sample3：

```r
samples$sample = factor(samples$sample, levels = c('sample1', 'sample2', 'sample3'))
```

将读入的基因表达矩阵和样本信息构建为 DESeqDataSet 对象，使 DESeq2 包可读：

```r
dds = DESeqDataSetFromMatrix(countData = counts, colData = samples, design = ~sample)
```

进行差异分析，计算差异倍数和 p 值：

```r
dds_count <- DESeq(dds, fitType = 'mean', minReplicatesForReplace = 7, parallel = FALSE)
```

- fitType = 'mean' 表示使用均值作为拟合模型
- minReplicatesForReplace = 7 表示在进行差异分析时，至少需要 7 个样本
- parallel = FALSE 表示不使用多线程运行

因为我这里有三组样本，分别是 C0、C50、C500，于是分为两组进行计算：

```r
sampl1_vs_sample2 <- results(dds_count, contrast = c('sample', 'sample1', 'sample2'))
sampl1_vs_sample3 <- results(dds_count, contrast = c('sample', 'sample2', 'sample3'))
```

最后将两组结果转为数据框并且写入文件：

```r
result1 <- data.frame(sampl1_vs_sample2, stringsAsFactors = FALSE, check.names = FALSE)
result2 <- data.frame(sampl1_vs_sample3, stringsAsFactors = FALSE, check.names = FALSE)

write.table(result1, 'sampl1_vs_sample2.DESeq2.txt', col.names = NA, sep = '\t', quote = FALSE)
write.table(result2, 'sampl1_vs_sample3.DESeq2.txt', col.names = NA, sep = '\t', quote = FALSE)
```

最后得到的数据格式如下：

```txt
	baseMean	log2FoldChange	lfcSE	stat	pvalue	padj
KYUSt_chr1.5-E1	213.082745455399	-0.159514113054599	0.32122397700057	-0.496582212025585	0.619483699498433	1
KYUSt_chr1.6-E1	12.858979620238	-0.583646140965058	0.84821388423366	-0.688088407668978	0.491397110057002	1
KYUSt_chr1.7-E1	1.93773722098783	0.307122539555924	2.44861358460283	0.125427115771613	0.900185422338187	NA
KYUSt_chr1.8-E2	27.843256050414	0.92386601716311	1.12261950538863	0.822955607602139	0.41053323848971	0.993851867813233
KYUSt_chr1.107-E3	9624.18154923748	1.2105461981764	0.753264345021032	1.60706690311036	0.108039692660408	0.826100628829101
KYUSt_chr1.107-E2	5756.42276327944	0.282255247362519	0.52083207523245	0.541931384000394	0.587865775653251	1
KYUSt_chr1.107-E1	6541.69156640286	0.396931212730972	0.672807064222869	0.589962908890457	0.555215516638021	1
KYUSt_chr1.117-E1	87.1271890512162	0.356042837051628	0.492560961499353	0.722840145446841	0.469778099923088	1
```

然后使用 ggplot2 包绘制火山图：

```r
library(ggplot2)

data <- read.table('./sampl1_vs_sample2.DESeq2.txt', header = TRUE)
data <- as.data.frame(data)
```

设置 pvalue 和 logFC 的阈值：

```r
cut_off_pvalue = 0.0000001
cut_off_logFC = 1
```

据阈值分为上调基因 down 和下调基因 down，无差异基因为 stable，并保存到 change 列：

```r
data$change <- ifelse (
    data$pvalue < cut_off_pvalue & abs(data$log2FoldChange) >= cut_off_logFC, 
    ifelse(data$log2FoldChange > cut_off_logFC, 'Up', 'Down'),
    'Stable'
)
```

```r
p <- ggplot(
    data,  # 使用的数据框
    aes(
        x = log2FoldChange,  # x 轴表示基因的对数倍变化
        y = -log10(pvalue),  # y 轴表示-p 值的对数
        colour = change,     # 根据变化情况着色
    )) +
    geom_point(
        alpha = 0.4,  # 设置散点的透明度
        size = 3.5,   # 设置散点的大小
    ) +
    scale_color_manual(values = c("#546de5", "#d2dae2", "#ff4757")) +  # 手动设置颜色
    geom_vline(xintercept = c(-1, 1), lty = 4, col = "black", lwd = 0.8) +  # 添加垂直虚线
    geom_hline(yintercept = -log10(cut_off_pvalue), lty = 4, col = "black", lwd = 0.8) +  # 添加水平虚线
    labs(
        x = "log2(fold change)",  # x 轴标签
        y = "-log10 (p-value)"    # y 轴标签
    ) +
    theme_bw() +  # 使用白底主题
    theme(
        plot.title = element_text(hjust = 0.5),  # 设置标题居中
        legend.position = "right",               # 设置图例位置
        legend.title = element_blank()           # 隐藏图例标题
    )
plot <- p
plot # 绘图并展示
```

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/202311242219293.png)

将绘图结果保存为文件：

```r
ggsave(plot, filename = "volcano.png", width = 10, height = 6, dpi = 300)
```