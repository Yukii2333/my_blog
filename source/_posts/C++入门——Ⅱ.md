---
title: C++入门——Ⅱ
date: 2023-04-18 14:10:45
categories: C++
tags:
 - C++
description: Note about C++
cover:
---

# 函数

## 缺省参数

### 概念

缺省参数是函数在**声名或者定义时**为函数的**参数指定一个缺省值**。在调用函数时如果没有指定实参则采用该函数的缺省参数，否则使用指定的实参。

如下

~~~C++
#include<iostream>

using namespace std;

//给参数a给定一个缺省值10
int fun(int a = 10)
{
	return a *= 2;
}

int main()
{
    //实参为1，则形参用实参的值进行运算
	cout << fun(1) << endl;
    //为指定实参，则应参以缺省值进行运算
	cout << fun() << endl;
	return 0;
}
~~~

### 分类

#### 全缺省参数

跟名字一样，对**所有参数**都指定缺省值就是全缺省参数

~~~C++
//对所有参数都指定缺省值
void fun(int a = 10, int b = 20, int c = 30)
{
	count<<"a = "<<a<<endl;
    count<<"b = "<<b<<endl;
    count<<"c = "<<c<<endl;
}
~~~

#### 半缺省参数

半缺省并不只是值对半数的 参数指定缺省值，只要不是所有参数都指定了缺省值都是半缺省参数（所有都没指定那肯定不算了）

~~~C++
void fun(int a, int b = 20, int c = 30)
{
	count<<"a = "<<a<<endl;
    count<<"b = "<<b<<endl;
    count<<"c = "<<c<<endl;
}
~~~

### 补充

* 半缺省参数必须**从右往左依次**给出，不能间隔着给
* 缺省参数不能在函数声名和定义中同时出现

~~~C++
//a.h
void fun(int a = 10);

//a.cpp
void fun(int a = 10)
{}

//同时出现时编译器无法判断该用哪一个缺省值（即便是一样）
~~~

* 缺省值必须是常量或者全局变量
* C语言不支持（编译器不支持）

## 函数重载

### 概念

函数重载，是函数的一种特殊情况，C++允许在**同一作用域**声名几个功能类似的**同名函数**，这些函数的**形参列表（参数个数或类型或类型顺序）不同**，常用来处理实现功能类似数据类型不同的问题。

如

* 参数类型不同

~~~C++
#include<iostream>

using namespace std;
//a与b是整形
int Add(int a, int b)
{
	return a + b;
}
//a与b是浮点数
double Add(double a, double b)
{
	return a + b;
}

int main()
{
	cout << "Add_int:" << Add(1, 2) << endl;
	cout << "Add_double:" << Add(1.1, 2.2) << endl;
	return 0;
}
~~~

* 参数个数不同

~~~C++
#include<iostream>

using namespace std;
//两数相加
int Add(int a, int b)
{
	return a + b;
}
//三数相加
int Add(int a, int b, int c)
{
	return a + b + c;
}

int main()
{
	cout << "Add_two:" << Add(1, 2) << endl;
	cout << "Add_three:" << Add(1, 2, 3) << endl;
	return 0;
}
~~~

* 参数类型顺序不同

~~~C++
#include<iostream>

using namespace std;
//先是int，再是double
void Add(int a, double b)
{
	cout << "Add:" << a + b << endl;
}
//先是double，再是int
void Add(double a, int b)
{
	cout << "Add:" << a + b << endl;
}

int main()
{
	Add(1, 1.1);
	Add(1.1, 1);
	return 0;
}
~~~

### 原理

在C/C++中，一个程序的允许需要经过以下几个阶段：**预处理、编译、汇编、链接**。

C与C++的预处理、编译还有链接都是一样的，在汇编生成符号表时有一些区别。C语言生成的符号表中，函数是直接以函数名呈现，在链接时通过函数名来查找函数的地址；而C++生成的符号表中，**不仅有函数名，还有函数的参数信息（函数名修饰规则）**，在链接时再根据函数名以及参数信息去查找函数的地址。

