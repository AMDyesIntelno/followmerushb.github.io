### 朴素算法

```cpp
#include<iostream>
#include<string>
using namespace std;
void simple(string a,string b){
    for(int i=0;i<a.size();++i){
        for(int j=0;j<=b.size();++j){
            if(j==b.size()){
                cout<<"posi: "<<i<<endl;
                break;
            }
            if(a[i+j]==b[j]){
                continue;
            }
            else{
                break;
            }
        }
    }
    return;
}
int main(){
    string a,b;
    cin>>a>>b;
    simple(a,b);
    return 0;
}
```

时间复杂度极高,为![](http://latex.codecogs.com/gif.latex?O(n*m))

### KMP 

~~看猫片~~

假定`a`为文本串,`b`为模板串.

设
```
a = abaabababb
b = ababa
```

|下标|0|1|2|3|4|5|6|7|8|9|
|---|---|---|---|---|---|---|---|---|---|---|
|文本串|**a**|**b**|**a**|a|b|a|b|a|b|b|
|模板串|**a**|**b**|**a**|b|a|

显然,此时模板串与文本串的前3个元素相同,但第四个元素不同

如果使用朴素算法,则b要后移一位,但是从表格中可以看出,后移一位是必然不能完成匹配的

kmp算法的精妙之处在于,在完成一次尝试后,根据已知条件来向后移动尽可能远的距离

在这个例子中,第一次尝试发现在第一个不匹配项之前,一共有3项成功匹配

在这三项中,a一共出现了两次,在匹配成功的模板子串`aba`中,第三个的`a`可以视为前两个元素`ab`所构成的串的子串,`a`与`ab`匹配

将模板子串`aba`拓展为模板串`ababa`,可以得到一系列子串(包含模板串自身)

在这些子串中,存在**前n个字符**与**后n个字符**相同的情况,称其为部分匹配.记录使得**前n个字符**恰等于**后n个字符**的**最大**的n

P.S. 由于自己与自己必然匹配,所以不考虑自匹配的n

|a|b|a|||
|---|---|---|---|---|
|||a|b|a||

`n=1`

|a|b|a|b|||
|---|---|---|---|---|---|
|||a|b|a|b|

`n=2`

|a|b|a|b|a|||
|---|---|---|---|---|---|---|
|||a|b|a|b|a|

`n=3`

|子串|n|匹配出的子串|
|---|---|---|
|a|0|无|
|ab|0|无|
|aba|1|a|
|abab|2|ab|
|ababa|3|aba|

将这些n写入数组中,可以得到一个**部分匹配**数组,设该数组为f

|下标|0|1|2|3|4|
|---|---|---|---|---|---|
|模板串|a|b|a|b|a|
|部分匹配f|0|0|1|2|3|

既然前三个字符成功匹配,说明模板串的前三个字符与文本串开始匹配处的前三个字符相同,同时,成功匹配的子串`aba`,存在部分匹配

那么,模板串的第一个字符和文本串开始匹配处的第三个字符是一样的

说明在开始匹配时,可以将模板串的第一个字符和文本串开始匹配处的第三个字符处对应,即将模板串向后移动两个字符的位置

从已知信息来看,是有可能完成匹配的

现在要实现kmp算法剩下的问题有两个

1. 如何得到部分匹配数组
2. 如何利用部分匹配数组加速串的模式匹配

#### 快速求出部分匹配数组

部分匹配数组,可以看作是自身对自身的多次匹配

|a|b|a|b|a|
|---|---|---|---|---|
|a|b|a|b|a|

初始值为0,f[0]=0,假定f[1]=0

f数组除了记录当前的最大匹配n之外,也是作为字符串的索引值

同时f数组也记录了在当前模板串的**相对位置**所进行匹配的已成功的长度

注意,是**相对位置**,模板串的自身匹配在本质上是比较指针在不断的移动

要完成匹配,则说明当前检测的字符要与**当前索引值所指向的字符**相同

例如,此时要进行检测模板串(string)中的第一个元素与第**零**个元素是否匹配

|a|b|a|b|a||
|---|---|---|---|---|---|
||a|b|a|b|a|
||↑|||||

string[1]=b,f[1]=0,f作为索引,说明要完成匹配,即string[1]要与string[f[1]]相同

string[f[1]]即为string[0]即为a,无法匹配,那么将f[2]设置为0

说明此时检测的任务转变为了检测模板串中的第二个元素与第零个元素是否匹配

将模板串向后移动

|a|b|a|b|a|||
|---|---|---|---|---|---|---|
|||a|b|a|b|a|
|||↑|||||

string[2]=a,f[2]=0,f作为索引,string[f[2]]=a

说明此时模板串中的第二个元素与第零个元素成功匹配

当前相对位置已成功匹配的长度为1

将f[3]设置为1

如果要让这个成功匹配延续下去

说明此时检测的任务转变为了检测模板串中的第三个元素与第一个元素是否匹配

**模板串不进行移动,比较指针向后移动**

|a|b|a|b|a|||
|---|---|---|---|---|---|---|
|||a|b|a|b|a|
||||↑||||

string[3]=b,f[3]=1,f作为索引,string[f[3]]=b

说明此时模板串中的第三个元素与第一个元素成功匹配

当前相对位置已成功匹配的长度为2

将f[4]设置为2

如果要让这个成功匹配延续下去

说明此时检测的任务转变为了检测模板串中的第四个元素与第二个元素是否匹配

模板串不进行移动,比较指针向后移动

|a|b|a|b|a|||
|---|---|---|---|---|---|---|
|||a|b|a|b|a|
|||||↑|||

string[4]=a,f[4]=2,f作为索引,string[f[4]]=a

说明此时模板串中的第四个元素与第二个元素成功匹配

当前相对位置已成功匹配的长度为3

将f[5]设置为3

如果要让这个成功匹配延续下去

说明此时检测的任务转变为了模板串中的第五个元素与第三个元素是否匹配

但是此时比较指针已经到达字符串的末尾,所以停止匹配

可以得到

|下标|0|1|2|3|4|
|---|---|---|---|---|---|
|模板串|a|b|a|b|a|
|部分匹配f|0|0|1|2|3|

(f中的下标相比模板串的下标要加1)


再举例一个模板串`aabaaab`

|子串|n|匹配出的子串|
|---|---|---|
|a|0|无|
|aa|1|a|
|aab|0|无|
|aaba|1|a|
|aabaa|2|aa|
|aabaaa|2|aa|
|aabaaab|3|aab|

初始值为0,f[0]=0,假定f[1]=0

|a|a|b|a|a|a|b||
|---|---|---|---|---|---|---|---|
||a|a|b|a|a|a|b|
||↑|||||||||||||

string[1]=string[f[1]]=a

f[2]=1

|a|a|b|a|a|a|b||
|---|---|---|---|---|---|---|---|
||a|a|b|a|a|a|b|
|||↑||||||

string[2]=b string[f[2]]=a

>这个字符匹配不成功,但是上一个字符匹配成功
>
>那么可以**尝试检索对应的上一个索引,直到可以匹配或f的索引值归0**
>
>如果可以匹配,那么新的f等于匹配处的f的值+1,说明这个字符可以和前面的**同次**成功匹配的字符构成一个新的匹配
>
>如果索引值最终归0,说明检索失败,那么新的f等于0,下一个匹配处从第零个字符开始匹配

|a|a|b|a|a|a|b|||
|---|---|---|---|---|---|---|---|---|
|||a|a|b|a|a|a|b|
|||↑|||||||

string[2]=b string[f[2]]=string[1]=a

string[f[f[2]]]=string[f[1]]=string[0]=a

f[3]=0

索引值最终归0,说明检索失败,那么新的f等于0,下一个匹配处从第零个字符开始匹配

|a|a|b|a|a|a|b||||
|---|---|---|---|---|---|---|---|---|---|
||||a|a|b|a|a|a|b|
||||↑|||||||||||||

string[3]=string[f[3]]=a

f[4]=1

|a|a|b|a|a|a|b||||
|---|---|---|---|---|---|---|---|---|---|
||||a|a|b|a|a|a|b|
|||||↑||||||

string[4]=string[f[4]]=a

f[5]=2

|a|a|b|a|a|a|b||||
|---|---|---|---|---|---|---|---|---|---|
||||a|a|b|a|a|a|b|
||||||↑|||||

string[5]=a string[f[5]]=string[2]=b

|a|a|b|a|a|a|b|||||
|---|---|---|---|---|---|---|---|---|---|---|
|||||a|a|b|a|a|a|b|
||||||↑||||||

string[f[f[5]]]=string[f[2]]=string[1]=a

匹配成功,新的f等于匹配处的f的值+1

f[6]=f[2]+1=2

说明这个字符可以和前面的**同次**成功匹配的字符构成一个新的匹配

>|子串|n|匹配出的子串|
>|---|---|---|
>|aabaa|2|aa|
>|aabaaa|2|aa|
>
>与前面的部分匹配表相对应

|a|a|b|a|a|a|b|||||
|---|---|---|---|---|---|---|---|---|---|---|
|||||a|a|b|a|a|a|b|
|||||||↑|||||

string[6]=b string[f[6]]=string[2]=b

f[7]=3

比较指针到达模板串末尾,匹配结束

可以得到

|下标|0|1|2|3|4|5|6|
|---|---|---|---|---|---|---|---|
|模板串|a|a|b|a|a|a|b|
|部分匹配f|0|1|0|1|2|2|3|

(f中的下标相比模板串的下标要加1)

由此,可以得到代码

```cpp
void getfail(string s,int *f){
    f[0]=0,f[1]=0;
    for(int i=1;i<s.size();++i){
        int j=f[i];
        while(j&&s[i]!=s[j]){//尝试检索对应的上一个索引,直到可以匹配或f的索引值归0
            j=f[j];
        }
        f[i+1]=s[i]==s[j]?j+1:0;
    }
    return;
}
```

***Sample1***
```
ababa
```

***Out1***
```
0 0 1 2 3
```

***Sample2***
```
aabaaab
```

***Out2***
```
0 1 0 1 2 2 3
```

不能将while直接改写成if,if只能检索单个索引

```cpp
//错误
void getfail(string s,int *f){
    f[0]=0,f[1]=0;
    for(int i=1;i<s.size();++i){
        int j=f[i];
        if(s[i]!=s[j]){
            j=0;
        }
        f[i+1]=s[i]==s[j]?j+1:0;
    }
    return;
}
```

#### 利用部分匹配数组加速串的模式匹配

|下标|0|1|2|3|4|5|6|7|8|9|
|---|---|---|---|---|---|---|---|---|---|---|
|文本串|**a**|**b**|**a**|a|b|a|b|a|b|b|
|模板串|**a**|**b**|**a**|b|a|

---------------------

|下标|0|1|2|3|4|
|---|---|---|---|---|---|
|模板串|a|b|a|b|a|
|部分匹配f|0|0|1|2|3|

假定有两个指针,分别指向了文本串和模板串的首字符

当两个指针所指向的字符相同时,两个指针都向后走一位

当两个指针所指向的字符不相同时,可分为两种情况

1. 模板串的指针仍然指向首字符,这说明了模板串与文本串之间没有发生成功的匹配,所以文本串的指针向后走一位,模板串的指针不动

2. 模板串的指针不是指向首字符,这说明了模板串与文本串之间已经发生了成功的匹配,但在这个字符失配(失去匹配)

那么,尝试将模板串的指针回溯,直到与文本串当前字符成功匹配或指针回溯至首字符

若回溯失败(回溯至首字符),文本串指针后移一位,开始新的匹配尝试

回溯方法:根据部分匹配数组,将模板串的指针回溯到当前索引值所指向的位置(注意文本串的指针不发生变动)

例如

|下标|0|1|2|3|4|5|6|
|---|---|---|---|---|---|---|---|
|模板串|a|a|b|a|a|a|b|
|部分匹配f|0|1|0|1|2|2|3|

------------------

|...|a|a|b|a|a|b|a|a|a|b|...|
|---|---|---|---|---|---|---|---|---|---|---|---|
||a|a|b|a|a|a|b|
|||||||↑||||||

模板串在string[5]失配(失去匹配),对当前指针进行回溯

f[5]=2,string[f[5]]=string[2]=b

匹配成功,在回溯后的位置再次开始匹配尝试

文本串指针

|||||||↓||||||
|---|---|---|---|---|---|---|---|---|---|---|---|
|...|a|a|b|a|a|b|a|a|a|b|...|

----------------
|a|a|b|a|a|a|b|
|---|---|---|---|---|---|---|
|||↑||||||

模板串指针

将两者绘制在同一个表中,即

|...|a|a|b|a|a|b|a|a|a|b|...|
|---|---|---|---|---|---|---|---|---|---|---|---|
|||||a|a|b|a|a|a|b|
|||||||↑||||||

继续向前匹配,匹配完成

由此可得代码

```cpp
void kmp(string a,string b,int *f){
    getfail(b,f);
    int i=0,j=0;//i为文本串指针,j为模板串指针
    while(i<a.size()){
        if(a[i]==b[j]){//当两个指针所指向的字符相同时,两个指针都向后走一位
            ++i;
            ++j;
        }
        else if(j){//失配,回溯
            j=f[j];
        }
        else{//模板串的指针仍然指向首字符,这说明了模板串与文本串之间没有发生成功的匹配,所以文本串的指针向后走一位,模板串的指针不动
            ++i;
        }
        if(j==b.size()){//当一次匹配完成后,j指向了空字符,在下一个while中失配,回溯
            cout<<i-j<<endl;//输出开始匹配的下标
        }
    }
    return;
}
```

#### kmp完整代码
```cpp
#include<iostream>
#include<string>
using namespace std;
void getfail(string s,int *f){
    f[0]=0,f[1]=0;
    for(int i=1;i<s.size();++i){
        int j=f[i];
        while(j&&s[i]!=s[j]){
            j=f[j];
        }
        f[i+1]=s[i]==s[j]?j+1:0;
    }
    return;
}
void kmp(string a,string b,int *f){
    getfail(b,f);
    int i=0,j=0;
    while(i<a.size()){
        if(a[i]==b[j]){
            ++i;
            ++j;
        }
        else if(j){
            j=f[j];
        }
        else{
            ++i;
        }
        if(j==b.size()){
            cout<<i-j<<endl;
        }
    }
    return;
}
int main(){
    string a,b;
    cin>>a>>b;
    int f[b.size()+1];
    kmp(a,b,f);
    return 0;
}
```

### Z函数

>对一个字符串string,Z函数能够对字符串的每一个后缀子串(不包括自身)与自身的最长匹配长度,该长度保存在一个数组z里面
例如:

假定字符串为`ababab` z[0]=0

|a|b|a|b|a|b|
|---|---|---|---|---|---|
|b|a|b|a|b|

最长匹配长度为0 z[1]=0

|a|b|a|b|a|b|
|---|---|---|---|---|---|
|a|b|a|b|

最长匹配长度为4 z[2]=4

|a|b|a|b|a|b|
|---|---|---|---|---|---|
|b|a|b|

最长匹配长度为0 z[3]=0

|a|b|a|b|a|b|
|---|---|---|---|---|---|
|a|b|

最长匹配长度为2 z[4]=2

|a|b|a|b|a|b|
|---|---|---|---|---|---|
|b|

最长匹配长度为0 z[5]=0

#### 利用Z函数进行串的模式匹配

假定`a`为文本串,`b`为模板串.

设
```
a = abaabababb
b = aba
```

设`c=b+"$"+a`

用`$`这个没有出现在a,b中的字符作为分割

得`c=aba$abaabababb`

对字符串c进行Z函数处理,当最长匹配长度(z[i])与模板串的长度相同时,说明匹配成功

设z[0]=0

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|b|a|$|a|b|a|a|b|a|b|a|b|b|

最长匹配长度为0 z[1]=0

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|a|$|a|b|a|a|b|a|b|a|b|b|

最长匹配长度为1 z[2]=1

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|$|a|b|a|a|b|a|b|a|b|b|

最长匹配长度为0 z[3]=0

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|a|b|a|a|b|a|b|a|b|b|

最长匹配长度为3 z[4]=3 在这里成功匹配

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|b|a|a|b|a|b|a|b|b|

最长匹配长度为0 z[5]=0

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|a|a|b|a|b|a|b|b|

最长匹配长度为1 z[6]=1

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|a|b|a|b|a|b|b|

最长匹配长度为3 z[7]=3 在这里成功匹配

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|b|a|b|a|b|b|

最长匹配长度为0 z[8]=0

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|a|b|a|b|b|

最长匹配长度为3 z[9]=3 在这里成功匹配

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|b|a|b|b|

最长匹配长度为0 z[10]=0

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|a|b|b|

最长匹配长度为1 z[11]=1

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|b|b|

最长匹配长度为0 z[12]=0

|a|b|a|$|a|b|a|a|b|a|b|a|b|b|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|b|

最长匹配长度为0 z[13]=0

有三处z[i]=3,因此文本串有三个子串与模板串匹配

#### Z函数的朴素算法

```cpp
#include<iostream>
#include<string>
using namespace std;
void simple(string c,int *z){
    int j=0;
    for(int i=1;i<c.size();++i){
        while(c[i+j]==c[j]){
            ++z[i];
            ++j;
        }
        j=0;
    }
}
int main(){
    string a,b,c;
    cin>>a>>b;
    c=b+"$"+a;
    int z[c.size()]={0};
    simple(c,z);
    for(int i=b.size()+1;i<c.size();++i){
        if(z[i]==b.size()){
            cout<<i-b.size()-1<<' ';
        }
    }
    return 0;
}
```

#### 线性算法

Z函数需要使用`l`和`r`去标记一个区间,在`string[l,r]`范围内的字符(包括**端点**)称为**匹配段**,匹配段与string的前缀子串一致

例如 `ababcaab` (下标从零开始)

z数组={0,0,2,0,0,1,2,0}

[l,r]分别为 [2,3] [5,5] [6,7]

初始化`l=0`,`r=0`

对于给定的字符串a和b,将其用特殊字符链接后,对z数组进行求值

1. `i>r` 当前位置在已经处理过的字符之外或者是还没有匹配段,直接使用朴素算法来求解,得到新的匹配段

```cpp
if(i>r){
    l=r=i;
    while(r<s.size()&&s[r-l]==s[r]){
        ++r;
    }
    z[i]=r-l;
    --r;
}
```

2. `i<=r` i在区间[l,r]之内,令`k=i-l`,显然有

`string[0,k]=string[l,i]`

所以,string[k...]和string[i...]**至少**有`r-i+1`个字符匹配

所以z[k]的取值可以分为两种情况

1. `z[k]<r-i+1` 

显然,`s[k,k+z[k]-1]`是`s[i,R]`的一个前缀子串

那么说明从string[i]开始和string的前缀匹配的情况是与从string[k]开始的情况一样

可以直接令z[i]=z[k],同时`l`和`r`保持不变

2. `z[k]>=r-i+1`

如果直接将z[k]的值赋给z[i],z[i]的最终匹配位置可能与z[k]不同

因为对计算机来说,`r`后的元素都是未知的,需要经过匹配才能确定

对于这种情况,要重新进行计算,令`l=i`,用朴素算法得到新的`r`,并得到此时的z[i]

```cpp
else{
    int k=i-l;
    if(z[k]<r-i+1){
        z[i]=z[k];
    }
    else{
        l=i;
        while(r<s.size()&&s[r-l]==s[r]){
            ++r;
        }
        z[i]=r-l;
        --r;
    }
}
```

例如

|下标|0|1|2|3|4|5|6|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|a|a|b|c|a|a|b|x|a|a|a|z|
|字符串|a|a|b|c|a|a|b|x|a|a|a|z|
|z数组|0|

初始化i=1,l=0,r=0,z[0]=0

|下标|0|1|2|3|4|5|6|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|**a**|~~a~~|b|c|a|a|b|x|a|a|a|z|
|字符串|a|**a**|~~b~~|c|a|a|b|x|a|a|a|z|
|z数组|0|1|

当i=1时,进行比较

当前的i>r,用朴素算法求解z[1]

得到z[1]=1,l=r=1

|下标|0|`[1]`|2|3|4|5|6|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|~~a~~|a|b|c|a|a|b|x|a|a|a|z|
|字符串|a|a|~~b~~|c|a|a|b|x|a|a|a|z|
|z数组|0|1|0|

当i=2时,进行比较

当前的i>r,用朴素算法求解z[2]

得到z[2]=0,l=2,r=1(在算法中r有一个回退的过程)

|下标|0|`[1]`|2|3|4|5|6|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|~~a~~|a|b|c|a|a|b|x|a|a|a|z|
|字符串|a|a|b|~~c~~|a|a|b|x|a|a|a|z|
|z数组|0|1|0|0|

当i=3时,进行比较

当前的i>r,用朴素算法求解z[3]

得到z[3]=0,l=3,r=2

|下标|0|`[1]`|2|3|4|5|6|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|**a**|**a**|**b**|~~c~~|a|a|b|x|a|a|a|z|
|字符串|a|a|b|c|**a**|**a**|**b**|~~x~~|a|a|a|z|
|z数组|0|1|0|0|3|

当i=4时,进行比较

当前的i>r,用朴素算法求解z[4]

得到z[4]=3,l=4,r=6

|下标|0|1|2|3|`[4`|`5`|`6]`|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|**a**|a|b|c|a|a|b|x|a|a|a|z|
|字符串|a|**a**|b|c|a|**a**|b|x|a|a|a|z|
|z数组|0|1|0|0|3|1|

当i=5时,进行比较

此时i在[l,r]里面,令k=i-l=1

`z[k]=z[1]=1`,`r-i+1=2`,`z[k]<r-i+1`

说明从i开始的与string的匹配情况和从k开始的与string的匹配情况一致,直接令z[i]=z[k]

得到z[5]=1,l=4,r=6

|下标|0|1|2|3|`[4`|`5`|`6]`|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|a|a|b|c|a|a|b|x|a|a|a|z|
|字符串|a|a|**b**|c|a|a|**b**|x|a|a|a|z|
|z数组|0|1|0|0|3|1|0|

当i=6时,进行比较

此时i在[l,r]里面,令k=i-l=2

`z[k]=z[2]=0`,`r-i+1=1`,`z[k]<r-i+1`

说明从i开始的与string的匹配情况和从k开始的与string的匹配情况一致,直接令z[i]=z[k]

得到z[6]=0,l=4,r=6

|下标|0|1|2|3|`[4`|`5`|`6]`|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|~~a~~|a|b|c|a|a|b|x|a|a|a|z|
|字符串|a|a|b|c|a|a|b|~~x~~|a|a|a|z|
|z数组|0|1|0|0|3|1|0|0|

当i=7时,进行比较

当前的i>r,用朴素算法求解z[7]

得到z[7]=0,l=7,r=6

|下标|0|1|2|3|`[4`|`5`|`6]`|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|**a**|**a**|~~b~~|c|a|a|b|x|a|a|a|z|
|字符串|a|a|b|c|a|a|b|x|**a**|**a**|~~a~~|z|
|z数组|0|1|0|0|3|1|0|0|2|

当i=8时,进行比较

当前的i>r,用朴素算法求解z[8]

得到z[8]=2,l=8,r=9

|下标|0|1|2|3|4|5|6|7|`[8`|`9]`|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|**a**|**a**|~~b~~|c|a|a|b|x|a|a|a|z|
|字符串|a|a|b|c|a|a|b|x|a|**a**|**a**|~~z~~|
|z数组|0|1|0|0|3|1|0|0|2|2|

当i=9时,进行比较

此时i在[l,r]里面,令k=i-l=1

`z[k]=z[1]=1`,`r-i+1=1`,`z[k]>=r-i+1`

如果直接将z[k]的值赋给z[i],z[i]的最终匹配位置可能与z[k]不同

因为对计算机来说,`r`后的元素都是未知的,需要经过匹配才能确定

重新用朴素算法计算z[i]

得到z[9]=2,l=9,r=10

|下标|0|1|2|3|4|5|6|7|8|`[9`|`10]`|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|**a**|~~a~~|b|c|a|a|b|x|a|a|a|z|
|字符串|a|a|b|c|a|a|b|x|a|a|**a**|~~z~~|
|z数组|0|1|0|0|3|1|0|0|2|2|1|

当i=10时,进行比较

此时i在[l,r]里面,令k=i-l=1

`z[k]=z[1]=1`,`r-i+1=1`,`z[k]>=r-i+1`

重新用朴素算法计算z[i]

得到z[10]=1,l=10,r=10

|下标|0|1|2|3|4|5|6|7|8|9|`[10]`|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|a|~~a~~|b|c|a|a|b|x|a|a|a|z|
|字符串|a|a|b|c|a|a|b|x|a|a|a|~~z~~|
|z数组|0|1|0|0|3|1|0|0|2|2|1|0|

当i=11时,进行比较

当前的i>r,用朴素算法求解z[11]

得到z[11]=0,l=11,r=10

z数组

|下标|0|1|2|3|4|5|6|7|8|9|10|11|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|字符串|a|a|b|c|a|a|b|x|a|a|a|z|
|z数组|0|1|0|0|3|1|0|0|2|2|1|0|

#### Z函数完整代码

```cpp
#include<iostream>
#include<string>
using namespace std;
void z_fun(string s,int *z){
    int l=0,r=0;
    for(int i=1;i<s.size();++i){
        if(i>r){
            l=r=i;
            while(r<s.size()&&s[r-l]==s[r]){
                ++r;
            }
            z[i]=r-l;
            --r;
        }
        else{
            int k=i-l;
            if(z[k]<r-i+1){
                z[i]=z[k];
            }
            else{
                l=i;
                while(r<s.size()&&s[r-l]==s[r]){
                    ++r;
                }
                z[i]=r-l;
                --r;
            }
        }
    }
    return;
}
int main(){
    string a,b;
    cin>>a>>b;
    string c=b+"$"+a;
    int z[c.size()];
    z_fun(c,z);
    for(int i=b.size()+1;i<c.size();++i){
        if(z[i]==b.size()){
            cout<<i-b.size()-1<<' ';
        }
    }
    return 0;
}
```

### 测试数据

(来源于[洛谷P3375 [模板]KMP字符串匹配](https://www.luogu.com.cn/problem/P3375))

```
ABABCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBBACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCACBBCACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBBACACCCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBBCBBBACCCBBCCBACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBCACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBAACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCBAABBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBBABCAABBCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBABAABCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBAACBACACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBAACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBCCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCABCACACCACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCABCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCABCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBAAABCCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBBBCCABCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCBBCACACCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCAACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBACBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCAACCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBBBBCACABCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCABCCBCCBAABAAACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCBBBCBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBABCACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCABCCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCAABACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBABCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCAAABACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCAACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBAACBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACBCCCBBCABBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCABBBAABCACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCCCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBAACABBCBABAAAACCAACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCAACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBAACCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBAACCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBCCBCCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCBBCBACCCCABCBACCCBCACBBABCBABCCBCCCCABABCCBCACAC
CCBABBACBABCAACBBCAACCBACACCCACCABABBABBABCABCCABABBCCABABACBCCACBACACBABCCBAAAABBBCBBABCBABABBCABCB
```

输出
```
4 109 210 312 413 514 614 722 831 932 1036 1153 1254 1359 1462 1563 1666 1767 1870 1972 2080 2193 2299 2399 2499 2601 2701 2808 2908 3012 3112 3212 3312 3414 3519 3621 3722 3822 3922
```

P.S. 若要通过洛谷P3375,需要修改数据输出格式,即将输出string的下标修改为输出string的逻辑位置,即第零个元素匹配成功就输出1