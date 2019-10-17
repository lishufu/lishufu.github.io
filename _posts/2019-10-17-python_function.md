---
layout: post
current: post
cover:  assets/images/sky.jpg
navigation: True
title: 记录python部分内置函数的使用规则
date: 2019-10-17 04:00:00
tags: [python]
class: post-template
subclass: 'post tag-getting-started'
author: lishufu
---
python给你提供的。 拿来直接⽤的函数，比如print., input等等。 截⽌到python版本3.6.2，python⼀共提供了68个内置函数

![alt 内置函数表](https://img2018.cnblogs.com/blog/1523707/201812/1523707-20181212174838105-2020336051.png)
* filter()  
  
> 用于过滤序列，过滤掉不符合条件的元素，返回一个迭代器对象，可用list()来转换为列表。
> filter()接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判断，然后返回True或 False，最后将返回 True 的元素放到新列表中。  
```python
arr = filter(lambda n:n>5, rang(10)) 
# arr中只会出现大于5的数
```

* format()

> 是一种格式化字符串的函数 ，基本语法是通过 {} 和 : 来代替以前的 % 。format 函数可以接受不限个参数，位置可以不按顺序。 
```python
# 位置映射
print( "{}{}".format('a','1'))
# a1
print('name:{n},url:{u}'.format(n='alex',u='www.xxxxx.com'))
# name:alex,url:www.xxxxx.com
# 元素访问
print( "{0[0]},{0[1]}".format(('baidu','com')) )                # 按顺序
# baidu,com
print( "{0[2]},{0[0]},{0[1]}".format(('baidu','com','www')) )   # 不按顺序
# www,baidu,com
```

* isinstance()、type()和issubclass()三者的比较  

> isinstance() 用于判断两个对象类型是否一直  
> type() 用于判断两个对象类型是否一直 考虑继承关系  
> issubclass() 比较两个对象是否为继承关系  
```python
print(issubclass(a,b))  
# 判断 a 是 b 的子类？
```

* iter()

> 用来生成迭代器。list、tuple等都是可迭代对象，我们可以通过iter()函数获取这些可迭代对象的迭代器，然后可以对获取到的迭代器不断用next()函数来获取下条数据。iter()函数实际上就是调了可迭代对象的__iter__方法。  
> ***注意：当已经迭代完最后一个数据之后，再次调用next()函数会抛出 StopIteration的异常，来告诉我们所有数据都已迭代完成***

* map()  

> 接收两个参数(一个处理函数f和集合list)，并通过把函数f依次作用在list的每个元素上，得到一个新的list并返回。一般可以结合lambda表达式使用。
```python
map(lambda n: n*2,[0,1,2]) # [0,2,4]
# [0,1,2] -->[0,2,4]
```

* reduce()  

> 函数会对参数序列中元素进行累积 一般用来聚合求和什么的
```python
a = functools.reduce(lambda x,y:x+y,[1,2,3]) 
# 6 -> 1 + 2 的结果 + 3 依次类推
```