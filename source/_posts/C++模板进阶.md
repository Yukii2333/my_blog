---
title: C++模板进阶
date: 2023-08-22 16:09:21
categories: C++
tags:
 - C++
 - template
description:
cover:
---

# 非类型模板参数

**模板参数分类类型形参与非类型形参，类型形参即：出现在模板参数列表中，跟在class或者typename之类的参数类型名称，非类型形参，就是用一个常量作为类(函数)模板的一个参数，在类(函数)模板中可将该参数当成常量来使用。**

~~~C++
template<class T, size_t N = 10>
class array
{
public:
	T& operator[](size_t index)
    {
        return _array[index];
    }
	const T& operator[](size_t index)const
    {
        return _array[index];
    }	
	size_t size()const
    {
        return _size;
    }
	bool empty()const
    {
        return 0 == _size;
    }	
private:
	T _array[N];
	size_t _size;
};
~~~

有点类似缺省值，但实则不相同。且：

* **浮点数、类对象以及字符串是不允许作为非类型模板参数的。**
* **非类型的模板参数必须在编译期就能确认结果。**

# 类模板的特化

## 概念

**使用模板可以实现一些与类型无关的代码，但对于一些特殊类型的可能会得到一些错误的结果，需要特殊处理**，比如常用用于的比较的模板函数

~~~C++
template<class T>
bool Less(T l, T r)
{
    return l<r;
}
~~~

一般的定义就如上，当然数与数或者重载了比较的类使用起来都是没有问题的，但是当是类的指针时，进行比较时虽然也不能说错，但是不符合我们的设计要求，程序会根据指针所指向的地址来进行比较，而不是指针存储的内容。

~~~C++
template<class T>
bool Less(T l, T r)
{
    return l<r;
}

int main()
{
    //正常比较正确
    cout<<Less(1.2)<<endl;
    //类中重载了运算符，也能进行正确比较
    Date d1(2022, 7, 7);
	Date d2(2022, 7, 8);
	cout << Less(d1, d2) << endl;
    //指针不会被当成类来处理，而进行地之间比较
    Date* p1 = &d1;
	Date* p2 = &d2;
	cout << Less(p1, p2) << endl;
        
    return 0;
}
~~~

此时，**就需要对模板进行特化**。即：**在原模板类的基础上，针对特殊类型所进行特殊化的实现方式。模板特 化中分为函数模板特化与类模板特化。**

## 函数模板特化

函数模板的特化步骤： 

1. 必须要先有一个基础的函数模板 
2. 关键字template后面接一对空的尖括号<> 
3. 函数名后跟一对尖括号，尖括号中指定需要特化的类型 
4. 函数形参表: 必须要和模板函数的基础参数类型完全相同，如果不同编译器可能会报一些奇怪的错误。

~~~C++
template<class T>
bool Less(T l, T r)
{
    return l<r;
}

//针对Date*的特化版本
template<>
bool Less(Date* l, Date* r)
{
    return *l < *r;
}

int main()
{
    //正常比较正确
    cout<<Less(1.2)<<endl;
    //类中重载了运算符，也能进行正确比较
    Date d1(2022, 7, 7);
	Date d2(2022, 7, 8);
	cout << Less(d1, d2) << endl;
    //此处再调用函数时就不会再去通过模板生成了，而是调用特化版本
    Date* p1 = &d1;
	Date* p2 = &d2;
	cout << Less(p1, p2) << endl;
        
    return 0;
}
~~~

**一般情况下如果函数模板遇到不能处理或者处理有误的类型，为了实现简单通常都是将该函数直接给出。**

~~~C++
bool Less(Date* l, Date* r)
{
    return *l < *r;
}
~~~

一方面书写更容易，另一方面代码的可读性也更高。

## 类模板特化

### 全特化

全特化即是将模板参数列表中所有的参数都确定化。

~~~C++
template<class T1, class T2>
class Data
{
public:
	Data() {cout<<"Data<T1, T2>" <<endl;}
private:
	T1 _d1;
	T2 _d2;
};

template<>
class Data<int, char>
{
public:
	Data() {cout<<"Data<int, char>" <<endl;}
private:
	int _d1;
	char _d2;
 };

void TestVector()
{
	Data<int, int> d1;
	Data<int, char> d2;
} 
~~~

### 偏特化

将任何针对模版参数进一步进行条件限制设计的特化版本

~~~C++
template<class T1, class T2>
class Data
{
public:
	Data() {cout<<"Data<T1, T2>" <<endl;}
private:
	T1 _d1;
	T2 _d2;
};

//部分特化
template <class T1>
class Data<T1, int>
{
public:
	Data() {cout<<"Data<T1, int>" <<endl;}
private:
	T1 _d1;
	int _d2;
}; 

//进一步限制

//偏特化为指针类型
template <typename T1, typename T2>
class Data <T1*, T2*>
{
public:
	Data() {cout<<"Data<T1*, T2*>" <<endl;}
private:
	T1 _d1;
	T2 _d2;
};

//偏特化为引用类型
template <typename T1, typename T2>
class Data <T1&, T2&>
{
public:
	Data(const T1& d1, const T2& d2)
	: _d1(d1)
	, _d2(d2)
{
	cout<<"Data<T1&, T2&>" <<endl;
}
private:
	const T1 & _d1;
	const T2 & _d2;
};
~~~

# 模板的分离编译

## 概念

一个程序（项目）由若干个源文件共同实现，而每个源文件单独编译生成目标文件，最后将所有目标文件链 接起来形成单一的可执行文件的过程称为分离编译模式。

## 模板的分离编译

在此前使用模板编写程序时，会发现当将模板的声明与定义分离时就会发生错误，如：

~~~C++
//.h文件
template<class T>
bool Less(const T& l,const T& r);

//.cpp文件
template<class T>
bool Less(const T& l,const T& r)
{
    return l < r;
}
~~~

用该种书写方式就会发生报错，原因是因为模板函数在编译时没有进行实例化（编译是每个文件单独进行的），但是由于没有语法错误此时不会报错，而进行链接时就会因为没有实例化的对应函数导致出错。

**解决办法：**

1. **将声明和定义放到一个文件 "xxx.hpp" 里面或者xxx.h**。推荐使用这种。 
2. **模板定义的位置显式实例化。**这种方法不实用，不推荐使用。

# 总结

**模板的优点：**

* 模板复用了代码，节省资源，更快的迭代开发，C++的标准模板库(STL)因此而产生。
* 增强了代码的灵活性。

**模板的缺点：**

* 模板会导致代码膨胀问题，也会导致编译时间变长。
* 出现模板编译错误时，错误信息非常凌乱，不易定位错误。
