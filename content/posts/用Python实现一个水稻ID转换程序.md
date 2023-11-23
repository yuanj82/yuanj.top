---
title: 用 Python 实现水稻 ID 转换程序
tags:
  - "Python"
slug: k9r5v4g4
date: 2023-10-02T09:55:32+08:00
---

使用 Python 实现水稻 OS ID 与 LOCs ID 互相转化、提取序列等功能。

<!--more-->

## 用法

GitHub 仓库：[OStools](https://github.com/UncleCAT4/OStools) 还在不断更新中。

安装 Python 后，Windows 系统运行`start.exe`，Linux 系统运行`main.py`。

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/202309292124230.png)

## 实现

主体程序是在一个 while 循环中，定义了两个函数`ID_converter`与`extract_id`

```python
def ID_converter(ID):
    with open("./ID.txt", "r+") as file:
        ID_corresponding_list = file.readlines()

    for ID in ID_corresponding_list:
        if gene in ID:
            print('RAP ID:{}'.format(ID.split('\t')[0]))
            print('MSU ID:{}'.format(ID.split('\t')[1]))
            global OS_ID
            OS_ID = ID.split('\t')[0]
            break
        else:
            continue
    else:
        print("sorry, ID not found!")
```

`ID_converter`函数从 [ID.txt](https://rapdb.dna.affrc.go.jp/download/archive/RAP-MSU_2023-09-07.txt.gz) 中读取 ID 对应关系，这个文件是从 [RAP-DB](https://rapdb.dna.affrc.go.jp/) 上下载的，RAP-DB 也是我们最常用的水稻 ID 转换工具，对应关系如下面的格式：

```txt
Os01g0100100	LOC_Os01g01010.1,LOC_Os01g01010.2
Os01g0100200	LOC_Os01g01019.1
Os01g0100300	None
```

逐行读取 ID 的对应关系之后，for 循环内嵌 if 判断在每一行 ID 中搜索包含输入的 ID，找到对应的行之后根据制表符（\t）进行分割，前面的是 RAP ID，后面的是 MSU ID，分开进行输出，最后再将 RAP ID 定义为全局变量以便于下一个函数调用。

```python
def extract_id():
    if OS_ID == 'None':
        print("can't")
        return
    else:
        pass
    id_list = []
    Processed_ID = OS_ID.replace('g', 't')
    Processed_ID_0 = Processed_ID + '-00'
    Processed_ID_1 = Processed_ID + '-01'
    id_list.append(Processed_ID_0)
    id_list.append(Processed_ID_1)
    print()
    print("Protein:")
    with open("./os.protein.fasta", "r") as protein:
        seq = protein.readlines()
    
    for protein_id in id_list:
        for i in range(len(seq)):
            if protein_id in seq[i]:
                print(seq[i], end='')
                print(seq[i+1])
                break
    
        else:
            print(">{}:Unable to find protein sequence".format(protein_id))
```

`extract_id`函数传入上一个函数中的 RAP ID，并且将其转换为两个转录本的形式 -00、 -01，再从蛋白质序列文件中进行搜索。

下面的这段代码主要是修改 ID 的格式，对应关系中水稻的 ID 是`Os01g0100466`这样的格式，而蛋白质序列文件中每一个基因可能包含多个转录本，如`Os01t0538000-01`、`Os01t0538000-02`、`Os01t0538000-03`，但我们通常所使用的蛋白质是转录本 -00 和-01，所以将 ID 转换成这两个转录本提取序列。

```python
    Processed_ID = OS_ID.replace('g', 't')
    Processed_ID_0 = Processed_ID + '-00'
    Processed_ID_1 = Processed_ID + '-01'
```

蛋白质序列文件中每个基因有两行，一行是 ID，另一行是序列，所以下面的这段代码在找到转录本所在行之后输出该行（ID）与下一行（序列）。

```python
        for i in range(len(seq)):
            if protein_id in seq[i]:
                print(seq[i], end='')
                print(seq[i+1])
```

## 拓展

如果要用于其它物种，更换 ID 对应关系和蛋白质序列，修改输出信息代码和对 ID 进行更改的代码即可。

```python
            print('RAP ID:{}'.format(ID.split('\t')[0]))
            print('MSU ID:{}'.format(ID.split('\t')[1]))
```

```python
    Processed_ID = OS_ID.replace('g', 't')
    Processed_ID_0 = Processed_ID + '-00'
    Processed_ID_1 = Processed_ID + '-01'
```

## TODO

- GUI 界面程序
- 增加选择项
- ID 对应关系与蛋白质序列更新程序
- 批量转换与提取