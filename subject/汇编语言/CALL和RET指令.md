### ret/retf

- ret指令用栈中的数据,修改IP的内容,从而实现近转移(`pop IP`)
    - IP=SS:[SP]
    - SP=SP+2
- retf指令用栈中的数据,修改CS和IP的内容,从而实现远转移(`pop IP`,`pop CS`)
    - IP=SS:[SP]
    - SP=SP+2
    - CS=SS:[SP]
    - SP=SP+2

```nasm
assume cs:codesg
stack segment
    db 16 dup (0)
stack ends
codesg segment
    mov ax, 4c00h
    int 21h
    start:
    mov ax,stack
    mov ss,ax
    mov sp,16
    mov ax,0
    push ax
    ret;IP=0
codesg ends
end start
```

```nasm
assume cs:codesg
stack segment
    db 16 dup (0)
stack ends
codesg segment
    mov ax, 4c00h
    int 21h
    start:
    mov ax,stack
    mov ss,ax
    mov sp,16
    mov ax,0
    push cs
    push ax
    retf;CS=codesg,IP=0
codesg ends
end start
```

### call

- 将当前的IP或CS和IP压入栈中
- 转移

!>call指令不能实现短转移

#### 依据位移进行转移

`call 标号`(将当前的IP(call的下一条指令)压栈后,转到标号处执行指令)

- SP=SP-2
- SS:[SP]=IP(call的下一条指令)
- IP=IP(call的下一条指令)+16位位移(标号处的地址-call指令后的第一个字节的地址)

相当于

```nasm
push IP(call的下一条指令)
jmp near ptr 标号
```

#### 转移的目的地址在指令中

`call far ptr 标号`实现段间转移

- SP=SP-2
- SS:[SP]=CS
- SP=SP-2
- SS:[SP]=IP(call的下一条指令)

#### 转移地址在寄存器中

`call 16位reg`

- SP=SP-2
- SS:[SP]=IP(call的下一条指令)
- IP=reg中的数值

#### 转移地址在内存中

`call word ptr 内存单元地址`

- SP=SP-2
- SS:[SP]=IP(call的下一条指令)
- IP=内存单元中的值

`call dword ptr 内存单元地址`

- SP=SP-2
- SS:[SP]=CS
- CS=高地址处的值
- SP=SP-2
- SS:[SP]=IP(call的下一条指令)
- IP=低地址处的值

### call和ret配合

```nasm
assume cs:code
code segment
main:
    ...
    call sub1
    ...
    mov ax,4c00h
    int 21h

sub1:
    ...
    call sub2
    ...
    ret

sub2:
    ...
    ret
code ends
end main
```

### mul指令

1. 两个相乘的数,要么都是8位,要么都是16位,如果是8位,一个默认放在AL中,另一个放在8位reg或内存单元中,如果是16位,一个默认放在AX中,一个放在16位reg或内存单元中

2. 如果是8位乘法,结果默认在AX中,如果是16位乘法,高位在DX中,低位在AX中

计算data段中第一组数据的3次方,结果保存在后面一组dword单元中

```nasm
assume cs:codesg
data segment
    dw 1,2,3,4,5,6,7,8
    dd 8 dup (0)
data ends
codesg segment
start:
    mov ax,data
    mov ds,ax
    mov si,0
    mov di,16
    mov cx,8
s:
    mov bx,word ptr ds:[si]
    call cubed
    mov word ptr ds:[di],ax
    mov word ptr ds:[di+2],dx
    add si,2
    add di,4
    loop s

    mov ax, 4c00h
    int 21h

cubed:
    mov ax,bx
    mul bx
    mul bx
    ret
codesg ends
end start
```

### 批量数据传递

?>将一个全是字母的字符串转化为大写

```nasm
assume cs:codesg
data segment
    db 'conversation'
data ends
codesg segment
main:
    mov ax,data
    mov ds,ax
    mov bx,0
    mov cx,12
    call upper

    mov ax,4c00h
    int 21h

upper:
    and byte ptr ds:[bx],11011111b
    inc bx
    loop upper
    ret

codesg ends
end main
```

?>计算1+3+5+7+9,用栈传递参数

