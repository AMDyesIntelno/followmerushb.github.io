?> Python 笔记

## 输出

快速多行输出

```python
print("""asdf
qwe""")
```

```
asdf
qwe
```

去掉`print`添加逗号后的自动空格(`sep=""`)和`print`的自动换行(`end=""`)

```python
print("abc","edf",end="")
print("ghi","jkl",sep="")
```

```
abc edfghijkl
```

## 变量

有关变量的规则

>1. 变量名只能包含字母、数字和下划线变量名可以字母或下划线打头,但不能以数字打头,例如,可将变量命名为message_1,但不能将其命名为1_message
>
>2. 变量名不能包含空格,但可使用下划线来分隔其中的单词例如,变量名greeting_message可行,但变量名greeting message会引发错误
>
>3. 不要将Python关键字和函数名用作变量名,即不要使用Python保留用于特殊用途的单词,如print
>
>4. 变量名应既简短又具有描述性例如,name比n好,student_name比s_n好,name_length比length_of_persons_name好
>
>5. 慎用小写字母l和大写字母O,因为它们可能被人错看成数字1和0

## 字符串&数字

>字符串类型的对象不支持更改
>
>TypeError: 'str' object does not support item assignment

```python
print("PI:%.2f"%3.14159)#浮点数精度控制
print("PI:%10f"%3.14159)#字符宽度,前面保留两个空格
print("PI:%10.2f"%3.14159)#前面保留6个空格
print("PI:%-10.2f"%3.14159)#左对齐(默认为右对齐)
print("PI:%010.2f"%3.14159)#补零
print("str:%.3s"%"abcdefg")#字符串截取
print("PI:%s"%3.14159)
print("str:%*.*s"%(10,3,"abcdefg"))#星号(*)作为字段宽度或精度,数值会从元组中读出
print("PI:%.2f E:%.2f"%(3.14159,2.71))#在有多个占位符的字符串中,使用元组传入多个格式化值
print("percent: %d"%85+"%")#输出%
print("percent: %d%%"%85)#如果要输出%,需要格式化字符%,需要使用%%
print(('%+5d'%10)+'\n'+('%+5d'%-10))#显示正负号并对齐
```

```
PI:3.14
PI:  3.141590
PI:      3.14
PI:3.14      
PI:0000003.14
str:abc
PI:3.14159
str:       abc
PI:3.14 E:2.71
percent: 85%
percent: 85%
  +10
  -10
```

### 常用方法

1. `find()`用于检测字符串中是否包含子字符串str
>如果指定beg(开始)和end(结束)范围,就检查是否包含在指定范围内
>
>如果包含子字符串,就返回开始的索引值,否则返回-1

`str.find(str, beg=0, end=len(string))`

2. `join()`用于将序列中的元素以指定字符连接成一个新字符串

`str.join(sequence)`

>进行join操作调用和被调用的对象必须都是字符串,任意一个不是字符串都会报错

3. `lower()`用于将字符串中所有大写字符转换为小写

`str.lower()`

4. `upper()`用于将字符串中所有小写字符转换为大写

`str.upper()`

5. `swapcase()`用于对字符串的大小写字母进行转换,将字符串中大写转换为小写,小写转换为大写

`str.swapcase()`

6. `replace()`把字符串中的old(旧字符串)替换成new(新字符串),如果指定第3个参数max,替换次数就不超过max次

`str.replace(old, new[, max])`

7. `split()`通过指定分隔符对字符串进行切片,如果参数num有指定值,就只分隔num个子字符串

`str.split(st="", num=string.count(str))`
 
8. `strip()`用于移除字符串头尾指定的字符(默认为空格)

`str.strip([char])`

9. `translate()`根据参数table给出的表(包含256个字符)转换字符串的字符,将要过滤掉的字符放到del参数中

`str.translate(table[, deletechars])`

```python
str="I have an apple"
print(str.find("q"))
print(str.find("apple"))
mark="+"
str=tuple("12345")
print(mark.join(str))
str=("","usr","bin","python3")
print("/".join(str))
str="I have an apple"
print(str.lower())
print(str.upper())
print(str.swapcase())
print(str.replace("an apple","a pen",1))
print(str.replace("a","_",2))
print(str.split())
print(str.split("a",2))
print(str.split("a"))
str="----a-b-c----"
print(str.strip("-"))
tab1="abcde"
tab2="12345"
trantab=str.maketrans(tab1,tab2)
print(str.translate(trantab))
```

```
-1
10
1+2+3+4+5
/usr/bin/python3
i have an apple
I HAVE AN APPLE
i HAVE AN APPLE
I have a pen
I h_ve _n apple
['I', 'have', 'an', 'apple']
['I h', 've ', 'n apple']
['I h', 've ', 'n ', 'pple']
a-b-c
----1-2-3----
```

## 列表

### 分片

使用分片可以对一定范围内的元素进行访问,分片通过冒号相隔的两个索引实现

分片操作的实现需要提供两个索引作为边界,第一个索引的元素包含在分片内,第二个索引的元素不包含在分片内

假设a是分片操作中的第一个索引,b是第二个索引,则得到的分片为[a,b)

```python
list=[1,2,3,4,5,6]
#直接确定分片
print(list[0:3])#list[0] list[1] list[2]
print(list[-3:-1])#list[-3] list[-2]
print(list[3:6])#list[3] list[4] list[5]
#间接确定分片
print(list[2:])#list[2] list[3] list[4] list[5]
print(list[:2])#list[0] list[1]
print(list[-3:])#list[-3] list[-2] list[-1]
print(list[:])
```

```
[1, 2, 3]
[4, 5]
[4, 5, 6]
[3, 4, 5, 6]
[1, 2]
[4, 5, 6]
[1, 2, 3, 4, 5, 6]
```

