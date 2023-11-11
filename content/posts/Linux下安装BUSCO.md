---
title: Linux 下安装 BUSCO
tags: [Linux,生物信息学]
slug: 24ed802
date: 2023-07-21 17:22:17
---

官网说 BUSCO 直接可以用 conda 安装，但... 我从来没有成功过，经过多次的实践，总结了能够成功安装 BUSCO 的方法...

<!--more-->

> Benchmarking Universal Single-Copy Orthologs (BUSCO) 是用于评估基因组组装和注释的完整性的工具
> 通过与已有单拷贝直系同源数据库的比较，得到有多少比例的数据库能够有比对，比例越高代表基因组完整度越好

可以评估三种数据类型：

- 组装的基因组
- 转录组
- 注释到的基因对应的氨基酸序列

使用需要评估的生物类别所属的数据库（从 busco 数据库下载）比对，得出比对上数据库的完整性比例的信息

- [BUSCO 官网](https://busco.ezlab.org/)
- [BUSCO v5 数据库](https://busco-data.ezlab.org/v5/data/lineages/)

> BUSCO 官网上介绍说可以直接使用 conda 安装，但据我实践，是不可以的，需要手动安装诸多依赖

## 安装 conda

conda 的安装参考 [Linux 下安装 conda](https://www.hieroglyphs.top/posts/1d0dd329/)

## 安装所需依赖

先创建一个 Python3.7 或 3.6 的环境

```bash
conda create -n busco python=3.7
```

激活刚刚创建的环境

```bash
conda activate busco
```

升级环境内的 pip 并且换源

```bash
python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

安装所需依赖

```
pip install sepp

conda install pandas

conda install biopython==1.7
```

## 安装 BUSCO

我们需要使用刚刚创建的虚拟环境中的 Python 来编译安装 BUSCO，所以一定要先激活对应的环境

下载 BUSCO 安装包

```bash
wget https://gitlab.com/ezlab/busco/-/archive/5.4.5/busco-5.4.5.tar.gz
```

如果没有 wget，使用 curl 也可以，或者安装 wget

编译安装 BUSCO

```bash
cd busco-5.4.5

python setup.py install
```

至此便安装完成了，输入下列命令可以查看 BUSCO 的帮助信息，也可以验证 BUSCO 是否安装完成

```bash
busco -h
```

后面使用某些命令时可能会提示缺少模块，缺少哪个模块，搜索一下模块的包名，然后使用 conda 安装，如果 conda 不能按照，那就使用 pip 安装

```bash
conda install package.name
pip install package.name
```
