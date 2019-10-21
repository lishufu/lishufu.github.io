---
layout: post
current: post
cover:  assets/images/bus.jpg
navigation: True
title: python中正则表达式的使用
date: 2019-10-21 04:00:00
tags: [python]
class: post-template
subclass: 'post tag-getting-started'
author: lishufu
---


 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;正则表达式\(regular expression\)描述了一种字符串匹配的模式\(pattern\)，可以用来检查一个串是否含有某种子串、将匹配的子串替换或者从某个串中取出符合某个条件的子串等。

 * ### 非打印字符  
非打印字符也可以是正则表达式的组成部分。下表列出了表示非打印字符的转义序列：  
 
> \cx：匹配由x指明的控制字符。例如， \cM 匹配一个 Control-M 或回车符。x 的值必须为 A-Z 或 a-z 之一。否则，将 c 视为一个原义的 'c' 字符。  
> \f：匹配一个换页符。等价于 \x0c 和 \cL。  
> \n：匹配一个换行符。等价于 \x0a 和 \cJ。  
> \r：匹配一个回车符。等价于 \x0d 和 \cM。  
> \s：匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。注意 Unicode 正则表达式会匹配全角空格符。  
> \S：匹配任何非空白字符。等价于 [^ \f\n\r\t\v]。  
> \t：匹配一个制表符。等价于 \x09 和 \cI。  
> \v：匹配一个垂直制表符。等价于 \x0b 和 \cK。  

* ### 特殊字符 

> $：以什么结束  
> ()： 捕获组的开始和结束  
> \*：表示出现0-n次  
> \+：表示出现1-n次  
> .：除换行\n之外的所有字符  
> []: 一个表达式的开始和结束  
> ?: 捕获元时使用；贪婪表达式(一般用在\*或者\+后面表示最小匹配)   
> ^: []内表示取反；其他地方表示以什么开头  
> {m,n}：限定符,表示长度在m-n之间  {0,}相当于\* {1,}相当于 \+

```python
#https://www.baidu.com.cn
str = 'http://www.hao123.com' 
re_str = '(?:[http|https]\:\/\/)([w]{3}\.[\w\.]+)' #匹配以http或者https开头 捕获以www.开头的所有字符串
res = re.findall(re_str, str)
print res
```

* ### 捕获组
1. 普通捕获组 (Expression) 例如([a-zA-Z]{10})[0-9]+ 括号括起来的就是一个捕获组，会将匹配到的内容缓存在缓存中，最多可缓存99个缓冲区，可通过\n访问。  
2. 命名捕获组 下面例子中，就命名了一个叫lsf的捕获组，后面使用\1来引用。python和php中使用(?P\<name\>Expression)。
```python
re_str = '(?P<lsf>[a-b]+[c-d])(?P<sf>[\d]{1,10}){1,4}(\\1)'
str = 'abc2abc'
res = re.findall(re_str, str)
print res[0]
```

* ### 非捕获组  

1. 非捕获型分组**?:**  有的时候我们可能只想匹配分组，但是并不想缓存（不想捕获）匹配到的结果，就可以在我们的分组模式前面加上?:。例如上面的例子，在缓存区中我们就不会有前面的http哪个组信息。
2. 正向前瞻型捕获**?=**  下面例子中，想匹配€前面的10，但不想匹配到1，而且不希望把€匹配出来(匹配挨着€并且在€前面的数字)。
```python
str = '1 apple costs 10€fsfs20'
re_str = '\d+'
re_str1 = '\d+(?=€)'
res = re.findall(re_str1, str)
print res
```  

3. 负向前瞻型捕获**?!** 匹配规则和**?=**相反，上面的如果替换成负向前瞻型捕获的话，就会匹配到["1". "1", "20"] (就是上面的取非 !匹配挨着€并且在€前面的数字)

4. 正向后顾型捕获**?<=** 下面例子中，想匹配€后面面的200，但不想匹配到1，而且不希望把€匹配出来(匹配挨着€并且在€后面的数字)。 
```python
str = '1 apple costs 10€20';
re_str = '\d+'
re_str1 = '(?<!€)\d+'
res = re.findall(re_str1, str)
print res
```  

5. 负向后顾型捕获**?<!** 匹配规则和**?=**相反，上面的如果替换成负向后顾型捕获的话，就会匹配到["1". "10", "0"] (就是上面的取非 !匹配挨着€并且在€后面的数字)
***注意?=和?!表达式在后面，?<=和?<!表达式在正则前面***

* ### re.match函数
尝试从字符串的开始进行匹配，如果匹配上了返回一个匹配对象，否则返回None，即有种自带^的感觉
re.match(pattern, string, flags=0)  
pattern	匹配的正则表达式。
string	要匹配的字符串。
flags	标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等。

* ### re.search
不会从开始进行匹配，只要匹配上了返回一个匹配对象，否则返回None
re.search(pattern, string, flags=0)  
pattern	匹配的正则表达式。
string	要匹配的字符串。
flags	标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等。
```python
str = 'rt1 apple costs 10€20';
re_str = '\d+'
res = re.search(re_str, str)
print res.string
```  

* ### re.sub检索和替换
替换字符串中的匹配项  
re.sub(pattern, repl, string, count=0, flags=0)  
pattern : 正则中的模式字符串。  
repl : 替换的字符串，也可为一个函数。  
string : 要被查找替换的原始字符串。  
count : 模式匹配后替换的最大次数，默认 0 表示替换所有的匹配。 

* ### re.compile 函数
用于编译正则表达式，生成一个正则表达式（ Pattern ）对象，供 match() 和 search() 这两个函数使用。

* ### re.findall 函数
匹配指定正则，返回一个列表list
re.finditer(pattern, string, flags=0)
```python
str = 'rt1 apple costs 10€20';
re_str = '\d+'
res = re.findall(re_str, str)
print res
# ['1', '10', '20']
```  
* ### re.finditer 函数
匹配指定正则，返回一个可迭代对象  
re.finditer(pattern, string, flags=0)

* ### re.split 函数
split 方法按照能够匹配的子串将字符串分割后返回列表，它的使用形式如下：
re.split(pattern, string[, maxsplit=0, flags=0])

文中绝大部分内容来源于[菜鸟教程](https://www.runoob.com/python/python-reg-expressions.html)  