进行分片时,分片的开始和结束点都需要指定(无论是直接还是间接)

隐藏参数: *步长*

分片操作按照步长逐个遍历序列的元素,遍历后返回开始和结束点之间的所有元素

```python
list=[1,2,3,4,5,6]
print(list[0:4:2])#list[0] list[2]
print(list[::3])#list[0] list[3]
print(list[-5:-3:1])#list[-5] list[-3]
print(list[-3:-5:-1])#list[-3] list[-5]
print(list[5:3:-1])#list[5] list[3]
print(list[::-1])
```

```
[1, 3]
[1, 4]
[2, 3]
[4, 3]
[6, 5]
[6, 5, 4, 3, 2, 1]
```

对于正数步长,从序列的头部开始向右提取元素,直到最后一个元素

对于负数步长,从序列的尾部开始向左提取元素,直到第一个元素

正数步长必须满足开始点小于结束点,而负数步长必须满足开始点大于结束点

### 序列相加

只有类型相同的序列才能通过加号进行序列连接操作,不同类型的序列不能通过加号进行序列连接操作

### 序列相乘

用一个数字n乘以一个序列会生成新的序列

在新的序列中,原来的序列将被重复n次

```python
list=[1]*10
print(list)
str="a"*10
print(str)
```

```
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
aaaaaaaaaa
```

### 元素赋值

可以对一个列表中的元素赋不同类型的值

```python
a=[1,2,3]
print(a)
a[0]="qwe"
print(a)
print(type(a[0]))
print(type(a[1]))
```

```
[1, 2, 3]
['qwe', 2, 3]
<class 'str'>
<class 'int'>
```

### 添加/删除元素

`append()`用于在列表末尾添加新对象

`list.append(obj)`

`del`用于删除某个对象

`del list[posi]`

```python
a=[1,2,3]
print(a)
a.append(5)
print(a)
a.append("qwer")
print(a)
del a[1]
print(a)
```

```
[1, 2, 3]
[1, 2, 3, 5]
[1, 2, 3, 5, 'qwer']
[1, 3, 5, 'qwer']
```

`range()`用于创建数字列表(左闭右开区间)

```python
for value in range(1,5):
    print(value)
list1=list(range(1,5))
list2=list(range(1,5,2))#步长
print(list1)
print(list2)
```

```
1
2
3
4
[1, 2, 3, 4]
[1, 3]
```

### 分片赋值

`list()`可将字符串转换为列表

```python
a=list("qwe1r")
print(a)
print(a[3],type(a[3]))
b=[1,2,3]
b[2:3]=list("abc")#使用与原序列不等长的序列将分片替换
print(b)
b[2:2]=list("34")#不替换任何元素并在任意位置插入元素
print(b)
```

```
['q', 'w', 'e', '1', 'r']
1 <class 'str'>
[1, 2, 'a', 'b', 'c']
[1, 2, '3', '4', 'a', 'b', 'c']
```

### 列表解析

```python
a=[value**2 for value in range(1,11)]
print(a)
a=[value**3 for value in range(1,11)]
print(a)
```

```
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
[1, 8, 27, 64, 125, 216, 343, 512, 729, 1000]
```

### 常用方法

1. `count()`用于统计某个元素在列表中出现的次数

`list.count(obj)`

2. `sum()`用于计算列表中的和

`sum(list)`

3. `extend()`用于在列表末尾一次性追加另一个序列中的多个值

`list.extend(seq)`

>`extend()`和序列相加的主要区别是:`extend()`方法修改了被扩展的序列,序列相加会返回一个全新的列表

4. `index()`用于从列表中找出某个值第一个匹配项的索引位置

`list.index(obj)`

>对于不在列表中的元素,用index()会报错

5. `insert()`用于在列表中的某个位置插入对象

`list.insert(index,obj)`

>list代表列表,index代表对象obj需要插入的索引位置,obj代表要插入列表中的对象

6. `pop()`用于移除列表中的一个元素(默认最后一个元素)并且返回该元素的值

`list.pop(obj=list[-1])`

7. `remove()`用于移除列表中某个值的第一个匹配项

`list.remove(obj)`

8. `reverse()`用于反向列表中的元素

`list.reverse()`

9. `sort()`用于对原列表进行永久排序(`sorted()`对列表进行临时排序),如果指定参数,就使用参数指定的比较方法进行排序

`list.sort(func)`

>list代表列表,func为可选参数,如果指定该参数,就会使用该参数的方法进行排序

10. `clear()`用于清空列表,类似于`del a[:]`

`list.clear()`

11.  `copy()`用于复制列表,类似于`a[:]`

`list.copy()`

```python
a=[1,3,6,8,4,5,2,3,7,9]
b=list("asdfgaa")
c=[[1,3],5,6,[1,3],2,"asdf"]
print(a.count(3))
print(b.count("a"))
print(c.count([1,3]))
print(sum(a))
d=list("bca")
b.extend(d)
print(b)
print(a.index(3))
print(c.index("asdf"))
c.insert(2,"插入位置c[2]之前")
print(c)
print(c.pop())
print(c)
c.remove([1,3])
print(c)
a.reverse()
print(a)
a.sort()
print(a)
a.clear()
a=b.copy()
print(a)
```

```
2
3
2
48
['a', 's', 'd', 'f', 'g', 'a', 'a', 'b', 'c', 'a']
1
5
[[1, 3], 5, '插入位置c[2]之前', 6, [1, 3], 2, 'asdf']
asdf
[[1, 3], 5, '插入位置c[2]之前', 6, [1, 3], 2]
[5, '插入位置c[2]之前', 6, [1, 3], 2]
[9, 7, 3, 2, 5, 4, 8, 6, 3, 1]
[1, 2, 3, 3, 4, 5, 6, 7, 8, 9]
['a', 's', 'd', 'f', 'g', 'a', 'a', 'b', 'c', 'a']
```

