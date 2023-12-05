---
title: Linux 下 R 使用新罗马字体
tags:
  - "R"
  - "Linux"
slug: m5q1u8y2
date: 2023-11-23T16:59:27+08:00
---

今天使用 R 绘制热图，由于我的 R 是在 Debian 下的，默认是没有新罗马字体的，而我们平时作图的时候喜欢用新罗马字体，于是就需要自己安装然后使用。

<!--more-->

Debian 系 Linux 安装 MS 字体：

```bash
sudo apt install msttcorefonts
```

刷新字体缓存：

```bash
sudo fc-cache
```

然后使用 sysfonts 包来查看 R 中的字体：

```r
library(sysfonts)
font_families()
```

```r
[1] "sans"            "serif"           "mono"            "wqy-microhei"
```

添加新罗马字体：

```bash
font_add("Times_New_Roman", "/usr/share/fonts/truetype/msttcorefonts/Times_New_Roman.ttf")
```

可以在`/usr/share/fonts/truetype/msttcorefonts/`目录查看安装的 MS 字体：

```bash
ls /usr/share/fonts/truetype/msttcorefonts/
```

添加完成后再查看 R 中的字体：

```bash
font_families()
```

```r
[1] "sans"            "serif"           "mono"            "wqy-microhei"   
[5] "Times_New_Roman"
```

使用 showtext 包在 R 中调用字体：

```r
library(showtext)
font_add("Times_New_Roman", "/usr/share/fonts/truetype/msttcorefonts/Times_New_Roman.ttf")
showtext_auto() # 始终启用字体
```

pheatmap 包中调用字体：

```r
p <- pheatmap(data,
  scale = "row", 
  clustering_method = 'ward.D',
  cellwidth = 30, 
  cellheight = 10, 
  fontfamily = "Times_New_Roman",
)
```