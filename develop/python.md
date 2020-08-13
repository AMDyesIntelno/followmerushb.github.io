?> Python 笔记

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

### 常用方法

1. `count()`用于统计某个元素在列表中出现的次数

`list.count(obj)`

2. `extend()`用于在列表末尾一次性追加另一个序列中的多个值

`list.extend(seq)`

>`extend()`和序列相加的主要区别是:`extend()`方法修改了被扩展的序列,序列相加会返回一个全新的列表

3. `index()`用于从列表中找出某个值第一个匹配项的索引位置

`list.index(obj)`

>对于不在列表中的元素,用index()会报错

4. `insert()`用于在列表中的某个位置插入对象

`list.insert(index,obj)`

>list代表列表,index代表对象obj需要插入的索引位置,obj代表要插入列表中的对象

5. `pop()`用于移除列表中的一个元素(默认最后一个元素)并且返回该元素的值

`list.pop(obj=list[-1])`

6. `remove()`用于移除列表中某个值的第一个匹配项

`list.remove(obj)`

7. `reverse()`用于反向列表中的元素

`list.reverse()`

8. `sort()`用于对原列表进行排序,如果指定参数,就使用参数指定的比较方法进行排序

`list.sort(func)`

>list代表列表,func为可选参数,如果指定该参数,就会使用该参数的方法进行排序

9. `clear()`用于清空列表,类似于`del a[:]`

`list.clear()`

10.  `copy()`用于复制列表,类似于`a[:]`

`list.copy()`

```python
a=[1,3,6,8,4,5,2,3,7,9]
b=list("asdfgaa")
c=[[1,3],5,6,[1,3],2,"asdf"]
print(a.count(3))
print(b.count("a"))
print(c.count([1,3]))
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

元组中的元素值不允许修改，但可以对元组进行连接组合

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

