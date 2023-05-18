---
title: C++类与对象——Ⅱ
date: 2023-05-01 16:27:55
categories: Notes
tags:
 - C++
 - Class
description: Note about Class of C++
cover:
---

# 类与对象 Ⅱ

## 类的6个默认成员函数

### 引入

如果一个类中什么成员都没有，那么这个类称为空类。

然而，空类中并非是 “空”，在任何成员都没有时，编译器会自动生成以下6个默认成员函数：

* 初始化和清理

**构造函数**：主要完成初始化工作

**析构函数**：主要完成清理工作

* 拷贝复制

**拷贝构造**：是使用同类对象初始化创建对象

**赋值重载**：赋值重载主要是把一个对象赋值给另一个对象

* 取地址重载

**取地址重载**：主要是普通对象和const对象取地址，这两个很少会自己实现

### 构造函数

#### 概念

构造函数是一个**特殊的成员函数，名字与类名相同，创建类类型对象时由编译器自动调用**，来保证每个数据成员有一个合适的初始值，并且，在**对象的整个生命周期内只调用一次**

譬如，下面的代码

~~~C++
class Date
{
public:
    //初始化函数
    //不传参会有默认值
	void Init(int year = 2022, int month = 12, int day = 1)
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
	int year_;
	int month_;
	int day_;
};

int main()
{
	Date d1;
	d1.Init(2022, 12, 1);
	d1.Print();

	Date d2;
	d2.Init();
	d2.Print();
	return 0;
}

//在调用Init函数时始终还是会有我们主动调用函数的情况
//而构造函数与InIT功能类似，但是能够被自动调用的函数（主动调用也行）
~~~

#### 特征

* 函数名与类名相同
* 无返回值
* 对象实例化时编译器**自动调用**对应的构造函数
* 构造函数可以重载

