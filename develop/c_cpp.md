?> C/C++ 笔记

## 输入输出处理

### 对保留数位的处理

调用`#include<iomanip>`

```cpp
cout<<setprecision(6)<<a;
对a保留6位有效数字
cout<<fixed<<setprecision(6)<<a;
对a保留6位小数
```

***Sample***

```cpp
#include<iostream>
#include<iomanip>
using namespace std;
int main(){
    double a;
    a=789.1234567;
    cout<<setprecision(7)<<a<<' '<<fixed<<setprecision(5)<<a<<' '<<fixed<<setprecision(8)<<a;
    return 0;
}
```

***Out***

```
789.1235 789.12346 789.12345670
```

### 判断读入情况

`scanf()`函数有返回值且为`int`型

`scanf()`函数返回的值为: 正确按指定格式输入变量的个数->即能正确接收到值的变量个数

`int x=scanf("%d%d%d",&a,&b,&c);`

若输入3个整数,则x为3

若输入的前两个为整数,最后一个为其他形式如 char,则x为2

当scanf函数的第一个变量格式不正确时返回值为0->当scanf函数的第n个变量格式不正确时,返回值为n-1

### setw 位宽处理

调用`#include<iomanip>std::setw`

设定栏位宽度->设置要在输出操作上使用的字段宽度

***Sample***

```cpp
#include<iostream>
#include<iomanip>
using namespace std;
int main(){
    cout<<setw(10)<<77;
    return 0;
}
```

***Out***

```
        77
```

`setw()`默认填充的内容为空格
可以使用`setfill()`配合使用设置其他字符填充

***Sample***

```cpp
cout<<setfill('*')<<setw(5)<<'a'<<endl;
```

***Out***

```
****a     4个*和字符a共占5个位置
```

使用`setw(n)`设置输出宽度时,默认为右对齐

***Sample***

```cpp
std::cout<<std::setw(5)<<"1"<<std::endl;
std::cout<<std::setw(5)<<"10"<<std::endl;
std::cout<<std::setw(5)<<"100"<<std::endl;
std::cout<<std::setw(5)<<"1000"<<std::endl;
```

***Out***

```
____1
___10
__100
_100
```

若想让它左对齐的话,只需要插入`std::left`

***Sample***

```cpp
std::cout<<std::left<<std::setw(5)<<"1"<<std::endl;
std::cout<<std::left<<std::setw(5)<<"10"<<std::endl;
std::cout<<std::left<<std::setw(5)<<"100"<<std::endl;
std::cout<<std::left<<std::setw(5)<<"1000"<<std::endl;
```

***Out***

```
1
10
100
1000
```

同理,右对齐只要插入`std::right,`不过右对齐是默认状态,不必显式声明

### 面向行的输入 getline()

getline()函数读取整行,它使用通过回车键输入的换行符来确定输入结尾

要调用这种方法,可以使用cin.getline()

该函数有两个参数第一个参数是用来存储输入行的数组的名称

第二个参数是要读取的字符数如果这个参数为20,则函数最多读取19个字符,余下的空间用于存储自动在结尾处添加的空字符

getline( )函数每次读取一行它通过换行符来确定行尾,但不保存换行符

`cin.getline(name,20);` (将姓名读入到一个包含20个元素的name数组中)

在string中保存含空格的字符串

`getline(cin,str);`

### 面向行的输入 get()

工作方式与getline()类似,但get并不再读取并丢弃换行符,而是将其留在输入队列中

假设连续两次调用get()

```cpp
cin.get(name1,size1);
cin.get(name2,size2);
```

由于第一次调用后,换行符将留在输入队列中,因此第二次调用时看到的第一个字符便是换行符因此get()认为已到达行尾,而没有发现任何可读取的内容

使用不带任何参数的cin.get()调用可读取下一个字符（即使是换行符）,因此可以用它来处理换行符,为读取下一行输入做好准备

```cpp
cin.get(name1,size1);
cin.get();
cin.get(name2,size2);
```

在char字符中保存空格

`cin.get(char);`

## 数学相关

### 圆周率表示

利用`<cmath>`头文件

