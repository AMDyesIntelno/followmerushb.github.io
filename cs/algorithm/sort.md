![](https://railgun.imfast.io/DataStruct/%E6%8E%92%E5%BA%8F/Untitled.png)

## 冒泡排序

```cpp
void bubble_sort(int *array, int len) {//冒泡排序
    for (int i = 1; i < len; i++) {//进行一次遍历将相邻的两个数修改为有序
        if (array[i] < array[i - 1]) {
            swap(array[i], array[i - 1]);
        }
    }
    for (int i = 1; i < len; i++) {//进行一次遍历检查是否完全有序
        if (array[i] < array[i - 1]) {
            bubble_sort(array, len);
        } else {
            continue;
        }
    }
    return;
}
```

## 桶排序

```cpp
int get_max(int *array, int len) {//获取上界
    int _max = array[0];
    for (int i = 1; i < len; ++i) {
        _max = max(_max, array[i]);
    }
    return _max;
}

int get_min(int *array, int len) {//获取下界
    int _min = array[0];
    for (int i = 1; i < len; ++i) {
        _min = min(_min, array[i]);
    }
    return _min;
}

void count_sort(vector<int> &bucket) {//计数排序(不能出现负数)
    if (bucket.size()) {
        int _max = bucket[0];
        for (int i = 0; i < bucket.size(); ++i) {
            _max = max(_max, bucket[i]);
        }
        int *temp = new int[_max + 1];
        fill(temp, temp + 1 + _max, 0);
        for (int i = 0; i < bucket.size(); ++i) {
            temp[bucket[i]]++;
        }
        int j = 0;
        for (int i = 0; i <= _max; ++i) {
            while (temp[i]) {
                bucket[j++] = i;
                --temp[i];
            }
        }
        delete[]temp;
    }
    return;
}

void bucket_sort(int *array, int len, int bucket_num, vector<int> *&bucket) {//桶排序(不能出现负数)
    int _min = get_min(array, len), _max = get_max(array, len);
    int bucket_size = (_max - _min) / bucket_num + 1;//桶中数字的取值范围
    for (int i = 0; i < bucket_num; ++i) {
        bucket[i].clear();//清空
    }
    for (int i = 0; i < len; ++i) {//将不同的数划分到不同的桶中(垃圾分类doge
        bucket[(array[i] - _min) / bucket_size].push_back(array[i]);
    }
    int k = 0;
    for (int i = 0; i < bucket_num; ++i) {
        count_sort(bucket[i]);
        for (int j = 0; j < bucket[i].size(); ++j) {
            array[k++] = bucket[i][j];
        }
    }
    return;
}
```

## 计数排序

```cpp
void count_sort(int *array, int len) {//计数排序(桶排序低配版,也可以作为桶排序的辅助排序函数),小范围数据神器,大范围数据没卵用(不能出现负数)
    int _max = array[0];
    for (int i = 0; i < len; ++i) {
        _max = max(array[i], _max);
    }
    int *temp = new int[_max + 1];
    fill(temp, temp + 1 + _max, 0);
    for (int i = 0; i < len; ++i) {
        temp[array[i]]++;
    }
    int j = 0;
    for (int i = 0; i <= _max; ++i) {
        while (temp[i]) {
            array[j++] = i;
            --temp[i];
        }
    }
    delete[]temp;
    return;
}
```

## 堆排序

```cpp
void heap(int *array, int len, int i) {//划分堆(模拟二叉树)
    int _max = i;
    int left = 2 * i + 1;//左子树
    int right = 2 * i + 2;//右子树
    if (left < len && array[left] > array[_max]) {//构建最大堆
        _max = left;
    }
    if (right < len && array[right] > array[_max]) {//构建最大堆
        _max = right;
    }
    if (_max != i) {
        swap(array[_max], array[i]);
        heap(array, len, _max);
    }
    return;
}

void heap_sort(int *array, int len) {//堆排序
    for (int i = len / 2 - 1; i >= 0; --i) {//当建立最大堆完毕,第0个元素必定为最大值
        heap(array, len, i);
    }
    for (int i = len - 1; i >= 0; --i) {//将第零个元素(最大值)与最后一个元素替换,并划分为有序堆和无序堆,无序堆继续划分,并修改无序堆的长度
        swap(array[0], array[i]);
        heap(array, i, 0);
    }
    return;
}
```

## 直接插入排序

```cpp
void direct_insert_sort(int *array, int len) {//直接插入排序
    int i, j;
    for (i = 1; i < len; ++i) {//从第二个元素开始遍历,不用管第一个,因为在插入阶段会遍历到第一个元素
        int temp = array[i];
        for (j = i; j > 0 && temp < array[j - 1]; j--) {//temp<a[j-1]保证了可以插入到第一个元素前
            array[j] = array[j - 1];//将元素后移
        }
        array[j] = temp;//将元素填入空位
    }
    return;
}
```

## 折半插入排序

```cpp
int binary_search(int *array, int start, int end, int data) {//二分查找
    int mid = (start + end) / 2;
    if (start >= end) {//这里有等于号
        return mid;
    }
    if (array[mid] > data) {
        return binary_search(array, start, mid, data);
    }
    else {
        return binary_search(array, mid + 1, end, data);
    }
}

void half_insert_sort(int *array, int len) {//折半插入排序
    for (int i = 1; i < len; ++i) {//从第二个元素开始遍历,不用管第一个,因为在插入阶段会遍历到第一个元素
        int posi = binary_search(array, 0, i, array[i]), temp = array[i], j;//通过二分查找确认插入位置
        for (j = i; j > posi; --j) {
            array[j] = array[j - 1];//将元素后移
        }
        array[j] = temp;//将元素填入空位
    }
    return;
}
```

## 归并排序

```cpp
void merge(int *array, int start, int mid, int end, int *temp) {//归并排序 治
    int left = start;
    int right = end;
    int mid_0 = mid;
    int mid_1 = mid + 1;
    int k = 0;
    while (left <= mid_0 && mid_1 <= right) {//两个片段进行比较,每次选出较小的一个数,写入temp数组中
        if (array[left] < array[mid_1]) {//注意,这里left是与mid_1进行比较而非mid_0
            temp[k++] = array[left++];
        }
        else {
            temp[k++] = array[mid_1++];
        }
    }
    while (left <= mid_0) {//对突出部分进行直接写入
        temp[k++] = array[left++];
    }
    while (mid_1 <= right) {
        temp[k++] = array[mid_1++];
    }
    for (left = 0; left < k; ++left) {
        array[left + start] = temp[left];//重新写入原数组,注意这里要加上函数传入的起始位置
    }
    return;
}

void merge_sort(int *array, int start, int end, int *temp) {//归并排序 分
    if (start < end) {
        int mid = (start + end) / 2;
        merge_sort(array, start, mid, temp);
        merge_sort(array, mid + 1, end, temp);
        merge(array, start, mid, end, temp);
        return;
    }
}
```

## 快速排序

```cpp
void quick_sort(int *array, int start, int end) {//快速排序
    //start=0,end=sizeof(array)/sizeof(array[0])-1或者start=1,end=sizeof(array)/sizeof(array[0])
    if (start >= end) {
        return;
    }
    int mid = array[(start + end) / 2];//以中间值为基准元素
    int left = start, right = end;
    while (left < right) {
        while (array[left] < mid) {//从左向基准比对,若左值比基准要小,则符合升序排列的标准,进行下一个元素的比对
            ++left;
        }
        while (array[right] > mid) {//从右向基准比对,若右值比基准要大,则符合升序排列的标准,进行下一个元素的比对
            --right;
        }
        if (left <= right) {//发现左右都有元素出现问题,则进行交换,此处必须要有等于号
            swap(array[left++], array[right--]);//swap之后进行将left+1,right-1
        }
    }
    quick_sort(array, start, right);
    quick_sort(array, left, end);
    return;
}
```

## 基数排序

```cpp
int radix_get_max(int *array, int len) {
    int _max = array[0];
    for (int i = 1; i < len; ++i) {
        _max = max(_max, array[i]);
    }
    return _max;
}

void radix_sort_core(int *array, int len, int exp, int *bucket, int *temp) {//基数排序的核心(不需要进行比较操作)
    for (int i = 0; i < len; ++i) {
        bucket[(array[i] / exp) % 10]++;//统计各个数位的数一共有多少个,比如个位为1的有5个,为2的有3个...
    }
    for (int i = 1; i < 10; ++i) {
        bucket[i] += bucket[i - 1];//累加,确定各个位的数的开始位置
    }
    for (int i = len - 1; i >= 0; --i) {//注意这里要倒序遍历
        temp[bucket[(array[i] / exp) % 10] - 1] = array[i];//从零开始存储,要减一
        /*将原数组的元素放入到临时数组中，但是两个个位相同但大小不同的数的相对位置不发生改变，
        例如 原本为141，81，1561 在放入后顺序并不会变化，仍为141，81，1561*/
        bucket[(array[i] / exp) % 10]--;//完成后递减一次，防止不同元素放置在同一个位置
    }
    /*
    例子 21 31 23 12 34
    第一次进入radix_sort_core,exp=1,说明此时以个位的数值为判断标准
    bucket[1]=2,bucket[2]=2+1=3,bucket[3]=3+1=4,bucket[4]=4+1=5
    倒序遍历
    _ _ _ _ 34
    _ _ 12 _ 34
    _ _ 12 23 34
    _ 31 12 23 34
    21 31 12 23 34
    第二次进入radix_sort_core,exp=10,说明此时以十位的数值为判断标准
    bucket[1]=1,bucket[2]=2+1=3,bucket[3]=2+3=5
    倒序遍历
    _ _ _ _ 34
    _ _ 23 _ 34
    12 _ 23 _ 34
    12 _ 23 31 34
    12 21 23 31 34
    */
    for (int i = 0; i < len; ++i) {
        array[i] = temp[i];//更新数组
    }
    for (int i = 0; i < 10; ++i) {
        bucket[i] = 0;//清零
    }
}

void radix_sort(int *array, int len, int *bucket, int *temp) {
    int _max = radix_get_max(array, len);//确认最大值
    for (int exp = 1; _max / exp > 0; exp *= 10) {//利用最大值确认最多有多少位
        radix_sort_core(array, len, exp, bucket, temp);
    }
}
```

## 选择排序

```cpp
void selection_sort(int *array, int len) {//选择排序
    for (int i = 0; i < len; ++i) {
        int posi = i;//记录每一次检索的最小值的位置
        for (int j = i + 1; j < len; ++j) {
            if (array[j] < array[posi]) {//更新最小值的位置
                posi = j;
            }
        }
        swap(array[posi], array[i]);
    }
    return;
}
```

## 希尔排序

```cpp
void shell_sort(int *array, int len) {//希尔排序
    for (int shell = len / 2; shell > 0; shell /= 2) {//切片
        for (int i = shell; i < len; ++i) {//片后递增
            int j;
            int temp = array[i];
            for (j = i; j >= shell && array[j - shell] > temp; j -= shell) {//修改为部分片段有序
                array[j] = array[j - shell];
            }
            array[j] = temp;
        }
    }
    return;
    /*
    例子
    87654321
    第一次切片 8765 | 4321
    修改为部分片段有序 4321 | 8765
    第二次切片 43 | 21 | 87 | 65
    修改为部分片段有序
    1.比较21的片段和43的片段并尝试替换
    21 | 43 | 87 | 65
    2.比较21,43的片段和87的片段并尝试替换
    21 | 43 | 87 | 65
    3.比较21,43,87的片段和65的片段并尝试替换
    21 | 43 | 65 | 87
    第三次切片 2 | 1 | 4 | 3 | 6 | 5 | 8 | 7
    修改为部分片段有序
    1.比较2和1并尝试替换
    1 | 2 | 4 | 3 | 6 | 5 | 8 | 7
    2.比较1,2和4并尝试替换
    1 | 2 | 4 | 3 | 6 | 5 | 8 | 7
    3.比较1,2,4和3并尝试替换
    1 | 2 | 3 | 4 | 6 | 5 | 8 | 7
    4.比较1,2,3,4和6并尝试替换
    1 | 2 | 3 | 4 | 6 | 5 | 8 | 7
    5.比较1,2,3,4,6和5并尝试替换
    1 | 2 | 3 | 4 | 5 | 6 | 8 | 7
    6.比较1,2,3,4,5,6和8并尝试替换
    1 | 2 | 3 | 4 | 5 | 6 | 8 | 7
    7.比较1,2,3,4,5,6,8和7并尝试替换
    1 | 2 | 3 | 4 | 5 | 6 | 7 | 8
    */
}
```