## 元组

>列表与元组的区别在于元组的元素不能修改,元组一旦初始化就不能修改
>
>元组使用小括号,列表使用方括号

`tuple()`功能和`list()`基本上一样,都是以一个序列作为参数,并把它转换为元组

如果参数是元组,参数就会被原样返回

元组中的元素值不允许修改,但可以对元组进行连接组合

元组中的元素值不允许删除,但可以使用`del`删除整个元组

```python
a=("abc","defg")
b=tuple("123")
print(b)
c=a+b
print(c)
d=[1,2,3]
e=tuple(d)
print(d,e)
del e
#print(e)
```

```
('1', '2', '3')
('abc', 'defg', '1', '2', '3')
[1, 2, 3] (1, 2, 3)
Traceback (most recent call last):
  File "/home/misaka/python/test.py", line 10, in <module>
    print(e)
NameError: name 'e' is not defined
```

>元组的"不变"是指每个元素的指向永远不变,如指向'a'就不能改成指向'b'
>
>指向一个list就不能改成指向其他对象,但指向的list列表本身是可变的

```python
a=('a','b',['A','B'])
a[2][0]='X'
a[2][1]='Y'
print(a)
```

```
('a', 'b', ['X', 'Y'])
```

## 序列解包

```python
nums=1,2,3
print(nums)
x,y,z=nums
print(x)
print(x,y,z)
```

```
(1, 2, 3)
1
1 2 3
```

## bool

>标准值False和None,所有类型的数字0(包括浮点型、长整型和其他类型),空序列(如空字符串、空元组和空列表)以及空字典都为假
>
>其他值都为真


## 字典

字典的创建格式

`d={key1:value1,key2:value2}`

>键(key)必须是唯一的,但值(value)不必
>
>不允许同一个键出现两次,创建时如果同一个键被赋值两次,后面的值会被记住
>
>值可以取任何数据类型,键必须是不可变的,如字符串,数字或元组,但不能用列表
>
>字典内部存放的顺序和键放入的顺序没有关系
>
>字典中的元素是没有顺序的

```python
dic={"a":1001,"b":1002,"c":1003,"d":1004}
print(dic)
dic["a"]=1002
print(dic)
dic["a"]=1001
dic["e"]=1005
dic["fgh"]="qwer"
print(dic)
print("len: %d"%len(dic))
dic=dict([("name","abc"),("num","123")])
print(dic)
print(dic["name"])
print(dic["num"])
print("name:%(name)s"%dic)#字典的格式化方式是在每个转换说明符中的%字符后加上用圆括号括起来的键,再跟上其他说明元素
print("num:%s"%dic["num"])
```

```
{'a': 1001, 'b': 1002, 'c': 1003, 'd': 1004}
{'a': 1002, 'b': 1002, 'c': 1003, 'd': 1004}
{'a': 1001, 'b': 1002, 'c': 1003, 'd': 1004, 'e': 1005, 'fgh': 'qwer'}
len: 6
{'name': 'abc', 'num': '123'}
abc
123
name:abc
num:123
```

### 常用方法

1. `clear()`用于删除字典内的所有项

`dict.clear()`

```python
a={}
b=a
print(id(a)==id(b))
a['key']='value'
print(b)
a={}
print(id(a)==id(b))
print(b)
'''
'''
c={}
d=c
print(id(c)==id(d))
c['key']='value'
print(d)
c.clear()
print(id(c)==id(d))
print(d)
```

```
True
{'key': 'value'}
False
{'key': 'value'}
True
{'key': 'value'}
True
{}
```

2. `copy()`返回一个具有相同键/值对的新字典(浅复制)

`dict.copy()`

浅复制时,仅对键进行复制,相当于两个键指向同一个值(id相同)

当对一个键的值进行替换时(`a["name"]="asdf"`),相当于这个键重新指向另一个值(id改变),因此不会对拷贝造成影响

当对一个键的值进行修改时(`a["date"][1]=10`)(`b["date"][0]=9`),这个键的指向并没有发生变化(id没有改变),只有值发生了改变,因此拷贝和源均会发生变化

```python
a={"name":"abc","num":"123","date":[12,13]}
b=a.copy()
print(id(a)==id(b))
print(id(a["name"])==id(b["name"]))
print(id(a["num"])==id(b["num"]))
print(id(a["date"])==id(b["date"]))
a["name"]="asdf"
print(id(a["name"])==id(b["name"]))
print(a)
print(b)
a["date"][1]=10
print(id(a["date"])==id(b["date"]))
print(a)
print(b)
b["date"][0]=9
print(id(a["date"])==id(b["date"]))
print(a)
print(b)
del a["date"]
print(a)
print(b)
```

```
False
True
True
True
False
{'name': 'asdf', 'num': '123', 'date': [12, 13]}
{'name': 'abc', 'num': '123', 'date': [12, 13]}
True
{'name': 'asdf', 'num': '123', 'date': [12, 10]}
{'name': 'abc', 'num': '123', 'date': [12, 10]}
True
{'name': 'asdf', 'num': '123', 'date': [9, 10]}
{'name': 'abc', 'num': '123', 'date': [9, 10]}
{'name': 'asdf', 'num': '123'}
{'name': 'abc', 'num': '123', 'date': [9, 10]}
```

3. `fromkeys()`用于创建一个新字典,以序列seq中的元素做字典的键,value为字典**所有键**对应的初始值

`dict.fromkeys(seq[, value]))`

4. `get()`返回指定键的值,如果值不在字典中.就返回默认值(`None`)

`dict.get(key, default=None)`

5. `key in dict`方判断键是否存在于字典中,如果键在字典dict中就返回true,否则返回false

`key in dict`

6. `items()`以列表返回可遍历的(键,值)**元组数组**

`dict.items()`

