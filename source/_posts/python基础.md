---
title: python基础
date: 2019-08-24 23:42:37
type: "categories"
tags:
---

### 数据类型
- 整数:
    Python可以处理任意大小的整数，当然包括负整数，在程序中的表示方法和数学上的写法一模一样，例如：1，100，-8080，0，等等。
- 浮点数:
     对于很大或很小的浮点数，就必须用科学计数法表示，把10用e替代，1.23x109就是1.23e9
- 字符串:
    1.字符串是以单引号或者双引号括起来的任意文本
    2.r''表示内部的字符串默认不转义
- 布尔值：
    1.True和False
    2.布尔值可以用and,or,not来进行计算
- 空值：
    空值是Python里一个特殊的值，用None表示
- 变量:
    1.变量必须是大小写英文，数字和_的组合，并且不能以数字开头
    2.同一个变量可以反复赋值，并且可以是不同类型的变量
- 常量:
    1.所谓常量就是不能变的变量，通常用全部大写的变量名来表示常量
    2./除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数
    3.//表示地板除，其结果为整数
    
### 字符编码
- 计算机最早的时候使用ASCII编码，但处理中文一个字节显然不够，至少需要两个字节，还不能和ASCII编码冲突，因此中国制定了GB2312编码。但因为世界上有上百种语言，这时候，Unicode编码诞生。ASCII编码是1个字节，而Unicode编码通常是2个字节。但是如果你写的文本基本上全部是英文的话，用Unicode编码比ASCII编码需要多一倍的存储空间，在存储和传输上就十分不划算，所以本着节约的精神，又出现了把Unicode编码转换为可变长编码的UTF-8编码。
- ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符
- 由于Python的字符串类型是str，在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把str变为以字节为单位的bytes。Python对bytes类型的数据用带b前缀的单引号或双引号表示：x = b'ABC
- 以Unicode表示的str通过encode()方法可以编码为指定的bytes，例如:
```
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> '中文'.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
```
- 纯英文的str可以用ASCII编码为bytes，内容是一样的，含有中文的str可以用UTF-8编码为bytes。含有中文的str无法用ASCII编码，因为中文编码的范围超过了ASCII编码的范围，Python会报错。在bytes中，无法显示为ASCII字符的字节，用\x##显示。反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes。要把bytes变为str，就需要用decode()方法：
```
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```
- 如果bytes中包含无法解码的字节，decode方法会报错，但是如果只有一小部分无效字节的话，可以传入参数errors='ignore'忽略错误的字节：
```
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'
```
- 格式化字符串，%运算符就是用来格式化字符串的。在字符串内部，%s表示用字符串替换，%d表示用整数替换，有几个%?占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个%?，括号可以省略。如果字符串里面本来就有%,那就用%%表示%
```
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```
- format():另一种格式化字符串的方法是使用字符串的format()方法，它会用传入的参数依次替换字符串内的占位符{0}、{1}……，不过这种方式写起来比%要麻烦得多：
```
>>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
'Hello, 小明, 成绩提升了 17.1%'
```

### list
- list是一种有序的集合，可以随时添加和删除其中的元素。
```
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates
['Michael', 'Bob', 'Tracy']
```
- 如果要取最后一个元素，可以用-1作索引，直接获取最后一个元素:```classmates[-1]```
- 往list中追加元素到末尾:
```
>>> classmates.append('Adam')
>>> classmates
['Michael', 'Bob', 'Tracy', 'Adam']
```
- 也可以把元素插入到指定的位置，比如索引号为1的位置：
```
>>> classmates.insert(1, 'Jack')
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']
```
- 要删除list末尾的元素，用pop()方法：
```
>>> classmates.pop()
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']
```
-  要把某个元素替换成别的元素，可以直接赋值给对应的索引位置：
```
>>> classmates[1] = 'Sarah'
>>> classmates
['Michael', 'Sarah', 'Tracy']
```
- list里面的元素的数据类型也可以不同，比如：
```
>>> L = ['Apple', 123, True]
```
- list元素也可以是另一个list，比如：
```
>>> s = ['python', 'java', ['asp', 'php'], 'scheme']
>>> len(s)
4
```