因此C语言不支持函数重载，因为同名函数不能进行区分；C++支持函数重载，因为可以通过参数信息的不同来进行区分。

### 补充

函数是否构成重载**与参数名称、函数类型、函数的返回类型无关**

# 引用

## 概念

**引用**不是定义一个新变量，而是**给已存在变量取了一个别名**，编译器不会为引用变量开辟内存和空间，它和它引用的变量**共用同一块空间**。

比如：李逵，在家称“铁牛”，江湖人称“黑旋风”。

## 实例

用法：

**类型& 引用变量名（别名）= 引用实体**

~~~C++
#include<iostream>

using namespace std;

int main()
{
	int a = 1;
    //理解成a有了另一个名字，叫b
    //但是a与b都是指同一个人（变量）
	int& b = a;
	return 0;
}

//！！！引用类型必须和实体是同种类型
~~~

## 特性

* 引用变量在**定义时**必须**初始化**
* 一个变量可以有多个引用
* 对引用变量的操作等价于对实体的操作
* 引用变量一旦引用某个实体，就不能再引用其他实体

~~~C++
//一个变量可以有多个引用
#include<iostream>

using namespace std;

int main()
{
	int a = 1;
	int& b = a;
	int& c = a;
	return 0;
}
~~~

~~~C++
#include<iostream>

using namespace std;

int main()
{
	int a = 1;
	int& b = a;
	int& c = a;
    int x = 0;
    //此处并不是在更改c所引用的实体，而是在赋值
    c = x;
	return 0;
}
~~~

* 引用不能扩大引用变量的权限，只能平移或者缩小

~~~C++
//权限平移
#include<iostream>

using namespace std;

int main()
{
	const int a = 1;
	//a具有常属性，而引用变量b是int类型，权限大于a，因而会报错
    int& b = a;
    //引用常属性的参数应该这样写
    //const int& b = a;
    //此时引用变量b与a同类型，权限相同，即权限平移
	return 0;
}
~~~

~~~C++
//权限缩小
#include<iostream>

using namespace std;

int main()
{
	int a = 1;
    //b是具有常属性，权限小于a，即权限缩小
	const int& b = a;
    //可以理解成不同的身份权限不同
	return 0;
}
~~~

## 用途

### 做参数

以C语言早期经常犯错误的Swap函数为例，在C语言里，将函数的参数定义成整形，然后对函数的参数进行交换，结束后发现主函数内的两数并没有进行交换。然后我们就知道了：形参是实参的一份拷贝，实参的改变不影响实参；想影响实参就要用对应类型的指针，然后对指针进行操作。

~~~C++
#include<iostream>

using namespace std;

//在C语言中用的Swap函数
//void Swap(int* x, int* y)
//{
//	int tmp = *x;
//	*x = *y;
//	*y = tmp;
//}

//C++的引用做参数的Swap
void Swap(int& x, int& y)
{
	int tmp = x;
	x = y;
	y = tmp;
}

int main()
{
	int a = 10;
	int b = 20;
	cout << a << " " << b << endl;
	Swap(a, b);
	cout << a << " " << b << endl;
	return 0;
}
~~~

功能上来说，与指针是一样的，但是引用做参数还是简化了一部分的代码，而且传参时不用每次都去取地址

### 做返回值

做参数还可以认为影响不是特别大，但返回值的一些情况就很舒服了，先说简单的

~~~C++
#include<iostream>

using namespace std;

//返回类型为返回值的一个引用
//此处就是c的一个引用
//因而也可以把Add看做n的一个别名
int& Add(int a, int b)
{
	static int c = a + b;
	return c;
}

int main()
{
    //ret就是add的别名，即n的别名
	int& ret = Add(1, 2);
	cout << "Add:" << ret << endl;
	return 0;
}
~~~

