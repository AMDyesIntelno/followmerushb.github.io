# 《汇编语言》王爽

## 基础知识

微型机存储器的存储单元可以存储一个Byte

微机存储器的容量是以字节为最小单位来计算的

8个`bit`组成一个`byte`

```
1KB=1024B
1MB=1024KB
1GB=1024MB
1TB=1024GB
```

存储单元从`零`开始顺序编号

**总线**:在计算机中专门链接CPU和其他芯片的导线

|总线分类|信息类型|内容|
|:---:|:---:|:---:|
|地址总线|地址信息|存储单元的地址|
|控制总线|控制信息|器件的选择,读或写的命令|
|数据总线|数据信息|读或写的数据|

### 地址总线

CPU通过地址总线来指定存储单元,**地址总线的宽度决定了CPU的寻址能力**

一个CPU有N根地址线,则CPU最多可以寻找2^N个内存单元

>一个具有16根地址线的CPU,可以寻址2^16=65536Byte=64KB内存

### 数据总线

CPU与其他器件之间的数据传送通过数据总线进行,**数据总线的宽度决定了CPU与其他器件进行数据传送时的一次数据传送量**

8根数据总线一次可以传送一个8位2进制数据(8bits=1Byte),即一个字节

>8086的数据总线宽度位16根(一次传输2Byte数据),从内存中读取1024字节的数据,至少要读512次

### 控制总线

CPU对外部器件的控制通过控制总线进行,**控制总线的宽度决定了CPU对系统中其他器件的控制能力**

---

8086内存地址分配情况

|范围|分配情况|
|:---:|:---:|
|00000~9FFFF|RAM|
|A0000~BFFFF|显存(写入会显示在终端中)|
|C0000~FFFFF|ROM(写入无效)|

## 寄存器

### 通用寄存器

寄存器就是CPU中用来存储数据的地方

寄存器有多大取决于CPU: 

32位CPU: 8,16,32

64位CPU: 8,16,32,64

- 8个32位通用寄存器

```
EAX: 返回值存放
EBX: 寻址存放基地址
ECX: 计数器
EDX: 余数存放
ESP,EBP: 栈顶指针,EBP指向系统栈最上面一个栈帧的底部
ESI,EDI: 源索引寄存器,指向源串/目标串
特殊寄存器
EIP: CPU即将执行指令地址,无法作为他用
```

- 16位通用寄存器

```
就是32位寄存器的拆分
AX
BX
CX
DX
SP
BP
SI
DI
```

- 8位通用寄存器

就是16位寄存器的拆分,与16位又不相同,因为他有高八位和低八位

AH可以理解为(A Hight)高八位,也就是16位寄存器的前两位

AL可以理解为(A Low)低八位,后两位

以此类推

SP,BP,SI,DI没有八位寄存器

### 数据尺寸

- 字节: 记为`byte`,保存在8位寄存器中

- 字: 记为`word`,保存在16位寄存器中

- 双字: 记为`dword`,保存在32位寄存器中

存放一个字型数据(16位)的内存单元称为**字单元**

### 大小端

数值`0x1A2B3C4D`想要在计算机中正确使用,就必须要考虑在内存中将其对应的四个字节合理存储

假设内存的地址都是从低到高分配的,那么对于一个数值多个字节顺序存储就有两种存储方式:

1. 数值的高位字节存放在内存的低地址端,低位字节存放在内存的高地址端:

```
内存低地址 --------------------> 内存高地址

0x1A   |  0x2B   |  0x3C   |   0x4D

高位字节 <-------------------- 低位字节
```

大端模式:即高位字节放在内存的低地址端,低位字节放在内存的高地址端