### tuple
- tuple和list很类似，但是一旦初始化就不能修改,它没有append，insert这样的方法。可以正常使用classmates[0],classmates[1]，但是不能进行重新赋值了。因为tuple不可变，所以代码更安全，如果可能，尽量用tuple代替list。
```
>>> classmates=('a','b','c')
```
- 当你定义一个tuple时，在定义的时候，tuple的元素就必须确定下来：
```
>>> t = (1, 2)
>>> t
(1, 2)
```
- 定义空的tuple
```
>>> t = ()
>>> t
()
```
- 定义只有一个元素的tuple，如果写成这样:t=(1)，会被误认为是1这个数字，因为python会把()判定为(),所以只有1个元素的tuple定义时必须加一个逗号，来消除歧义:
```
>>> t = (1,)
>>> t
(1,)
```
- tuple只能保证其指向不变，但是并不能保证tuple中每一个元素本身不能发生变化。
```
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```
### 条件判断
- 根据Python的缩进规则，如果if语句判断是True，就把缩进的两行print语句执行了，否则，什么也不做。也可以给if添加一个else语句，意思是，如果if判断是False，不要执行if的内容，去把else执行了：
```
age = 3
if age >= 18:
    print('your age is', age)
    print('adult')
else:
    print('your age is', age)
    print('teenager')
```
- elif是else if的缩写，完全可以有多个elif，所以if语句的完整形式就是：
```
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```
- if判断条件还可以简写，只要x是非零数值、非空字符串、非空list等，就判断为True，否则为False。
```
if x:
    print('True')
```

### input
- input()可以读取用户的输入：
```
birth = input('birth: ')
```
- input()返回的数据类型是str，不能直接和整数作比较，可通过int()来完成类型转换，但注意，当int()发现字符串不是一个合法的数字的时候就会报错。

### 循环
-  for x in ...
```
sum = 0
for x in range(101):
    sum = sum + x
print(sum)
```
- while循环，只要条件满足，就不断循环，条件不满足时退出循环
```
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
    print(sum)
print(sum)
```
- break:提前退出循环
```
n = 1
while n <= 100:
    if n > 10: # 当n = 11时，条件满足，执行break语句
        break # break语句会结束当前循环
    print(n)
    n = n + 1
print('END')
```
- continue:跳过当前的这次循环，直接开始下一次循环。
```
n = 0
while n < 10:
    n = n + 1
    if n % 2 == 0: # 如果n是偶数，执行continue语句
        continue # continue语句会直接继续下一轮循环，后续的print()语句不会执行
    print(n)
```

### dict
- dict类似map，使用key-value来存储，具有极快的查找速度。给定一个名字，比如'Michael'，dict在内部就可以直接计算出Michael对应的存放成绩的“页码”，也就是95这个数字存放的内存地址，直接取出来，所以速度非常快。
```
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```
- 除了初始化指定以外，还可以通过key放入,多次对通过一个key赋值，后面的值会把前面的值覆盖掉。
```
>>> d['Adam'] = 67
>>> d['Adam']
67
```
- 通过in判断key是否存在
```
>>> 'Thomas' in d
False
```
- 通过dict提供的get()方法，如果key不存在，可以返回None，或者自己指定的value：
```
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```
- 要删除一个key，用pop(key)方法，对应的value也会从dict中删除:
```
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```
- dict内部存放的顺序和key放入的顺序没有关系。
- dict特点:
    1. 查找和插入的速度极快，并且不会随着key的增加而变慢
    2. 需要占用大量的内存，内寸浪费多
- list特点:
    1. 查找和插入的时间随着元素的增加而增加
    2. 占用空间小，浪费内存小