7. `keys()`以列表返回一个字典所有键

`dict.keys()`

8. `setdefault()`方用于获得与给定键相关联的值,如果键不存在于字典中,就会添加键并将值设为默认值

`dict.setdefault(key, default=None)`

>当键不存在时,setdefault方法返回默认值并更新字典；如果键存在,就返回与其对应的值,不改变字典

9. `update()`用于把字典dict2的键/值对更新到dict中

`dict.update(dict2)`

>提供的字典中的项被添加到旧字典中,如果有相同的键就会覆盖

10. `values()`以列表形式返回字典中所有值

`dict.values()`

```python
seq=("a","b","c")
dic=dict.fromkeys(seq)
print(dic)
dic=dict.fromkeys(seq,10)
print(dic)
print(dic.get("a"))
print(dic.get("d"))
print(dic.get("e","无"))
print("a" in dic)
print("e" in dic)
print(dic.items())
print(dic.keys())
print(dic.values())
print(dic.setdefault("d"))
print(dic)
print(dic.setdefault("e",10086))
print(dic)
print(dic.setdefault("e"))
dic2={"f":123,"e":100}
dic.update(dic2)
print(dic)
```

```
{'a': None, 'b': None, 'c': None}
{'a': 10, 'b': 10, 'c': 10}
10
None
无
True
False
dict_items([('a', 10), ('b', 10), ('c', 10)])
dict_keys(['a', 'b', 'c'])
dict_values([10, 10, 10])
None
{'a': 10, 'b': 10, 'c': 10, 'd': None}
10086
{'a': 10, 'b': 10, 'c': 10, 'd': None, 'e': 10086}
10086
{'a': 10, 'b': 10, 'c': 10, 'd': None, 'e': 100, 'f': 123}
```

## import

引入模块

`import module1[, module2[,... moduleN]]`

`import sys,math`

从模块中导入指定部分到当前命名空间

`from modname import name1[, name2[, ... nameN]]`

`from math import pi,sin`

为模块/函数取别名

`import math as m`

`from math import pi as p`

## 断言

```python
a=-1
assert a>0,"a<=0"
assert a%2==0,"a%2==1"
```

```
Traceback (most recent call last):
  File "/home/misaka/python/test3.py", line 2, in <module>
    assert a>0,"a<=0"
AssertionError: a<=0
```

```python
a=5
assert a>0,"a<=0"
assert a%2==0,"a%2==1"
```

```
Traceback (most recent call last):
  File "/home/misaka/python/test3.py", line 3, in <module>
    assert a%2==0,"a%2==1"
AssertionError: a%2==1
```

>使用assert断言时,要注意以下几点:
>
>1. assert断言用来声明某个条件是真的
>
>2. 如果你非常确信你使用的列表中至少有一个元素,想要检验这一点,并在它非真时引发一个错误,那么assert语句是应用在这种情形下的理想语句
>
>3. assert语句失败时,会抛出一个AssertionError

## 流程控制语句

在`for`循环中使用序列解包

```python
tup={"a":1,"b":2,"c":3}
for key,value in tup.items():
   print("%s:%s"%(key,value))
```

```
a:1
b:2
c:3
```

### 并行迭代

`zip`函数用来进行并行迭代,可以作用于任意数量的序列,返回一个元组的列表

`zip函数`以短序列为准,当短序列遍历结束时,for循环就会遍历结束

```python
a=[1,2,3,4,5,6]
b=["a","b","c","d"]
c=["abc","edf","gh"]
for num,ch,string in zip(a,b,c):
    print(num,ch,string)
```

```
1 a abc
2 b edf
3 c gh
```

### 翻转/排序迭代

`reversed`进行翻转迭代

`sorted`进行排序迭代

两者可作用于任何序列或可迭代对象,但不对源对象进行修改,而是返回翻转或排序后的版本

```python
a=[1,2,3,6,5,4]
print(sorted(a))
print(a)
b=["a","b","c","d"]
print(list(reversed(b)))
print(b)
```

```
[1, 2, 3, 4, 5, 6]
[1, 2, 3, 6, 5, 4]
['d', 'c', 'b', 'a']
['a', 'b', 'c', 'd']
```

### 在while循环中使用else语句

```python
n=0
while n<3:
  print(n)
  n+=1
else:
  print (n,">=3",sep="")
print("...")
```

```
0
1
2
3>=3
...
```

```python
n=0
while n<3:
  print(n)
  n+=1
  if n==2:
      break
else:
  print (n,">=3",sep="")
print("...")
```

```
0
1
...
```

### 在for循环中使用else语句

`pass`是空语句,作用是保持程序结构的完整性

```python
n=0
for n in range(0,4):
  print(n)
else:
  print (n,">=3",sep="")
print("...")
```

```
0
1
2
3
3>=3
...
```

```python
n=0
for n in range(0,4):
  print(n)
  if n==2:
      break
else:
  print (n,">=3",sep="")
print("...")
```

```
0
1
2
...
```

### pass语句

```python
str="asdf"
if str=="asdf":
   print('hello')
elif str=="qwer":
    #None
else:
   print("...")
```

```
  File "/home/misaka/python/test3.py", line 6
    else:
       ^
IndentationError: expected an indented block
```

```python
str="asdf"
if str=="asdf":
   print('hello')
elif str=="qwer":
    pass
else:
   print("...")
```

```
hello
```

## 函数

### 函数定义

>函数代码块以`def`关键词开头,后接函数标识符名称和圆括号`()`
>
>所有传入的参数和自变量都必须放在圆括号中,可以在圆括号中定义参数
>
>函数的第一行语句可以选择性使用文档字符串,用于存放函数说明
>
>函数内容以冒号开始,并且要缩进
>
>`return [表达式]`结束函数,选择性返回一个值给调用方
>
>不带表达式的`return`相当于返回`None`
>
>没有`return`语句时,函数执行完毕也会返回结果,不过结果为`None`
>
>`return None`可以简写为`return`