```cpp
const double pi=acos(-1.0);
```

### 弧度与角度

`c/c++`中进行`sin/cos`运算时是使用弧度制

弧度转角度

`a=a*pi/180;`

### 关于素数

除2 3外的任何质数,除以6的余数一定是1或5

若余数是0或2或4,则该数可被2整除,不是质数,若余数是3,则该数可被3整除,不是质数

首先6的倍数(包括六)肯定不是质数,因为它能被6整除

其次6x+2肯定也不是质数,因为它还能被2整除

依次类推

6x+3肯定能被3整除

6x+4肯定能被2整除

那么,就只有6x+1和6x+5(等同于6x-1)可能是质数了

所以循环的步长可以设为6,然后每次只判断6两侧的数(5和7)即可

```cpp
bool is_prime(int a){
    if(a==1)return 0;
    if(a==2||a==3)return 1;
    if(a%6!=1&&a%6!=5)return 0;//此处必须为&&,若为||,则a=7时,a%6==1,a%6!=5,return 0,但显然7为素数
    for(int i=5;i<=sqrt(a);i+=6)
        if(a%i==0||a%(i+2)==0)return 0;//5和1可以转化为5和6+1即5和7
    return 1;
}
```

### 素数筛

#### Eratosthenes筛法

```cpp
#include<iostream>

using namespace std;
int a[1000001];

void Eratosthenes(long long n) {
    fill(a, a + 1000001, 1);/*将数组填充为1,假设当前范围内的数均为素数,然后开始筛选*/
    for (long long i = 2; i <= n; ++i) {
        /*先用2去筛,即把2留下,把2的倍数剔除掉；再用下一个素数,也就是3筛,把3留下,把3的倍数剔除掉；接下去用下一个素数5筛,把5留下,把5的倍数剔除掉；不断重复下去......*/
        for (long long j = i * i; j <= n; j += i) {/*此处应从i的平方开始计算,以减少计算量*/
            a[j] = 0;
        }
    }
}

int main() {
    long long n;
    cin >> n;
    Eratosthenes(n);
    for (long long i = 2; i <= n; ++i) {
        if (a[i]) {
            cout << i << ' ';
        }
    }
    return 0;
}
```

#### Euler筛法

```cpp
#include<iostream>

using namespace std;
long long a[1000001];//a用于记录已经判断出是素数的数
bool b[1000001];//b用于判断当前数字是否为素数
void Euler(long long n) {
    fill(b, b + 1000001, 1);/*将数组填充为1,假设当前范围内的数均为素数,然后开始筛选*/
    b[0] = false;
    b[1] = false;/*0,1均不是素数*/
    int cnt = 0;/*确定素数在数组a中的放置位置*/
    for (long long i = 2; i <= n; ++i) {
        if (b[i]) {
            a[cnt++] = i;/*将当前素数储存并记录*/
        }
        for (int j = 0; j < cnt && i * a[j] <= n; ++j) {
            b[i * a[j]] = false;/*已储存素数的倍数均为合数*/
            if (!(i % a[j])) {/*i%a[j]==0表明i是一个素数的倍数,说明i在之前已经被筛过了,无需再筛*/
                break;
            }
        }
    }
}

int main() {
    long long n;
    cin >> n;
    Euler(n);
    for (long long i = 0; i <= n; ++i) {
        if (a[i]) {
            cout << a[i] << ' ';
        }
    }
    return 0;
}
```

### 欧拉函数

