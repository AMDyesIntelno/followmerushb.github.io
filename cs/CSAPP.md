# 深入理解计算机系统

## 计算机系统漫游

系统中的所有信息都是由bit串构成(明文->编码->01),信息的解释取决于具体的context

C程序编译过程:预处理->编译->汇编->链接

```cpp
#include<stdio.h>
int main(){
    printf("Hello World\n");
    return 0;
}
```

`gcc -o hello hello.c`

- 预处理阶段

预处理器根据以字符`#`开头的语句,修改原始的C程序

`#include<stdio.h>`使预处理器读取系统头文件`stdio.h`,并将其插入,得到`hello.i`

`gcc -E -o hello.i hello.c`

```cpp
# 1 "hello.c"
# 1 "<built-in>"
# 1 "<命令行>"
# 31 "<命令行>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 32 "<命令行>" 2
# 1 "hello.c"
# 1 "/usr/include/stdio.h" 1 3 4
...
typedef unsigned char __u_char;
typedef unsigned short int __u_short;
typedef unsigned int __u_int;
typedef unsigned long int __u_long;


typedef signed char __int8_t;
typedef unsigned char __uint8_t;
typedef signed short int __int16_t;
typedef unsigned short int __uint16_t;
typedef signed int __int32_t;
typedef unsigned int __uint32_t;

typedef signed long int __int64_t;
typedef unsigned long int __uint64_t;
...
extern void funlockfile (FILE *__stream) __attribute__ ((__nothrow__ , __leaf__));
# 857 "/usr/include/stdio.h" 3 4
extern int __uflow (FILE *);
extern int __overflow (FILE *, int);
# 874 "/usr/include/stdio.h" 3 4

# 2 "hello.c" 2

# 2 "hello.c"
int main(){
    printf("Hello World\n");
    return 0;
}
```

- 编译阶段

编译器将`hello.i`翻译成`hello.s`,其内容为汇编语言

`gcc -S -o hello.s hello.i`

```nasm
	.file	"hello.c"
	.text
	.section	.rodata
.LC0:
	.string	"Hello World"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	leaq	.LC0(%rip), %rdi
	call	puts@PLT
	movl	$0, %eax
	popq	%rbp
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE0:
	.size	main, .-main
	.ident	"GCC: (GNU) 10.2.0"
	.section	.note.GNU-stack,"",@progbits
```

- 汇编阶段

汇编器将`hello.s`翻译成机器语言指令,将这些指令打包成**可重定位目标程序**的格式,并保存为`hello.o`的二进制文件

`gcc -c hello.s -o hello.o`

- 链接阶段

该程序调用了`printf`函数,该函数存在与一个名为`printf.o`的**单独**的**已预编译**的目标文件中,这个文件要和`hello.o`进行合并

链接器负责执行这种合并,得到名为`hello`的可执行文件

`gcc hello.o -o hello`

---

**进程**是操作系统对一个正在运行的程序的一种抽象,在一个操作系统上可以同时运行多个进程,**并发运行**则是说一个进程的指令和另一个进程的指令交错执行

操作系统保持跟踪进程运行所学的所有状态信息,该状态称为**上下文**

一个进程可以由多个**线程**组成,每个线程都运行在进程的上下文中,共享同样的代码和全局数据

---

**虚拟内存**是一个抽象概念,其为每个进程提供了一个假象,即每个进程都在独占地使用内存,每个进程看到的内存都是一直的,称为**虚拟地址空间**

在Linux中,地址空间最上面的区域是保留给操作系统中的代码与数据,对所有进程一致,底部区域存放用户进程定义的代码和数据,地址**从下往上增大**

