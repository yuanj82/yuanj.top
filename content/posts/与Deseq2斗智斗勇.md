---
title: 与 Deseq2 斗智斗勇
tags:
  - "R"
slug: j7t0q0i1
date: 2024-03-05T18:40:24+08:00
---

今天要做一个差异表达分析，理所当然的用上 Deseq2 包。

<!--more-->

此次的分析方式与之前有差别，需要先进行 fpkm 标准化后再进行差异表达分析，于是读入 counts 矩阵：

```r
rm(list = ls())

counts <- read.csv(
    'rna-seq.counts',
    header = TRUE,
    sep = '\t',
    # row.names = "Geneid",
    comment.char = '#',
    check.names = FALSE
)
```

然后使用一个 for 循环进行 fpkm 计算：

```r
for (clm in colnames(counts)[6:ncol(counts)]) {
    col_fpkm <- paste0(clm, "_FPKM")     # 新列的名称，加上"_FPKM"后缀
    total <- sum(counts[, clm])          # 计算每个样本的总读数
    counts[col_fpkm] <- (counts[, clm] * 10^6) / (counts[, "Length"] / 1000)  # 使用相应样本的长度值计算 FPKM 并添加 FPKM 列
}
```

计算之后，删去矩阵里没用的数据和原来的 counts 矩阵：

```r
counts = counts[,-c(2:19)]
```

看了一下数据，有很多的 0，为了防止意外，全部给它加一个不怎么影响结果的数值 1，然后再写入文件：

```r
numeric_mask <- sapply(counts, is.numeric)
counts[numeric_mask] <- lapply(counts[numeric_mask], function(x) ifelse(x < 1, x + 1, x))

write.table(counts, file = 'fpkm_output', sep = '\t', row.names = FALSE)
```

拿到 fpkm 矩阵后开始进行差异表达分析，还是使用之前的代码：

```r
# 数据示例：
# "Geneid"	"Normal-1-1_FPKM"	"Normal-1-2_FPKM"	"Normal-1-3_FPKM"	"HS-1-1_FPKM"	"HS-1-2_FPKM"	"HS-1-3_FPKM"	"Normal-2-1_FPKM"	"Normal-2-2_FPKM"	"Normal-2-3_FPKM"	"HS-2-1_FPKM"	"HS-2-2_FPKM"	"HS-2-3_FPKM"
# "Os01g0100100"	254855195.911414	242248722.316865	273253833.049404	587052810.902896	594548551.959114	539011925.042589	186030664.39523	145826235.093697	125383304.940375	103918228.279387	110391822.827939	100851788.756388
# "Os01g0100200"	1	887311.446317657	887311.446317657	1774622.89263531	3549245.78527063	5323868.67790595	1	887311.446317657	1	1	1	1
# "Os01g0100300"	1	1	1	1	1	1	1	1	1	1	1	1
# "Os01g0100400"	48588616.3813049	32855159.648311	51365108.7459509	136973623.322536	130032392.410921	124479407.681629	75890791.3003239	51827857.473392	59694585.8398889	9254974.54881999	16658954.187876	15270708.005553
# "Os01g0100466"	1	1	1	1	1	1	1	1	1	1	1	1
# "Os01g0100500"	610261026.10261	571107110.711071	669216921.692169	919441944.19442	886138613.861386	851485148.514851	698019801.980198	548154815.481548	536903690.369037	458595859.585959	527452745.274527	504500450.045004
# "Os01g0100600"	188775510.204082	179591836.734694	202040816.326531	398469387.755102	369387755.102041	339795918.367347	218367346.938776	152551020.408163	142346938.77551	1.75e+08	186224489.795918	137755102.040816
# 使用 fpkm 标准化之后的数据

# setwd('./pub_share/')
rm(list = ls())  
Sys.setenv(LANGUAGE = "en")

library(DESeq2)

fpkm = read.csv(
    'fpkm_output', 
    header = T,  
    sep = '\t', 
    row.names = "Geneid", 
    comment.char = '#', 
    check.name = F
)

samples = data.frame(
    sampleID = c("Normal-1-1_FPKM", "Normal-1-2_FPKM", "Normal-1-3_FPKM", "HS-1-1_FPKM", "HS-1-2_FPKM", "HS-1-3_FPKM", "Normal-2-1_FPKM", "Normal-2-2_FPKM", "Normal-2-3_FPKM", "HS-2-1_FPKM", "HS-2-2_FPKM", "HS-2-3_FPKM"), 
    sample = c("sample1", "sample1", "sample1", "sample2", "sample2", "sample2", "sample3", "sample3", "sample3", "sample4", "sample4", "sample4")
)

rownames(samples) = samples$sampleID

samples$sample = factor(samples$sample, levels = c('sample1', 'sample2', 'sample3', 'sample4'))

dds = DESeqDataSetFromMatrix(countData = fpkm, colData = samples, design = ~sample)

dds_count <- DESeq(dds, fitType = 'mean', minReplicatesForReplace = 7, parallel = FALSE)

sampl1_vs_sample2 <- results(dds_count, contrast = c('sample', 'sample1', 'sample2'))

sampl3_vs_sample4 <- results(dds_count, contrast = c('sample', 'sample3', 'sample4'))

result1 <- data.frame(sampl1_vs_sample2, stringsAsFactors = FALSE, check.names = FALSE)
result2 <- data.frame(sampl3_vs_sample4, stringsAsFactors = FALSE, check.names = FALSE)

write.table(result1, 'sampl1_vs_sample2.DESeq2.txt', col.names = NA, sep = '\t', quote = FALSE)
write.table(result2, 'sampl3_vs_sample3.DESeq4.txt', col.names = NA, sep = '\t', quote = FALSE)

```

报错说是 Deseq2 处理的数据得是整数，于是加上：

```r
fpkm <- round(fpkm)
```

将数值四舍五入为整数。

这里突然报错说有 NA 空值`converting counts to integer mode Error in validObject(.Object) : invalid class “DESeqDataSet” object: NA values are not allowed in the count matrix In addition: Warning message: In mde(x) : NAs introduced by coercion to integer range`，那就奇怪了，我不是全部加了 1 吗？于是这里判断空值，给空值加 1，再加一个判断，如果加 1 后还是空值那就直接删掉该行：

```r
numeric_mask <- sapply(fpkm, is.numeric)
fpkm[numeric_mask] <- lapply(fpkm[numeric_mask], function(x) ifelse(is.numeric(x) & x < 1, x + 100, x))

missing_values <- sum(is.na(fpkm))
if (missing_values > 0) {
  fpkm <- na.omit(fpkm)
}
```

还是会报同样的错误 ... 于是 Google 了一下，发现原来是某些比较大的数值仍然会保留小数点，例如：

```r
as.character(x)
```

```r
[1] "8903.00000000001" "946.999999999999"
[3] "9113.99999999999" "9451.00000000001"
[5] "9156.99999999999" "875.999999999999"
```

直接给我无语住了，于是根据网上的资料加上：

```r
fpkm = apply(fpkm, 2, as.integer)
```

成功跑出差异表达分析，查看分析结果的时候突然发现，原本第一列的基因 ID 居然全部变成了数字？？

排查了半天才发现，哦，原来`fpkm = apply(fpkm, 2, as.integer)`会直接把 ID 也给转化数字 ... 于是把这一句改成：

```r
fpkm[-1, ] <- apply(fpkm[-1, ], 2, as.integer)
```

问题就解决了~真的费劲~