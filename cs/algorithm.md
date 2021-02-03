?> 算法笔记

## 关键路径与拓扑排序

### 代码

```cpp
/*求关键路径*/
#include<iostream>
#include<algorithm>
#include<stack>

#define INF INT32_MAX
#define _MAX 21
using namespace std;
typedef struct Arc {
    int weight = 0;//带权图记录权重,无权图记录是否链接
} Martix[_MAX][_MAX];//从(1,1)开始存
typedef struct Martrix_Graph {
    char vertex[_MAX];//顶点的值
    Martix Mar;//邻接矩阵
    int _vertex, _arc;//顶点数量,弧数量
} *M_G;
int Indegree[_MAX] = {0};//记录入度
int Earliest[_MAX] = {0};//记录节点的最早出现时间
int Latest[_MAX] = {0};//记录节点的最迟出现时间
stack<int> path;

inline int find(M_G graph, char item) {//查找当前值对应的序号,用于确定其在邻接矩阵的行或列
    for (int i = 1; i <= graph->_vertex; ++i) {
        if (graph->vertex[i] == item) {
            return i;
        }
    }
    return 0;
}

void Create_DG(M_G &graph) {//创建有向图
    graph = new Martrix_Graph;
    cout << "顶点数量: ";
    cin >> graph->_vertex;
    cout << "弧数量: ";
    cin >> graph->_arc;
    cout << "输入顶点的值:" << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {
        cin >> graph->vertex[i];
    }
    for (int i = 0; i < _MAX; ++i) {
        for (int j = 0; j < _MAX; ++j) {
            graph->Mar[i][j].weight = INF;
        }
    }
    cout << "输入弧尾,弧头和弧的权重:" << endl;
    char v1, v2;
    int weight;//v1弧头,v2弧尾,即从v2指向v1,带权
    for (int i = 1; i <= graph->_arc; ++i) {
        cin >> v2 >> v1 >> weight;
        if (find(graph, v2) == 0 || find(graph, v1) == 0) {
            exit(-1);
        }
        graph->Mar[find(graph, v2)][find(graph, v1)].weight = weight;
        Indegree[find(graph, v1)]++;
    }
}

int Fir_posi(M_G graph, char item) {//返回与item链接的第一个邻接顶点序号,即与item链接的第一个弧头(检索邻接矩阵的某一列)
    int check = INF;
    int posi = find(graph, item);
    if (posi) {
        for (int i = 1; i <= graph->_vertex; ++i) {
            if (graph->Mar[posi][i].weight != check) {
                return i;
            }
        }
    }
    return 0;
}

int Next_posi(M_G graph, char item, char now) {//返回与item链接并且在now之后的顶点序号,即与item链接的其他弧头
    int check = INF;
    int posi1 = find(graph, item);
    int posi2 = find(graph, now);
    if (posi1 && posi2) {
        for (int i = posi2 + 1; i <= graph->_vertex; ++i) {
            if (graph->Mar[posi1][i].weight != check) {
                return i;
            }
        }
    }
    return 0;
}

int TopologicalSort(M_G graph) {
    stack<int> topology;
    for (int i = 1; i <= graph->_vertex; ++i) {
        if (!Indegree[i]) {//将入度为0的点压入栈中
            topology.push(i);
        }
    }
    int count = 0;//记录顶点个数,若有向图中含有环,则count小于图的顶点个数
    int now;
    cout << "拓扑排序: ";
    while (!topology.empty()) {
        now = topology.top();
        path.push(now);
        topology.pop();
        cout << graph->vertex[now] << " ";
        ++count;
        for (int i = Fir_posi(graph, graph->vertex[now]); i > 0; i = Next_posi(graph, graph->vertex[now], graph->vertex[i])) {//将以now为弧尾的弧去除
            if (!(--Indegree[i])) {//若入度减为0,则入栈
                topology.push(i);
            }
            Earliest[i] = max(Earliest[i], Earliest[now] + graph->Mar[now][i].weight);//按最大的权重计算
            /*例子
            1->2 a1=6 2->4 a3=1
            1->3 a2=4 3->4 a4=1
            4->....
            对于节点4来说,要开始4之后的状态,必须要完成1->2->4以及1->3->4
            1->2->4所花费的时间为7
            1->3->4所花费的时间为5
            若以1->3->4计算,完成1->3->4时,1->2->4尚未完成,此时4之后的状态无法开始
            因此求出到达各个状态的最早时间必须按最大值来计算
            */
        }
    }
    cout << endl;
    if (count < graph->_vertex) {//含有环
        return -1;
    }
    else {
        return 0;
    }
}

int CriticalPath(M_G graph) {
    if (TopologicalSort(graph) == -1) {
        return -1;
    }
    fill(Latest, Latest + 1 + graph->_vertex, Earliest[graph->_vertex]);
    int now;
    while (!path.empty()) {
        now = path.top();
        path.pop();
        for (int i = Fir_posi(graph, graph->vertex[now]); i > 0; i = Next_posi(graph, graph->vertex[now], graph->vertex[i])) {
            Latest[now] = min(Latest[now], Latest[i] - graph->Mar[now][i].weight);
            /*例子
            假定汇点为9
            Earliest[9]=18
            5->7 weight=5 7->9 weight=2
            5->8 weight=7 8->9 weight=4
            则节点7的最迟开始时间为18-2=16
            则节点8的最迟开始时间为18-4=14
            则节点5的最迟开始时间为16-5=11或者14-7=7
            如果取11,那么要完成5->8->9这段路径的时间将超出预期
            所以只能取7,保证两条路径都能顺利完成,即取最小值
            */
        }
    }
    cout << "关键路径: ";
    int i, sum = 0;
    for (i = 1; i <= graph->_vertex; ++i) {
        if (Latest[i] == Earliest[i]) {
            cout << graph->vertex[i];
            sum += Latest[i];
            break;
        }
    }
    for (++i; i <= graph->_vertex; ++i) {
        if (Latest[i] == Earliest[i]) {
            cout << "->" << graph->vertex[i];
            sum += Latest[i];
        }
    }
    cout << " " << sum;
    return 0;
}

int main() {
    M_G graph = nullptr;
    Create_DG(graph);
    CriticalPath(graph);
    delete graph;
    graph = nullptr;
    return 0;
}
```

