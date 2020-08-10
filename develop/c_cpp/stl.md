## string

### erase

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

### insert

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