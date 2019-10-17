---
layout: post
current: post
cover:  assets/images/piano.jpg
navigation: True
title: 简单介紹python中with上下文的用法
date: 2019-10-17 04:00:00
tags: [python]
class: post-template
subclass: 'post tag-getting-started'
author: lishufu
---

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;with做为一种上下文管理器，在Python中的作用可以简单的理解为是用来代替try...except...finally的处理流程。有一些任务，可能事先需要设置，事后做清理工作。对于这种场景，Python的with语句提供了一种非常方便的处理方式。
# 什么是with上下文？  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;with通过__enter__方法初始化，然后在__exit__中做善后以及处理异常。对于一些需要预先设置，事后要清理的一些任务，with提供了一种非常方便的表达。
在紧跟with后面的语句被求值运算后，会调用运算返回对象的__enter__方法，并将__enter__的返回结果赋值给as后面的变量。当with后面的代码块全部被执行完之后，将调用返回对象的__exit__()方法执行清理工作。 
* __enter__方法将在进入代码块前被调用。
* __exit__将在离开代码块之后被调用(即使在代码块中遇到了异常)。
```python
file = open("file.txt")
try:
    data = file.read()
finally:
    file.close()
```
等价于：
```python
with open("file.txt") as file:
    data = file.read()
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;任何对象只要拥有__enter__方法和__exit__方法，都可使用with···as···，创建上下文简化代码  
```python  
class WithDemo:
    def __enter__(self):
        return self
    def __exit__(self, type, value, trace):
        print "type:", type
        print "value:", value
        print "trace:", trace

    def create_trace(self):
        return (1 / 0) + 10
# 调用
with WithDemo() as demo:
    demo.create_trace()
    raise Exception('error')
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中exit中几个参数，type代表异常类型；value代表异常内容；trace代表异常所在位置，如果没有异常，三个值都为None，当方法返回True时，不会抛出异常，因此我们可以利用这一点过滤掉指定异常。