直接看可能看不出什么猫腻，在C语言的学习中我们知道，传值返回的函数在返回时，**并不是直接将值返回**，而是先将值拷贝到一个空间内（**创建临时变量**），因为函数在内存上是在栈区的，执行函数时函数在栈顶，而函数结束后会**销毁栈帧**，此时再来访问返回值时由于栈帧已经销毁，**可能是随机值也可能是正确的值**。为了保证稳定性，都会在返回时创建临时变量，栈帧销毁后再将临时变量的值拷贝给用于接收的变量。而引用作返回值是直接返回返回值的别名，根本不需要拷贝，节省了空间，提升了效率。

但是，正如前文提到的，栈帧销毁后函数内的变量也会销毁，那么返回**已经销毁的变量的别名**不危险吗？答案是显而易见的**很危险**，就好比野指针，但是上面的代码需要返回别名的变量是静态的，即存储在内存中的静态区，**栈区上函数栈帧的销毁不会影响静态区**，因此不会对引用的返回造成影响（传值返回无论是返回栈区的内容还是静态区的内容都会创建临时变量）。

可能还是觉得还是没啥大用途，还觉得麻烦？那么下面这个将直接颠覆认知

在数据结构的学习时，我们写过一种结构叫做线性表，其中的每个功能都需要一个单独的函数来实现，而引用做返回值可以直接一下代替多个函数，如

~~~C++
#include<iostream>
#include<assert.h>

using namespace std;

//线性表的结构体
typedef struct SList
{
    //方便距离就直接定义了大小
	int a[100];
	int size;
}ST;

//引用做返回值
int& Get(int pos,ST& ps)
{
    assert(pos>=0 && pos<=100);
	return ps.a[pos];
}

int main()
{
	ST s;
    //假设已经初始化了s，线性表s的第一个数据为0
	s.a[0] = 0;
    
    //取第一个位置的值
	int a = Get(0, s);
	cout << a << endl;
    //更改第一个位置的值
	Get(0, s) = 1;
    //打印第一个位置的值
	cout <<"a[pos]:" << Get(0, s) << endl;
	return 0;
}
~~~

之前在线性表里，改指定位置值、查看指定位置的值也是两个函数了，而现在一个函数就代替了两个函数，并且函数内容也不多，而且在C++中，结构体也是一个类，可以在结构体内定义函数，因而有了更简单的用法

~~~C++
#include<iostream>
#include<assert.h>

using namespace std;

struct SList
{
	int a[100];
	int size;
    //结构体内的函数
	int& Get(int pos)
	{
		assert(pos >= 0 && pos <= 100);
		return a[pos];
	}
}ST;


int main()
{
    //C++中结构体也是上升到了类
    //定义了结构体后可以直接用结构体的名称来创建结构体
	SList s;
	s.a[0] = 0;
    
	int a = s.Get(0);
	cout << a << endl;
	s.Get(0) = 1;
	cout << "a[pos]:" << s.Get(0) << endl;
	return 0;
}
~~~

## 总结

* 基本任何场景都可以用引用传参
* 谨慎用引用做返回值。出了函数作用域，对象不在了，就不能用引用返回，还在就可以用引用返回
* 引用的使用还要注意权限的问题。权限只能被平移与缩小，不能被放大

## 补充

**语法层面上**：指针会开辟空间来存储指向空间的地址；而引用不会开辟空间，因为引用是对变量取别名

**从底层汇编角度看**：引用是类似指针的方式来实现的

### 引用和指针的不同点

* 引用概念上定义一个变量的别名，指针存储一个变量的地址
* 引用在定义时**必须初始化**，指针没有要求
* 引用在初始化时引用一个实体后，就**不能再引用其他实体**，而指针可以在任何时候指向任何一个同类型的实体
* **没有NULL引用**，但是有NULL指针
* 在sizeof中的含义不同：**引用**的结果为**引用类型的大小**，但是**指针**始终是**地址空间所占字节个数**（32位平台下占4字节）
* 引用自加即引用的实体增加1，指针自加即指针向后偏移一个类型的大小
* 有多级指针，但是**没有多级引用**
* 访问实体的方式不同，**指针需要解引用，引用编译器自己会处理**
* 引用比指针用起来更安全