![](https://img.misaka.gq/Notes/subject/汇编语言/大端模式.png)

2. 数值的低位字节存放在内存的低地址端,高位字节存放在内存的高地址端:

```
内存低地址 --------------------> 内存高地址

0x4D   |  0x3C   |  0x2B   |   0x1A

低位字节 --------------------> 高位字节
```

小端模式:即低位字节放在内存的低地址端,高位字节放在内存的高地址端

![](https://img.misaka.gq/Notes/subject/汇编语言/小端模式.png)

### 物理地址

8086有20位地址总线,可以寻址1MB内存,但8086是16位CPU,一次传输的地址为16位

8086采用将两个16位地址合成一个20位的物理地址

当8086CPU要读写内存时:

1. CPU中提供两个16位地址,一个是**段地址**,另一个是**偏移地址**

2. 段地址和偏移地址通过内部总线送入**地址加法器**

3. 地址加法器将两个16位地址合成为一个20位的物理地址

4. 地址加法器通过内部总线将20位物理地址送入输入输出控制电路

5. 输入输出控制电路将20位物理地址送到地址总线

6. 20位物理地址被地址总线传送到存储器

地址加法器采用**物理地址=段地址x16(段地址左移4位)+偏移地址**的方法将段地址和偏移地址合成物理地址

CPU可以用不同的段地址和偏移地址形成同一个物理地址,显然一个物理地址可以对应多个逻辑地址

```
21F60=2000*16+1F60
      2100*16+0F60
      21F0*16+0060
```

数据在`21F60H`内存单元中,可以表示位数据在内存`2000:1F60`单元中

偏移地址位16位,16位地址的寻址能力位64KB,所以一个段的长度最大为64KB

>如果给定一个段地址,仅通过变化偏移地址来进行寻址,最多可定位`2^16`个内存单元

### 段寄存器

段地址在8086CPU的段寄存器中存放,8086共有4个段寄存器分别为`CS`,`DS`,`SS`,`ES`

`CS`为代码段寄存器,`IP`为指令指针寄存器

在任意时刻,设CS中的内容位`M`,IP中的内容位`N`,8086CPU将从内存`Mx16+N`单元开始(将`CS:IP`指向的内容),读取一条指令并执行

8086CPU的工作过程:

1. 从`CS:IP`指向的内存单元读取指令,读取的指令进入指令缓冲器

2. IP=IP+所读取指令的长度,从而指向下一条指令

3. 执行指令,回到步骤1,重复过程

在8086CPU加电启动或复位后,CS和IP被设置为`CS=FFFFH`,`IP=0000H`,即FFFF0H单元中的指令是8086开机后执行的第一条指令

### 修改CS,IP

不能通过MOV来直接修改CS,IP的值,但可以通过JMP指令进行

1. 若想同时修改CS,IP的内容,可以用`JMP 段地址:偏移地址`完成

`JMP 2AE3:3`->`CS=2AE3H`,`IP=0003H`,CPU从`2AE33H`处读取指令

`JMP 3:0B16`->`CS=0003H`,`IP=0B16H`,CPU从`00B46H`处读取指令

2. 若想仅修改IP的内容,可以用`JMP 寄存器`完成

`JMP AX`

指令执行前`AX=1000H`,`CS=2000H`,`IP=0003H`

指令执行后`AX=1000H`,`CS=2000H`,`IP=1000H`

---

下面的3条指令执行后,CPU几次修改IP?都是在什么时候?最后IP中的值是多少

```nasm
mov ax,bx
sub ax,ax
jmp ax
```

读取`mov ax,bx`后,IP自动修改,指向下一条指令

读取`sub ax,ax`后,IP自动修改,指向下一条指令

读取`jmp ax`后,IP自动修改,指向下一条指令

执行`jmp ax`后,IP被修改

一共修改了4次,IP最后为0

### 实验1 Debug的使用

- 用Debug的`R`命令查看,改变CPU寄存器的内容
- 用Debug的`D`命令查看内存中的内容
- 用Debug的`E`命令改写内存中的内容
- 用Debug的`U`命令将内存中的机器指令翻译成汇编指令
- 用Debug的`T`命令执行一条机器指令
- 用Debug的`A`命令以汇编指令的格式在内存中写入一条机器指令
- 用Debug的`G`命令连续执行程序

1. 用`R`命令查看,改变CPU寄存器的内容

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_r.png)

2. 用`D`命令查看内存中的内容

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_d_1.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_d_2.png)

默认输出128个内存单元的内容

`d 1000:5 L 11`用L选择范围,查看1000H段的5H号单元到15H号单元共11H个单元

3. 用`E`命令改写内存中的内容

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_e.png)

`-e 1000:0 0 1 2 3 4`批量更改

以提问的方式逐个修改

- 输入`e 1000:5`
- 输入想要写入的数据,按空格保存当前修改,切换到下一个内存单元,不进行修改则直接空格
- 回车退出

`-e 1000:8 'a'`,`-e 1000:7 'abc'`直接写入字符/字符串

4. 用`U`命令将内存中的机器指令翻译成汇编指令

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_u.png)

|机器码|汇编指令|
|:---:|:---:|
|`b80100`|`mov ax,0001`|
|`b90200`|`mov cx,0002`|
|`01c8`|`add ax,cx`|

5. 用`T`命令执行一条机器指令

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_t.png)

在执行指令前先将CS:IP指向要指向的机器指令

6. 用`A`命令以汇编指令的格式在内存中写入一条机器指令

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_a_1.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_a_2.png)

7. 用Debug的`G`命令连续执行程序

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_g.png)

8. 用段寄存器代替段地址

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_d_3.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_eau.png)

---

向内存从`B8100H`开始的单元中填写数据

`-e B810:0000 01 01 02 02 03 03 04 04`

