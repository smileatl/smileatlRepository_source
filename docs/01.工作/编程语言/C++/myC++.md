---
title: myC++
date: 2023-06-01 20:01:18
permalink: /language/C++/myC++
categories:
  - C++
  - 学习笔记
tags:
  - C++
author:
  name: xugaoyi
  link: https://github.com/xugaoyi
---





#  自己的知识点



返回值不可以作为重载的区分



## 引用

本质：**引用的本质在c++内部实现是一个指针常量.**
$\textcolor{red}{指针常量：指针的指向不可修改，指针指向的值是可以改变的}$

```C++
//发现是引用，转换为 int* const ref = &a;
void func(int& ref){
	ref = 100; // ref是引用，转换为*ref = 100
}
int main(){
	int a = 10;
    
    //自动转换为 int* const ref = &a; 指针常量是指针指向不可改，也说明为什么引用不可更改
	int& ref = a; 
	ref = 20; //内部发现ref是引用，自动帮我们转换为: *ref = 20;
    
	cout << "a:" << a << endl;
	cout << "ref:" << ref << endl;
    
	func(a);
	return 0;
}
```

![1663253977101](myC++.assets/1663253977101.png)





## new , delete

堆区程序员手动开辟，也需要手动释放，不delete掉的话会造成内存泄漏

利用new创建的数据，会返回该数据对应的类型的指针

```cpp
//利用new关键字  可以将数据开辟到堆区
//利用new创建的数据，会返回该数据对应的类型的指针
//指针  本质也是局部变量，放在栈区，指针保存的数据是放在堆区
int* a = new int(10);

//释放
delete a;

//释放数组 delete 后加 []
delete[] arr;
```

指针：本质也是局部变量，放在栈区
指针保存的数据放在堆区

当我们使用未初始化内存的指针时：
会导致程序无法进行，因为指针并没有指向一个合法的地址，这时候其内部存的只是一些乱码，所以在调用函数时，会使用乱码所指的内存，指针根本就无权访问，导致出错。
解决办法：new 为指针动态分配内存，之后再用delete将其释放





容器中*it是什么是

```cpp
for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
        //*it就是int
		cout << *it << " ";
	}
```



## STL

### vector容器

emplace_back具有比push_back更好的性能

#### 底层

大家都知道对于普通数组，一旦定义了大小就不能改变，例如int a[10];，这个数组a至多只能放10个元素，改不了的。对于动态数组，就是可以不用关心初始时候的大小，可以随意往里放数据，那么耗时的原因就在于动态数组的底层实现。动态数组为什么可以不受初始大小的限制，可以随意push_back数据呢？

**首先vector的底层实现也是普通数组**。vector的大小有两个维度一个是size一个是capicity，size就是我们平时用来遍历vector时候用的，例如：

```text
for (int i = 0; i < vec.size(); i++) {

}
```

而capicity是vector底层数组（就是普通数组）的大小，capicity可不一定就是size。当insert数据的时候，如果已经大于capicity，capicity会成倍扩容，但对外暴漏的size其实仅仅是+1。

那么既然vector底层实现是普通数组，怎么扩容的？就是重新申请一个二倍于原数组大小的数组，然后把数据都拷贝过去，并释放原数组内存。（对，就是这么原始粗暴的方法！）

举一个例子，如图： ![vector原理](myC++.assets/20201218185902217.png)

原vector中的size和capicity相同都是3，初始化为1 2 3，此时要push_back一个元素4。那么底层其实就要申请一个大小为6的普通数组，并且把原元素拷贝过去，释放原数组内存，**注意图中底层数组的内存起始地址已经变了**。

**同时也注意此时capicity和size的变化，关键的地方我都标红了**。



### set容器

- 所有元素插入时都会被自动排序，默认从小到大
- 容器内的各个元素都互不相等
- 自己主动插入重复的数字会被去掉

```cpp
s.begin()  // 正序迭代器
s.rbegin()  // 逆序迭代器，相当于正序的最后一个数
```



### unordered_set容器

- 等价为无序的set_容器

### map容器

- map中所有的元素都是pair
- pair中第一个元素为key（键值），起到索引作用，第二个元素为value（实值）
- 所有元素都会根据元素的键值自动排序

### unordered_map容器