### 函数参数

#### 必须参数

>必须参数必须以正确的顺序传入函数,调用时数量必须和声明时一样

```python
def fun(string):
    print("string: ",string,sep="")
s="asdf"
fun(s)
#fun() #TypeError: fun() missing 1 required positional argument: 'string'
#fun(s,s) #TypeError: fun() takes 1 positional argument but 2 were given
```

```
string: asdf
```

#### 关键字参数

>使用关键字参数允许调用函数时参数的顺序与声明时不一致,因为Python解释器能够用参数名匹配参数值

```python
def fun(string1,string2):
    print("string1: ",string1,sep="")
    print("string2: ",string2,sep="")
s1="asdf"
s2="qwer"
fun(s1,s2)
print("---")
fun(string1=s1,string2=s2)
```

```
string1: asdf
string2: qwer
---
string1: asdf
string2: qwer
```

#### 默认参数

>使用默认参数,就是在定义函数时,给参数一个默认值,如果没有给调用的函数的参数赋值,调用的函数就会使用这个默认值
>
>无论有多少默认参数,默认参数都不能在必须参数之前
>
>无论有多少默认参数,若不传入默认参数值,则使用默认值
>
>若要更改某一个默认参数值,又不想传入其他默认参数,且该默认参数的位置不是第一个,则可以通过参数名更改想要更改的默认参数值
>
>若有一个默认参数通过传入参数名更改参数值,则其他想要更改的默认参数都需要传入参数名更改参数值,否则报错
>
>更改默认参数值时,传入默认参数的顺序不需要根据定义的函数中的默认参数的顺序传入,不过最好同时传入参数名,否则容易出现执行结果与预期不一致的情况

```python
def fun(string1,string2="qwer",string3="zxcv"):
    print("string1: ",string1,sep="")
    print("string2: ",string2,sep="")
    print("string3: ",string3,sep="")
s1="asdf"
s2="1234"
s3="5678"
print('-------传入必须参数-------')
fun(s1)
print('-------传入必须参数,更改第一个默认参数值-------')
fun(s1,s2)
print('-------传入必须参数,默认参数值都更改-------')
fun(s1,s2,s3)
print('-------传入必须参数,指定默认参数名并更改参数值-------')
fun(s1,string2=s2)
print('-------传入必须参数,指定参数名并更改值-------')
fun(s1,string3=s3,string2=s2)
print('-------第一个默认参数不带参数名,第二个带-------')
fun(s1,s2,string3=s3)
print('-------两个默认参数都带参数名-------')
fun(s1,string2=s2,string3=s3)
'''
print('-------第一个默认参数带参数名,第二个不带,报错-------')
fun(s1,string2=s2,s3)
#SyntaxError: positional argument follows keyword argument
'''
```

```
-------传入必须参数-------
string1: asdf
string2: qwer
string3: zxcv
-------传入必须参数,更改第一个默认参数值-------
string1: asdf
string2: 1234
string3: zxcv
-------传入必须参数,默认参数值都更改-------
string1: asdf
string2: 1234
string3: 5678
-------传入必须参数,指定默认参数名并更改参数值-------
string1: asdf
string2: 1234
string3: zxcv
-------传入必须参数,指定参数名并更改值-------
string1: asdf
string2: 1234
string3: 5678
-------第一个默认参数不带参数名,第二个带-------
string1: asdf
string2: 1234
string3: 5678
-------两个默认参数都带参数名-------
string1: asdf
string2: 1234
string3: 5678
```

#### 可变参数

`def functionname([formal_args,] *var_args_tuple):`

>加了星号(*)的变量名会存放所有未命名的变量参数
>
>如果变量参数在函数调用时没有指定参数,就是一个空元组

```python
def fun(string,*strings):
    print(string)
    print(strings)
fun("asdf")
fun("asdf","qwer")
fun("asdf","qwer","zxcv")
fun("asdf","qwer",123,"zxcv")
def fun2(*strings,string):
    print(string)
    print(strings)
#fun2("asdf","qwer","zxcv",123)
#TypeError: fun2() missing 1 required keyword-only argument: 'string'
#fun2("asdf","qwer",string="zxcv",123)
#SyntaxError: positional argument follows keyword argument
fun2("asdf","qwer",string="zxcv")
```

```
asdf
()
asdf
('qwer',)
asdf
('qwer', 'zxcv')
asdf
('qwer', 123, 'zxcv')
zxcv
('asdf', 'qwer')
```

`def functionname([formal_args,] **var_argvs_dict):`

>使用任意数量的关键字实参

```python
def fun(string,**strings):
    print(string)
    print(strings)
fun("asdf")
fun("asdf",fir="qwer")
fun("asdf",fir="qwer",sec=123)
#fun("asdf",fir="qwer",sec=123,string=456)
#TypeError: fun() got multiple values for argument 'string'
#fun("asdf","fir"="qwer")
#SyntaxError: keyword can't be an expression
#fun("asdf",1="qwer")
#SyntaxError: keyword can't be an expression
'''
def fun2(**strings,string):
    print(string)
    print(strings)

def fun2(**strings,string):
                        ^
SyntaxError: invalid syntax
'''
```

```
asdf
{}
asdf
{'fir': 'qwer'}
asdf
{'fir': 'qwer', 'sec': 123}
```

#### 组合参数

```python
def fun(a,b,c=0,*d,**e):
   print(a,b,c,d,e)
fun(1,2)
fun(1,2,c=3)
fun(1,2,c=3,d=3)
fun(1,2,3,"a","b","c")
fun(1,2,3,"abc","def",x=9,y=10,z=11)
```