[欧拉函数 OI-WIKI](https://oi-wiki.org/math/euler/)

#### 求单个数的欧拉函数

```cpp
#include<iostream>
#include<cmath>

using namespace std;

int euler_phi(int n) {
    int m = int(sqrt(n + 0.5));
    int ans = n;
    for (int i = 2; i <= m; ++i) {
        /*φ(x)=x(1-1/p(1))(1-1/p(2))(1-1/p(3))…..(1-1/p(n))*/
        if (n % i == 0) {/*判断i是否为n的质因数*/
            ans = ans / i * (i - 1);/*先进行除法运算避免溢出*/
            /*ans/i*(i-1)=ans*(1-1/i)*/
            while (n % i == 0) {
                n /= i;/*保证n在遇到下一个质因数之前不会再进行计算*/
                /*假定n=20,n可由质因数累积而成,n=2*2*5
                  显然4为n的因数,但不是质因数,在该while循环中
                  4被除去,因此可以保证下一次运算必定为质因数*/
            }
        }
    }
    if (n > 1) {
        ans = ans / n * (n - 1);/*最后剩下的一个质因数*/
        /*例如n=5,与n互质的数为1,2,3,4
          在上一步的for循环中没有符合条件的质因数
          说明n是质数,所以φ(n)=n-1
          又例如n=6,在上一步的for循环中只有2被筛出
          n=3,此时n为最后一个质因数
          φ(x)=x(1-1/p(1))(1-1/p(2))(1-1/p(3))…..(1-1/p(n))
          φ(x)=ans(1-1/p(n))
          综合两种情况将其写成ans=ans/n*(n-1)
          */
    }
    return ans;
}

int main() {
    int n;
    cin >> n;
    cout << euler_phi(n);
    return 0;
}
```

#### 筛法求欧拉函数

```cpp
#include<iostream>

using namespace std;
int phi[1000001];

void phi_table(int n) {
    phi[1] = 1;
    /*φ(1)=1*/
    for (int i = 2; i <= n; ++i) {
        if (!phi[i]) {/*保证i是质因数*/
            for (int j = i; j <= n; j += i) {
                if (!phi[j]) {
                    phi[j] = j;/*phi数组的元素与欧拉函数值一一对应*/
                }
                phi[j] = phi[j] / i * (i - 1);/*i作为当前for循环中j的因数*/
                /*例如求6的欧拉函数时,以i=2作为j的循环,以i=3作为j的循环
                  在每一个循环中,当j=6时会以i作为质因数求解欧拉函数*/
                /*ans/i*(i-1)=ans*(1-1/i)*/
                /*φ(x)=x(1-1/p(1))(1-1/p(2))(1-1/p(3))…..(1-1/p(n))*/
            }
        }
    }
}

int main() {
    int n;
    cin >> n;
    phi_table(n);
    for (int i = 1; i <= n; ++i) {
        cout << phi[i] << ' ';
    }
    return 0;
}
```

### 贝祖等式

[wikipedia-扩展欧几里得算法](https://zh.wikipedia.org/wiki/%E6%89%A9%E5%B1%95%E6%AC%A7%E5%87%A0%E9%87%8C%E5%BE%97%E7%AE%97%E6%B3%95)


|序号 ![](https://latex.codecogs.com/gif.latex?i)|	商 ![](https://latex.codecogs.com/gif.latex?q_{i-1})|	余数 ![](https://latex.codecogs.com/gif.latex?q_i)	|![](https://latex.codecogs.com/gif.latex?s_i)|	![](https://latex.codecogs.com/gif.latex?t_i)|
|---|----|----|---|---|
|0|	|	240|	1|	0|
|1|	|	46|	0|	1|
|2	|240 ÷ 46 = 5|	240 − 5 × 46 = 10	|1 − 5 × 0 = 1|	0 − 5 × 1 = −5|
|3|	46 ÷ 10 = 4|	46 − 4 × 10 = 6|	0 − 4 × 1 = −4	|1 − 4 × −5 = 21|
|4	|10 ÷ 6 = 1|	10 − 1 × 6 = 4|	1 − 1 × −4 = 5	|−5 − 1 × 21 = −26|
|5|	6 ÷ 4 = 1	|6 − 1 × 4 = 2	|−4 − 1 × 5 = −9	|21 − 1 × −26 = 47|
|5(答案)|	6 ÷ 4 = 1	|6 − 1 × 4 = 2	|−4 − 1 × 5 = −9	|21 − 1 × −26 = 47|
|6	|4 ÷ 2 = 2|	4 − 2 × 2 = 0|	5 − 2 × −9 = 23	|−26 − 2 × 47 = −120|

当 ![](https://latex.codecogs.com/gif.latex?i>1) 时

![](https://latex.codecogs.com/gif.latex?s_i=s_{i-2}-s_{i-1}*r_i)

![](https://latex.codecogs.com/gif.latex?t_i=t_{i-2}-t_{i-1}*r_i)

```cpp
#include<iostream>

using namespace std;
int s[3] = {1, 0};
int t[3] = {0, 1};

int gcd(int a, int b) {
    if (a % b == 0) {
        return b;
    }
    else {
        s[2] = s[0] - (a / b) * s[1];
        t[2] = t[0] - (a / b) * t[1];
        s[0] = s[1], s[1] = s[2], t[0] = t[1], t[1] = t[2];
        return gcd(b, a % b);
    }
}

int main() {
    int a, b;
    cin >> a >> b;
    cout << "gcd: " << gcd(a, b) << " s:" << s[1] << " t:" << t[1];
    return 0;
}
```

### 快速幂

#### 递归法(fun1)

`a^b` 假设b为奇数,则 `a^b` 可转化为 `a^(b/2)*a^(b/2)*a`

假设b为偶数,则 `a^b` 可转化为 `a^(b/2)*a^(b/2)`

以此类推,可以得到递归

#### 非递归法(fun2)

假设 `ans = 1` (保证在零次幂时可用)

`a^b` 假设 b 转化为二进制是 101 那么 `a^b` 可以转化为 `a^4 * a^1`

当 b 的二进制的末位是 1 时,将当前二进制位为 1 所对应的幂乘到 `ans` 中

无论此时b 的二进制的末位是否为 1 ,都将 a 平方以对应 b 的下一个二进制位,并将 b 的二进制右移 1 位以更新 b 的二进制末位

```cpp
#include<iostream>

using namespace std;

long long fun1(long long a, long long b) {/*递归法*/
    if (b == 0) {
        return 1;
    }
    long long base = fun1(a, b / 2);
    if (b % 2) {
        return base * base * a;
    }
    else {
        return base * base;
    }
}

long long fun2(long long a, long long b) {/*非递归法*/
    long long ans = 1;
    while (b > 0) {
        if (b & 1) {
            ans *= a;
        }
        a *= a;
        b >>= 1;
    }
    return ans;
}

int main() {
    long long a, b;
    cin >> a >> b;
    cout << fun1(a, b) << " " << fun2(a, b);
    return 0;
}
```

#### 快速幂取余

`(a*b)%c=(a%c*b%c)%c`

`(a^b)%c=((a%c)^b)%c`

注意在最后还有一个 `fun(a, b, c) % c` 

```cpp
#include<iostream>

using namespace std;

long long fun(long long a, long long b, long long c) {
    if (b == 0) {
        return 1;
    }
    long long base = fun(a, b / 2, c);
    if (b % 2) {
        return base * base * a % c;
    }
    else {
        return base * base % c;
    }
}

int main() {
    long long a, b, c;
    cin >> a >> b >> c;
    cout << a << "^" << b << " mod " << c << "=" << fun(a, b, c) % c;
    return 0;
}
```

### 高精度运算

```cpp
/*高精度非负整数四则运算*/
#include<iostream>
#include<cstring>

#define MAX_LEN 20000
using namespace std;

void read(int *num) {//读入,将字符转换为数字并调整位置
    char temp[MAX_LEN + 1];
    memset(temp, '0', sizeof(temp));
    cin >> temp;
    int len = strlen(temp);
    for (int i = 0; i < len; ++i) {
        num[MAX_LEN - i - 1] = temp[len - i - 1] - '0';//位置调整
    }
}

void print(int *num) {//找到第一个非零的数,特殊情况为结果为0,所以最后一位直接输出,不进行判断
    int i;
    for (i = 0; i < MAX_LEN - 1; ++i) {
        if (num[i]) {
            break;
        }
    }
    for (; i < MAX_LEN; ++i) {
        cout << num[i];
    }
}

void add(const int *a, const int *b, int *c) {//竖式加法模拟,满十进一
    int sum = 0;
    for (int i = MAX_LEN - 1; i >= 0; --i) {
        sum += a[i] + b[i];
        c[i] = sum % 10;
        sum /= 10;
    }
}

inline bool is_able(const int *a, const int *b) {//判断能否进行相减
    for (int i = 0; i < MAX_LEN; ++i) {
        if ((a[i] || b[i]) && a[i] != b[i]) {//其中一个不为零且两者不相等
            return a[i] - b[i] > 0;
        }
    }
    return true;//两者完全相等
}

void sub(int *a, int *b, int *c) {//竖式减法模拟
    if (!is_able(a, b)) {//无法直接相减时对换a,b
        swap(a, b);
        cout << '-';
    }
    int sum = 0;
    for (int i = MAX_LEN - 1; i >= 0; --i) {
        sum += a[i] - b[i];
        if (sum < 0) {//借位
            c[i] += sum + 10;
            a[i - 1]--;
        }
        else {
            c[i] = sum;
        }
        sum = 0;
    }
}

void mul(const int *a, const int *b, int *c) {//竖式乘法模拟
    for (int i = MAX_LEN - 1; i >= 0; --i) {
        for (int j = MAX_LEN - 1; j >= i; --j) {
            c[i] += a[j] * b[i - j + MAX_LEN - 1];
        }
        if (c[i] >= 10) {
            c[i - 1] += c[i] / 10;
            c[i] %= 10;
        }
    }
}

void div(int *a, int *b, int *c, int *d) {//竖式除法模拟并生成余数
    int la, lb;
    for (la = 0; la < MAX_LEN; ++la) {//确定位数
        if (a[la] != 0) {
            break;
        }
    }
    for (lb = 0; lb < MAX_LEN; ++lb) {//确定位数
        if (b[lb] != 0) {
            break;
        }
    }
    if (lb == MAX_LEN) {//被除数不能为0
        cout << "ERROR";
        exit(0);
    }
    la = MAX_LEN - la;//确定位数
    lb = MAX_LEN - lb;//确定位数
    if (is_able(a, b)) {//除法本质是多次减法
        for (int i = MAX_LEN - lb; i < MAX_LEN; ++i) {//b向前移位并补零
            b[i - la + lb] = b[i];
        }
        for (int i = MAX_LEN - la + lb; i < MAX_LEN; ++i) {
            b[i] = 0;
        }
    }
    for (int i = la - lb; i >= 0; --i) {//最多需处理的位数
        while (is_able(a, b)) {
            int sum = 0;
            for (int j = MAX_LEN - 1; j >= 0; --j) {
                sum += a[j] - b[j];
                if (sum < 0) {
                    d[j] += sum + 10;
                    a[j - 1]--;
                }
                else {
                    d[j] = sum;
                }
                sum = 0;
            }
            for (int j = 0; j < MAX_LEN; ++j) {
                a[j] = d[j];//余数成为除数
            }
            fill(d, d + MAX_LEN, 0);
            c[MAX_LEN - i - 1]++;//商的对应位置+1
        }
        for (int j = MAX_LEN - 1; j >= MAX_LEN - la; --j) {
            b[j] = b[j - 1];
        }
    }
}

int main() {
    int a[MAX_LEN] = {0};
    int b[MAX_LEN] = {0};
    int c[MAX_LEN] = {0};
    int d[MAX_LEN] = {0};
    cout << "Sample: " << endl << "a" << endl << "<op>" << endl << "b" << endl;
    read(a);
    char temp;
    cin >> temp;
    read(b);
    switch (temp) {
        case '+':
            add(a, b, c);
            print(c);
            break;
        case '-':
            sub(a, b, c);
            print(c);
            break;
        case '*':
            mul(a, b, c);
            print(c);
            break;
        case '/':
            div(a, b, c, d);
            cout << "商: ";
            print(c);
            cout << endl;
            cout << "余: ";
            print(a);
            break;
        default:
            cout << ">-<";
            break;
    }
    return 0;
}
```

## 数值表示

### printf函数转换说明

|转换说明|类型|
|:---:|:---:|
|%d|int|
|%hd|short|
|%ld|long|
|%lld|long long|
|%o|八进制|
|%#o|带前缀八进制|
|%x|16进制(小写)|
|%#x|带前缀16进制(小写)|
|%X|16进制(大写)|
|%#X|带前缀16进制(大写)|
|%c|单个字符|
|%s|字符串|
|%p|指针地址(带前缀16进制)|
|%f|单精度浮点|
|%lf|双精度浮点|
|%.2f|保留两位小数|
|%e|科学计数法|
|%.2e|保留两位小数|
|%u|unsigned int|
|%%|打印一个百分号|

```cpp
#include <stdio.h>

int main() {
    int a = 255;
    printf("%d %o %#o %x %#x %X %#X %p\n", a, a, a, a, a, a, a, &a);
    short b = 200;
    printf("%hd %d\n", b, b);
    long c = 12345678987654;
    printf("%ld\n", c);
    long long d = 123456789876543212;
    printf("%lld\n", d);
    char e = 'e';
    printf("%c\n", e);
    const char *f = "asdf";
    printf("%s\n", f);
    float g = 0.1234567890123456;
    double h = 0.12345678901234567890123456789;
    printf("%f %lf %.10f %.20lf\n", g, h, g, h);
    double i = 123456789.987654321;
    double j = 0.9876543210123456789;
    printf("%e %e %.2e %.12e\n", i, j, i, j);
    unsigned int k = 4294967295;
    printf("%d %u %%", k, k);
    return 0;
}
```

```
255 377 0377 ff 0xff FF 0XFF 0x7ffe1223337c
200 200
12345678987654
123456789876543212
e
asdf
0.123457 0.123457 0.1234567910 0.12345678901234567737
1.234568e+08 9.876543e-01 1.23e+08 9.876543210123e-01
-1 4294967295 %
```

## 指针

`int array[10];`

`&array` 和 `&array[0] (即array)` 不同

数组名被解释为其第一个元素的地址,而对数组名应用地址运算符时,得到的是整个数组的地址

```cpp
#include<iostream>
#include<cstring>
using namespace std;
int main(){
    int array[10];
    cout<<array<<endl;//输出array[0]的地址
    cout<<&array<<endl;
    cout<<array+1<<endl;//输出array[1]的地址
    cout<<&array+1<<endl;
    return 0;
}

/*
0x7fffffffdfa0
0x7fffffffdfa0
0x7fffffffdfa4
0x7fffffffdfc8
*/
```

从数字上说,`&array` 和 `&array[0] (即array)` 相同

但从概念上说, `&array[0](即array)` 是一个4字节内存块的地址,而 `&array` 是一个40字节内存块的地址

因此,表达式 `array+1` 将地址值加4,而表达式 `&array+1` 将地址加40

换句话说,`array`是一个int指针`(* int)`,而`&array`是一个这样的指针,即指向包含10个元素的array数组`(int (*) [10])`

### 指针数组

```cpp
const char *ch[3]={"asfd","qwer","zxcv"};
int a=0,b=1,c=2,d=3;
int *p[4];
p[0]=&a;
p[1]=&b;
p[2]=&c;
p[3]=&d;
```

### 数组指针

```cpp
int a[2][2]={1,2,3,4};
int (*c)[2]=a;
```

### 指针函数

```cpp
char *fun(void){
    char temp[80];
    cin.get(temp,80);
    char *s=new char[strlen(temp)+1];
    strcpy(s,temp);
    return s;
}
int main(){
    char *str=fun();
    cout<<str;
    delete []str;
    return 0;
}

int *fun(void){
    int *a=new int[3];
    a[0]=123;
    a[1]=654;
    a[2]=987;
    return a;
}
int main(){
    int *arr=fun();
    cout<<arr[0]<<' '<<arr[1]<<' '<<arr[2]<<endl;
    cout<<arr<<' '<<arr+1<<' '<<arr+2;
    delete []arr;
    return 0;
}
```

### 函数指针

```cpp
/*typeName (*fun_ptr)(type1,type2...)  声明一个指向同样参数、返回值的函数指针类型*/

int get_max(int a,int b){
    return a>b?a:b;
}
int fun(int x,int y,int z,int(*f)(int a,int b)){
    return f(f(x,y),z);
}
```

### 指针的递增递减

```cpp
int arr[5]={10,20,30,40,50};
int *pt=arr;
cout<<*pt<<' '<<++*pt<<' '<<*(++pt)<<' '<<*pt++<<' '<<*pt<<endl;
/*后缀运算符++的优先级更高,这意味着将运算符用于pt,而不是*pt,因此对指针递增
然而后缀运算符意味着将对原来的地址(&arr[1])而不是递增后的新地址解除引用
因此*pt++的值为arr[1],即20,但该语句执行完毕后,pt的值将为arr[2]的地址*/
for(int i=0;i<5;++i){
    cout<<arr[i]<<' ';
}
cout<<endl;
pt=&arr[4];
cout<<*pt<<' '<<--*pt<<' '<<*(--pt)<<' '<<*pt--<<' '<<*pt<<endl;
for(int i=0;i<5;++i){
    cout<<arr[i]<<' ';
}
return 0;

/*
10 11 20 20 30
11 20 30 40 50 
50 49 40 40 30
11 20 30 40 49
*/
```

### 创建二维数组

1.使用一维数组模拟二维数组

```cpp
int x=2,y=3;
int arr[6]={1,2,3,4,5,6};
for(int i=0;i<x;++i){
    for(int j=0;j<y;++j){
				cout<<arr[i*y+j]<<' ';
		}
}
```

2.静态二维数组

```cpp
int arr[2][3]={1,2,3,4,5,6};
```

3.动态二维数组

```cpp
int **a=new int*[2];
for(int i=0;i<2;++i){
		a[i]=new int[3];
}
for(int i=0;i<2;++i){
    for(int j=0;j<3;++j){
        a[i][j]=i+j;
    }
}
for(int i=0;i<2;++i){
    for(int j=0;j<3;++j){
        cout<<a[i][j]<<' ';
    }
}
for(int i=0;i<2;++i){
    delete []a[i];
}
delete []a;
```

### const

```cpp
int a = 3, b = 5;
const int* pt1 = &a;
int* const pt2 = &a;
const int* const pt3 = &a;
//(*pt1)++; 无法执行
cout << *pt1 << endl;
pt1 = &b;
cout << *pt1 << endl;
cout << a << endl;
(*pt2)++;
cout << a << endl;
//pt2 = &b; 无法执行
//(*pt3)++; 无法执行
//pt3 = &b; 无法执行

/*
3
5
3
4
const int* pt1 = &a
const修饰*pt1,说明不能修改*pt1的值,即不能通过*pt1修改a的值,但是可以修改pt1,即pt1 = &b,修改指针的指向
int* const pt2 = &a;
const修饰pt2,说明不能修改pt2的值,即不能修改pt2的指向,但是可以修改*pt2,即(*pt2)++可以成立
const int* const pt3 = &a;
const同时修饰了pt3和*pt3,完全不能修改
const int 与 int const等价
*/
```

## STL

### string

#### erase

[std::string::erase](http://www.cplusplus.com/reference/string/string/erase/)

***Sample***

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/develop/c_cpp/String/1.png)

1. `str.erase(num)`

将`str[num]`后的所有字符去掉,包括`str[num]`,即只保留前`num`个字符

2. `str.erase(num1,num2)`

将`str[num1-1]`即第`num1`个元素到`str[num1-1+num2]`之间的字符去掉,不包括`str[num1-1]`和`str[num1-1+num2]`

3. `str.erase(str.begin()+num)`

将`str[num]`这个元素去掉

4. `str.erase(str.begin()+num1,str.end()-num2)`

从`str[num1]`这个元素起到`str[str.size()-1-num2]`为止全部去掉,包括`str[num1]`

#### insert

[std::string::erase](http://www.cplusplus.com/reference/string/string/insert/)

***Sample***

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/develop/c_cpp/String/2.png)

1. `str.insert(num,str2)`

在第`num`个元素后面插入`str2`

2. `str.insert(num1,str2,num3,num4)`

在`str`的第`num1`个元素后面插入`str2`的第`num3`个元素起往后的`num4`个元素

3. `str.insert(num1,str2,num2)`

在`str`的第`num1`个元素后面插入`str2`的前`num2`个元素

4. `str.insert(num,str2)`

在`str`的第`num`个元素后面插入`str2`