- 等价为无序的map容器，其它都一样

### stack容器

- stack是一种**先进后出**(First in Last Out)的数据结构，它只有一个出口

 构造函数：

- `stack<T> stk;`                                 //stack采用模板类实现， stack对象的默认构造形式
- `stack(const stack &stk);`            //拷贝构造函数

赋值操作：

- `stack& operator=(const stack &stk);`           //重载等号操作符

数据存取：

- `push(elem);`      //向栈顶添加元素
- `pop();`                //从栈顶移除第一个元素
- `top(); `                //返回栈顶元素

大小操作：

- `empty();`            //判断堆栈是否为空
- `size(); `              //返回栈的大小

### queue容器

Queue是一种**先进先出**（First In First Out，FIFO）的数据结构

构造函数：

- `queue<T> que;`                                 //queue采用模板类实现，queue对象的默认构造形式
- `queue(const queue &que);`            //拷贝构造函数

赋值操作：

- `queue& operator=(const queue &que);`           //重载等号操作符

数据存取：

- `push(elem);`                             //往队尾添加元素
- `pop();`                                      //从队头移除第一个元素
- `back();`                                    //返回最后一个元素
- `front(); `                                  //返回第一个元素

大小操作：

- `empty();`            //判断堆栈是否为空
- `size(); `              //返回栈的大小

## STL-函数对象

### 1 函数对象

#### 1.1 函数对象概念

**概念：**

- 重载**函数调用操作符**的类，其对象常称为**函数对象**
- **函数对象**使用重载的()时，行为类似函数调用，也叫**仿函数**

**本质：**

函数对象(仿函数)是一个**类**，不是一个函数

#### 1.2  函数对象使用

**特点：**

- 函数对象在使用时，可以像普通函数那样调用, 可以有参数，可以有返回值
- 函数对象超出普通函数的概念，函数对象可以有自己的状态
- 函数对象可以作为参数传递



### 2  谓词

#### 2.1 谓词概念

**概念：**

- 返回bool类型的仿函数称为**谓词**
- 如果operator()接受一个参数，那么叫做一元谓词
- 如果operator()接受两个参数，那么叫做二元谓词



## 指针

### 指针与常量

```cpp
int x;
int * p1 = &x; //指针可以被修改，值也可以被修改
const int * p2 = &x; //指针可以被修改，值不可以被修改（const int），常量指针
int * const p3 = &x; //指针不可以被修改（* const），值可以被修改，指针常量
const int * const p4 = &x; //指针不可以被修改，值也不可以被修改
```

### 指针函数与函数指针

```cpp
// addition是指针函数，一个返回类型是指针的函数
int* addition(int a, int b) {
	int* sum = new int(a + b);
	return sum;
}
int subtraction(int a, int b) {
	return a - b;
}
int operation(int x, int y, int (*func)(int, int)) {
	return (*func)(x, y);
}

int* m = addition(1, 2);
// minus是函数指针，指向函数的指针
int (*minus)(int, int) = subtraction;
int n = operation(3, *m, minus);
```

### 指向指针的引用

在函数调用时用指针或者引用做参数，表示把变量的地址传递给子函数，
但是子函数只能修改指针所指变量的值，并不能修改指针的指向。
==如果想要修改指针的指向，就要用指针的指针，或者指针的引用。==



## 字符串

```cpp
string s; // 字符串
s[i]; // 字符串中取出来单个字符还是字符串
```



## 其他知识点

return static_cast<bool>(L.length);  //c++运算符，类型转换，将一个表达式转换为某种类型，但没有运行时的类型检查来保证转换的安全



 cerr<<"error"<<endl;  //输出到标准错误的ostream对象，常用于程序错误信息；



size_t：size type，一种用来记录大小的数据类型，表示C中任何对象所能达到的最大长度，它是无符号整数。size_t在32位系统上定义为 unsigned int，也就是32位无符号整型，4字节。在64位系统上定义为 unsigned long ，也就是64位无符号整形，8字节。





### NULL和nullptr的区别

在C语言中，NULL：
是一个宏，被定义为空指针，定义如下：#define NULL ((void *)0) 
NULL实际上是一个空指针，如果在C语言中写入以下代码，编译是没有问题的，因为在C语言中把空指针赋给int和char指针的时候，发生了隐式类型转换，把void指针转换成了相应类型的指针。