```
1 2 0 () {}
1 2 3 () {}
1 2 3 () {'d': 3}
1 2 3 ('a', 'b', 'c') {}
1 2 3 ('abc', 'def') {'x': 9, 'y': 10, 'z': 11}
```

#### 形参与实参

>在函数体内都是对形参进行操作,不能操作实参,即无法对实参做出更改
>
>作为实参传入函数的变量名称和函数定义里形参的名字没有关系
>
>函数只关心形参的值,而不关心它在调用前叫什么名字

### 变量作用域

#### 局部变量

```python
def fun1():
    a=1
    print(a)
fun1()
#print(a)
#NameError: name 'a' is not defined

b=2
def fun2():
    #在函数体中可以直接使用函数体外的变量
    print(b)
fun2()

c=3
'''
def fun3():
    #UnboundLocalError: local variable 'c' referenced before assignment
    print(c)
    c=4
    print(c)
'''
def fun3(c):
    #在函数体中更改变量的值并不会更改函数体外变量的值
    print(c)
    c=4
    print(c)
fun3(c)
print(c)
```

```
1
2
3
4
3
```

#### 全局变量

>在函数体中更改全局变量的值不会影响全局变量在其他函数或语句中的使用
>
>函数中使用某个变量时,如果该变量名既有全局变量又有局部变量,就默认使用局部变量
>
>要在函数中将某个变量定义为全局变量,在需要被定义的变量前加一个关键字global即可
>
>在函数体中定义global变量后,在函数体中对变量做的其他操作也是全局性的

```python
total=0#全局变量
def fun1(a,b):
    total=a+b#局部变量
    print(total)
    return total
print(fun1(1,2))
print(total)
print("---")
a=5
def fun2():
    a=10
    print(a)
fun2()
print(a)
print("---")
def fun3():
    #global a=10
    #SyntaxError: invalid syntax
    global a
    a=10
    print(a)
fun3()
print(a)
```

```
3
3
0
---
10
5
---
10
10
```

### 闭包

#### 嵌套函数

```python
def fun1():
    print("asdf")
    def fun2():
        print("qwer")
    fun2()
fun1()
```

```
asdf
qwer
```

#### 函数作为返回值

```python
def sum(*num):
    def core():
        s=0
        for n in num:
            s+=n
        return s
    return core
_sum=sum(1,2,3)
print(_sum)
print(_sum())
```

```
<function sum.<locals>.core at 0x7f90cba1bd08>
6
```

```python
def sum(a):
    def fun(b):
        return a+b
    return fun
_sum=sum(1)
print(_sum)
print(_sum(2))
```

```
<function sum.<locals>.fun at 0x7febff01ed08>
3
```

#### 闭包

>一个函数返回了一个内部函数,该内部函数引用了外部函数(不是在全局作用域)的相关参数和变量,将该返回的内部函数称为**闭包**

```python
def fun():
    s=[]
    for i in range(1,4):
        def f():
             return i*i
        s.append(f)
        #当循环结束以后,循环体中的临时变量i不会销毁,而是继续存在于执行环境中
        #python的函数只有在执行时,才会去找函数体中变量的值
        #f没有被立即执行,因此i的值并没有传入,因此传入的i值由最终的i值确定,所以i=3
    return s
f1,f2,f3=fun()#序列解包
print(f1,f1.__closure__[0].cell_contents)#__closure__属性返回的是一个元组对象,包含了闭包引用的外部变量,cell_contents对元组进行读取
print(f2,f2.__closure__[0].cell_contents)
print(f3,f3.__closure__[0].cell_contents)
print(f1())
print(f2())
print(f3())
```

```
<function fun.<locals>.f at 0x7fd44911bd08> 3
<function fun.<locals>.f at 0x7fd44911bd90> 3
<function fun.<locals>.f at 0x7fd44911be18> 3
9
9
9
```

```python
def fun():
    def g(i):
        def h():
            return i*i
        return h
    s=[]
    for i in range(1, 4):
        s.append(g(i))#g(i)被立刻执行,在列表中的元素为h,同时因为i的当前值被传入函数g(),而函数g()的返回值为h,所以h可以使用当前的i
    return s
f1,f2,f3=fun()#序列解包
print(f1,f1.__closure__[0].cell_contents)#__closure__属性返回的是一个元组对象,包含了闭包引用的外部变量,cell_contents对元组进行读取
print(f2,f2.__closure__[0].cell_contents)
print(f3,f3.__closure__[0].cell_contents)
print(f1())
print(f2())
print(f3())
```

```
<function fun.<locals>.g.<locals>.h at 0x7f8c5be86d90> 1
<function fun.<locals>.g.<locals>.h at 0x7f8c5be86e18> 2
<function fun.<locals>.g.<locals>.h at 0x7f8c5be86ea0> 3
1
4
9
```

>闭包可以访问但无法直接修改外部函数的局部变量(通过`nonlocal`或者容器类型来间接修改)

```python
def fun():
    x=5
    def fun2():
        #x*=x
        #UnboundLocalError: local variable 'x' referenced before assignment
        print("fun2:",x,id(x))
    print("fun:",x,id(x))
    fun2()
    print("fun:",x,id(x))
fun()
```

```
fun: 5 9062752
fun2: 5 9062752
fun: 5 9062752
```

```python
def fun():
    x=5
    def fun2():
        nonlocal x
        x*=x
        print("fun2:",x,id(x))
    print("fun:",x,id(x))
    fun2()
    print("fun:",x,id(x))
fun()
```

```
fun: 5 9062752
fun2: 25 9063392
fun: 25 9063392
```

```python
def fun():
    x=[5]
    def fun2():
        x[0]*=x[0]
        print("fun2:",x,id(x))
    print("fun:",x,id(x))
    fun2()
    print("fun:",x,id(x))
fun()
```

```
fun: [5] 140178018971848
fun2: [25] 140178018971848
fun: [25] 140178018971848
```

