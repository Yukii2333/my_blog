---
title: C++类与对象——Ⅰ
date: 2023-04-22 19:18:34
categories: C++
tags:
 - C++
 - Class
description: Note about Class of C++
cover:
---

# 类与对象 Ⅰ

## 面向过程和面向对象初步认识

* C语言是**面向过程**的，**关注**的是**过程**，分析出求解问题的步骤，通过函数的调用逐步解决问题。

​	拿洗衣服来说，C语言关注的是洗衣服的：拿盆子->放水->放衣服->放洗衣粉······->拧干->晾衣服



* C++是**基于面向对象**的，**关注**的是**对象**，将一件事情拆分成不同的对象，靠对象间的交互完成。

​	对于C++而言，不会去关注洗衣服的过程，而是去关注参与该过程的对象：人、衣服、洗衣粉······

## 类

在**C语言**中，结构体内部**只能定义变量**，而在**C++**中，结构体内部不仅可以**定义变量**，还可以**定义函数**，譬如上一篇博客中写到的函数：

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

其中就是用了C++中的特性。

不过在C++中，由于结构体也是一种类，因而为了有区分，在C++中更喜欢用**class**来代替。

## 类的定义

类的定义类似结构体

~~~C++
//class为类定义关键字，classname为类名
class classname
{
  //类体：由成员变量和成员函数组成
    
  
};//不要忘记分号
~~~

类体中的内容称为**类的成员**，类中的**变量**称为**类的属性**或成员变量；类中的**函数**称为**成员函数**。

类的定义方式有两张，在此之前，需要了解**内联函数**

### 内联函数

#### 概念

以**inline修饰**的函数叫做内联函数，**编译时**C++编译器会在**调用内联函数的地方展开**，没有函数调用建立栈帧的开销，内联函数提升程序运行效率。

#### 实现

如

~~~C++
//一般形式
int Add(int a, int b)
{
	return a + b;
}

int main()
{
	int ret = Add(1, 2);
	return 0;
}
~~~

将上面的函数改为内联函数为

~~~C++
//由inline修饰
inline int Add(int a, int b)
{
	return a + b;
}

int main()
{
	int ret = Add(1, 2);
	return 0;
}
~~~

在编译期间，会对调用 Add 函数的地方进行替换并展开

**注意**：

* 在release模式下，可以通过查看编译器生成的汇编代码中是否存在call Add来查看是否实现内联
* 在debug模式下，需要对编译器进行设置，否则不会展开（在debug模式下，编译器默认不会对代码进行优化，以下为vs2022的设置方式）

1. 右击项目，点击属性

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/setting.png" alt="操作1" title="操作1" style="zoom:95%;" />

2. 点击 **C/C++**一栏，然后将**调试信息格式**设置为如图所示的

![操作2](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/setting3.png "操作2")

3. 再点击下面的**优化**，将内联函数扩展更改为**只适用于 _inline (/Ob1)**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305182000019.png" alt="操作3" title="操作3" style="zoom:80%;" />

**反汇编码**

未修改和没有用inline修饰的应该是一样的，就用未修改的图了

![反汇编码](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/%E5%8F%8D%E6%B1%87%E7%BC%96%E7%A0%81.png "反汇编码")

通过标出出来的指令可以看出来，在此处**调用了函数**（也就是**开辟了栈帧**）

而修改后的应该是这样

![反汇编码](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/%E5%8F%8D%E6%B1%87%E7%BC%96%E7%A0%812.png "反汇编码")

可以看出，经inline修饰的Add函数直接在主函数进行了**展开**，运算也在主函数内部进行，即，没有开辟栈帧。

#### 特性

* 内联函数是一种**以空间换时间**的方式（函数内联减少栈帧开辟与销毁），如果编译器将函数当做内联函数处理，在**编译阶段，会用函数替换函数调用**，因而也有一个缺陷，就是**可能使目标文件变大**，但毕竟换来了一定的运行效率。

* **inline**对于编译器而言**只是一个建议**，**不同的编译器关于inline的实现机制可能不同**，一般情况下，将**函数规模小**（大概几行代码的函数，不过具体取决于编译器）、**不是递归**、**频繁调用**的函数用inline来修饰，否则编译器会忽略inline特性。（《C++primer》第五版写到**内联说明只是向编译器发出一个请求，编译器可以选择忽略这个请求。**）
* inline不建议**声名与函数分离**，会导致**链接错误**。因为inline被展开，就没有了函数地址，链接就会找不到。

#### 补充

从上面的信息来看，内联函数与宏有几分类似，我们知道宏的优点有：**增强代码的复用性；提高性能**，而缺点就是：**不方便调试；使得代码可读性、可维护性差，容易误用；没有类型安全检查**。而内联函数虽然牺牲了一定的空间，但可读性更高、性能也相比原代码有所提升。



### 类的两种定义方式