```c
int  *pi = NULL;
char *pc = NULL;
```

C++程序中的NULL，因为C++是强类型语言，void*是不能隐式转换成其他类型的指针的，所以实际上编译器提供的头文件做了相应的处理：

```cpp
#ifdef __cplusplus
#define NULL 0
#else
#define NULL ((void *)0)
#endif
```

可见，在C++中，NULL实际上是0。因为C++中不能把void*类型的指针隐式转换成其他类型的指针，所以为了结果空指针的表示问题，C++引入了0来表示空指针，这样就有了上述代码中的NULL宏定义。
但是实际上，用NULL代替0表示空指针在函数重载时会出现问题，程序执行的结果会与我们的想法不同，举例如下：

```cpp
#include <iostream>
using namespace std;
 
void func(void* i)
{
	cout << "func1" << endl;
}
 
void func(int i)
{
	cout << "func2" << endl;
}
 
void main(int argc,char* argv[])
{
	func(NULL);
	func(nullptr);
	getchar();
}
```

![1666951796042](myC++.assets/1666951796042.png)

在这段代码中，我们对函数func进行可重载，参数分别是void*类型和int类型，但是运行结果却与我们使用NULL的初衷是相违背的，因为我们本来是想用NULL来代替空指针，但是在将NULL输入到函数中时，它却选择了int形参这个函数版本，所以是有问题的，这就是用NULL代替空指针在C++程序中的二义性。

为解决NULL代指空指针存在的二义性问题，在C++11版本(2011年发布)中特意引入了nullptr这一新的关键字来代指空指针，从上面的例子中我们可以看到，使用nullptr作为实参，确实选择了正确的以void*作为形参的函数版本。

**总结：**NULL在C++中就是0，这是因为在C++中void* 类型是不允许隐式转换成其他类型的，所以之前C++中用0来代表空指针，但是在重载整形的情况下，会出现上述的问题。所以，C++11加入了nullptr，可以保证在任何情况下都代表空指针，而不会出现上述的情况，因此，建议以后还是都用nullptr替代NULL吧，而NULL就当做0使用。





### auto

就是根据后面的值，来自己推测前面的类型是什么。
作用就是为了简化变量初始化，如果这个变量有一个很长很长的初始化类型，就可以用auto代替。
用auto声明的变量必须初始化（auto是根据后面的值来推测这个变量的类型，如果后面没有值，自然会报错）





C++的for循环中++i和i++的区别：
最终输出的结果没有区别。
二者的实现代码如下：

```cpp
   A  operator ++()         //前++
    {
        i=i+1;
        return *this;
    }
 
    A  operator ++(int)      //后++
    {
        A t=*this;          //先保存一份变量
        ++(*this);          //调用前++
        return t;
     }
```

可以发现，i++需要一个暂时变量，然后将i加1后，返回的是暂时变量。而++i就是自增后返回i。
所以在**空间损耗**上，**i++要略高于++i**，因此，**在不影响代码逻辑的前提下，要尽量使用++i**。





### 无穷大与无穷小

如果是int，可以用INT_MAX表示正无穷，INT_MIN表示负无穷，需要包含limits.h。
如果是double，可以用DBL_MAX表示正无穷，-DBL_MAX表示负无穷(注意不是DBL_MIN)，需要包含float.h。







返回值很奇怪，一般是没有初始化。



![1679407169616](myC++.assets/1679407169616.png)

逻辑都正确，出现不应该出现的error，很可能是数组访问下标越界。



![1679402810832](myC++.assets/1679402810832.png)

要把int改为long



### constexpr

`constexpr` 是 C++11 引入的关键字，用于声明一个编译期常量表达式。在编译期间，编译器会对 `constexpr` 声明的表达式进行求值，并将其结果作为常量使用。使用 `constexpr` 可以让编译器在编译期间进行更多的优化，从而提高程序的性能。

除了用于声明编译期常量表达式之外，`constexpr` 还可以用于修饰函数和构造函数。当一个函数或构造函数被声明为 `constexpr` 时，它们必须满足一定的条件，比如函数中只能包含一些简单的语句，不能有分支语句等。这样，编译器就可以在编译期间对这些函数进行求值，从而提高程序的性能。