- dict是用空间换时间的一种方法，需牢记的一条就是**dict的key必须是不可变对象**。
这是因为dict根据key来计算value的存储位置，如果每次计算相同的key得出的结果不同，那dict内部就完全混乱了。这个通过key计算位置的算法称为哈希算法（Hash）。要保证hash的正确性，作为key的对象就不能变。在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list是可变的，就不能作为key：
```
>>> key = [1, 2, 3]
>>> d[key] = 'a list'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

### set
- set和dict类似，也是一组key的集合，但不存储value,由于key不能重复，所以，在set中，没有重复的key。要创建一个set，需要提供一个list作为输入集合:
```
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```
- 通过add(key)方法可以添加元素到set中，可以重复添加，但不会有效果：
```
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}

```
- 通过remove(key)方法可以删除元素：
```
>>> s.remove(4)
>>> s
{1, 2, 3}
```
- set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：
```
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```
### 函数
- python有很多内置函数可以直接调用，比如abs,max,help:
```
>>> abs(100)
100
>>> abs(-20)
20
>>> abs(12.34)
12.34
```
- 数据类型转换函数:
```
>>> int('123')
123
>>> int(12.34)
12
>>> float('12.34')
12.34
>>> str(1.23)
'1.23'
>>> str(100)
'100'
>>> bool(1)
True
>>> bool('')
False
```
- 定义一个函数要使用def语句，依次写出函数名、括号、括号中的参数和冒号:，然后，在缩进块中编写函数体，函数的返回值用return语句返回。我们以自定义一个求绝对值的my_abs函数为例：
```
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```
- 空函数:如果想定义一个什么也不做的函数，可以用pass,pass语句可以用来作为占位符，让代码可以先跑起来。
```
def nop():
    pass
```
- 一个函数可以返回多个值
```
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```
```
>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print(x, y)
151.96152422706632 70.0
```
但其实这只是一种假象，python函数返回的依然是单一值
```
>>> r = move(100, 100, 60, math.pi / 6)
>>> print(r)
(151.96152422706632, 70.0)
```

### 参数检查
- 自定义的my_abs和内置的abs的区别:
```
>>> my_abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in my_abs
TypeError: unorderable types: str() >= int()
>>> abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: bad operand type for abs(): 'str'

```
- 当传入了不当的参数，内置函数abs能够检查出参数错误，但是自定义的my_abs没有参数检查，会导致报错。数据类型检查可以用内置函数isinstance()来实现:
```
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x
```
添加了参数检查后，如果传入错误的参数类型，函数会抛一个错误:
```
>>> my_abs('A')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in my_abs
TypeError: bad operand type
```

### 函数的参数
- 位置参数，调用函数时，依次将传入的值按照位置顺序赋值给参数
```
def power(x, n):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```
- 默认参数，在这个例子里当只传入了一个参数时，第二个参数默认就是2。默认参数必须在必选参数之后，否则python解释器会报错
```
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```
- 默认参数必须指向不变对象
```
def add_end(L=[]):
    L.append('END')
    return L
```
正常调用，结果没啥问题
```
>>> add_end()
['END']
```
但再次调用，发现不对了。原因是，Python函数在定义的时候，默认参数L的值就被计算出来了，即[]，因为默认参数L也是一个变量，它指向对象[]，每次调用该函数，如果改变了L的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的[]了。
```
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```
我们可以用None这个不变对象来实现:
```
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```
- 可变参数，就是传入的参数个数是可变的，可以是1个，2个，多个。
```
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```
如果现在已经有一个tuple或者list，可以这么调用:
```
>>> nums = [1, 2, 3]
>>> calc(nums[0], nums[1], nums[2])
14
```
但这种写法太繁琐，所以python允许你在list或者tuple前面加上一个* 号，把list或者tuple的元素变成可变参数传进去:
```
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```







    


