### 参考

[关键路径算法演示（AOE网）](https://www.jianshu.com/p/1857ed4d8128)

### Sample

```
9 11 123456789
1 2 6
1 3 4
1 4 5
2 5 1
3 5 1
4 6 2
5 7 9
5 8 7
6 8 4
7 9 2
8 9 4
```

### Out

```
拓扑排序: 1 4 6 3 2 5 8 7 9 
关键路径: 1->2->5->7->8->9 61
```

## 图的最短路径

### Dijkstra算法

```cpp
/*Dijkstra算法,邻接矩阵表示*/
#include<iostream>
#include<string>

#define INF INT32_MAX
#define _MAX 21
using namespace std;
typedef enum {
    DN, UDN
} Graphkind;//带权有向图,带权无向图
typedef struct Arc {
    int weight = 0;//带权图记录权重,无权图记录是否链接
} Martix[_MAX][_MAX];//从(1,1)开始存
typedef struct Martrix_Graph {
    char vertex[_MAX];//顶点的值
    Martix Mar;//邻接矩阵
    Graphkind kind;//图的类型
    int _vertex, _arc;//顶点数量,弧数量
} *M_G;
bool visited[_MAX] = {false};

inline int find(M_G graph, char item) {//查找当前值对应的序号,用于确定其在邻接矩阵的行或列
    for (int i = 1; i <= graph->_vertex; ++i) {
        if (graph->vertex[i] == item) {
            return i;
        }
    }
    return 0;
}

void Create_DN(M_G &graph) {//创建带权有向图
    cout << "顶点数量: ";
    cin >> graph->_vertex;
    cout << "弧数量: ";
    cin >> graph->_arc;
    cout << "输入顶点的值:" << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {
        cin >> graph->vertex[i];
    }
    for (int i = 0; i < _MAX; ++i) {
        for (int j = 0; j < _MAX; ++j) {
            graph->Mar[i][j].weight = INF;
        }
    }
    cout << "输入弧尾,弧头和弧的权重:" << endl;
    char v1, v2;
    int weight;//v1弧头,v2弧尾,即从v2指向v1,带权
    for (int i = 1; i <= graph->_arc; ++i) {
        cin >> v2 >> v1 >> weight;
        if (find(graph, v2) == 0 || find(graph, v1) == 0) {
            exit(-1);
        }
        graph->Mar[find(graph, v2)][find(graph, v1)].weight = weight;
    }
}

void Create_UDN(M_G &graph) {//创建带权无向图
    cout << "顶点数量: ";
    cin >> graph->_vertex;
    cout << "弧数量: ";
    cin >> graph->_arc;
    cout << "输入顶点的值:" << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {
        cin >> graph->vertex[i];
    }
    for (int i = 0; i < _MAX; ++i) {
        for (int j = 0; j < _MAX; ++j) {
            graph->Mar[i][j].weight = INF;
        }
    }
    cout << "输入弧尾,弧头和弧的权重:" << endl;
    char v1, v2;
    int weight;//v1弧头,v2弧尾,即从v2指向v1,带权
    for (int i = 1; i <= graph->_arc; ++i) {
        cin >> v2 >> v1 >> weight;
        if (find(graph, v2) == 0 || find(graph, v1) == 0) {
            exit(-1);
        }
        graph->Mar[find(graph, v2)][find(graph, v1)].weight = weight;
        graph->Mar[find(graph, v1)][find(graph, v2)].weight = weight;
    }
}

void Create_Graph(M_G &graph) {
    cout << "0:创建带权有向图 1:创建带权无向图" << endl;
    cout << "选项: ";
    graph = new Martrix_Graph;
    cin >> (int &) graph->kind;
    switch (graph->kind) {
        case DN:
            Create_DN(graph);
            break;
        case UDN:
            Create_UDN(graph);
            break;
        default:
            cout << "ERROR!" << endl;
            break;
    }
}

void Dijkstra(M_G graph, char start, int *final, string *str) {
    int mark = find(graph, start);
    visited[mark] = true;//不对自身查找
    for (int i = 1; i <= graph->_vertex; ++i) {//根据邻接矩阵的起始点所在行的各个权重,初始化起始点到其他顶点的距离
        final[i] = graph->Mar[mark][i].weight;
        str[i] += graph->vertex[mark];
        str[i] += graph->vertex[i];
    }
    final[mark] = 0;//自身到自身的距离为0
    for (int i = 1; i < graph->_vertex; ++i) {//遍历graph->_vertex-1次,因为已经进行了初始化
        int _min = INF;
        int posi;
        for (int j = 1; j <= graph->_vertex; ++j) {//遍历final数组中没有确定最短路的值,查找新的最短路,并记录其位置
            if (!visited[j] && final[j] < _min) {
                _min = final[j];
                posi = j;
            }
        }
        visited[posi] = true;//标记新遍历得到的最短路
        if (str[posi][str[posi].size() - 1] != graph->vertex[posi]) {
            str[posi] += graph->vertex[posi];
        }
        for (int j = 1; j <= graph->_vertex; ++j) {//更新final数组中的值
            if (graph->Mar[posi][j].weight < INF) {
                //依据新的最短路的位置,对其邻接矩阵的所在行进行遍历,寻找对于其他点可能存在的最短路并更新final数组
                if (final[j] <= final[posi] + graph->Mar[posi][j].weight) {
                    continue;
                }
                else {
                    final[j] = final[posi] + graph->Mar[posi][j].weight;
                    str[j] = str[posi];
                }
            }
        }
    }
    fill(visited, visited + _MAX, 0);
}

void print(M_G graph) {
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            if (graph->Mar[i][j].weight == INF) {
                cout << "∞" << ' ';
                continue;
            }
            cout << graph->Mar[i][j].weight << ' ';
        }
        cout << endl;
    }
    cout << endl;
}

int main() {
    M_G graph = nullptr;
    Create_Graph(graph);
    cout << "当前图的类型: ";
    switch (graph->kind) {
        case DN:
            cout << "带权有向图" << endl;
            break;
        case UDN:
            cout << "带权无向图" << endl;
            break;
    }
    cout << "邻接矩阵: " << endl;
    print(graph);
    int *final = new int[graph->_vertex + 1];
    string *str = new string[graph->_vertex + 1];
    fill(final, final + graph->_vertex, 0);
    Dijkstra(graph, graph->vertex[1], final, str);
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 0; j < str[i].size(); ++j) {
            cout << str[i][j];
            if (j != str[i].size() - 1) {
                cout << "->";
            }
        }
        if (final[i] == INF) {
            cout << ' ' << "∞" << endl;
            continue;
        }
        cout << ' ' << final[i] << endl;
    }
    delete graph;
    graph = nullptr;
    delete[]final;
    final = nullptr;
    delete[]str;
    str = nullptr;
    return 0;
}
```
#### 参考

[算法 7：Dijkstra 最短路算法](https://wiki.jikexueyuan.com/project/easy-learn-algorithm/dijkstra.html)

#### Sample

```
6 8 abcdef
a c 10
a e 30
a f 100
b c 5
c d 50
d f 10
e d 20
e f 60
```

#### Out

```
当前图的类型: 带权有向图
邻接矩阵: 
∞ ∞ 10 ∞ 30 100 
∞ ∞ 5 ∞ ∞ ∞ 
∞ ∞ ∞ 50 ∞ ∞ 
∞ ∞ ∞ ∞ ∞ 10 
∞ ∞ ∞ 20 ∞ 60 
∞ ∞ ∞ ∞ ∞ ∞ 

a->a 0
a->b ∞
a->c 10
a->e->d 50
a->e 30
a->e->d->f 60
```

### Floyd算法

```cpp
/*Floyd算法,邻接矩阵表示*/
#include<iostream>

#define INF INT32_MAX/2
#define _MAX 21
using namespace std;
typedef enum {
    DN, UDN
} Graphkind;//带权有向图,带权无向图
typedef struct Arc {
    int weight = 0;//带权图记录权重,无权图记录是否链接
} Martix[_MAX][_MAX];//从(1,1)开始存
typedef struct Martrix_Graph {
    char vertex[_MAX];//顶点的值
    Martix Mar;//邻接矩阵
    Graphkind kind;//图的类型
    int _vertex, _arc;//顶点数量,弧数量
} *M_G;
bool visited[_MAX] = {false};

inline int find(M_G graph, char item) {//查找当前值对应的序号,用于确定其在邻接矩阵的行或列
    for (int i = 1; i <= graph->_vertex; ++i) {
        if (graph->vertex[i] == item) {
            return i;
        }
    }
    return 0;
}

void Create_DN(M_G &graph) {//创建带权有向图
    cout << "顶点数量: ";
    cin >> graph->_vertex;
    cout << "弧数量: ";
    cin >> graph->_arc;
    cout << "输入顶点的值:" << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {
        cin >> graph->vertex[i];
    }
    for (int i = 0; i < _MAX; ++i) {
        for (int j = 0; j < _MAX; ++j) {
            graph->Mar[i][j].weight = INF;
        }
    }
    cout << "输入弧尾,弧头和弧的权重:" << endl;
    char v1, v2;
    int weight;//v1弧头,v2弧尾,即从v2指向v1,带权
    for (int i = 1; i <= graph->_arc; ++i) {
        cin >> v2 >> v1 >> weight;
        if (find(graph, v2) == 0 || find(graph, v1) == 0) {
            exit(-1);
        }
        graph->Mar[find(graph, v2)][find(graph, v1)].weight = weight;
    }

}

void Create_UDN(M_G &graph) {//创建带权无向图
    cout << "顶点数量: ";
    cin >> graph->_vertex;
    cout << "弧数量: ";
    cin >> graph->_arc;
    cout << "输入顶点的值:" << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {
        cin >> graph->vertex[i];
    }
    for (int i = 0; i < _MAX; ++i) {
        for (int j = 0; j < _MAX; ++j) {
            graph->Mar[i][j].weight = INF;
        }
    }
    cout << "输入弧尾,弧头和弧的权重:" << endl;
    char v1, v2;
    int weight;//v1弧头,v2弧尾,即从v2指向v1,带权
    for (int i = 1; i <= graph->_arc; ++i) {
        cin >> v2 >> v1 >> weight;
        if (find(graph, v2) == 0 || find(graph, v1) == 0) {
            exit(-1);
        }
        graph->Mar[find(graph, v2)][find(graph, v1)].weight = weight;
        graph->Mar[find(graph, v1)][find(graph, v2)].weight = weight;
    }
}

void Create_Graph(M_G &graph) {
    cout << "0:创建带权有向图 1:创建带权无向图" << endl;
    cout << "选项: ";
    graph = new Martrix_Graph;
    cin >> (int &) graph->kind;
    switch (graph->kind) {
        case DN:
            Create_DN(graph);
            break;
        case UDN:
            Create_UDN(graph);
            break;
        default:
            cout << "ERROR!" << endl;
            break;
    }
}

void Floyd(M_G graph, int weight[][_MAX], int path[][_MAX]) {
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            if (i == j) {
                weight[i][j] = 0;
                continue;
            }
            weight[i][j] = graph->Mar[i][j].weight;
        }
    }
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            path[i][j] = j;
        }
    }
    cout << "初始化..." << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            if (weight[i][j] == INF) {
                cout << "∞" << ' ';
                continue;
            }
            cout << weight[i][j] << ' ';
        }
        cout << endl;
    }
    /*cout<<endl<<"经过顶点3进行中转"<<endl;
    for(int i=1;i<=graph->_vertex;++i){
        for(int j=1;j<=graph->_vertex;++j){
            weight[i][j]=min(weight[i][j],weight[i][3]+weight[3][j]);
        }
    }
    for(int i=1;i<=graph->_vertex;++i){
        for(int j=1;j<=graph->_vertex;++j){
            if(weight[i][j]==INF){
                cout<<"∞"<<' ';
                continue;
            }
            cout<<weight[i][j]<<' ';
        }
        cout<<endl;
    }
    cout<<endl<<"经过顶点3,4进行中转"<<endl;
    for(int i=1;i<=graph->_vertex;++i){
        for(int j=1;j<=graph->_vertex;++j){
            weight[i][j]=min(weight[i][j],weight[i][3]+weight[3][j]);
        }
    }
    for(int i=1;i<=graph->_vertex;++i){
        for(int j=1;j<=graph->_vertex;++j){
            weight[i][j]=min(weight[i][j],weight[i][4]+weight[4][j]);
        }
    }
    for(int i=1;i<=graph->_vertex;++i){
        for(int j=1;j<=graph->_vertex;++j){
            if(weight[i][j]==INF){
                cout<<"∞"<<' ';
                continue;
            }
            cout<<weight[i][j]<<' ';
        }
        cout<<endl;
    }*/
    cout << endl << "结果" << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {//在第i次循环中,weight数组中所有与i有关的值都不会改变
        for (int j = 1; j <= graph->_vertex; ++j) {
            for (int k = 1; k <= graph->_vertex; ++k) {
                if (weight[j][k] > weight[j][i] + weight[i][k]) {
                    weight[j][k] = weight[j][i] + weight[i][k];
                    path[j][k] = path[j][i];//将最短路径更新
                    /*解析
                    假定有1,2,3,4共四个点
                    1->2=5,2->3=6,3->4=7,共三条弧
                    以1为中转点时,最短路径无更新
                    以2为中转点时
                    1->3由INF转变为1->2 + 2->3 = 11
                    path[1][3]=path[1][2]=2
                    以3为中转点时
                    1->4由INF转变为1->3 + 3->4 = 18
                    path[1][4]=path[1][3]=path[1][2]=2
                    2->4由INF转变为2->3 + 3->4 = 13
                    path[2][4]=path[2][3]=3
                    打印 1->4 的最短路径
                    起点(start)为1,终点(end)为4
                    path[1][4]=2,输出2,问题转化为打印 2->4 的最短路径
                    path[2][4]=3,输出3,问题转化为打印 3->4 的最短路径
                    path[3][4]=4,输出4,问题转化为打印 4->4 的最短路径
                    4=4,结束
                    */
                }
            }
        }
    }
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            if (weight[i][j] == INF) {
                cout << "∞" << ' ';
                continue;
            }
            cout << weight[i][j] << ' ';
        }
        cout << endl;
    }
    cout << endl;
}

void print_path(M_G graph, int start, int end, int path[][_MAX]) {
    if (start == end) {
        cout << graph->vertex[start] << "->" << graph->vertex[end];
    }
    int i = start;
    cout << graph->vertex[i];
    while (i != end) {
        cout << "->";
        i = path[i][end];
        cout << graph->vertex[i];
    }
}

void print(M_G graph) {
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            if (graph->Mar[i][j].weight == INF) {
                cout << "∞" << ' ';
                continue;
            }
            cout << graph->Mar[i][j].weight << ' ';
        }
        cout << endl;
    }
    cout << endl;
}

int main() {
    M_G graph = nullptr;
    Create_Graph(graph);
    cout << "当前图的类型: ";
    switch (graph->kind) {
        case DN:
            cout << "带权有向图" << endl;
            break;
        case UDN:
            cout << "带权无向图" << endl;
            break;
    }
    cout << "邻接矩阵: " << endl;
    print(graph);
    int weight[_MAX][_MAX] = {0};
    int path[_MAX][_MAX] = {0};
    Floyd(graph, weight, path);
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            print_path(graph, i, j, path);
            cout << ' ' << weight[i][j] << endl;
        }
    }
    delete graph;
    graph = nullptr;
    return 0;
}
```

#### 参考

[算法 6：只有五行的 Floyd 最短路算法](https://wiki.jikexueyuan.com/project/easy-learn-algorithm/floyd.html)

#### Sample

```
4 8 1234
1 2 2
1 3 6
1 4 4
2 3 3
3 1 7
3 4 1
4 1 5
4 3 12
```

#### Out

```
当前图的类型: 带权有向图
邻接矩阵: 
∞ 2 6 4 
∞ ∞ 3 ∞ 
7 ∞ ∞ 1 
5 ∞ 12 ∞ 

初始化...
0 2 6 4 
∞ 0 3 ∞ 
7 ∞ 0 1 
5 ∞ 12 0 

结果
0 2 5 4 
9 0 3 4 
6 8 0 1 
5 7 10 0 

1->1 0
1->2 2
1->2->3 5
1->4 4
2->3->4->1 9
2->2 0
2->3 3
2->3->4 4
3->4->1 6
3->4->1->2 8
3->3 0
3->4 1
4->1 5
4->1->2 7
4->1->2->3 10
4->4 0
```

## 线索二叉树

### 代码

```cpp
#include<iostream>
#include<stack>

using namespace std;
enum pointtag {
    Link,//0
    Thread//1
};
typedef struct Tree {
    char data = '\0';
    struct Tree *left = nullptr;
    struct Tree *right = nullptr;
    pointtag left_Tag, right_Tag;
} *tree;
tree pre = nullptr;//指向前一个访问过的结点
void Create_tree(tree &t) {//以先序次序创建树,以空格代表空结点
    char temp;
    temp = getchar();
    t = new Tree;
    if (temp != '\n') {
        if (temp == ' ') {
            delete t;
            t = nullptr;
        }
        else {
            t->data = temp;
            Create_tree(t->left);
            if (t->left) {//有实际节点就设置为Link
                t->left_Tag = Link;
            }
            else {
                t->left_Tag = Thread;
            }
            Create_tree(t->right);
            if (t->right) {//有实际节点就设置为Link
                t->right_Tag = Link;
            }
            else {
                t->right_Tag = Thread;
            }
        }
    }
}

void Clear_Thread(tree &t) {//清除前一次线索化所产生的线索
    if (t) {
        if (t->left_Tag == Thread) {
            t->left = nullptr;
        }
        if (t->right_Tag == Thread) {
            t->right = nullptr;
        }
        Clear_Thread(t->left);
        Clear_Thread(t->right);
    }
}

void Clear_tree(tree &t) {//清空树
    if (t) {
        Clear_tree(t->left);
        Clear_tree(t->right);
        delete t;
        t = nullptr;
    }
}

void Preorder_Thread(tree t) {//先序线索化
    if (t) {
        if (!t->left) {
            t->left_Tag = Thread;
            t->left = pre;
        }
        if (pre && !pre->right) {
            pre->right_Tag = Thread;
            pre->right = t;
        }
        pre = t;
        if (t->left_Tag == Link) {
            Preorder_Thread(t->left);
        }
        if (t->right_Tag == Link) {
            Preorder_Thread(t->right);
        }
    }
}

void Inorder_Thread(tree t) {//中序线索化
    if (t) {
        if (t->left_Tag == Link) {
            Inorder_Thread(t->left);
        }
        if (!t->left) {
            t->left_Tag = Thread;
            t->left = pre;
        }
        if (pre && !pre->right) {
            pre->right_Tag = Thread;
            pre->right = t;
        }
        pre = t;
        if (t->right_Tag == Link) {
            Inorder_Thread(t->right);
        }
    }
}

void Postorder_Thread(tree t) {//后序线索化
    if (t) {
        if (t->left_Tag == Link) {
            Postorder_Thread(t->left);
        }
        if (t->right_Tag == Link) {
            Postorder_Thread(t->right);
        }
        if (!t->left) {
            t->left_Tag = Thread;
            t->left = pre;
        }
        if (pre && !pre->right) {
            pre->right_Tag = Thread;
            pre->right = t;
        }
        pre = t;
    }
}

void Preorder_traversal(tree t) {//先序遍历并先序线索化
    if (t) {
        pre = nullptr;
        Preorder_Thread(t);
    }
}

void Inorder_traversal(tree t) {//中序遍历并中序线索化
    if (t) {
        pre = nullptr;
        Inorder_Thread(t);
    }
}

void Postorder_traversal(tree t) {//后序遍历并后序线索化
    if (t) {
        pre = nullptr;
        Postorder_Thread(t);
    }
}

void Preorder_traversal_core(tree t) {//借助先序线索化遍历二叉树
    if (t) {
        while (t) {
            while (t->left_Tag == Link) {//直到最左边的节点
                cout << t->data;
                t = t->left;
            }
            cout << t->data;
            t = t->right;
            //如果最左边的节点有直接右子树,那么进入该右子树
            //如果最左边的节点的右节点是线索形式,那么直接到达当前节点的后继节点,继续开始左子树的遍历
        }
    }
}

void Inorder_traversal_core(tree t) {//借助中序线索化遍历二叉树
    if (t) {
        while (t) {
            while (t->left_Tag == Link) {//先遍历左子树到底
                t = t->left;
            }
            cout << t->data;
            if (t->right_Tag == Link) {//如果当前节点的右子树是直接右子树,那么进入该右子树
                t = t->right;
            }
            else {//如果当前的节点的右节点是线索形式,那么直接转到当前节点的后继节点,直到当前节点为空,或者有新的右子树可以进入
                while (t && t->right_Tag == Thread) {
                    t = t->right;
                    if (t) {
                        cout << t->data;
                    }
                }
                if (t) {//进入新的右子树,再次开始遍历
                    t = t->right;
                }
            }
        }
    }
}

void Postorder_traversal_core(tree t) {//借助后序线索化遍历二叉树
    if (t) {
        stack<tree> s;
        //如果在定义二叉树时,没有定义父节点(二叉链表形式),那么使用栈来进行回溯
        //如果有定义(三叉链表形式),那么直接使用父节点
        tree prev = nullptr;//前一个节点,用于判断当前节点是否已经遍历完成
        while (t) {
            while (t->left_Tag == Link && t->left != prev) {//遍历左子树到底,并且避免出现重复遍历的情况
                if (t->right_Tag == Link || t->left->right_Tag == Link) {//当前节点存在右子树或者无法通过后继节点指向当前节点时,节点进栈
                    s.push(t);
                }
                t = t->left;
            }
            while (t && t->right_Tag == Thread) {//当前节点的右节点为线索节点,那么进入右节点直接转到当前节点的后继节点,直到存在右子树或当前节点为空
                cout << t->data;
                prev = t;
                t = t->right;
            }
            while (t && t->right == prev) {//判断当前右子树是否已经遍历
                //如果prev是当前节点的右节点,那么说明当前右子树已经遍历
                //那么取一个与当前节点不同的新节点,判断能否进入
                //如果不是,那么说明当前右子树没有被遍历
                cout << t->data;
                prev = t;
                if (!s.empty()) {
                    if (t == s.top()) {//栈顶节点与当前节点相同,直接弹出栈顶节点
                        s.pop();
                    }
                    if (!s.empty()) {//如果不同,取栈顶节点并弹出
                        t = s.top();
                        s.pop();
                    }
                    else {
                        t = nullptr;
                    }
                }
                else {
                    t = nullptr;
                }
            }
            if (t && t->right_Tag == Link) {//进入右子树,并将当前节点入栈,作为根节点
                s.push(t);
                t = t->right;
            }
        }
    }
}

int main() {
    tree t = nullptr;
    Create_tree(t);
    cout << "Preorder_traversal: ";
    Preorder_traversal(t);
    Preorder_traversal_core(t);
    cout << endl;
    Clear_Thread(t);
    cout << "Inorder_traversal: ";
    Inorder_traversal(t);
    Inorder_traversal_core(t);
    cout << endl;
    Clear_Thread(t);
    cout << "Postorder_traversal: ";
    Postorder_traversal(t);
    Postorder_traversal_core(t);
    Clear_Thread(t);
    Clear_tree(t);
    return 0;
}
```

#### Sample1

`4210---3--75-6--8-9--(-代表输入样例中的空格)`

```
4210   3  75 6  8 9  
```

#### Out1

```
Preorder_traversal: 4210375689
Inorder_traversal: 0123456789
Postorder_traversal: 0132659874
```

#### Sample2

> 数据样例来源: 数据结构P125 图6.4-d 特殊形态的二叉树(d)

`124--5--3-6--(-代表输入样例中的空格)`

```
124  5  3 6  
```

#### Out2

```
Preorder_traversal: 124536
Inorder_traversal: 425136
Postorder_traversal: 452631
```

#### Sample3

`ABD-G---CE-H--F--(-代表输入样例中的空格)`

```
ABD G   CE H  F  
```

#### Out3

```
Preorder_traversal: ABDGCEHF
Inorder_traversal: DGBAEHCF
Postorder_traversal: GDBHEFCA
```

#### Sample4

`4210-3----75-6--8-9--(-代表输入样例中的空格)`

```
4210 3    75 6  8 9  
```

#### Out4

```
Preorder_traversal: 4210375689
Inorder_traversal: 0312456789
Postorder_traversal: 3012659874
```

## 最小生成树

### Prim算法

```cpp
/*带权无向图最小生成树prim算法*/
#include<iostream>

#define INF INT32_MAX
#define _MAX 21
using namespace std;
typedef struct Arc {
    int weight = 0;//带权图记录权重,无权图记录是否链接
} Martix[_MAX][_MAX];//从(1,1)开始存
typedef struct Martrix_Graph {
    char vertex[_MAX];//顶点的值
    Martix Mar;//邻接矩阵
    int _vertex, _arc;//顶点数量,弧数量
} *M_G;
typedef struct PRIM {/*存放节点和权重*/
    char data;
    int weight;
} Prim[_MAX];
bool visited[_MAX] = {false};/*标记是否已经访问*/
inline int find(M_G graph, char item) {//查找当前值对应的序号,用于确定其在邻接矩阵的行或列
    for (int i = 1; i <= graph->_vertex; ++i) {
        if (graph->vertex[i] == item) {
            return i;
        }
    }
    return 0;
}

void Create_UDN(M_G &graph) {//创建带权无向图
    graph = new Martrix_Graph;
    cout << "顶点数量: ";
    cin >> graph->_vertex;
    cout << "弧数量: ";
    cin >> graph->_arc;
    cout << "输入顶点的值:" << endl;
    for (int i = 1; i <= graph->_vertex; ++i) {
        cin >> graph->vertex[i];
    }
    for (int i = 0; i < _MAX; ++i) {
        for (int j = 0; j < _MAX; ++j) {
            graph->Mar[i][j].weight = INF;
        }
    }
    cout << "输入弧尾,弧头和弧的权重:" << endl;
    char v1, v2;
    int weight;//v1弧头,v2弧尾,即从v2指向v1,带权
    for (int i = 1; i <= graph->_arc; ++i) {
        cin >> v2 >> v1 >> weight;
        if (find(graph, v2) == 0 || find(graph, v1) == 0) {
            exit(-1);
        }
        graph->Mar[find(graph, v2)][find(graph, v1)].weight = weight;
        graph->Mar[find(graph, v1)][find(graph, v2)].weight = weight;
    }
}

void prim(M_G graph, char start, Prim p) {
    fill(visited, visited + _MAX, 0);
    int start_posi = find(graph, start);
    if (start_posi == 0) {
    }
    visited[start_posi] = true;
    int _min = INF, _min_posi;
    for (int i = 1; i <= graph->_vertex; ++i) {/*根据给定的根节点进行初始化*/
        p[i].data = graph->vertex[start_posi];
        p[i].weight = graph->Mar[start_posi][i].weight;
        if (graph->Mar[start_posi][i].weight < _min) {/*标记最小权重*/
            _min = graph->Mar[start_posi][i].weight;
            _min_posi = i;
        }
    }
    visited[_min_posi] = true;/*标记找到的最小权重点*/
    start_posi = _min_posi;/*从新的最小权重点开始*/
    _min = INF;/*初始化为最大值*/
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            if (!visited[j]) {
                if (graph->Mar[start_posi][j].weight < p[j].weight) {/*刷新权重值和权重点*/
                    p[j].weight = graph->Mar[start_posi][j].weight;
                    p[j].data = graph->vertex[start_posi];
                }
                if (p[j].weight < _min) {/*标记最小权重*/
                    _min = p[j].weight;
                    _min_posi = j;
                }
            }
        }
        visited[_min_posi] = true;
        start_posi = _min_posi;
        _min = INF;
    }
}

void print(M_G graph) {
    for (int i = 1; i <= graph->_vertex; ++i) {
        for (int j = 1; j <= graph->_vertex; ++j) {
            if (graph->Mar[i][j].weight == INF) {
                cout << "∞" << ' ';
                continue;
            }
            cout << graph->Mar[i][j].weight << ' ';
        }
        cout << endl;
    }
    cout << endl;
}

void print_prim(M_G graph, char start, const Prim p) {
    int start_posi = find(graph, start);
    if (start_posi == 0) {
    }
    for (int i = 1; i <= graph->_vertex; ++i) {
        if (i == start_posi) {
            continue;
        }
        cout << p[i].data << "-" << p[i].weight << "->" << graph->vertex[i] << endl;
    }
}

int main() {
    M_G graph = nullptr;
    Prim p;
    Create_UDN(graph);
    print(graph);
    prim(graph, 'A', p);
    print_prim(graph, 'A', p);
    delete graph;
    graph = nullptr;
    return 0;
}
```

#### 参考图

![](https://img.misaka.gq/Notes/algorithm/%E6%9C%80%E5%B0%8F%E7%94%9F%E6%88%90%E6%A0%91/Prim.png)

#### Sample

```
6 10 ABCDEF
A B 6
A C 1
A D 5
B C 5
B E 3
C D 5
C E 6
C F 4
D F 2
E F 6

```

#### Out

```
∞ 6 1 5 ∞ ∞ 
6 ∞ 5 ∞ 3 ∞ 
1 5 ∞ 5 6 4 
5 ∞ 5 ∞ ∞ 2 
∞ 3 6 ∞ ∞ 6 
∞ ∞ 4 2 6 ∞ 

C-5->B
A-1->C
F-2->D
B-3->E
C-4->F
```

### Kruskal算法

```cpp
/*带权无向图最小生成树kruskal算法*/
#include<iostream>
#include<algorithm>
#define INF INT32_MAX
#define _MAX 21
using namespace std;
typedef struct Arc{
    int weight=0;//带权图记录权重,无权图记录是否链接
}Martix[_MAX][_MAX];//从(1,1)开始存
typedef struct Martrix_Graph{
    char vertex[_MAX];//顶点的值
    Martix Mar;//邻接矩阵
    int _vertex,_arc;//顶点数量,弧数量
}*M_G;
typedef struct KRUSKAL_Store{
    int arc_tail;/*弧尾*/
    int arc_head;/*弧头*/
    int weight;
}*Kruskal_Store;
bool visited[_MAX]={false};/*标记是否已经访问*/
inline int find(M_G graph,char item){//查找当前值对应的序号,用于确定其在邻接矩阵的行或列
    for(int i=1;i<=graph->_vertex;++i){
        if(graph->vertex[i]==item){
            return i;
        }
    }
    return 0;
}
inline bool cmp(KRUSKAL_Store a,KRUSKAL_Store b){
    if(a.weight<b.weight){
        return true;
    }
    return false;
}
void Create_UDN(M_G &graph){//创建带权无向图
    graph=new Martrix_Graph;
    cout<<"顶点数量: ";
    cin>>graph->_vertex;
    cout<<"弧数量: ";
    cin>>graph->_arc;
    cout<<"输入顶点的值:"<<endl;
    for(int i=1;i<=graph->_vertex;++i){
        cin>>graph->vertex[i];
    }
    for(int i=0;i<_MAX;++i){
        for(int j=0;j<_MAX;++j){
            graph->Mar[i][j].weight=INF;
        }
    }
    cout<<"输入弧尾,弧头和弧的权重:"<<endl;
    char v1,v2;
    int weight;//v1弧头,v2弧尾,即从v2指向v1,带权
    for(int i=1;i<=graph->_arc;++i){
        cin>>v2>>v1>>weight;
        if(find(graph,v2)==0||find(graph,v1)==0){
            exit(-1);
        }
        graph->Mar[find(graph,v2)][find(graph,v1)].weight=weight;
        graph->Mar[find(graph,v1)][find(graph,v2)].weight=weight;
    }
}
void Read_arc(M_G graph,Kruskal_Store &k){/*读入所有的弧并对弧长进行排序*/
    int temp=1;
    for(int i=1;i<=graph->_vertex;++i){
        for(int j=i;j<=graph->_vertex;++j){
            if(graph->Mar[i][j].weight!=INF){
                k[temp].arc_tail=i;
                k[temp].arc_head=j;
                k[temp].weight=graph->Mar[i][j].weight;
                ++temp;
            }
        }
    }
    sort(k+1,k+1+graph->_arc,cmp);
}
void Init(M_G graph,int *parent){
    for(int i=0;i<=graph->_arc;++i){
        parent[i]=i;/*初始化自身为自身的根*/
    }
}
int find_parent(int *parent,int root){
    if(parent[root]!=root){
        parent[root]=find_parent(parent,parent[root]);/*路径压缩*/
    }
    return parent[root];
}
void union_root(int *parent,int rootx,int rooty){
    int x=find_parent(parent,rootx);
    int y=find_parent(parent,rooty);
    if(x==y){
        return;
    }
    parent[x]=y;/*根合并*/
}
void kruskal(M_G graph,Kruskal_Store k,int *parent){
    int count=1;/*计数器*/
    for(int i=1;i<=graph->_arc;++i){
        if(find_parent(parent,k[i].arc_head)!=find_parent(parent,k[i].arc_tail)){/*弧头和弧尾的根不同,说明弧头和弧尾不在一个集合中,将这两个根合并*/
            union_root(parent,k[i].arc_head,k[i].arc_tail);
            ++count;
            cout<<graph->vertex[k[i].arc_tail]<<"-"<<graph->Mar[k[i].arc_tail][k[i].arc_head].weight<<"->"<<graph->vertex[k[i].arc_head]<<endl;
            if(count==graph->_vertex){/*最小生成树的条件*/
            }
        }
    }
}
void print(M_G graph){
    for(int i=1;i<=graph->_vertex;++i){
        for(int j=1;j<=graph->_vertex;++j){
            if(graph->Mar[i][j].weight==INF){
                cout<<"∞"<<' ';
                continue;
            }
            cout<<graph->Mar[i][j].weight<<' ';
        }
        cout<<endl;
    }
    cout<<endl;
}
int main(){
    M_G graph=nullptr;
    Create_UDN(graph);
    Kruskal_Store k=new KRUSKAL_Store[graph->_arc+1];
    Read_arc(graph,k);
    int *parent=new int[graph->_arc+1];
    Init(graph,parent);
    print(graph);
    kruskal(graph,k,parent);
    delete []k;
    k=nullptr;
    delete []parent;
    parent=nullptr;
    delete graph;
    graph=nullptr;
    return 0;
}
```

#### 参考图

![](https://img.misaka.gq/Notes/algorithm/%E6%9C%80%E5%B0%8F%E7%94%9F%E6%88%90%E6%A0%91/Kruskal.png)

#### Sample

```
6 10 ABCDEF
A B 6
A C 1
A D 5
B C 5
B E 3
C D 5
C E 6
C F 4
D F 2
E F 6

```

#### Out

```
∞ 6 1 5 ∞ ∞ 
6 ∞ 5 ∞ 3 ∞ 
1 5 ∞ 5 6 4 
5 ∞ 5 ∞ ∞ 2 
∞ 3 6 ∞ ∞ 6 
∞ ∞ 4 2 6 ∞ 

A-1->C
D-2->F
B-3->E
C-4->F
B-5->C
```

## 串的模式匹配

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

时间复杂度极高,为![](https://latex.codecogs.com/gif.latex?O(n*m))

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

## 排序

![](https://img.misaka.gq/Notes/algorithm/sort.png)

### 冒泡排序

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

### 桶排序

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

### 计数排序

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

### 堆排序

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

### 直接插入排序

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

### 折半插入排序

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

### 归并排序

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

### 快速排序

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

### 基数排序

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

### 选择排序

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

### 希尔排序

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