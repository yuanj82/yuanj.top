---
title: "文献分享|在对拟南芥进行诱导转录因子 bZIP11 处理后利用 ATAC Seq 技术鉴定其全基因组可及染色质区域"
tags:
  - "Genomics"
  - "Publications"
slug: x7g8z0v6
date: 2024-04-08T13:04:30+08:00
---

文章题为"Genome-wide identification of accessible chromatin regions by ATAC-seq upon induction of the transcription factor bZIP11 in Arabidopsis"。该文章发表于 scientific data（中科院二区，IF=9.8，第一作者：Alicia M. Hellens，通讯作者：François F. Barbier。

<!--more-->

碱性亮氨酸拉链 11（bZIP11）是植物在低能量条件下被激活的转录因子（TF），在帮助植物适应饥饿情况方面起着至关重要的作用。虽然之前的研究结果显示 bZIP11 通过从单个基因组位点获得的证据来调节染色质可及性，但该转录因子在整个基因组水平上对染色质景观的调节程度仍然未知。

在本研究中，作者通过对拟南芥（Arabidopsis）叶原生质体进行了 ATAC-seq，以获得 bZIP11 诱导响应的染色质模式谱。作者在 bZIP11 诱导后平均鉴定出了 10,000 个可及区域的差异，对应于拟南芥基因组中的 8,420 个不同基因。本研究为了解 bZIP11 在染色质水平上如何调节基因组提供了资源，并提供了单个转录因子对整个植物基因组的影响的示例。

![](https://images.yuanj.top/202404081307956.png)

<div style="text-align:center; margin-bottom:20px;">
<em>图 1 ATAC-seq 工作流程</em>
</div>

为了验证 dexamethasone（DEX）处理后 bZIP11 活性的诱导，作者测量了 ASN1（ASPARAGINE SYNTHASE1 ）的表达，这是该转录因子的已知直接靶标。与 DEX 处理的 WT 样品和 Mock 处理的 p35S:bZIP11-HBD 样品相比，DEX 处理的 p35S:bZIP11-HBD 样品中 ASN1 表达上调近 350 倍，证实 DEX 处理有效诱导 p35S:bZIP11-HBD 样品中的 bZIP11 活性。

![](https://images.yuanj.top/202404081313844.png)

<div style="text-align:center; margin-bottom:20px;">
<em>图 2 验证了 bZIP11 活性的诱导效果，并确定了 bZIP11 为 ASN1 响应 DEX 处理的直接靶点</em>
</div>

![](https://images.yuanj.top/202404081317517.png)

<div style="text-align:center; margin-bottom:20px;">
<em>图 3 ATAC-seq 读取的质量指标</em>
</div>

bZIP11 诱导的样本在分层排名图中聚集在一起，由橙色 (-CHX) 和红色 (+CHX) 样品描绘（图 4a）。作者使用不同的线条描绘了 bZIP11 诱导基因周围染色质可接近信号。值得注意的是，转录起始位点周围的区域被富集，符合传统转录因子的预期（图 4b）。同时，超过 90%的 DAR 存在于基因的启动子区域中，这是在拟南芥这样的小基因组中预料到的现象（图 4c）。此外，在响应 bZIP11 诱导而被鉴定的 DAR 序列中，无论有无 CHX，bZIPCRE 和 G-box 基序均高度富集（图 4d）。与通过 DNA 亲和层析和测序（DAP-seq）鉴定的相应 bZIP11 的基序非常相似（图 4d）9，这支持这些 DAR 受到 bZIP11 调控的观点。最后，作者测试了 bZIP11 诱导是否有效地增强了染色质可接近目标的能力。为此，作者可视化了 GH3.3 位点上的 ATAC-seq 结果，已知该位点在染色质水平上受到 bZIP11 直接调控。结果表明，在不存在或存在 CHX 的情况下，在 p35S:bZIP11-HBD 样品中 bZIP11 诱导时，染色质更容易接近（图 4e）。这与 bZIP11 在染色质水平上调控 GH3.3，同时在本实验中成功地调节染色质可及性相关。这些数据表明 bZIP11 调节染色质以增强顺式元件对反式调节因子的可及性，且这种调节机制涉及大部分 bZIP11 的靶标。

![](https://images.yuanj.top/202404081319219.png)

<div style="text-align:center; margin-bottom:20px;">
<em>图 4 该数据集中确定的 ATAC-seq 峰的特征</em>
</div>

## 文献分析与总结

bZIP11 是植物中的一种转录因子，可在低能量条件下调整基因表达，帮助植物适应不良环境。在拟南芥中，bZIP11 是参与糖反应的蛋白质组之一。作者通过高通量测序技术发现，bZIP11 能影响拟南芥约三分之一的基因。bZIP11 结合名为 G 盒的顺式调控元件来调节基因表达，并通过组蛋白乙酰化增强染色质可及性。为深入探究 bZIP11 对染色质可及性的全基因组影响，作者使用 ATAC-seq 技术，在野生型拟南芥和转基因植物中比较了染色质可及性。结果显示，bZIP11 的诱导导致约 10,000 个基因组区域的差异可及，这些区域涉及 2,553 个基因，表明这些基因的染色质可及性受到 bZIP11 的直接影响。