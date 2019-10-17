---
layout: post
current: post
cover:  assets/images/design.jpg
navigation: True
title: 浅谈python装饰器的用法
date: 2019-10-17 04:00:00
tags: [python]
class: post-template
subclass: 'post tag-getting-started'
author: lishufu
---

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;装饰器在Python中使用的是比较广泛的，装饰器主要的作用就是在不改变原先代码的情况下给源代码添加功能，比如在版本迭代过程中需要给源代码添加功能，有不方便修改原先的代码，这时候用装饰器是最好的选择。在介紹装饰器之前，我们先来了解下闭包的概念和思想:

## 一、什么是闭包  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;闭包这个概念在 JavaScript 中讨论和使用得比较多，不过在 Python 中却不是那么显而易见，之所以说“不是那么”，是因为即使用到了，也没用注意到而已，比如定义一个 Decorator 时，就已经用到闭包了。 
```python
def fun1(count=1):
    print count
    
    def addCount(x):
        return x + count
    return addCount

fun2 = fun1(100)
print fun2(2)
print fun2.__closure__ // --> (<cell at 0x0000000002FEC8B8: int object at 0x0000000002ACB860>,)
```

## 二、如何形成闭包  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;要形成闭包，首先得有一个嵌套的函数，即函数中定义了另一个函数，闭包则是一个集合，它包括了外部函数的局部变量，这些局部变量在外部函数返回后也继续存在，并能被内部函数引用。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;上面的例子就是一个简单的闭包，外部函数的局部变量可以被内部函数引用，即使外部函数已经返回了。可以用于延时执行内部函数，保存了外部函数的值，用比较容易懂的人话说，就是当某个函数被当成对象返回时，夹带了外部变量，就形成了一个闭包，通常可用于属性封装。***__closure__*** 属性中定义了一个包含了cell的元祖，用来保存作用域中变量值，这也是为什么函数中的值能暂时保存下来的原因。  
***注：尽量不要在外部函数中使用循环变量，如果需要使用，不要直接调用外部循环变量，可使用传参的方式进行传值***

## 三、什么是装饰器

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用一个方法装饰包装另一个方法，起到不影响原方法而达到拓展功能的作用，底层就是基于闭包实现的，有点像注解的方式，可实现类似于基于动态代理的aop切面编程。

* 普通装饰器 
```python
def log(f):
    @wraps(f)
    def exec_methods(*args, **kwargs):
        print 'before exec'
        res = f(*args, **kwargs)
        print 'after exec'
        return res
    return exec_methods
```

* 基于类的装饰器 
```python
class logit:
    def __init__(self, logfile='test.log'):
        self.logfile = logfile

    def __call__(self, func):
        @wraps(func)
        def exec_methods(*args, **kwargs):
            print 'before exec'
            print 'logfile: %s' % self.logfile
            res = func(*args, **kwargs)
            print 'after exec'
            return res
        return exec_methods
```  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;装饰器用法灵活，使用的地方很多，这里只是简单介紹了两种方式，还要很多方式实现，比如借用__call__()来实现也是可以的，多个装饰器可以嵌套作用，至上而下，其实就是上一个装饰器对下一个装饰器进行装饰。

## 四、拓展@的用法

* 在python2.x和<=3.4版本中，@只用作装饰器
* 矩阵乘法 此用法只能是在>=python3.5中使用，python2中不能用

这里主要介紹矩阵乘法，此用法其实就是操作符重载,解释器会把@运算转换成对__matmul__()方法的调用，所以要使用@作为矩阵乘法，则这个类必须实现__matmul__()方法噢。
```python
class A:
	def __init__(self, val):
		self.val = val
	def __matmul__(self, b):	# 定义与@符号绑定的函数
		print("in __matmul__ A", self.val, b.val)
	def __mul__(self, b):		# 定义与*符号绑定的函数
		print("in __mul__ A", self.val, b)
	def __rmul__(self, b):		# 定义右乘,即A类对像在*右侧,且*左侧不是A对象时调用
		print("in __rmul__ A", self.val, b) 
a = A(100)
b = A(200)
a@b 	#out: in __matmul__ A 100 200
a*b	#out: in __mul__ A 100 <__main__.A object at 0x7f8aca1f16d8>
a*10	#out: in __mul__ A 100 10
10*a	#out: in __rmul__ A 100 10
```
当然我上面只是为了验证@和*所绑定的方法是哪些,实际应用中还是要根据需要来定义这些方法的，比如@一般是用于矩阵乘法,实现__matmul__时,就要根据你的需求来实现矩阵乘法。