![](https://img.misaka.gq/Notes/subject/汇编语言/向显存写入数据.png)

---

### DS

内存地址由段地址和偏移地址组成,8086CPU中有一个DS寄存器,用来存放要访问数据的段地址

要读取`10000H`单元的内容

```nasm
mov bx,1000H
mov ds,bx
mov al,[0]
```

`[...]`表示一个内存单元,`[0]`中的0表示偏移地址,而段地址由8086自动从DS中获取

!>8086不支持将数据直接送入段寄存器的操作,需要通过一个寄存器进行中转

!>段寄存器不能进行算术运算(`add`,`sub`...),也不能进行立即数赋值运算

### 栈

!>8086CPU的入栈和出栈操作都是以**字**为单位进行的

在8086CPU中,栈顶的段地址存放在段寄存器SS中,偏移地址存放在寄存器SP中,任意时刻,`SS:SP`指向栈顶元素

`push ax`

- `SP=SP-2`,`SS:SP`指向新的内存单元,以新的内存单元作为栈顶
- 将ax中的内容送入`SS:SP`指向的内存单元,`SS:SP`此时指向新栈顶

!>不能push立即数

8086CPU在入栈时,栈顶从高地址向低地址方向增长

!>如果将`10000H~1000FH`这段空间当作栈,初始状态栈为空,此时,SS=1000H,SP=?

将`10000H~1000FH`这段空间当作栈段,`SS=1000H`,栈空间大小位16字节,栈最底部的字单元地址位1000:000E,当栈中只有一个元素的时候,`SS=1000H`,`SP=000EH`

栈为空,则相当于栈中唯一的元素出栈,出栈后,`SP=SP+2`,`SP=10H`

`pop ax`

- 将`SS:SP`指向的内存单元处的数据送入ax中
- `SP=SP+2`,`SS:SP`指向当前栈顶下面的单元,以该单元为新的栈顶

!>pop操作前的栈顶元素在pop操作后仍然存在,但是已经不在栈中

!>8086没有栈超界检测,需要注意防止入栈数据过多/栈空时仍然出栈

!>用栈来暂存以后需要回复的寄存器的内容时,寄存器出栈的顺序要和入栈的顺序相反

---

在`10000H`处写入字型数据`2266H`

```nasm
mov ax,1000H
mov ss,ax
mov sp,2
mov ax,2266H
push ax
```

注意`mov sp,2`,因为CPU在执行`push ax`前要先将`SP=SP-2`使得`SS:SP`指向`10000H`

### 栈段

如果将`10000H~1FFFFH`这段空间作为栈段,初始状态栈时空的,此时SS=1000H,SP=?

如果将`10000H~1FFFFH`这段空间作为栈段,`SS=1000H`,栈底的字单元地址为`1000:FFFE`,任意时刻,`SS:SP`指向栈顶单元,当栈中只有一个元素时,`SS=1000H`,`SP=FFFEH`

栈为空,则相当于栈中唯一的元素出栈,出栈后,`SP=SP+2`,`SP=0000H`

---

一个栈段最大可以设为多少?为什么?

push和pop等指令在执行时只修改SP,所以栈顶的变化范围时`0~FFFFH`,所以一个栈段的最大容量为`64KB`

---

![](https://img.misaka.gq/Notes/subject/汇编语言/逆序复制示意图.png)

1. 补全下面的程序,使其可以将`10000H~1000FH`中的8个字,逆序复制到`20000H~2000FH`中

```nasm
mov ax,1000H
mov ds,ax
```

```nasm
;补全内容
mov ax,2000H
mov ss,ax
mov sp,0010H
```

```nasm
push [0]
push [2]
push [4]
push [6]
push [8]
push [A]
push [C]
push [E]
```

2. 补全下面的程序,使其可以将`10000H~1000FH`中的8个字,逆序复制到`20000H~2000FH`中

```nasm
mov ax,2000H
mov ds,ax
```

```nasm
;补全内容
mov ax,1000H
mov ss,ax
mov sp,0000H
```

```nasm
pop [E]
pop [C]
pop [A]
pop [8]
pop [6]
pop [4]
pop [2]
pop [0]
```

### 实验2 用机器指令和汇编指令编程

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_t_ss1.png)

汇编指令对`SS`和`SP`是同时进行操作的,当两条语句在相邻的上下行时,使用`T`命令单步执行,这两个指令会同时进行(在实体机上)

便于控制栈段大小,防止特别是在有子程序调用时出错

![](https://img.misaka.gq/Notes/subject/汇编语言/debug_t_ss2.png)

在使用T命令时,产生了中断,为了保护现场,CPU将`CS`和`IP`此时的位置入栈,导致了内存相关位置内容的改变

## 第一个程序

>C程序编译过程:预处理->编译->汇编->链接

[C程序编译过程](cs/CSAPP)

### 源程序

```nasm
assume cs:codesg
codesg segment

    mov ax,0123h
    mov bx,0456h
    add ax,bx
    add ax,ax

    mov ax,4c00h
    int 21h

codesg ends
end
```

1. 伪指令

- segment

```nasm
XXX segment
...
XXX ends
```

`segment`和`ends`是一对成对使用的伪指令,功能是定义一个段,`segment`标明开始,`ends`标明结束,一个段必须有段名`XXX`

- end

`end`是一个汇编程序的结束标记,注意区分`end`和`ends`

- assume

假设某一段寄存器和程序中的某一个用`segment...ends`定义的段相关联

`codesg segment ... codesg ends`定义了一个名为`codesg`的段,`assume cd:codesg`将这个段与段寄存器`cs`联系起来

2. 源程序中的"程序"

源程序中包括伪指令和汇编指令,汇编指令组成了最后由计算机执行的程序,伪指令由编译器处理

3. 标号

一个标号指代了一个地址,比如`codesg`,作为一个段的名称,这个段的名称最终将被处理成一个段的段地址

4. 程序结构

?>编程运算2^3

- 定义一个段,名称为`abc`

```nasm
abc segment
...
abc ends
```

- 在段中写入汇编指令

```nasm
abc segment

    mov ax,2
    add ax,ax
    add ax,ax

abc ends
```

- 确定程序合适结束

```nasm
abc segment

    mov ax,2
    add ax,ax
    add ax,ax

abc ends
end
```

- 将`abc`和`cs`联系起来

```nasm
assume cs:abc
abc segment

    mov ax,2
    add ax,ax
    add ax,ax

    mov ax,4c00H
    int 21H

abc ends
end
```

5. 程序返回

!>一个程序P2在可执行文件中,则必须由一个正在运行的程序P1,将P2从可执行文件中加载入内存后,将CPU的控制权交给P2,P2才能得以运行

!>P2开始运行后,P1暂停运行

!>而当P2运行完毕后,应该将CPU的控制权交还给P1,此后,P1继续运行

一个程序结束后,将CPU的控制权交还给使其运行的程序,这个过程称为**程序返回**,因此应该在程序的末尾添加返回的程序段

```nasm
mov ax,4c00H
int 21H
```

|目的|相关指令|指令性质|指令执行者|
|:---:|:---:|:---:|:---:|
|通知编译器一个段结束|段名`ends`|伪指令|编译时,由编译器执行|
|通知编译器程序结束|`end`|伪指令|编译时,由编译器执行|
|程序返回|`mov ax,4c00H  int 21H|汇编指令|执行时,由CPU执行|

### 编译

![](https://img.misaka.gq/Notes/subject/汇编语言/编译.png)

输入`.asm`源程序文件,输出`.obj`目标文件(`.list`列表文件,`.crf`交叉引用文件)

### 连接

![](https://img.misaka.gq/Notes/subject/汇编语言/连接.png)

程序中没有调用任何子程序,所以忽略库文件名的输入,`Libraries [.LIB]:`为空

`LINK:warning L4021:no stack segment`说明这个程序没有栈段

连接的作用

1. 当源程序很大时,将其分为多个源程序文件来编译,得到多个目标文件,再将其连接得到可执行文件

2. 调用了某个库文件中的子程序,需要将这个库与目标文件连接

3. 一个源程序编译后,得到了存有机器码的目标文件,目标文件中的有些内容不能直接用来生成可执行文件,连接程序将这些内容处理为最终的可执行信息

简化方式进行编译和连接

![](https://img.misaka.gq/Notes/subject/汇编语言/简化编译和连接.png)

`masm test;`,`link test;`注意分号

---

此时,有一个正在运行的程序将1.exe中的程序加载入内存,这个正在运行的程序是什么?它将程序载入内存后,如何是程序得以运行?程序运行结束后,返回到哪里?

1. 在DOS中直接执行1.exe时,使正在运行的`command`,将1.exe中的程序载入内存

2. `command`设置CPU的`CS:IP`指向程序的第一条指令(即程序的入口),从而使程序得以运行

3. 程序运行结束后,返回到`command`中,CPU继续运行`command`

### 程序执行过程

![](https://img.misaka.gq/Notes/subject/汇编语言/PSP.png)

1. 程序加载后,`ds`中存放着程序所在内存区的段地址,这个内存区的偏移地址为0,则程序所在的内存区的地址为`ds:0`

2. 这个内存区的前256个字节中存放的是`PSP`,DOS用来和程序进行通信,从256字节处向后的空间存放的是程序

|区划|地址|
|:---:|:---:|
|空闲内存区|`SA:0`|
|PSP区|`SA:0`|
|程序区|`SA+10H:0`|

用`P`命令执行`INT 21`,返回`Program terminated normally`

### 实验3 编程,编译,连接,跟踪

```nasm
assume cs:codesg
codesg segment

    mov ax,2000H
    mov ss,ax
    mov sp,0
    add sp,10
    pop ax
    pop bx
    push ax
    push bx
    pop ax
    pop bx

    mov ax,4c00H
    int 21H

codesg ends
end
```

![](https://img.misaka.gq/Notes/subject/汇编语言/实验3_1.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/实验3_2.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/实验3_3.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/实验3_4.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/实验3_5.png)

## [BX]和loop指令

### [BX]

`[0]`表示内存单元,偏移地址为0,`[BX]`同样表示内存单元,偏移地址为`BX`寄存器中的内容

![](https://img.misaka.gq/Notes/subject/汇编语言/bx.png)

!>必须是基地址寄存器bx,不能是其它寄存器,如ax等,都会报错

!>约定使用`idata`表示常量,`mov bx,idata`代表如`mov bx,1`

!>在DOS方式下,通常使用`0:200~0:2ff`的空间

### loop

```nasm
assume cs:code
code segment

    mov ax,2
    mov cx,11
s:  add ax,ax
    loop s

    mov ax,4c00h
    int 21h

code ends
end
```

1. 标号

在汇编语言中,标号代表一个地址,标号`s`标识了一个地址,这个地址有一条指令`add ax,ax`

2. `loop s`

CPU执行`loop s`时,进行以下两步操作

- `cx=cx-1`
- 判断`cx`中的值,不为0则跳转到标号s所标识的地址处执行,如果为0则执行下一条指令

```nasm
mov cx,循环次数
s:循环执行的程序段
loop s
```

```nasm
assume cs:codesg
codesg segment

    mov ax,0ffffh
    mov ds,ax
    mov ax,0
    mov al,byte ptr ds:[06h];mov bx,6  mov al,[bx]  mov ah,0
    mov dx,0

    mov cx,3
s:  add dx,ax
    loop s

    mov ax,4c00h
    int 21h

codesg ends
end
```

!>在汇编源程序中,数据不能以字母开头,`A000h`要在前面加0,即`0A000h`

!>在汇编源程序中,不能直接写`mov ax,[0]`,而是`mov bx,0  mov ax,[bx]`或者`mov ax,ds:[0]`

---

计算`FFFF:0~FFFF:B`单元中的数据的和

```nasm
assume cs:codesg
codesg segment

    mov ax,0ffffh
    mov ds,ax
    mov ax,0
    mov bx,0
    mov dx,0

    mov cx,0ch
s:  mov al,[bx]
    inc bx
    add dx,ax
    loop s

    mov ax,4c00h
    int 21h

codesg ends
end
```

### 段前缀

```nasm
mov ax,ds:[bx];段地址在ds中
mov ax,cs:[bx];段地址在cs中
mov ax,ss:[bx];段地址在ss中
mov ax,es:[bx];段地址在es中
```

`ds`,`cs`,`ss`,`es`称为**段前缀**

`ES`附加段寄存器可以作为`DS`,当`DS`不够用时可以用`ES`作为补充

---

将内存`ffff:0~ffff:b`单元中的数据复制到`0:200~0:20b`单元中

`0:200~0:20b`等价于`0020:0~0020:b`

```nasm
assume cs:codesg
codesg segment

    mov ax,0ffffh
    mov ds,ax
    mov ax,0020h
    mov es,ax
    mov ax,0
    mov bx,0
    mov cx,0ch
    
    copy:
    mov al,ds:[bx]
    mov es:[bx],al
    inc bx
    loop copy

    mov ax,4c00h
    int 21h

codesg ends
end
```

### 实验4 [bx]和loop的使用

编程,向内存`0:200~0:23f`依次传送数据`0~3fh`

```nasm
assume cs:codesg
codesg segment

    mov ax,0020h
    mov ds,ax
    mov bx,0
    mov cx,40h

    copy:
    mov ds:[bx],bl
    inc bl
    loop copy

    mov ax,4c00h
    int 21h

codesg ends
end
```

![](https://img.misaka.gq/Notes/subject/%E6%B1%87%E7%BC%96%E8%AF%AD%E8%A8%80/%E5%AE%9E%E9%AA%8C4.png)

## 包含多个段的程序

### 在代码段中使用数据

编程计算8个数据的和,结果保存在ax中

```nasm
assume cs:code
code segment

    dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h
    mov ax,0
    mov bx,0

    mov cx,8
    s:
    add ax,cs:[bx]
    add bx,2
    loop s

    mov ax,4c00h
    int 21h

code ends
end
```

`dw`即`define word`,定义**字型**数据,程序在运行时`CS`中存放代码,所以这8个数据存放在`CS`中,位于代码段的开始,所以偏移地址为0

!>由于是字型数据,所以`add bx,2`

![](https://img.misaka.gq/Notes/subject/汇编语言/dw_1.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/dw_2.png)

debug加载后的的`IP`为`0000`,要将其修改为`0010`才能正常运行程序,因此存在缺陷

```nasm
assume cs:code
code segment

    dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h
    start:
    mov ax,0
    mov bx,0

    mov cx,8
    s:
    add ax,cs:[bx]
    add bx,2
    loop s

    mov ax,4c00h
    int 21h

code ends
end start
```

在程序的第一条指令的前面加上一个标号`start`,并在伪指令`end`后面出现

!>`end`除了通知编译器程序结束外,还可以通知编译器程序的入口在什么地方,在不指明程序入口的情况下,程序默认按照顺序从头开始执行

因此`mov bx,0`是程序的第一条指令

### 在代码段中使用栈

利用栈,将程序中定义的数据逆序存放

```nasm
assume cs:code
code segment

    dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h
    dw 0,0,0,0,0,0,0,0;用dw定义了16个字型数据,在程序加载后,将这16个字的内存空间作为栈来使用
    start:
    mov ax,cs
    mov ss,ax
    mov sp,20h;将栈顶指向cs:20

    mov bx,0
    mov cx,8
    s0:
    push cs:[bx]
    add bx,2
    loop s0
    
    mov bx,0
    mov cx,8
    s1:
    pop cs:[bx]
    add bx,2
    loop s1

    mov ax,4c00h
    int 21h

code ends
end start
```

### 将数据,代码,栈放入不同的段

将程序中定义的数据逆序存放

```nasm
assume cs:code,ds:data,ss:stack

data segment
    dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h
data ends

stack segment
    dw 0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0
stack ends

code segment
    start:
    mov ax,stack
    mov ss,ax
    mov sp,20h;设置栈顶ss:sp指向stack:20

    mov ax,data
    mov ds,ax
    mov bx,0;ds:bx指向data段中的第一个单元

    mov cx,8
    s0:
    push [bx]
    add bx,2
    loop s0

    mov bx,0

    mov cx,8
    s1:
    pop [bx]
    add bx,2
    loop s1
    
    mov ax,4c00h
    int 21h
code ends

end start
```

设置了三个段`assume cs:code,ds:data,ss:stack`,其后将段寄存器指向这些段

### 实验5 编写,调试具有多个段的程序

```nasm
assume cs:code,ds:data,ss:stack

data segment
   ...
data ends

stack segment
   ...
stack ends

code segment

start:  
   ...

code ends

end start
```

!>数据段空间大小为定义数据所需的16字节的最小整数倍

比如定义了1个字节,系统就给数据段分配16个字节;定义了17个字节,系统就分配32个字节

![](https://img.misaka.gq/Notes/subject/汇编语言/实验5_1.png)

---

```nasm
assume cs:code,ds:data,ss:stack

code segment
    start:
    mov ax,stack
    mov ss,ax
    mov sp,16

    mov ax,data
    mov ds,ax

    push ds:[0]
    push ds:[2]
    pop ds:[2]
    pop ds:[0]
    
    mov ax,4c00h
    int 21h
code ends

data segment
    dw 0123h,0456h
data ends

stack segment
    dw 0,0
stack ends

end start
```

设程序加载后,code段的段地址为`X`,则data段的段地址为`X+3`,stack段的段地址为`X+4`

![](https://img.misaka.gq/Notes/subject/汇编语言/实验5_2.png)

## 定位内存地址

在数据段中保存字符

```nasm
data segment
    db 'unIX'
    db 'foRK'
    ;wrong: dw 'asdf'
data ends
```

大小写转换

```nasm
assume cs:code,ds:datasg
datasg segment
    db 'BaSiC'
    db 'iNfOrMaTiOn'
datasg ends

code segment
    start:
    mov ax,datasg
    mov ds,ax

    mov bx,0
    mov cx,5
    s0:
    or byte ptr ds:[bx],00100000b;将ds:[bx]中的ASCII码的第5位置为1,变为小写字母
    inc bx
    loop s0

    mov cx,11
    s1:
    and byte ptr ds:[bx],11011111b;将ds:[bx]中的ASCII码的第5位置为0,变为大写字母
    inc bx
    loop s1

    mov ax,4c00h
    int 21h
code ends
end start
```

### [bx+idata]

`[bx+idata]`表示一个内存单元,其偏移地址为bx中的数值加上idata,可以用这种表示方式进行类似数组的处理

- C语言:`a[i],b[i]`

- 汇编:`0[bx],5[bx]`

可以表示为以下格式

```nasm
mov ax,[200+bx]
mov ax,200[bx]
mov ax,[bx].200
```

```nasm
assume cs:code,ds:datasg
datasg segment
    db 'BaSiC'
    db 'MinIX'
datasg ends

code segment
    start:
    mov ax,datasg
    mov ds,ax

    mov bx,0
    mov cx,5
    s:
    and byte ptr ds:[bx+5],11011111b;将ds:[bx]中的ASCII码的第5位置为0,变为大写字母
    or byte ptr ds:[bx+0],00100000b;将ds:[bx]中的ASCII码的第5位置为1,变为小写字母
    inc bx
    loop s

    mov ax,4c00h
    int 21h
code ends
end start
```

### SI和DI

SI和DI是8086中和BX功能相近的寄存器,但SI和DI不能够分成两个8位寄存器

`[bx+si]`表示一个内存单元,其偏移地址为bx中的数值加上si中的数值

`[bx+si+idata]`表示一个内存单元,其偏移地址为bx中的数值加上si中的数值加上idata

用SI和DI实现将字符串复制到它后面的数据区中

```nasm
assume cs:code,ds:datasg
datasg segment
    db 'welcome to masm!'
    db '................'
datasg ends

code segment
    start:
    mov ax,datasg
    mov ds,ax

    mov si,0
    mov di,16
    mov bx,0
    mov cx,16
    s:
    mov al,ds:[si+bx]
    mov ds:[di+bx],al
    inc bx
    loop s

    mov ax,4c00h
    int 21h
code ends
end start
```

### 不同寻址方式的灵活使用

将datasg段中每个单词的头一个字母改为大写字母

```nasm
assume cs:code,ds:datasg
datasg segment
    db '1. file         '
    db '2. edit         '
    db '3. search       '
    db '4. view         '
    db '5. options      '
    db '6. help         '
datasg ends

code segment
    start:
    mov ax,datasg
    mov ds,ax

    mov bx,0
    mov cx,6
    s:
    and byte ptr ds:[bx+3],11011111b
    add bx,16
    loop s

    mov ax,4c00h
    int 21h
code ends
end start
```

---

将datasg段中每个单词改为大写字母

!>使用栈进行数据的暂存

```nasm
assume cs:code,ds:datasg,ss:stacksg
datasg segment
    db 'ibm             '
    db 'dec             '
    db 'dos             '
    db 'vax             '
datasg ends

stacksg segment
    dw 0,0,0,0,0,0,0,0;定义一个16字节的栈段
stacksg ends

code segment
    start:
    mov ax,datasg
    mov ds,ax
    mov ax,stacksg
    mov ss,ax
    mov sp,16

    mov bx,0;bx作为行
    mov cx,4
    s0:
    push cx
    mov si,0;si作为列
    mov cx,3
    s1:
    and byte ptr ds:[bx+si],11011111b
    inc si
    loop s1
    pop cx
    add bx,16
    loop s0

    mov ax,4c00h
    int 21h
code ends
end start
```

---

将datasg段中每个单词的前4个字母改为大写字母

```nasm
assume cs:code,ds:datasg,ss:stacksg
datasg segment
    db '1. display      '
    db '2. brows        '
    db '3. replace      '
    db '4. modify       '
datasg ends

stacksg segment
    dw 0,0,0,0,0,0,0,0;定义一个16字节的栈段
stacksg ends

code segment
    start:
    mov ax,datasg
    mov ds,ax
    mov ax,stacksg
    mov ss,ax
    mov sp,16

    mov bx,0;bx作为行
    mov cx,4
    s0:
    push cx
    mov si,0;si作为列
    mov cx,4
    s1:
    and byte ptr ds:[bx+si+3],11011111b
    inc si
    loop s1
    pop cx
    add bx,16
    loop s0

    mov ax,4c00h
    int 21h
code ends
end start
```

## 数据处理

两个基本问题

- 处理的数据在什么地方
- 要处理的数据有多长

### BP

BP:基址指针寄存器

BP寄存器主要适用于给出堆栈中数据区的偏移,从而可以方便的实现直接存取堆栈中的数据

可以使用`[bp]`进行内存寻址,当指令中没有显性地给出段地址时,段地址默认为`SS`

!>错误指令

`mov ax,[bx+bp]`

`mov ax,[si+di]`

在没有寄存器名存在的情况下,用操作符`X ptr`指明内存单元的长度,X可以表示为`word`或者`byte`

### DIV指令

DIV是除法指令,`div reg`或`div 内存单元`

- 除数

有8位和16位两种,在一个reg或内存单元中

- 被除数

默认放在AX或DX和AX中,**如果除数为8位,被除数则为16位,默认在AX中存放,如果除数为16位,被除数则为32位,在DX存放高16位,在AX存放低16位**

- 结果

如果除数为8位,则AL存储除法操作的商,AH存储除法操作的余数,如果除数为16位,则AX存储除法操作的商,DX存储除法操作的余数

!>高位放余数,低位放商

```nasm
div byte ptr ds:[0]
;al=ax/(ds*16+0)的商
;ah=ax/(ds*16+0)的余数
div word ptr es:[0]
;ax=(dx*10000H+ax)/(es*16+0)的商
;dx=(dx*10000H+ax)/(es*16+0)的余数
```

利用DIV计算1001/100

```nasm
mov ax,1001
mov bl,100;除数为8位,被除数则为16位
div bl
```

利用DIV计算100001/100

```nasm
mov dx,1
mov ax,86a1h
mov bx,100;除数为16位,被除数则为32位
div bx
```

### 伪指令DD

- db:字节型数据
- dw:字型数据
- dd:double word双字型数据

---

用DIV计算data段中第一个数据处以第二个数据后的结果,商存在第三个数据的存储单元中

```nasm
assume cs:code,ds:data
data segment
    dd 100001;双字
    dw 100
    dw 0
code segment
    start:
    mov ax,data
    mov ds,ax

    mov ax,ds:[0];低16位
    mov dx,ds:[2];高16位
    div word ptr ds:[4];除数为16位,商存放在ax中
    mov ds:[6],ax

    mov ax,4c00h
    int 21h
code ends
end start
```

### dup

dup是一个操作负,配合db,dw等伪指令使用,用来进行数据的重复

使用格式如下

```nasm
db 重复的次数 dup (重复的字节型数据)
dw 重复的次数 dup (重复的字型数据)
dd 重复的次数 dup (重复的双字型数据)
```

`db 3 dup (0)`->`db 0,0,0`

`db 3 dup (0,1,2)`->`db 0,1,2,0,1,2,0,1,2`

`db 3 dup('abc','ABC')`->`db 'abcABCabcABCabcABC'`

### 实验7 结构化数据的访问

```nasm
assume cs:code
data segment
    db '1975','1976','1977','1978','1979','1980','1981','1982','1983'
    db '1984','1985','1986','1987','1988','1989','1990','1991','1992'
    db '1993','1994','1995'

    dd 16,22,382,1356,2390,8000,16000,24486,50065,97479,140417,197514
    dd 345980,590827,803530,1183000,1843000,2759000,3753000,4649000,5937000
    
    dw 3,7,9,13,28,38,130,220,476,778,1001,1442,2258,2793,4037,5635,8226
    dw 11542,14430,15257,17800
data ends

table segment
    db 21 dup ('year summ ne ?? ')
table ends

stack segment
    db 16 dup (0)
stack ends

code segment
    start:
    mov ax,data
    mov ds,ax
    mov ax,table
    mov es,ax
    mov ax,stack
    mov ss,ax
    mov sp,16

    mov bx,0
    mov si,0
    mov di,0
    mov bp,0
    mov cx,21
    s0:
    push cx
    mov cx,4
    mov di,0
    s1:
    mov al,byte ptr ds:[si];年份
    mov byte ptr es:[bx+di],al
    mov al,byte ptr ds:[si+84];收入
    mov byte ptr es:[bx+di+5],al
    inc si
    inc di
    loop s1

    mov ax,word ptr ds:[bp+168];雇员数
    mov word ptr es:[bx+10],ax
    add bp,2

    mov ax,es:[bx+5];人均收入
    mov dx,es:[bx+7]
    div word ptr es:[bx+10]
    mov word ptr es:[bx+0dh],ax

    pop cx
    add bx,16
    loop s0

    mov ax,4c00h
    int 21h
code ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/实验7_1.png)

![](https://img.misaka.gq/Notes/subject/汇编语言/实验7_2.png)

## 转移指令的原理

!>可以修改`IP`或同时修改`CS`和`IP`的指令统称位转移指令

- 只修改`IP`时,称为段内转移
- 同时修改`CS`和`IP`时,称为段间转移

### offset

!>操作符`offset`是由编译器处理的符号,其功能时取得标号的偏移地址

```nasm
assume cs:codesg
codesg segment
    start:
    mov ax,offset start;相当于mov ax,0
    s:
    mov ax,offset s;相当于mov ax,3,前一条指令的长度为3
codesg ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/offset_1.png)

```nasm
assume cs:codesg
codesg segment
    s:
    mov ax,bx;占两个字节
    mov si,offset s
    mov di,offset s0
    mov ax,cs:[si]
    mov cs:[di],ax
    s0:
    nop;nop的机器码占一个字节
    nop
codesg ends
end s
```

![](https://img.misaka.gq/Notes/subject/汇编语言/offset_2.png)

### jmp

!>jmp为无条件转移指令,可以只修改`IP`也可以同时修改`CS`和`IP`

jmp指令要给出以下信息

- 转移的目的地址
- 转移的距离(段间转移,段内短转移,段内近转移)

#### 依据位移进行转移

`jmp short 标号`->IP=IP+8位位移(段内短转移),对IP的修改范围是`-128~127`

```nasm
assume cs:codesg
codesg segment
    start:
    mov ax,0
    jmp short s
    add ax,1
    s:
    inc ax
codesg ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/jmp_1.png)

```nasm
assume cs:codesg
codesg segment
    start:
    mov ax,0
    mov bx,0
    jmp short s
    add ax,1
    s:
    inc ax
codesg ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/jmp_2.png)

```nasm
assume cs:codesg
codesg segment
    start:
    mov ax,0
    mov bx,0
    jmp short s
    add ax,1
    add ax,1
    s:
    inc ax
codesg ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/jmp_3.png)

