---
title: Python 计算等效 A 声级
tags:
  - "Python"
slug: b2m1h0n5
date: 2023-09-13T21:24:56+08:00
---

利用 Python 计算国家环境噪声监测技术规范中的等效声级。

<!--more-->

```python
import math

with open("./data.txt") as noise_data:
    lines = noise_data.readlines()
    number_string = '\t'.join(lines)

number = number_string .split('\t')
number = [x.strip() for x in number if x.strip()!='']
number.pop(0)
number.pop(200)
# number.pop(400)

def L_number(order):
    for i in number[:order]:
        l = 0
        l = l + (1/order)*pow(order, (0.1)*float(i))
    L_10 = 10*math.log(10,l)
    
    for i in number[:order+40]:
        l = 0
        l = l + (1/(order + 40))*pow(order, (0.1)*float(i))
    L_50 = 10*math.log(10,l)

    for i in number[:order+80]:
        l = 0
        l = l + (1/(order + 80))*pow(order, (0.1)*float(i))
    L_90 = 10*math.log(10,l)

    L_eq = L_50 + ((L_10 - L_90)**2)/60
```

从 data.txt 中导入 200 个数据进行计算，分别求得 L10、L50、L90，最后计算 Leq

![](https://jihulab.com/UncleCAT4/static/-/raw/main/blog/20230913212744.png)