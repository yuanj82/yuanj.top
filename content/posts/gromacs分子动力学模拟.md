---
title: Gromacs 分子动力学模拟
tags:
  - "Molecular dynamics"
slug: v5p7o4y0
date: 2023-11-15T18:16:25+08:00
---

近期做了一次分子动力学模拟实验，命令比较多，整理一下步骤，参考了生信实战的教程，但他们的教程存在很多问题，这里完善一下。

以江苏大学一位师兄提供的蛋白文件为基础，前提是要去除小分子，否则 gromacs 会报错提示有非标准残基。

<!--more-->

## 环境准备

使用 conda 来安装软件：

```bash
conda create -n gmx python=3.9 #创建虚拟环境
conda activate fmx
conda install -c bioconda gromacs
```

安装完成后会 WARING，需要安装 pocl：

```bash
conda install -c conda-forge pocl
```

## 数据准备

准备好蛋白文件，我这里的蛋白文件是`YdcW_C284S.pdb`。

首先去除本实验中不需要的水分子，将去除水分子的蛋白文件命名为`YdcW_C284S.clean.pdb `：

```bash
grep -v HOH YdcW_C284S.pdb > YdcW_C284S.clean.pdb 
```

生成拓扑文件，并且将拓扑文件命名为`YdcW_C284S_processed.gro`：

```bash
gmx pdb2gmx -f YdcW_C284S.clean.pdb -o YdcW_C284S_processed.gro -water spce
```

这时会提示我们进行选择，输入`15`选择`OPLS-AA/L all-atom force field (2001 aminoacid dihedrals)`力场。

执行后记录下体系的电荷情况，例如本例：`Total charge 9.000 e`。

接下来需要下载一些参数文件，为后面的分析做准备：

```bash
wget http://www.mdtutorials.com/gmx/lysozyme/Files/ions.mdp
wget http://www.mdtutorials.com/gmx/lysozyme/Files/minim.mdp
wget http://www.mdtutorials.com/gmx/lysozyme/Files/nvt.mdp
wget http://www.mdtutorials.com/gmx/lysozyme/Files/npt.mdp
wget http://www.mdtutorials.com/gmx/lysozyme/Files/md.mdp
```

## 定义模拟体系的尺寸并加水

先使用 editconf 模块定义晶胞容器的尺寸，并且将处理后的文件命名为`YdcW_C284S_newbox.gro`：

```bash
gmx editconf -f YdcW_C284S_processed.gro -o YdcW_C284S_newbox.gro -c -d 1.0 -bt cubic
```

使用 solvate 调用溶剂模块向晶胞容器中填充水：

```bash
gmx solvate -cp YdcW_C284S_newbox.gro -cs spc216.gro -o YdcW_C284S_solv.gro -p topol.top
```

## 为模拟体系添加离子

生成`ions.tpr`文件：

```bash
gmx grompp -f ions.mdp -c YdcW_C284S_solv.gro -p topol.top -o ions.tpr
```

加入中和离子：

```bash
gmx genion -s ions.tpr -o YdcW_C284S_solv_ions.gro -p topol.top -pname NA -nname CL -neutral
```

选择 13 后回车。

## 体系能量最小化

生成能量最小化的拓扑文件：

```bash
gmx grompp -f minim.mdp -c YdcW_C284S_solv_ions.gro -p topol.top -o em.tpr
```

执行能量最小化：

```bash
gmx mdrun -v -deffnm em
```

这一步需要很长时间，所以建议使用下列命令挂在后台运行：

```bash
nohup gmx mdrun -v -deffnm em &
```

gmx energy 模块来分析能量最小化的结果：

```bash
gmx energy -f em.edr -o potential.xvg
```

输入 10 0 来选择势能 Potential(10), 并用零 (0) 来结束输入。

xvg 文件可以使用 DuIvyTools 来进行查看，DuIvyTools 需要用 pip 来安装：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple #设置为清华源
pip install DuIvyTools
```

使用下列命令查看

```bash
dit xvg_show -f potential.xvg
```

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/202311151946742.png)

## NVT 温度平衡

执行：

```bash
gmx grompp -f nvt.mdp -c em.gro -r em.gro -p topol.top -o nvt.tpr
```

再执行下列命令，所需时间比较长，依旧挂在后台：

```bash
nohup gmx mdrun -deffnm nvt &
```

平衡结果分析：

```bash
gmx energy -f nvt.edr -o temperature.xvg
```

输入 16 0，选择温度代号 16，选择 0 退出。

查看温度平衡情况：

```bash
dit xvg_show -f temperature.xvg
```

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/202311151946187.png)

## NPT 压力平衡

执行：

```bash
gmx grompp -f npt.mdp -c nvt.gro -r nvt.gro -t nvt.cpt -p topol.top -o npt.tpr

nohup gmx mdrun -deffnm npt &
```

压力分析：

```bash
gmx energy -f npt.edr -o pressure.xvg
```

根据提示输入 18 0 获得压力的数据结果。

查看压力分析结果：

```bash
dit xvg_show -f pressure.xvg
```

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/202311151947851.png)

密度分析：

```bash
gmx energy -f npt.edr -o density.xvg
```

根据提示 输入 24 0 获得结果。

查看压力分析结果：

```bash
dit xvg_show -f pressure.xvg
```

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/202311151947188.png)

## 正式的动力学模拟

执行 grompp 生成模拟的 tpr 文件：

```bash
gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p topol.top -o md_0_1.tpr
```

执行模拟：

```bash
nohup gmx mdrun -deffnm md_0_1 & #挂在后台
```

## 分子动力学模拟结果分析

使用下列命令进行修复：

```bash
gmx trjconv -s md_0_1.tpr -f md_0_1.xtc -o md_0_1_noPBC.xtc -pbc mol -center
```

选择 1 (Select 1 ("Protein") as the group to be centered and 0 ("System") for output) 完成后生成 md_0_1_noPBC.xtc，我们将基于这个修复的数据进行后续的数据分析。

计算 RMSD：

```bash
gmx rms -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rmsd.xvg -tu ns
```

选择 4 ("Backbone") for both the least-squares fit and the group for RMSD calculation。

查看 RMSD 结果：

```bash
dit xvg_show -f rmsd.xvg
```

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/202311151947005.png)

计算相对于模拟之前晶体的结构差异，可以使用下面的命令：

```bash
gmx rms -s em.tpr -f md_0_1_noPBC.xtc -o rmsd_xtal.xvg -tu ns
```

也是选择 4 ("Backbone")。

计算回旋半径：

```bash
gmx gyrate -s md_0_1.tpr -f md_0_1_noPBC.xtc -o gyrate.xvg
```

选择 group 1 (Protein)，执行完成后新生成了 gyrate.xvg。

查看结果：

```bash
dit xvg_show -f gyrate.xvg
```

![](https://cdn.jsdelivr.net/gh/yuanj82/static/blog/202311151957493.png)

这一套流程都是按照默认参数和步骤来的，实际上我也不太懂，但是大多数情况下使用默认参数是足以满足实验需求。