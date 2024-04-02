---
title: C++解汉诺塔递归算法
tags:
  - "C/C++"
slug: y1a3q4d6o0o
date: 2024-04-02T13:14:29+08:00
---

汉诺塔（又称河内塔）问题是源于印度一个古老传说的益智玩具。大梵天创造世界的时候做了三根金刚石柱子，在一根柱子上从下往上按照大小顺序摞着 64 片黄金圆盘。大梵天命令婆罗门把圆盘从下面开始按大小顺序重新摆放在另一根柱子上。并且规定，在小圆盘上不能放大圆盘，在三根柱子之间一次只能移动一个圆盘。

<!--more-->

抽象为数学问题就是从左到右有 A、B、C 三根柱子，其中 A 柱子上面有从小叠到大的 n 个圆盘，现要求将 A 柱子上的圆盘移到 C 柱子上去，期间只有一个原则：一次只能移到一个盘子且大盘子不能在小盘子上面，求移动的步骤和移动的次数。

![](https://images.yuanj.top/202404021317052.png)

解：

- n == 1
  - 第 1 次  1 号盘  A---->C       sum = 1 次
- n == 2
  - 第 1 次  1 号盘  A---->B
  - 第 2 次  2 号盘  A---->C
  - 第 3 次  1 号盘  B---->C       sum = 3 次
- n == 3
  - 第 1 次  1 号盘  A---->C
  - 第 2 次  2 号盘  A---->B
  - 第 3 次  1 号盘  C---->B
  - 第 4 次  3 号盘  A---->C
  - 第 5 次  1 号盘  B---->A
  - 第 6 次  2 号盘  B---->C
  - 第 7 次  1 号盘  A---->C       sum = 7 次

那么规律就是：

- 1 个圆盘的次数 2 的 1 次方减 1
- 2 个圆盘的次数 2 的 2 次方减 1
- 3 个圆盘的次数 2 的 3 次方减 1
- ... 
- n 个圆盘的次数 2 的 n 次方减 1

故：移动次数为：2^n - 1

用 C 语言来实现就是：

```c
#include <stdio.h>
int main()
{
    void hanoi(int n,char one,char two,char three);
    int m;
    printf("input the number of diskes:");
    scanf("%d",&m);
    printf("The step to move %d diskes:\n",m);
    hanoi(m,'A','B','C');
    return 0;
}

void hanoi(int n,char one,char two,char three)
{
    void move(char x,char y);
    if (n==1)
        move(one,three);
    else
        {
            hanoi(n-1,one,three,two);
            move(one,three);
            hanoi(n-1,two,one,three);
        }
}

void move(char x,char y)
{
    printf("%c-->%c\n",x,y);
}
```