```nasm
assume cs:codesg
codesg segment
    s:
    inc ax
    start:
    mov ax,0
    mov bx,0
    jmp short s
    add ax,1
    add ax,1
codesg ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/jmp_4.png)

!>CPU在执行jmp指令时并不需要转移的目的地址

- CS=07E4,IP=0006,CS:IP指向`EB ??`(jmp short s的机器码)
- 读取指令(`EB ??`)进入指令缓冲器
- IP=IP+所读取的指令的长度,CS:IP指向`add ax,1`
- CPU执行指令缓冲器中的指令
- 指令执行后,IP指向`inc ax`

指令`EB ??`中没有指出要转移的目的地址,而是指出了要转移的位移

`jmp near ptr 标号`->IP=IP+16位位移(段内近转移),对IP的修改范围是`-32768~32767`

```nasm
assume cs:codesg
codesg segment
    start:
    mov ax,0
    mov bx,0
    jmp near ptr s
    db 128 dup (0)
    add ax,1
    s:
    inc ax
codesg ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/jmp_5.png)

`jmp far ptr 标号`->CS=标号所在段的段地址,IP=标号所在段中的偏移地址

```nasm
assume cs:codesg
codesg segment
    start:
    mov ax,0
    mov bx,0
    jmp far ptr s
    db 128 dup (0)
    add ax,1
    s:
    inc ax
codesg ends
end start
```

![](https://img.misaka.gq/Notes/subject/汇编语言/jmp_6.png)

`8E 00 E4 07`是目的地址在指令中的存储顺序,高地址的`E4 07`是转移的段地址`07E4`,低地址的`8E 00`是偏移地址`008E`

`jmp 16位reg`->IP=16位reg中的值