### 匿名函数

`lambda [arg1 [,arg2,.....argn]]:expression`

>假定要对一个列表中的奇数进行筛选

1. 常规函数

```python
def fun(num):
    return num%2
l1=[1,2,3,4,5,6,7]
l2=[]
for i in l1:
    if(fun(i)):
        l2.append(i)
print(l2)
```

```
[1, 3, 5, 7]
```

2. `filter()`+常规函数

>filter()函数用于过滤序列,过滤掉不符合条件的元素
>
>Pyhton2返回列表,Python3返回迭代器对象

```python
def fun(num):
    return num%2
l1=[1,2,3,4,5,6,7]
l2=filter(fun,l1)
print(l2)
l3=list(l2)
print(l3)
```

```
<filter object at 0x7f1ce0a91c50>
[1, 3, 5, 7]
```

3. `filter()`+匿名函数

```python
l1=[1,2,3,4,5,6,7]
l2=filter(lambda x:x%2,l1)
print(l2)
l3=list(l2)
print(l3)
```

```
<filter object at 0x7fe37ed26ba8>
[1, 3, 5, 7]
```

>x为lambda函数的一个参数
>
>:为分割符
>
>x%2则是返回值,在lambda 函数中不能有return
>
>而冒号(:)后面的就是返回值

---

>无参匿名函数

```python
a=lambda :True
print(a())
```

```
True
```

>含参匿名函数

```python
a=lambda x:x>0
b=lambda x,y:x+y
c=lambda x,y,z=3:x*y*z#带有默认参数
print(a(1))
print(a(-1))
print(b(1,2))
print(c(2,3))
print(c(2,3,4))
```

```
True
False
3
18
24
```

### 偏函数

>偏函数通过`functools`模块被用户调用
>
>偏函数是将所要承载的函数作为`partial()`函数的第一个参数,原函数的各个参数依次作为`partial()`函数的后续参数,除非使用关键字参数

```python
from functools import partial
def fun1(x,y):
    return x%y
def fun2(x,y,z):
    return x*y*z
a=partial(fun1,100)
print(fun1(100,3))
print(a(3))
b=partial(fun2,2)
c=partial(fun2,2,3)
print(fun2(2,3,4))
print(b(3,4))
print(c(4))
```

```
1
1
24
24
24
```

## 面向对象

>类:用来描述具有相同属性和方法的对象的集合,类定义了集合中每个对象共有的属性和方法,对象是类的实例
>
>类变量(属性):类变量在整个实例化的对象中是公用的,类变量定义在类中,且在方法之外,类变量通常不作为实例变量使用,类变量也称作属性
>
>数据成员:类变量或实例变量用于处理类及其实例对象的相关数据
>
>方法重写:如果从父类继承的方法不能满足子类的需求,就可以对其进行改写,这个过程称为方法的覆盖(Override),也称为方法的重写
>
>实例变量:定义在方法中的变量只作用于当前实例的类
>
>多态(Polymorphism):对不同类的对象使用同样的操作
>
>封装(Encapsulation):对外部世界隐藏对象的工作细节
>
>继承(Inheritance):即一个派生类(derived class)继承基类(base class)的字段和方法,继承允许把一个派生类的对象作为一个基类对象对待,以普通类为基础建立专门的类对象
>
>实例化(Instance):创建一个类的实例、类的具体对象
>
>方法:类中定义的函数
>
>对象:通过类定义的数据结构实例,对象包括两个数据成员(类变量和实例变量)和方法

### 类定义

```python
class ClassName:
    <statement-1>
    ...
    <statement-n>
```

在类中定义方法的要求:在类中定义方法时,第一个参数必须是self

除第一个参数外,类的方法和普通函数没什么区别,如可以用默认参数,可变参数,关键字参数和命名关键字参数等

### 类对象

类对象支持两种操作:属性引用和实例化

属性引用使用和所有的属性引用一样的标准语法:`obj.name`

```python
class test:
    num=123
    def fun1(self):
        print("asdf")
    def fun2(self):
        return "qwer"
a=test()#类的实例化,a为类的具体对象
print(a.num)#访问类的属性
a.fun1()#访问类的方法
print(a.fun2())#访问类的方法
```

```
123
asdf
qwer
```

### 类的构造

>类有一个名为`__init__()`的特殊方法(构造方法),该方法在类实例化时会自动调用
>
>在定义类时,若不显式地定义一个`__init__()`方法,则程序默认调用一个无参的`__init__()`方法
>
>一个类中可定义多个构造方法,但实例化类时只实例化最后的构造方法,即**后面的构造方法会覆盖前面的构造方法,并且需要根据最后一个构造方法的形式进行实例化**
>
>建议一个类中只定义一个构造函数

```python
class test:
    def __init__(self,str):
        self.name=str
    def out(self):
        print(self.name)
a=test("asdf")
a.out()
```

```
asdf
```

```python
class test:
    def __init__(self,str):
        print(str)
    def __init__(self):
        print("none")
test()
#test("asdf")
#TypeError: __init__() takes 1 positional argument but 2 were given
```

```
none
```

### 类的访问权限

#### 私有变量

>要让内部属性不被外部访问,可以在属性名称前加两个下划线`__`
>
>在Python中,实例的变量名如果以`__`开头,就会变成私有变量(private),只有内部可以访问,外部不能访问
>
>不能直接访问私有变量是因为Python解释器把私有变量变成了`_classname__xxx`,所以仍然可以通过`_classname__xxx`来访问/修改私有变量

```python
class student:
    def __init__(self,name,mark):
        self.__name=name
        self.__mark=mark
    def info(self):
        print(self.__name,self.__mark)
stu = student("zhangsan",90)
stu.info()
#print(stu.__mark)
#AttributeError: 'student' object has no attribute '__mark'
stu.__mark=100
print(stu.__mark)
stu.info()
```