* 声名和定义全部放在类体中，此时需要注意一点：**成员函数定义在类中定义**，编译器可能会将其当做**内联函数**处理

~~~C++
class stu
{
    //public和private在下面一点会提到，先不用在意
public:
	void who()
	{
		cout << name_ << "-" << sex_ << "-" << age_ << endl;
	}
private:
	//分别是名字、性别、年龄
	char* name_;
	char* sex_;
	int age_;
};
~~~



* 类的声名放在 .h 文件中，成员函数的定义放在 .cpp 文件中，要注意，**成员函数名前需要加类名**

~~~C++
//.h文件
class stu
{
public:
	void who();
private:
	//分别是名字、性别、年龄
	char* name_;
	char* sex_;
	int age_;
};
~~~

~~~C++
//.cpp文件
//在意类名不要忘记
void  stu::who()
{
	cout << name_ << "-" << sex_ << "-" << age_ << endl;
}
~~~

平时的练习为了简便可以使用第一种，但是在**工作中以及项目中尽量使用第二种**。

### 成员函数命名规则

先看下面一段代码

~~~C++
class Date
{
public:
	void Init(int year)
	{
        //可以发现这里的可读性就很差了
        //从前面的学习知道，局部优先，因而这里是局部的year
        //即两个year都是形参的year
		year = year;
	}
private:
	int year;
};
~~~

为了代码的可读性一般是这样写

~~~C++
class Date
{
public:
	void Init(int year)
	{
		year_ = year;
	}
private:
    //声明时在成员变量前或者后加一个单下划线
	int year_;
};
~~~

还有另一种写法

~~~C++
class Date
{
public:
	void Init(int year)
	{
		mYear = year;
	}
private:
    //声明时在成员变量前加一个m
    //m是member的首字母
	int mYear;
};
~~~

## 类的访问限定符以及封装

### 限定符

C++实现封装的方式：用**类将对象的属性与方法结合在一块，让对象更完整，通过访问权限选择性的将其接口提供给外部的用户使用**。

访问限定符有三种：

**public（公有）、protected（保护）、private（私有）**

#### 说明

* public修饰的成员在类外可以直接被访问
* protected和 private修饰的成员在类外不能直接被访问
* 访问权限的**作用域从该访问限定符出现的位置开始到下一个访问限定符出现时为止**
* 如果没有访问限定符，作用域就到 '}' ，即，类结束
* class的**默认访问权限**为private，而struct的默认访问权限为public（因为C++兼容C）

**限定符只在编译时有用，当数据映射到内存后，没有任何访问限定符上的区别**



如下面的例子，由于成员变量被private修饰，因而在外部就不能直接访问成员变量了（vs2022的提示里没有 year_）

![例图](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/20230423162821.png "例图")

而在C语言中是能够直接访问的

### 封装

#### 概念

封装，即**将数据和操作数据的方法进行有机结合，隐藏对象的属性和实现细节，仅对外公开接口来和对象进行交互**。

封装的**本质是一种管理，让用户更方便使用此类**。



*就比如我们日常使用的电脑，我们点点鼠标敲敲键盘就能完成很多事，但实际上电脑内真正工作的是CPU、显卡、内存等硬件。对于计算机的使用者而言，并不关心计算机的内部结构（如CPU是如何随机的，主板是如何布局的等），用户只需要知道怎么开机、怎么通过键盘和鼠标与计算机进行交互即可。因此计算机在出场时，在外部套壳，奖内部隐藏，仅仅对外提供开关机、鼠标以及键盘插孔等，让用户能够与计算机交互即可。*

### 类的作用域

**类定义了一个新的作用域**，类的所有成员都在类的作用域中。在类体外定义成员时，需要使用  :: 作用域操作符来指明成员属于哪个类域。

~~~C++
class stu
{
public:
	void who();
private:
	//分别是名字、性别、年龄
	char* name_;
	char* sex_;
	int age_;
};

//指定下面函数属于哪一个域
void  stu::who()
{
	cout << name_ << "-" << sex_ << "-" << age_ << endl;
}
~~~

### 类的实例化

#### 概念

**用类类型创建对象的过程，称为类的实例化**

#### 理解

* **类是对对象进行描述的**，是一个**模型**一样的东西，限定了类有哪些成员，定义出一个类并**没有分配实际的内存空间**来存储它；就比如房子的蓝图，可以用来描述该房子的信息，但是本身只是一张图纸，并不能住人。

~~~C++
class Person
{
public:
	int age_;
};

int main()
{
	Person.age_ = 100;
	return 0;
}

//error C2059: 语法错误:“.”
~~~

* 一个类可以实例化出多个对象，**实例化出的对象占用实际的物理空间，存储类的成员变量**

~~~C++
class Person
{
public:
	int age_;
};

int main()
{
	Person a;
	Person b;
	Person c;
	return 0;
}
~~~

