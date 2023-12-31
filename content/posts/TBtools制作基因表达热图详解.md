---
title: TBtools 制作基因表达热图详解
tags:
  - "Bioinformatics"
  - "TBtools"
slug: p5x7e7k5
date: 2023-06-23 12:07:50
---

使用 TBtools 制作基因表达热图实在是方便，美化起来也很简单，本文以水稻的一组基因为例，讲述基因表达数据的获取、处理并使用 TBtools 出图。

<!--more-->

## 数据准备

基因表达量数据一般由实验得来，对于一些物种也会有相对的数据库提供各种条件下实验得来的数据，我们课题组做的大都与水稻有关，我就以水稻的为例

[RED](http://expression.ic4r.org/) 数据库提供了多数水稻基因在不同条件下的表达量数据，输入基因名称进行搜索，这里使用 RAP ID 或者 MUS ID 都可以，只不过貌似 RAP ID 的要全一点，很多 MUS ID 的都找不到

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623123234.png)

搜索完成后，这里有很多的 Project，每个 Project 对应不同的实验条件，例如 DRP000391 就是正常状况下的表达量

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623123524.png)

从 Project 的详情页的可以看到该项目的实验条件

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623123605.png)

点击`Show Data`然后导出数据，这里要注意一下，导出数据是需要 flash 支持的，但 21 年开始 chrome、edge 这些浏览器已经不支持 flash 了，可以使用 360 浏览器或者搜狗浏览器这些，我提供一个绿色版的 [360 浏览器便携版](https://www.123pan.com/s/JYtA-SeW0v.html)，解压之后打开`360chrome.exe`直接使用

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623124152.png)

导出之后对数据进行处理，使用 Excel 的筛选功能，将数据整理成基因名称与各个项目的数据相对应的形式

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623124953.png)

对数据进行 `Log2` 处理，报错的值一律改为 0 即可

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623125050.png)

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623125135.png)

## 绘图

打开 TBtools 的 `Heatmap` 功能，把数据表格粘贴进去，start 出图

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623125233.png)

## 美化

默认的图颜色并不是很美观，我们可以对它进行调整点击 Show Control Dialog 打开控制面板，具体的参数注释如下

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623125518.png)

一般就是先对其进行聚类，然后调整颜色，我个人一般把 0.00 作为分界线改为白色，图形改为圆形，将上下限改为一样的数值，那么中心值自然就是 0.00 了

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623125936.png)

然后修改字体，个人一般喜欢新罗马字体

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623130249.png)

## 导出图形

最好选 600dpi 的，这个像素要高一些，更加清晰

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/20230623130336.png)