### extern

在C++中，`extern`是一个关键字，用于指示一个变量或函数是在其他文件中定义的。具体来说，如果一个变量或函数被声明为`extern`，那么编译器将在其他文件中查找该变量或函数的定义。这通常用于在多个源文件中共享变量或函数的定义。例如，如果你在一个源文件中定义了一个全局变量，并且想在另一个源文件中使用该变量，你可以在第二个源文件中使用`extern`关键字来引用该变量，而不需要重新定义它。

例如，假设你在一个源文件中定义了一个全局变量`int num = 10;`，你可以在另一个源文件中使用`extern int num;`来引用该变量，而不需要重新定义它。这样，编译器将在其他文件中查找该变量的定义，然后将其链接到你的程序中。

注意，`extern`关键字只是一个声明，它并不会分配内存或定义变量或函数。它只是告诉编译器该变量或函数在其他文件中定义，需要在链接时进行解析。



### template

C++中的模板（template）是一种通用的编程工具，可以让我们编写可以处理多种数据类型的代码。模板可以用于函数、类和类成员函数，它们可以接受任意类型的参数，包括基本数据类型、自定义类型和STL容器等。使用模板可以提高代码的复用性和灵活性，可以减少代码量，提高代码的可读性和可维护性。

#### 函数模板

建立一个通用函数，其函数返回值类型和形参类型可以不具体制定，用一个**虚拟的类型**来代表。

**语法：** 

```C++
template<typename T>
函数声明或定义
```

**解释：**

template  ---  声明创建模板

typename  --- 表面其后面的符号是一种数据类型，可以用class代替

T    ---   通用的数据类型，名称可以替换，通常为大写字母



#### 类模板

类模板作用：

- 建立一个通用类，类中的成员 数据类型可以不具体制定，用一个**虚拟的类型**来代表。

**语法：** 

```c++
template<typename T>
类
```

**解释：**

template  ---  声明创建模板

typename  --- 表面其后面的符号是一种数据类型，可以用class代替

T    ---   通用的数据类型，名称可以替换，通常为大写字母

类模板在模板参数列表中可以有默认参数



### explict

`explicit` 是 C++ 中的一个关键字，用于修饰构造函数，表示该构造函数只能用于显式地创建对象，而不能隐式地进行类型转换。举个例子，如果有一个类 A，它有一个带有一个参数的构造函数，如果这个构造函数被声明为 `explicit`，那么就不能像下面这样隐式地进行类型转换：

```
A a = 1; // 错误，不能隐式地进行类型转换
A a(1); // 正确，可以显式地创建对象
```

如果该构造函数没有被声明为 `explicit`，那么上面的代码就会编译通过，因为编译器会自动把整型转换成类 A 的一个对象。`explicit` 关键字可以避免这种情况的发生，使代码更加明确和安全。



### static

C++中的`static`关键字有多种用法：

1. 声明静态变量：在函数内部使用`static`关键字声明的变量将成为静态变量，其生命周期将与程序的生命周期相同，而不是与函数的生命周期相同。
2. 声明静态成员变量：在类中使用`static`关键字声明的变量将成为静态成员变量，它们属于类而不是属于类的任何实例。静态成员变量在所有类的实例之间共享。
3. 声明静态成员函数：在类中使用`static`关键字声明的函数将成为静态成员函数，它们不属于任何特定的实例，可以直接通过类名调用。
4. 声明静态局部变量：在函数内部使用`static`关键字声明的变量将成为静态局部变量，其生命周期将与程序的生命周期相同，而不是与函数的生命周期相同。静态局部变量只能在声明的函数内部访问。

总的来说，`static`关键字的作用是限制变量或函数的作用域，并且可以使变量或函数在程序的生命周期内保持状态。



# 常用实例 

## 1、函数与库的对应

1、max()、min()、abs()、swap()、reverse()、sort()、find()、count()在<algorithm>

2、内建函数对象，greater<>()、less<>()在<functional>

3、accumulate在<numeric>

4、unordered_map在<unordered_map>，unordered_set在<unordered_set>

## 2、常用操作

bool型数据输出true/false，而并不是1/0

```cpp
cout << boolalpha << ss.search(nums, target) << endl;
```



