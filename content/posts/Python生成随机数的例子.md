---
title: Python 生成随机数例子
tags:
  - "Python"
slug: a7n0j4j9
date: 2023-09-12T12:35:51+08:00
---

最近在学习 Pyhotn，突然想到实验课作业里需要几百个噪音的数据，于是使用 Pyhton 生成随机数来模拟一下。

<!--more-->

```python
import random
import os

def noise(location, number):
    result = ""
    if location == 'library':
        result += "图书馆噪声数据："
        result += '\n\n'
    if location == 'lab':
        result += "实验楼噪声数据："
        result += '\n\n'
    if location == 'dormitory':
        result += "寝室噪声数据："
        result += '\n\n'
    for i in range(10):
        for i in range(10):
            noise_figure = number + random.uniform(0,6)
            result += str("{:.5}".format(noise_figure),) + '\t'
        result += '\n'
    result += '\n'
    with open("./data.txt", "a") as data:
        data = data.write(result)

data = "./data.txt"
file = open(data, "w")
file.close()
if os.path.getsize(data) != 0:
    os.remove(data)
else:
    pass

noise('library', 45)
noise('lab', 50)
noise('dormitory', 60)
```