```
zhangsan 90
100
zhangsan 90
```

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img@master/Notes/develop/python/python_private.png)

```python
class student:
    def __init__(self,name,mark):
        self.__name=name
        self.__mark=mark
    def info(self):
        print(self.__name,self.__mark)
stu = student("zhangsan",90)
stu.info()
stu._student__mark=100
stu.info()
```

```
zhangsan 90
zhangsan 100
```

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img@master/Notes/develop/python/python_change_private.png)

>如果需要从外部获取类中的私有变量的值,可以在类中添加读取方法

```python
class student:
    def __init__(self,name,mark):
        self.__name=name
        self.__mark=mark
    def info(self):
        print(self.__name,self.__mark)
    def get_name(self):
        return self.__name
    def get_mark(self):
        return self.__mark
stu = student("zhangsan",90)
stu.info()
print(stu.get_name())
print(stu.get_mark())
```

```
zhangsan 90
zhangsan
90
```

>如果需要从外部修改类中的私有变量的值,可以在类中添加修改方法

```python
class student:
    def __init__(self,name,mark):
        self.__name=name
        self.__mark=mark
    def info(self):
        print(self.__name,self.__mark)
    def change_name(self,name):
        self.__name=name
    def change_mark(self,mark):
        self.__mark=mark
stu = student("zhangsan",90)
stu.info()
stu.change_name("lisi")
stu.change_mark(85)
stu.info()
```

```
zhangsan 90
lisi 85
```

#### 私有方法

```python
class test:
    def __init__(self):
        pass
    def __private(self):
        print("private")
    def out(self):
        print("__private: ",end="")
        self.__private()
a=test()
a.out()
#a.__private()
#AttributeError: 'test' object has no attribute '__private'
a._test__private()
```

```
__private: private
private
```

### 继承

>定义

```python
class DerivedClassName(BaseClassName):
    <statement-1>
    ...
    <statement-N>
```

>在继承中,基类的构造方法`__init__()`不会被自动调用,需要在子类的构造方法中专门调用
>
>在调用基类的方法时需要加上基类的类名前缀,并带上`self`参数,区别于在类中调用普通函数时不需要带`self`参数
>
>在Python中,首先查找对应类型的方法,如果在子类中找不到对应的方法,才到基类中逐个查找
>
>继承最大的好处是子类获得了父类全部非私有的功能
>
>在继承关系中,如果一个实例的数据类型是某个子类,那它的数据类型也可以看作是父类

```python
class animal:
    def __init__(self):
        pass
    def action(self):
        print("running")
class cat(animal):
    def __init__(self):
        pass
class dog(animal):
    def __init__(self):
        pass
miao=cat()
miao.action()
wang=dog()
wang.action()
#isinstance()判断一个变量是否是某个类型
print(isinstance(miao,cat))
print(isinstance(miao,animal))
print(isinstance(wang,dog))
print(isinstance(wang,animal))
```

```
running
running
True
True
True
True
```

>子类不能继承父类中的私有方法,也不能调用父类的私有方法

```python
class father:
    def __init__(self):
        pass
    def fun1(self):
        print("asdf")
    def __fun2(self):
        print("qwer")
class son(father):
    def __init__(self):
        pass
a=son()
a.fun1()
#a.__fun2()
#AttributeError: 'son' object has no attribute '__fun2'
a._father__fun2()
```

```
asdf
qwer
```

### 多重继承

>定义

```python
class DerivedClassName(Base1,Base2,Base3...):
    <statement-1>
    ...
    <statement-N>
```

>多重继承就是有多个基类
>
>需要注意圆括号中父类的顺序,若是父类中有相同的方法名,而在子类使用时未指定,python从左至右搜索 即方法在子类中未找到时,从左到右查找父类中是否包含方法

```python
class base1:
    def __init__(self):
        pass
    def out(self):
        print("asdf")
class base2:
    def __init__(self):
        pass
    def out(self):
        print("qwer")
class base3:
    def __init__(self):
        pass
    def out(self):
        print("zxcv")
class test1(base1,base2,base3):
    def __init__(self):
        pass
class test2(base2,base1,base3):
    def __init__(self):
        pass
a=test1()
a.out()
b=test2()
b.out()
```

```
asdf
qwer
```

### 多态

>当子类和父类存在相同的方法时,子类的方法会覆盖父类的方法
>
>在代码运行时总是会调用子类的方法,称之为多态

```python
class animal:
    def __init__(self):
        pass
    def action(self):
        print("running")
class cat(animal):
    def __init__(self):
        pass
class dog(animal):
    def __init__(self):
        pass
    def action(self):
        print("sleeping")
class snake(animal):
    def __init__(self):
        pass
    def action(self):
        print("crawling")
def get_action(name):
    name.action()
miao=cat()
wang=dog()
python=snake()
get_action(miao)
get_action(wang)
get_action(python)
```

```
running
sleeping
crawling
```

### 封装

>封装是全局作用域中其他区域隐藏多余信息的原则,主要体现的是对数据的保护
>
>封装还可以在无须知道内部实现细节的前提下,直接调用类的内部函数实现对应的功能

```python
class student:
    def __init__(self,name,chinese,maths,english):
        self.__name=name
        self.__chinese=chinese
        self.__maths=maths
        self.__english=english
    def __get_sum(self):
        self.__sum=self.__chinese+self.__maths+self.__english
    def __get_avg(self):
        self.__avg=(self.__chinese+self.__maths+self.__english)//3
    def info(self):
        self.__get_avg()
        self.__get_sum()
        print(self.__name,self.__chinese,self.__maths,self.__english,self.__sum,self.__avg)
student0=student("zhangsan",87,92,86)
student0.info()
```

```
zhangsan 87 92 86 265 88
```