字符空格用‘ ’表示



## 3、数组

### 动态创建数组并输出

```cpp
int n;   //输入数组长度
cin >> n;
int* p;  //声明一个指针
p = new int[n]; /*创建了一个长度为n的动态数组，并且返回这个数组的首地址给p，p就指向了这个动态数组，可以通过指针p来操作数组，因为创建的动态数组并没有名字，只返回了首地址给p，所以可以把p看作是这个动态数组的名字 */
for (int i = 0; i < n; i++) {
	cin >> p[i];
}
for (int i = 0; i < n; i++) {
    cout << p[i] << " ";
}
delete[]p;  //释放这个一维的动态数组，而不是释放指针p。用完以后要记得释放掉
```

```cpp
//无注释版
int n;
cin >> n;
int* p = new int[n];
for (int i = 0; i < n; i++) {
	cin >> p[i];
}
for (int i = 0; i < n; i++){
    cout << p[i] << " ";
}
delete[]p;
```



## 4、vector

### vector创建不定长的一组数据

```cpp
vector<int> createVector() {
	vector<int> ivec;
	int num;
	do
	{
		cin >> num;
		ivec.push_back(num);
	} while (getchar() != '\n'); //检测到换行键时，停止输入
	return ivec;
}
//返回值vector<int>是要返回vector容器，int则是返回整数

vector<int> createVector() {
	vector<int> nums;
	int tmp;
	while(cin >> tmp){
		nums.push_back(tmp);
		if(cin.get() == '\n') break;
	}
	return nums;
}
```

> 注：
> a. 以空格为间隔输入数组
> b. 输入完成，回车必须紧跟最后一个数字，若回车前是空格，则此回车会像空格一样被忽略

### 手动创建vector容器嵌套容器

```cpp
int main()
{
	vector<vector<int>> intervals = { {1, 2},{2, 3},{3, 4},{1, 3} };
	printVectorInVector(intervals);

	system("pause");
	return 0;
}
```

### vector容器打印

```cpp
void printVector(vector<int>& v) {
    for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
        cout << *it << " ";
    }
    cout << endl;
}

void printVector(vector<int>& v) {
    for (int i = 0; i<v.size(); ++i) {
        cout << v[i] << " ";
    }
    cout << endl;
}
```

### vector容器嵌套容器的打印

```cpp
void printVectorInVector(vector<vector<int>>& v) {
	//通过大容器，把所有数据遍历一遍
	for (vector<vector<int>>::iterator it = v.begin(); it != v.end(); it++) {
		//(*it) ---- 容器 vector<int>
		for (vector<int>::iterator vit = (*it).begin(); vit != (*it).end(); vit++) {
			cout << *vit << " ";
		}
		cout << endl;
	}
	//每一个小容器输出，输出完了做一个换行的操作
}
```

### 求容器中的最大最小值

**方法：**
`min_element`和`max_element`
输入参数为`vector`迭代器，输出为单一元素迭代器，要获得变量需要加*

```cpp
#include <iostream>
#include <vector>

#include <algorithm>
using namespace std;

int main(){
    vector<int> a = { 2,4,6,7,1,0,8,9,6,3,2 };
    auto maxPosition = max_element(a.begin(), a.end());
    auto minPosition = min_element(a.begin(), a.end());
    cout << *maxPosition << " at the postion of " << maxPosition - a.begin() <<endl;
    cout << *minPosition << " at the postion of " << maxPosition - a.begin() <<endl;
    system("pause");
    return 0;
}
```

![1670400864834](myC++.assets/1670400864834-1670400864939.png)

## 5、字符串

### 输入一个不带空格的字符串

```c++
string str;
cin >> str;
```

### 输入一个带空格的字符串

```cpp
string str;
getline(cin, str);
```

### 常用函数

substr

```cpp
// substr有2种用法：
// 假设：string s = "0123456789";

string sub1 = s.substr(5); //只有一个数字5表示从下标为5开始一直到结尾：sub1 = "56789"

string sub2 = s.substr(5, 3); //从下标为5开始截取长度为3位：sub2 = "567"
```



stoi
string->int
作用是将 n 进制的字符串转化为十进制，使用时包含头文件string.
定义如下：

