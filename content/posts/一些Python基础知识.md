---
title: 一些 Python 基础知识
date: 2023-08-31T21:29:55+08:00
tags: [Python]
slug: pyd23ds
---

整理一些学习 Python 过程中遇到的一些知识。

<!--more-->

## 标识符

标识符的命名规则：

- 区分大小写
- 首字符可以是下划线和字母，但不能是数字
- 除首字符外的其他字符必须是下划线、字母和数字
- 关键字不能作为标识符
- 不要使用 Python 的内置函数作为标识符

## 关键字

False、def、if、raise、None、del、import、return、True、elif、in、try、and、else、is、lambda、with、as、except、while、assert、finally、nonlocal、yield、break、for、not、class、from、or、continue、global、pass

## 位运算符

| 运算符 | 名称 | 例子|
| -- | -- | -- |
| ~ | 位反 | ~x |
| & | 位与 | x&y |
|&#124;| 位或 | x&#124;y |
| ^ | 位异或 | x^y |
| >> | 右移 | x << a x 右移 a 位，高位用符号补位 |
| << | 左移 | x << a x 左移 a 位，低位用 0 补位 |

运算符优先级：算术运算符>位运算符>关系运算符>逻辑运算符>赋值运算符

## 容器类型数据

### 字符串

**切片**
[start: end :step] 

- 切片包括 start 位置的元素但不包括 end 位置的元素
- start 和 end 可以省略
- 切片从零开始

str.split(sep=None, maxsplit=1)

- 使用 sep 子字符串切割 str 字符串
- maxsplit 是最大分割次数，如被省略则表明不限分割次数

**查找字符串**

str.fing(sub[, start[, end]]) 在 start 和 end 之间查找子字符串 sub

str.find('i',4,6) 在 4，6 位置之间查找 i，4、6 可以省略

### 列表

| 方法 | 用法 |
| -- | -- | 
| append(x) | 末尾追加一个元素 |
| extend(x) | 末尾追加多个元素 | 
| insert(i,x) | 在 i 位置插入元素 x |
| list[1]=80 | 将列表 list 中的元素 1 替换为 80 |
| remove(x) | 删除元素 x |

### 元组

元组不可更改

| 方法 | 用法 |
| -- | -- |
| tuple(item) | 创建元组，item 可以是字符串、列表、元组、集合、字典等 |
| / | 直接使用 () 将元素括起来便可直接创建元组 |
| / | 使用变量分别对应元素内的每个元素便可拆包，元素被赋值给变量 |

### 集合

集合可迭代、无序、不能包含重复元素

| 方法 | 用法 |
| -- | -- |
| set(item) | 创建集合，item 是可迭代对象字符串、列表、元素、集合、字典等 |
| / | 直接使用{}将元素括起来便可直接创建集合 |
| add(x) | 添加元素 |
| remove(x) | 删除元素 |
| clear() | 清除集合 |

### 字典

字典可迭代，通过键来访问元素

| 方法 | 用法 |
| -- | -- |
| dict(item) | 创建字典
| / | {key1:value1,key2:value2,....}直接创建字典 |
| items() | 返回字典的所有键值对 |
| keys() | 返回字典的键 |
| values() | 返回字典的值 |

## 转义字符

| 字符表示 | Unicode 编码 | 说明 |
| -- | -- | -- |
| \t | \u0009 | 水平制表符 |
| \n | \u000a | 换行 |
| \r | \u000d | 回车 |
| \" | \u0022 | 双引号 |
| \' | \u0027 | 单引号 |
| \\\ | \u005c | 反斜线 |

## format 格式化

### 占位符
eg：
```python
a = 1
b = 2
abc = '{}{}'.format(a,b) # abc=12
print(abc)
```

{}为占位符

### 控制符

| 控制符 | 说明 |
| -- | -- |
| s | 字符串 |
| d | 十进制整数 |
| f、F | 十进制浮点数 |
| g、G | 十进制整数或浮点数 |
| e、E | 科学计数法表示浮点数 |
| o | 八进制整数 |
| x、X | 十六进制整数，x 为小写表示，X 为大写表示 |

使用方法

```python
a = int(1)
b = int(2)
abc = '{:g}{}'.format(a,b) # abc=12
print(abc)
```

## 异常处理

```python
# 异常捕获与资源释放
i = input('请输入数字')
n = 8888
try:
    result = n / int(i)
    print(result)
    print('{0}除以{1}等于{2}'.format(n, i, result))
except ZeroDivisionError as e:
    print("不能处理 0，异常：{}".format(e))
except ValueError as e:
    print("输入的数字是无效数字，异常：{}".format(e))
finally:
    print("资源释放。..")
```

```python
# try-except 语句嵌套
i = input('请输入数字')
n = 8888
try:
    i2 = int(i)
    try:
        result = n / i2
        print('{0}除以{1}等于{2}'.format(n, i2, result))
    except ZeroDivisionError as e1:
        print("不能处理 0，异常：{}".format(e1))
except ValueError as e2:
    print("输入的数字是无效数字，异常：{}".format(e2))
finally:
    print("资源释放。..")
```

```python
# 手动触发异常
class ChufaException(Exception):
    def __init__(self, message): 
        super().__init__(message)

# 异常捕获与资源释放
i = input('请输入数字')
n = 8888
try:
    result = n / int(i)
    print(result)
    print('{0}除以{1}等于{2}'.format(n, i, result))
except ZeroDivisionError as e:
    raise ChufaException('不能除以 0')
except ValueError as e:
    raise ChufaException('输入的是无效数字')
finally:
    print("资源释放。..")
```

## 数学计算模块

```python
# 数学计算模块
import math
print(math.ceil(10.332)) # 返回大于或等于 x 的最小整数
print(math.floor(21.4654)) # 返回小于或等于 x 的最小整数
print(math.sqrt(4))  # 返回平方根
print(pow(10, 3)) # 返回 10 的三次方
print(math.log(10, 3)) # 求对数
print(math.sin(30)) # 返回弧度的三角正弦
print(math.degrees(120)) # 将弧度转换为角度
print(math.radians(30)) # 将角度转换为弧度
```