~~~C++
class Date
{
public:
    //此处就是一个构造函数
	Date()
	{
		year_ = 2022;
		month_ = 12;
		day_ = 1;
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
    //类里没有Init函数，也没有主动去调用构造函数
	Date d1;
	d1.Print();
	return 0;
}
//看下面的结果也可以看出来，在实例化类Date时自动调用了构造函数
//通常情况下我们显示定义的构造函数都喜欢写成全缺省函数
~~~



![运行结果](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305181939216.png "运行结果")

* 如果类中没有显示定义构造函数，则C++编译器会自动生成一个无参的默认构造函数，一旦用户显示定义编译器将不再生成

~~~C++
//class Date
{
public:
	//Date()
	//{
	//	year_ = 2022;
	//	month_ = 12;
	//	day_ = 1;
	//}
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
	d1.Print();
	return 0;
}
//编自动生成的构造函数不会去初始化内置类型、基本类型（char、int、double...）
//自定义类型（struct、class......）会去调用其默认构造
//虽然自定义类型看似不会被自动生成的构造函数初始化，但是自定义类型的底层还是内置类型、基本类型
//也就是说，自定义类型没有对应的构造函数依然会自动生成其对应的够造函数
//在某些编译器下自动生成的构造函数可能会处理内置类型，但那是个性化行为
//全是自定义类型时（且定义了默认构造函数）可以考虑让编译器自己生成
//有内置类型时都需要自己显示定义
~~~



![自动生成](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305181940600.png "自动生成")

*虽然自动生成的构造函数有点坑，但是在类中大多数类型是自定义类型的情况下，可以给内置类型在类声名时给一个默认值*

~~~C++
class Date
{
public:
	void Print()
	{
		cout << year_ << "-" << month_ << "-" << day_ << endl;
	}
private:
    //声名给默认值
	int year_ = 2022;
	int month_ = 12;
	int day_ = 1;
};

int main()
{
	Date d1;
	d1.Print();
	return 0;
}
~~~

* 无参的构造函数和全缺省的构造函数都称为默构造函数，并且默认构造函数**只能有一个**。注意：无参构造函数、全缺省构造函数、没显示定义编译器生成的构造函数，都可以认为是默认构造函数。

### 析构函数

#### 概念

与构造函数功能相反，析构函数不是完成对对象本身的销毁，局部对象的销毁工作是由编译器完成的。而**对象在销毁时会自动调用析构函数，完成对象中资源的清理工作。**

#### 特性

* 析构函数名是在类名前加**~**
* 无参数无返回值
* 一个类只能有一个析构函数。若为显示定义，系统会自动生成默认的析构函数，**析构函数不能重载**
* 对象生命周期结束时，C++编译系统会自动调用析构函数

~~~C++
class Stack
{
public:
    //构造函数
	stack(int capacity = 4)
	{
		a_ = (int*)malloc(sizeof(int) * capacity);
		if (a_ == nullptr)
		{
			perror("malloc fail");
			return;
		}
		capacity_ = capacity;
		top_ = 0;
	}
    //析构函数
    //由于在生命周期结束时才会被调用
    //因而存放在栈区的capacity以及top不用理会
	~Stack()
	{
		free(a_);
        //同理，其实a_不用置空也行
		a_ = nullptr;
	}
private:
	int* a_;
	int capacity_;
	int top_;
};
~~~

* 类中没有申请资源时，析构函数可以不写，直接使用编译器生成的默认析构函数（比如Date类）；有资源申请时一定要写，否则会造成内存泄漏（如Stack类）

### 拷贝构造函数

#### 概念

**只有单个形参**，该形参是**对本类类型对象的引用（一般常用const修饰）**，在用**已存在**的类类型对象**创建新对象时**由编译器自动调用。

#### 特征

* 拷贝构造函数是**构造函数的一个重载形式**
* 拷贝构造函数的**参数只有一个**且**必须是类类型对象的引用**，使用**传值方式编译器直接报错**，因为会引发无穷递归调用

~~~c++
class Date
{
public:
    //构造函数
	Date(int year, int month, int day)
	{
		year_ = year;
		month_ = month;
		day_ = day;
	}
    //拷贝构造函数
	Date(const Date& d)
	{
		year_ = d.year_;
		month_ = d.month_;
		day_ = d.day_;
	}
private:
	int year_ = 2022;
	int month_ = 12;
	int day_ = 1;
};

int main()
{
	Date d1(2023,12,1);
	Date d2(d1);
	return 0;
}

//写成 Date(const Date d)会引发无穷递归
//因为传值是将实参拷贝给形参，拷贝会调用拷贝构造，传值又会调用拷贝构造...
//因而会陷入无穷递归，不过一般编译器会报错
~~~

* 若未**显示定义**，编译器会生成**默认的拷贝构造函数**。默认的拷贝构造函数对对象按内存存储字节序完成拷贝，也叫做浅拷贝或者值拷贝。

*对于有指针的成员进行拷贝时，成员指针指向的空间地址会被直接拷贝，也就是拷贝和的成员内的指针与拷贝前的成员内的指针指向同一块空间，在使用以及空间释放时会存在问题，因而这种情况需要我们自己显示定义实现深拷贝*

### 赋值运算符重载

#### 运算符重载

C++为了增强代码可读性引入了运算符重载，**运算符重载是具有特殊函数名的函数**，也具有其返回值类型，函数名字以及参数列表，其返回类型与参数列表与普通函数类似

如：

~~~C++
class Date
{
public:
	Date(int year = 2022, int month = 12, int day = 1)
	{
		year_ = year;
		month_ = month;
		day_ = day;
	}
	Date(const Date& d)
	{
		year_ = d.year_;
		month_ = d.month_;
		day_ = d.day_;
	}
    //运算符重载用法就是 返回类型+operator+运算符(参数)
    //对与日期类大小比较，在C语言里一样也能实现，但是实现方式是调用一个函数
    //没有运算符重载这样来得直接方便以及易读
	bool operator>(const Date& d)
	{
		if (year_ < d.year_)
			return false;
		else if (year_ > d.year_)
			return true;
		else
		{
			if (month_ < d.month_)
				return false;
			else if (month_ > d.month_)
				return true;
			else
			{
				if (day_ > d.day_)
					return true;
				else
					return false;
			}
		}
	}
private:
	int year_;
	int month_;
	int day_;
};
~~~



![结果](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305181940519.png "结果")

**注意**：

* 不能通过连接其他符号来创建新的操作符，比如operator@.
* 重载操作符必须有一个类类型参数
* 用于内置类型的运算符，其含义不变，如整形+整形，运算符重载后也不会改变
* 作为类成员函数重载时，其形参看似比操作数少一个，因为成员函数的一个参数为隐藏的this
* .*    ::    sizeof    ?*    .    以上五个运算符不能重载

*定义在全局的操作符重载函数，会发现在调用时不能访问类类型中的成员变量，因为是private，想访问可以改成public（但是封装性不能得到保证）或者使用友元（后面会学到）*

#### 赋值运算符重载

**赋值运算符重载格式**

* **参数类型：**const T&，传递引用可以提高传递效率
* **返回类型**：T&，返回引用可以提高返回效率，有返回值的目的是为了支持连续赋值
* **检测是否自己给自己赋值**
* **返回*this**：要符合连续赋值的含义

~~~C++
class Date
{
public:
	Date(int year = 2022, int month = 12, int day = 1)
	{
		year_ = year;
		month_ = month;
		day_ = day;
	}
	Date(const Date& d)
	{
		year_ = d.year_;
		month_ = d.month_;
		day_ = d.day_;
	}
    //赋值运算符重载
	Date& operator=(const Date& d)
	{
		year_ = d.year_;
		month_ = d.month_;
		day_ = d.day_;
        //返回*this是为了符合'='的连续赋值
		return (*this);
	}

private:
	int year_;
	int month_;
	int day_;
};

int main()
{
	Date d1(2023,12,1);
	Date d2(d1);
	Date d3;
	Date d4;
    //连续赋值
	d4 = d3 = d2;
	return 0;
}
~~~

**赋值运算符只能重载成类的成员函数，不能重载成全局函数**

~~~C++
class Date
{
public:
	Date(int year = 2022, int month = 12, int day = 1)
	{
		year_ = year;
		month_ = month;
		day_ = day;
	}
	Date(const Date& d)
	{
		year_ = d.year_;
		month_ = d.month_;
		day_ = d.day_;
	}