```cpp
int stoi( const std::string& str, std::size_t* pos = nullptr, int base = 10 );

参数：
str - 待转换的字符
pos - 其取值可以是一个空字符，在这种情况下，pos未被使用；另外如果pos不是空指针，函数将pos的值设置为str中数字后面的第一个字符的位置。
base - 字符中数字的进制，默认为10进制，如果base取值为0，则进制由字符串中的格式决定。
```




## 6、链表

### 尾插法创建不定长的单向链表（带头结点）

```cpp
//尾插法创建不定长的单向链表（带头结点）
void createListTail(ListNode* L) {
    //r、L指向头结点
	ListNode* r = L;
	do {
		ListNode* p = new ListNode;
		cin >> p->val;  //循环创建p，输入结点数据
		p->next = nullptr;
		r->next = p;
		r = p;
	} while (getchar() != '\n');
}
```

### 尾插法创建不定长的单向链表（不带头结点）

```cpp
//不带头结点
void createListTail(ListNode* L) {
    //r、L直接指向首元结点而不是头结点
	ListNode* r = L;
    //第一次先输入首元结点的数据
	cin >> r->val;
	do {
		ListNode* p = new ListNode;
		cin >> p->val;  //循环创建p，输入结点数据
		p->next = nullptr;
		r->next = p;
		r = p;
	} while (getchar() != '\n');
}
```

```cpp
//尾插法创建不定长的单向链表
//不带头结点
//返回值类型直接为ListNode*
ListNode* createListTail() {
	ListNode* L= new ListNode;
	ListNode* r = L;
	cin >> r->val;
	do {
		ListNode* p = new ListNode;
		cin >> p->val;  //循环创建p，输入结点数据
		p->next = nullptr;
		r->next = p;
		r = p;
	} while (getchar() != '\n');
	return L;
}

//new出来的L，并且返回了，记得delete掉
delete L;
```



### 输出链表元素（带头结点）

```cpp
//输出链表元素
void printList(ListNode* L) {
	ListNode* p = L->next;
	while (p) {
		cout << p->val<<" ";
		p = p->next;
	}
	cout << endl;
}
```

### 输出链表元素（不带头结点）

```cpp
//输出链表元素
void printList(ListNode* L) {
	ListNode* p = L->next;
	while (p) {
		cout << p->val<<" ";
		p = p->next;
	}
	cout << endl;
}
```

## 7、树

### 根据数组，层次法创建二叉树

```cpp
#include <vector>

//根据数组，层次法创建二叉树
TreeNode* createTree(vector<int>& nums, int i) //层次法创建二叉树
{
    if (i >= nums.size() || nums[i] == 0) //数值为0或超出数组范围
        return nullptr;
    TreeNode* root = new TreeNode(nums[i]);
    root->left = createTree(nums, i * 2 + 1);
    root->right = createTree(nums, i * 2 + 2);
    return root;
}

int main()
{
    //创建所使用的数组
    vector<int> nums = { 3,9,20,0,0,15,7 };
    TreeNode* root = createTree(nums, 0);
    levelOrderIter(root);

    system("pause");
    return 0;
}
```



### 二叉树的遍历，并输出

```cpp
#include <queue>

//层次遍历，迭代法
void levelOrderIter(TreeNode* root) {
    queue<TreeNode*> que;
    que.push(root);
    while (!que.empty()) {
        cout << que.front()->val << " ";
        if (que.front()->left != nullptr) {
            que.push(que.front()->left);
        }
        if (que.front()->right != nullptr) {
            que.push(que.front()->right);
        }
        que.pop();
    }
    cout << endl;
}

//中序遍历，迭代法  左-根-右
void InOrderIter(TreeNode *root, vector<int> &nodes)
{ 
    stack<TreeNode *> stk;
    while (root || !stk.empty())
    {
        while (root)
        {
            stk.push(root);
            root = root->left;
        }
        root = stk.top();
        stk.pop();
        nodes.emplace_back(root->val);
        root = root->right;
    }
}


int main()
{
    vector<int> nums = { 3,9,20,0,0,15,7 };
    TreeNode* root = createTree(nums, 0);
    levelOrderIter(root);

    system("pause");
    return 0;
}
```

## 数学

### 公倍数与公因数

```

```