![](https://img.misaka.gq/Notes/CSAPP/虚拟地址空间.png)

- 程序代码和数据

对所有进程来说,代码是从同一固定地址开始,紧接着是和全局变量相对应的数据位置,代码和数据区是直接按照可执行文件的内容初始化的

- 堆

代码和数据区后便是堆,代码和数据区在进程一开始便被指定了大小,而堆可以通过`malloc`和`free`等函数进行动态调整

- 共享库

存放如C标准库之类的共享库的代码和数据区域

- 栈

位于**用户虚拟地址空间**顶部的就是**用户栈**,编译器用其实现函数调用,用户栈在程序执行期间也可以动态调整大小,每次调用一个函数时,用户栈增长,从一个函数返回时,用户栈收缩

- 内核虚拟内存

位于**地址空间**顶部的区域是为内核保留,不允许应用程序读写该区域的内容或者直接调用内核代码定义的函数,只能通过调用内核来执行这些操作

---

操作系统内核是应用程序和硬件之间的媒介,提供了三个基本的抽象

1. 文件是对I/O设备的抽象

2. 虚拟内存是对内存和磁盘的抽象

3. 进程是处理器,内存和I/O设备的抽象

---

Amdahl定律

若系统执行某应用程序所需的时间为$T_{old}$,假设系统某部分所需执行时间与该时间的比例为$\alpha$,而该部分性能提升比例为$k$,既该部分初始所需时间为$\alpha T_{old}$,现在所需时间为$(\alpha T_{old})/k$

总的执行时间应为$T_{new}=(1-\alpha)T_{old}+(\alpha T_{old})/k=T_{old}[(1-\alpha)+\alpha /k]$

加速比$S=T_{old}/T_{new}$为$S=1/((1-\alpha)+\alpha/k)$

## 信息的处理和表示

### 信息存储

十进制转16进制(辗转相除法)

`314156=19634*16+12`(C)

`19634=1227*16+2`(2)

`1227=76*16+11`(B)

`76=4*16+12`(C)

`4=0*16+4`(4)

314156=0x4CB2C

---

每台计算机都有一个**字长**,指明指针数据的标称大小,因为虚拟地址是以一个字来编码的,所以字长决定了虚拟地址的空间的最大大小

对于一台字长为`w`位的机器,虚拟地址的范围是$0$~$2^w-1$,程序最多访问$2^w$个字节

`32`位字长限制虚拟地址空间为`4GB`,`64`位字长限制虚拟地址空间为`16EB`

`gcc -m32 xxx.c`(ELF 32-bit LSB pie executable)

`gcc -m64 xxx.c`(ELF 64-bit LSB pie executable)

![](https://img.misaka.gq/Notes/CSAPP/C数据类型的典型大小.png)

`__int32_t`共`4`个字节,`__int64_t`共`8`个字节

---

数值**0x1A2B3C4D**想要在计算机中正确使用,就必须要考虑在内存中将其对应的四个字节合理存储

假设内存的地址都是从低到高分配的,那么对于一个数值多个字节顺序存储就有两种存储方式

**方式一**:数值的高位字节存放在内存的低地址端,低位字节存放在内存的高地址端

内存低地址 --------------------> 内存高地址

0x1A   |  0x2B   |  0x3C   |   0x4D

高位字节 <-------------------- 低位字节

**方式二**:数值的低位字节存放在内存的低地址端,高位字节存放在内存的高地址端

内存低地址 --------------------> 内存高地址

0x4D   |  0x3C   |  0x2B   |   0x1A

低位字节 --------------------> 高位字节

**方式一**我们就称之为**大端模式**,即高位字节放在内存的低地址端,低位字节放在内存的高地址端。

**方式二**我们就称之为**小端模式**即低位字节放在内存的低地址端,高位字节放在内存的高地址端。

![](https://img.misaka.gq/Notes/CSAPP/大端模式.png)

![](https://img.misaka.gq/Notes/CSAPP/小端模式.png)

大端小端是不同的字节顺序存储方式,统称为字节序

大端模式,是指数据的高字节位保存在内存的低地址中,而数据的低字节位保存在内存的高地址中

这样的存储模式有点儿类似于把数据当作字符串顺序处理:地址由小向大增加,而数据从高位往低位放,和我们"从左到右"阅读习惯一致

小端模式,是指数据的高字节位保存在内存的高地址中,而数据的低字节位保存在内存的低地址中

这种存储模式将地址的高低和数据位权有效地结合起来,高地址部分权值高,低地址部分权值低,和我们的逻辑方法一致

---

|操作|值|
|:---:|:---:|
|参数x|[01100011] [10010101]|
|x<<4|[00110000] [01010000]|
|x>>4(逻辑右移)|[00000110] [00001001]|
|x>>4(算术右移)|[00000110] [11111001]|

对于有符号数使用算术右移,对于无符号数使用逻辑右移

```cpp
#include<iostream>
using namespace std;
int main(){
    int a=0xffffffff;
    cout<<hex<<(a>>2)<<endl;
    unsigned int b=0xffffffff;
    cout<<hex<<(b>>2)<<endl;
    return 0;
}
```

```
ffffffff
3fffffff
```

![](https://img.misaka.gq/Notes/CSAPP/位移量过高.png)

### 整数表示

补码的最高有效位的权为$-2^{w-1}$,反码的最高有效位的权为$-(2^{w-1}-1)$,原码的最高有效位是符号位,用于确定剩下的位应该取正权还是负权

在原码和反码中对于0用两种编码方式,既`+0`与`-0`

原码`0000 0000`与`1000 0000`均表示`0`

反码`0000 0000`与`1111 1111`均表示`0`

`0101`转补码为`-0*2^3+1*2^2+0*2^1+1*2^0=0+4+0+1=5`

`1111`转补码为`-1*2^3+1*2^2+1*2^1+1*2^0=-8+4+2+1=-1`

---

有符号数与无符号数之间的转换

```cpp
#include "stdio.h"
int main(){
    short int v=-12345;
    unsigned short uv=(unsigned short) v;
    printf("v=%d uv=%u",v,uv);
    return 0;
}
```

`v=-12345 uv=53191`

```cpp
#include "stdio.h"
int main(){
    unsigned u=0xFFFFFFFF;
    int tu=(int) u;
    printf("u=%u tu=%d",u,tu);
    return 0;
}
```

`u=4294967295 tu=-1`

```cpp
#include "stdio.h"
int main(){
    int x=-1;
    unsigned u=2147483648;
    printf("x=%u=%d\n",x,x);
    printf("u=%u=%d",u,u);
    return 0;
}
```

`x=4294967295=-1`

`u=2147483648=-2147483648`

强制类型转换的结果保持位值不变,只是改变了解释这些位的方式

---

当执行一个运算时,如果它的一个运算数是有符号的而另一个是无符号的,那么C语言会隐式地将有符号参数强制类型转换为无符号数,并假设这两个数都是非负的,来执行这个运算

|表达式|类型|求值|原因|
|:---:|:---:|:---:|:---:|
|0==0U|无符号|1||
|-1<0|有符号|1||
|-1<0U|无符号|0|hex(-1)=0xFFFFFFFF|
|2147483647>-2147483647-1|有符号|1||
|2147483647U>-2147483647-1|无符号|0|hex(2147483647)=0x7fffffff,hex(-2147483648)=0x8000000|
|2147483647>(int)2147483648U|有符号|1|(int)2147483648U=-2147483648|
|-1>-2|有符号|1||
|(unsigned)-1>-2|无符号|1|(unsigned)-1=0xffffffff|
|-2147483647-1==2147483648U|无符号|1|(unsigned)-2147483648=2147483648|
|-2147483647-1<2147483647|有符号|1||
|-2147483647-1U<2147483647|无符号|0|-2147483647-1U=2147483648|
|-2147483647-1<-2147483647|有符号|1||
|-2147483647-1U<-2147483647|无符号|1|(unsigned)-2147483647=2147483649|

---

转换顺序的影响

```cpp
#include<stdio.h>
typedef unsigned char * byte_pointer;
void show_bytes(byte_pointer start,size_t len){
    size_t i;
    for(i=0;i<len;++i){
        printf("%.2x",start[i]);
    }
    printf("\n");
}
int main(){
    short sx=-12345;
    unsigned uy=sx;
    printf("uy=%u\t",uy);
    show_bytes((byte_pointer)&uy,sizeof(unsigned));
    return 0;
}
```

`uy=4294954951   c7cfffff`

当把`short`转换成`unsigned`时,**先改变大小,然后再完成从有符号到无符号的转变**,既`(unsigned)sx`等价于`(unsigned)(int)sx`,而不是`(unsigned)(unsigned short)sx`

```cpp
#include<stdio.h>
typedef unsigned char * byte_pointer;
void show_bytes(byte_pointer start,size_t len){
    size_t i;
    for(i=0;i<len;++i){
        printf("%.2x",start[i]);
    }
    printf("\n");
}
int main(){
    short sx=-12345;
    unsigned a=(unsigned)(int)sx;
    unsigned b=(unsigned)(unsigned short)sx;
    printf("%u %u\n",a,b);
    show_bytes((byte_pointer)&a,sizeof(unsigned));
    show_bytes((byte_pointer)&b,sizeof(unsigned));
    return 0;
}
```

```
4294954951 53191
c7cfffff
c7cf0000
```