	int year_;
	int month_;
	int day_;
};

//赋值运算符重载成全局函数，注意此处就没有了this指针，需要传两个参数
//error C2801: “operator =”必须是非静态成员
Date& operator=(Date& d1,const Date& d2)
{
	d1.year_ = d2.year_;
	d1.month_ = d2.month_;
	d1.day_ = d2.day_;
	return d1;
}

int main()
{
	Date d1(2023, 12, 1);
	Date d2(d1);
	Date d3;
	Date d4;
	d4 = d3 = d2;
	return 0;
}
~~~

*原因：赋值运算符如果不显示定义，编译器就会生成一个默认的。此时用户再在类外自己实现一个全局的赋值运算符重载，就和编译器在类中自动生成的冲突了，因而**赋值运算符重载只能是类的成员函数***

*《C++prime》第五版也提到了*



![p500](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202305181945819.png "p500")

**用户未显示定义时，编译器也会自动生成一个默认的赋值运算符重载，以值的方式逐字节拷贝**。内置类型成员变量是直接赋值的，而自定义成员变量需要调用对应类的赋值运算符重载完成赋值

*与拷贝构造函数类似，编译器给出的赋值运算符在拷贝时也是浅拷贝，因而在涉及资源管理就需要自己显示定义*

#### 前置++和后置++重载

~~~C++
class Date
{
public:
	Date(int year = 2022, int month = 12, int day = 1)
	{
		year_ = year;
		month_ = month;
		day_ = day;
	}
    //前置++重载
	Date& operator++()
	{
		day_ += 1;
		return *this;
	}
    
    //后置++重载
	Date operator++(int)
	{
		Date tmp = *this;
		day_ += 1;
		return tmp;
	}

private:
	int year_;
	int month_;
	int day_;
};

//前置++和后置++都是一元运算符
//为了让前置++和后置++能正确重载
//C++规定：后置++重载时多增加一个int类型的参数，但调用函数时该参数不用传递，编译器自动传递
//注意：后置++是先使用然后再+1，因此要返回+1前的值，故需要先存储旧值然后再+1，最后返回旧值
//后置++返回的是临时存储的旧值，只能以值的方式返回，不能引用返回
~~~

### 日期类的实现

[点击这里](https://github.com/Yukii2333/Daily-Code/tree/main/Works/Work_cpp/2023_04_26_class_date "日期类代码")即可查看
