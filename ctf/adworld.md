## Misc

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/CTF/Misc%E8%B7%AF%E7%BA%BF%E5%9B%BE/mindmap.png)

---

### Erik_Baleog_and_Olaf

[题目地址](https://adworld.xctf.org.cn/task/answer?type=misc&number=1&grade=1&id=4755&page=2)

#### 方法一

1.将解压得到的图片丢进stegsolve中,切换通道可以看到有二维码

2.将二维码区域截图,用PS进行调色处理

但是此方法存在极大的不确定性,需要对多个通道进行比较才能得出一个类似的图像

同时得到的图像不一定能够正确解码(我用PS调出来的就无法解码,要命.....

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/CTF/Erik-Baleog-and-Olaf/1.png)

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/CTF/Erik-Baleog-and-Olaf/2.png)

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/CTF/Erik-Baleog-and-Olaf/3.png)

#### 方法二

通过 `strings stego100` 或者 `pngcheck -7 -f stego100` 可以得到 [hint: http://i.imgur.com/22kUrzm.png](https://i.imgur.com/22kUrzm.png) 

打开链接得到一张看起来相同的图片,通过 `compare 22kUrzm.png stego100 -compose src out.png`

得到一个二维码,扫描得到结果

```
flag{#justdiffit}
```

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/CTF/Erik-Baleog-and-Olaf/out.png)

---

### 双色块

解压得到一个gif文件,在 `honeyview` 中查看,发现一共有576帧,576=24x24,显然不可能是二维码

[二维码的生成细节和原理 | | 酷 壳 - CoolShell](https://coolshell.cn/articles/10590.html)

version1的二维码是21x21,version2的二维码是25x25

---

通过 `binwalk` 分析可知一共有两个图片,用 `foremost` 将其分离,在第二张图片中得到一个 `key:ctfer2333` 

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/CTF/%E5%8F%8C%E8%89%B2%E5%9D%97/1.png)

将gif文件中的绿色看作0,将紫色看作1,可以得到

```
011011110011100001000100
011011000111100001001011
001010110100100000111000
011101110111001101101001
010110000110010100101111
010001010101001001000110
011100000100000101001101
011000010100001001010000
011010010100100101100011
011010100011000101110011
010010000111100101000111
010011110100110101101101
010100010100010001101011
010010110010101101110101
010110000111001101010110
010110100110011101110010
011001010011010101000100
010100110101100001110111
001111010011110101101000
011010000110100001101000
011010000110100001101000
011010000110100001101000
011010000110100001101000
011010000110100001101000
```

将每8个二进制看作一组,可以发现每一组的开头都是0,猜测为ascii码

```cpp
#include<iostream>
#include<string>
using namespace std;
int main(){
    string a="011011110011100001000100011011000111100001001011001010110100100000111000011101110111001101101001010110000110010100101111010001010101001001000110011100000100000101001101011000010100001001010000011010010100100101100011011010100011000101110011010010000111100101000111010011110100110101101101010100010100010001101011010010110010101101110101010110000111001101010110010110100110011101110010011001010011010101000100010100110101100001110111001111010011110101101000011010000110100001101000011010000110100001101000011010000110100001101000011010000110100001101000011010000110100001101000";
    int b;
    for(int i=0;i<a.size();++i){
        if(i&&i%8==0){
            b=(a[i-8]-'0')*2*2*2*2*2*2*2+(a[i-7]-'0')*2*2*2*2*2*2+(a[i-6]-'0')*2*2*2*2*2+(a[i-5]-'0')*2*2*2*2+(a[i-4]-'0')*2*2*2+(a[i-3]-'0')*2*2+(a[i-2]-'0')*2+(a[i-1]-'0');
            cout<<char(b);
        }
    }
    return 0;
}

/*
o8DlxK+H8wsiXe/ERFpAMaBPiIcj1sHyGOMmQDkK+uXsVZgre5DSXw==hhhhhhhhhhhhhhh
*/
```

结合开始得到的key,可以判断得到的结果为DES而不是BASE64

[在线DES加密解密、DES在线加密解密、DES encryption and decryption](http://tool.chacuo.net/cryptdes)

在进行DES解密前,将最后的一串h去掉(在其他的转换平台上可能要将 `ctfer2333` 修改成 `ctfer233` )

> DES数据块长度为64位，所以IV长度需要为8个字符（ECB模式不用IV），密钥长度也为8个字符，IV与密钥超过长度则截取，不足则在末尾填充'\0'补足

![](https://cdn.jsdelivr.net/gh/AMDyesIntelno/blog_img/Notes/CTF/%E5%8F%8C%E8%89%B2%E5%9D%97/2.png)