* 类的实例化就好比**根据蓝图建造出了房子**，而**占据空间的是实例化出来的房子**，而不是图纸

## 类对象模型

### 类对象的大小计算

#### 类对象的存储方式

在一个类中，既有成员函数又有成员变量，倘若类的大小为成员函数与成员变量所占空间的大小之和，那会发现很不现实，因为**一个类能够实例化多个对象**，如果每一个都这样算那所占用的空间也太大了，不如直接类似C语言那样把函数写在外面。

因此，有：

* **对象内只存放成员变量，而成员函数存放在公共的代码段**
* **一个类对象的大小就是该类中“成员变量”之和，但是要注意内存对齐（与结构体一样）**

在通过 sizeof 计算类对象的大小时，会发现，空类（没有成员函数，也没有成员变量）的大小并不为0，原因是因为**编译器给了空类一个字节来唯一标识这个类对象**，即消耗一字节来占位，表示对象的存在，但不存储有效数据

## this指针

### 引入

看下面一个日期类

~~~C++
class Date
{
public:
	void Init(int year, int month, int day)
	{
		year_ = year;
		month_ = month;
		day_ = day;
	}
	void Print()
	{
		cout << year_ << "-" << month_ << "-" << day_ << endl;
	}
private:
	int year_;     //年
	int month_;    //月
	int day_;      //日
};

int main()
{
	Date d1, d2;
	d1.Init(2022, 1, 1);
	d1.Print();
	d2.Print();
	return 0;
}
~~~

在Date类中有Init与Print两个成员函数，函数体中并没有关于不同对象的区分，那当d1调用Init函数时，该函数是如何知道应该设置d1对象而不是d2呢？（前面我们已经知道了成员函数存放在公共代码区，也就是说，d1，d2调用同名函数的话其实也是同一个函数）

在C++中通过引入this指针来解决该问题，即，**C++编译器给每个“非静态的成员函数”添加了一个隐藏的指针参数，让该指针指向当前对象（函数运行时调用该函数的对象），在函数体中所有“成员变量”的操作，都是通过该指针去访问。只不过所有的操作对用户是透明的，即，用户不需要来传递，编译器自动来完成。**

简而言之，就是编译器进行了一个类似C语言中传参取地址的操作。

### 特性

* **this指针类型：类类型* const ***（比如上面的，就是 Date* const this），**即，成员函数中，不能给this指针赋值**。

~~~C++
	void Init(int year, int month, int day)
	{
        //先不论后面，在此处修改就会报错
		this = nullptr;
		year_ = year;
		month_ = month;
		day_ = day;
	}
//error C2106: “=”: 左操作数必须为左值
~~~

* 只能在**成员函数内部**使用
* this指针**本质上是成员函数的形参**，当对象调用成员函数时，将对象地址作为实参传递给this形参。所以**对象中不存储this指针**，this指针作为形参存储在**栈区**
* vs下对this指针传递进行了优化，对象地址是放在ecx，而ecx存储this指针的值

~~~C++
class Date
{
public:
	void Init(int year, int month, int day)
	{
        //虽然不能修改this指针的值，但是可以查看
		cout << this << endl;
		year_ = year;
		month_ = month;
		day_ = day;
	}
	void Print()
	{
		cout << year_ << "-" << month_ << "-" << day_ << endl;
	}
private:
	int year_;
	int month_;
	int day_;
};

int main()
{
	Date d1;
	d1.Init(2022, 1, 1);
    //此处打印d1的地址会发现与this指针存储的地址一样
	cout << &d1 << endl;
	d1.Print();
	return 0;
}
~~~

* this指针是**成员函数的第一个隐含指针形参，一般情况下由编译器通过ecx自动传递，不需要用户传递**

* this指针**可以为空**

例：

~~~C++
class A
{
public:
	void Print()
	{
		cout << "Print()" << endl;
	}
private:
	int a_;
};

int main()
{
    //p为空指针
    //nullptr跟C语言中的NULL一样，但是C++里的NULL有了值（值为0）
    //在C++中尽量使用nullptr来表示空指针
	A* p = nullptr;
	p->Print();
	return 0;
}
~~~

会发现成功运行并打印，再看一下下面的代码作为区分

~~~C++
class A
{
public:
	void Print()
	{
        //此处是差异
		cout << a_<< endl;
	}
private:
	int a_;
};

int main()
{
	A* p = nullptr;
	p->Print();
	return 0;
}
~~~

这个也能运行但是并不能成功打印并正常结束，两个样例中，p都为空，传递给this指针的值也为空，但是第一个样例只是进行了字符串的打印，与this是否为空没有关系；而第二个打印的是对象中的成员变量，但是由于this是空指针，因而程序中断了。

因此，this可以为空，但是能否运行取决于成员函数内部是否访问**this指针指向的内容**。
