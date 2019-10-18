---
layout: post
current: post
cover:  assets/images/locked.jpg
navigation: True
title: python类中__init__()、__new__()和__call__()三者的区别
date: 2019-10-18 04:00:00
tags: [python]
class: post-template
subclass: 'post tag-getting-started'
author: lishufu
---
**__new__()**、**__init__()**、**__call__()**等方法不是必须写的，会默认调用，如果自己定义了，就是override,可以custom。既然override了，通常也会显式调用进行补偿以达到extend的目的。


* **__init__()**: 负责对象的初始化，对象属性的初始化赋值
内置一个self对象，表示被实例化的对象
```python
class func_tool:
    count = 0
    def __init__(self, count):
        self.count = count
# 实例化
func = func_tool(1)
```
* **__new__()**: python2.x中 如果对象没有继承object不会有这个函数 python3.x默认继承object类该方法用来实例化一个对象，返回值即会传给__init_中的self。在__init__调用之前，两个方法除self外，保持一致，__new__最常见用法的即为单例模式。
```python
class func_tool(object):
    _has_been_instantiated = None
    def __init__(self, count):
        self.count = count
    def __new__(cls, *args, **kwargs):
        if not cls._has_been_instantiated:
            cls._has_been_instantiated = object.__new__(cls, *args, **kwargs)
        return cls._has_been_instantiated
```
* **__call__()**:当实例对象被当作函数调用时，会自动触发这个函数，实现**__call__**可以将类变成一个可调用对象，你可以像调用函数一样调用他们 **obj.__call()__**和**obj()**本质上是一样的，由于**__call__()**参数是不固定的，使用起来就很灵活，我们可以使用其封装参数固定的接口，也可以实现基于类的装饰器。 
```python
# 用法一：使用闭包和工厂模式,封装length验证方法,扩展其用法
class Length(object):
    def __init__(self, min=-1, max=-1, message=None):
        self.min = min
        self.max = max
        if not message:
            message = u'Field must be between %i and %i characters long.' % (min, max)
        self.message = message
    def __call__(self, form, field):
        l = field.data and len(field.data) or 0
        if l < self.min or self.max != -1 and l > self.max:
            raise ValidationError(self.message)
class MyForm(Form):
    name = StringField('Name', [InputRequired(), Length(max=50)])
# 用法二：统计方法被调用次数
class Counter:
    def __init__(self, func):
        self.func = func
        self.count = 0
    def __call__(self, *args, **kwargs):
        self.count += 1
        return self.func(*args, **kwargs)
@Counter
def foo():
    pass
```