```nasm
assume cs:codesg
stack segment
    db 16 dup (0)
stack ends
data segment
    dw 1,3,5,7,9
data ends
codesg segment
main:
    mov ax,stack
    mov ss,ax
    mov sp,16
    mov ax,data
    mov ds,ax
    mov bx,0
    mov cx,5
s0:
    push word ptr ds:[bx]
    add bx,2
    loop s0

    call calculate
    mov ax,4c00h
    int 21h

calculate:
    push bp;bp入栈,先保存bp原数据
    mov bp,sp;把sp值给bp,使用基于bp的固定偏移量对栈中的内容进行读取
    mov ax,ss:[bp+4];9
    add ax,ss:[bp+6];7
    add ax,ss:[bp+8];5
    add ax,ss:[bp+10];3
    add ax,ss:[bp+12];1
    mov sp,bp;恢复sp
    pop bp;恢复bp
    ret 10;堆栈平衡,返回到call的下一条指令

codesg ends
end main
```

### 实验10 编写子程序

1. 显示字符串

```nasm
assume cs:code
data segment
    db 'Welcome to masm!',0
data ends
stack segment
    dw 8 dup (0)
stack ends
code segment
main:
    mov dh,8;行号
    mov dl,3;列号
    mov cl,2;颜色
    mov ax,data
    mov ds,ax
    mov si,0;ds:si指向字符串的首地址
    call show_str

    mov ax, 4c00h
    int 21h
show_str:

    push dx
    push cx;保存相关寄存器

    mov ax,0b800h
    mov es,ax;显存
    mov ax,00a0h
    sub dh,1
    mul dh
    mov bx,ax;es:[bx]
    mov ax,0
    sub dl,1
    mov al,dl
    add bx,ax
    add bx,ax;es:[bx]记录起始位置

    mov di,0
    mov si,0
    mov ah,cl
    mov cx,0
show_str_loop:
    mov cl,byte ptr ds:[di]
    jcxz show_str_ret
    mov al,cl
    mov word ptr es:[bx+si],ax
    inc di
    add si,2
    jmp show_str_loop
show_str_ret:
    pop cx
    pop dx
    ret
code ends
end main
```

![](https://cdn.jsdelivr.net/gh/followmerushb/followmerushb.github.io@master/static/subject/汇编语言/实验10_1.png)

2. 解决除法溢出问题

```nasm
assume cs:code
stack segment
    dw 16 dup (0)
stack ends
code segment
main:
    mov ax,stack
    mov ss,ax
    mov sp,32
    mov ax,4240h
    mov dx,0fh
    mov cx,0ah
    push ax
    push dx
    push cx
    call divdw
    mov ax, 4c00h
    int 21h
divdw:
    push bp
    mov bp,sp
    mov ax,word ptr ss:[bp+6];高16位
    mov dx,0;dx清零
    div word ptr ss:[bp+4];dx:余数,ax:商
    push ax;结果的高16位
    mov ax,word ptr ss:[bp+8];低16位
    div word ptr ss:[bp+4];dx:余数,ax:商(低16位)
    mov cx,dx;cx存放余数
    mov dx,word ptr ss:[bp-2];结果的高16位
    mov sp,bp
    pop bp
    ret 6
code ends
end main
```

3. 数值显示

```nasm
assume cs:code
data segment
    db 10 dup (0)
data ends
stack segment
    dw 16 dup (0)
stack ends

code segment
main:
    mov ax,12666
    mov bx,data
    mov ds,bx
    mov bx,stack
    mov ss,bx
    mov sp,32
    mov si,0
    call dtoc

    mov dh,8
    mov dl,3
    mov cl,2
    call show_str
    
    mov ax,4c00h
    int 21h

dtoc:
    push bp
    mov bp,sp
    mov dx,0
    push dx
    mov bx,10
dtoc_loop:
    div bx;ax:商,dx:余数
    add dx,030h;0->'0'
    push dx;ascii入栈
    mov cx,ax
    jcxz dtoc_re
    mov dx,0
    jmp dtoc_loop
dtoc_re:;用栈将ascii倒序
    pop cx
    jcxz dtoc_ret
    mov byte ptr ds:[si],cl;移动到内存区
    inc si
    jmp dtoc_re
dtoc_ret:
    mov sp,bp
    pop bp
    ret

show_str:
    push dx
    push cx;保存相关寄存器

    mov ax,0b800h
    mov es,ax;显存
    mov ax,00a0h
    sub dh,1
    mul dh
    mov bx,ax;es:[bx]
    mov ax,0
    sub dl,1
    mov al,dl
    add bx,ax
    add bx,ax;es:[bx]记录起始位置

    mov di,0
    mov si,0
    mov ah,cl
    mov cx,0
show_str_loop:
    mov cl,byte ptr ds:[di]
    jcxz show_str_ret
    mov al,cl
    mov word ptr es:[bx+si],ax
    inc di
    add si,2
    jmp show_str_loop
show_str_ret:
    pop cx
    pop dx
    ret
code ends
end main
```