---
title: C++string
date: 2023-08-11 16:12:20
categories: C++
tags:
 - C++
 - STL
 - String
description: 
cover:
---

# 认识string

## string的定位

在C语言中，字符串是以`\0`结尾的一些字符结合，为了便于对字符串的操作，C语言提供了一系列str库函数，根据模拟实现这些库函数不难发现，这些库函数都与字符串息息相关，这不符合OOP（Object Oriented Programming——面向对象编程）思想而且底层需要用户自己管理，很容易就出现边界问题（譬如越界），因而在C++中就有了string。

## 初步了解

**string是一个对字符串操作管理的类**，底层大致是一个类似C语言学习时写的通讯录，也就是动态数组，经过封装后，用户调用所需的函数来使用即可，底层的维护由string类自身来完成。string类是由basic_string模板类实例化而来，使用char类型来实例化basic_string。详细内容可以查阅[string文档](https://legacy.cplusplus.com/reference/string/string/)。

# string常用接口（函数）

## 构造函数

| **接口名称**             | **功能说明**                       |
| ------------------------ | ---------------------------------- |
| string()                 | 构造空的string类对象，即空字符串   |
| string(const char* s)    | 用字符串s来构造string类对象        |
| string(size_t n, char c) | 用n个字符c来初始化string类对象     |
| string(const string& s)  | 用string类对象来初始化string类对象 |

~~~C++
void test()
{
    string s1();                 //构造空的对象，括号可以省略
    string s2("Hello world!");   //用字符串构造对象
    string s3(6,'a');              //用6个a构造对象
    string s4(s2);				 //用s2构造s4
}
~~~

## 容量操作

| **接口名称**                                                 | **功能说明**                                   |
| ------------------------------------------------------------ | ---------------------------------------------- |
| [size()](https://legacy.cplusplus.com/reference/string/string/) | 返回字符串的有效长度                           |
| [length()](https://legacy.cplusplus.com/reference/string/string/length/) | 返回字符串的有效长度                           |
| [capacity()](https://legacy.cplusplus.com/reference/string/string/capacity/) | 返回总空间大小                                 |
| [empty()](https://legacy.cplusplus.com/reference/string/string/empty/) | 字符串为空返回true否则返回false                |
| [clear()](https://legacy.cplusplus.com/reference/string/string/clear/) | 清空字符串（不释放空间，区分delete）           |
| [resize(size_t n, char c)](https://legacy.cplusplus.com/reference/string/string/resize/) | 将有效字符的个数改成n个，多出的空间用字符c填充 |
| [reserve(size_t n = 0)](https://legacy.cplusplus.com/reference/string/string/reserve/) | 为字符串预留空间                               |

~~~C++
void test()
{
    string s("Hello World!");
    cout<<s.size()<<endl;           //输出s的有效字符个数（12），lenth()效果相同，一般常用size()
    cout<<s.capacity()<<endl;       //输出s的容量（根据编译器不同结果也会有所不同）
    string s1(s);
    s.clear();                      //清空s
    if(s1.empty())                  //empty()返回类型为bool
    {
        cout<<"s1 empty!"<<endl;
    }
    if(s.empty())
    {
        cout<<"s empty!"<<endl;
    }
    s1.resize(3,'a');                  //将s1的字符数改为3个，并用a覆盖，string类中还有一个重载函数，参数只有size_t n，多出的空间用0填充
    s.reserve(5);                    //将s的字符数改为5，多余的直接舍去
}
									  //resize()传入值小于size()则会将多余数据“清除”（在n位置下一个位置放入'\0'），而reserve()不会
~~~

##  修改操作

| **接口名称**                                                 | **功能说明**                                               |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| [push_back(char c)](https://legacy.cplusplus.com/reference/string/string/push_back/) | 在字符串后尾插字符c                                        |
| [append()](https://legacy.cplusplus.com/reference/string/string/append/) | 在字符串后追加一个字符串                                   |
| [operator+=()](https://legacy.cplusplus.com/reference/string/string/operator+=/) | 在字符串后追加字符串str                                    |
| [c_str()](https://legacy.cplusplus.com/reference/string/string/c_str/) | 返回char*格式字符串                                        |
| [find()](https://legacy.cplusplus.com/reference/string/string/find/)和[npos](https://legacy.cplusplus.com/reference/string/string/npos/) | 从字符串pos位置开始往后找字符c，返回该字符在字符串中的位置 |
| [rfind()](https://legacy.cplusplus.com/reference/string/string/rfind/) | 从字符串pos位置开始往前找字符c，返回该字符在字符串中的位置 |
| [substr()](https://legacy.cplusplus.com/reference/string/string/substr/) | 在str中从pos位置开始，截取n个字符，然后将其返回            |

~~~C++
void test()
{
    string s;
    s.push_back('a');                          //尾差字符a
    s+="bcd";								   //追加字符串bcd
    cout<<s.c_str()<<endl;					   //c_str()返回char*类型字符串（实则动态数组的地址）
    if(s.find('e')==(size_t)-1)				   //find()返回size_t类型的值，找到返回器对应下标，未找到返回npos
    {
        cout<<"don't find it!"<<endl;
    }
}
~~~

**npos**是string内的一个静态成员变量，为`static const size_t npos = -1`，虽然初始化为 -1，但由于size_t是无符号整形，因而反而是一个极大的值，通常情况下size也不会有如此之大，因而作为查找时未查找到的返回值。

**append()**的重载函数有如下几种

~~~C++
string& append (const string& str);
string& append (const string& str, size_t subpos, size_t sublen);
string& append (const char* s);
string& append (const char* s, size_t n);
string& append (size_t n, char c);

template <class InputIterator>  //迭代器插入，迭代器在下面会有提到
string& append (InputIterator first, InputIterator last);
~~~

由于功能也类似操作符重载的+=，因而通常也更多的使用`oparetor+=()`。

**substr()**的功能是截取字符串，声明为`string substr (size_t pos = 0, size_t len = npos) const`，长度len缺省值为npos，表示不传len或者len较大（len+pos大于size）时，会从pos位置到结尾全部进行截取。其在实际使用时出现也较少。

**find()**的几种重载函数如下

~~~C++
size_t find (const string& str, size_t pos = 0) const;	
size_t find (const char* s, size_t pos = 0) const;
size_t find (const char* s, size_t pos, size_t n) const;
size_t find (char c, size_t pos = 0) const;
~~~

根据声明不难发现，find()不仅仅只能查找单个字符，还能查找整个字符串，rfind()与之类似。

## 遍历操作

| **接口名称**                                                 | **功能说明**                                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [operator[]](https://legacy.cplusplus.com/reference/string/string/operator%5B%5D/) | 返回pos位置的字符，const string类对象调用                    |
| [begin](https://legacy.cplusplus.com/reference/string/string/begin/)+[end](https://legacy.cplusplus.com/reference/string/string/end/) | begin获取第一个字符的迭代器 ，end获取最后一个字符下一个位置的迭 代器 |
| [rbegin](https://legacy.cplusplus.com/reference/string/string/rbegin/)+[rend](https://legacy.cplusplus.com/reference/string/string/rend/) | rbegin获取第一个字符前一个位置的迭代器 ，rend获取最后一个字符的迭 代器 |
| 范围for                                                      | C++11支持更简洁的范围for的新遍历方式                         |

~~~C++
void test()
{
    string s("Hello world!");
    //用方括号遍历字符串，运算符重载
    for (int i = 0; i < s.size(); ++i)
    {
        cout << s[i];
    }
    cout << endl;
    //迭代器遍历
    string::iterator it = s.begin();
    while (it != s.end())
    {
        cout << *it;
        ++it;
    }
    cout << endl;
    //反向迭代器遍历
    string::reverse_iterator r_it = s.rbegin();
    while (r_it != s.rend())
    {
        cout << *r_it;
        ++r_it;
    }
    cout << endl;
    //范围for遍历
    for (auto a_it : s)
    {
        cout << a_it;
    }
    cout << endl;
}
~~~

关于迭代器，在后面会深入学习，string中的迭代器实则就是指针，但**并不是所有迭代器都是指针**。

## 非成员函数

| **接口名称**                                                 | **功能说明**   |
| ------------------------------------------------------------ | -------------- |
| [operator+ ](https://legacy.cplusplus.com/reference/string/string/operator+/) | 加运算符重载   |
| [operator>>](https://legacy.cplusplus.com/reference/string/string/operator%3E%3E/) | 输入运算符重载 |
| [operator<<](https://legacy.cplusplus.com/reference/string/string/operator%3C%3C/) | 输出运算符重载 |
| [getline](https://legacy.cplusplus.com/reference/string/string/getline/) | 获取一行字符串 |
| [relational operators ](https://legacy.cplusplus.com/reference/string/string/operators/) | 大小比较       |

~~~C++
void test()
{
	string s1("Hello ");
	string s2("World!");
    //超载的+由于是传值返回，效率较低，更建议使用+=
	string s3 = s1 + s2;
	cout << s3 << endl;
	
    //从命令行输入字符串，当读到'\n'时停止读取
	getline(cin, s3);
	cout << s3 << endl;
    
    //从命令行输入字符串，当读取到'\n'或'\0'时停止
	cin >> s3;
	cout << s3 << endl;
    
	//relational operators包括字符串间的比较（以长度为比较方式）
	if (s1 > s2)
	{
		cout << s1 << endl;
	}
	else
	{
		cout << s2 << endl;
	}
}
~~~

# string的结构

## vs下的结构

**string总共占28个字节，内部结构先是有一个联合体，联合体用来定义string中字 符串的存储空间：**

*  当字符串长度小于16时，使用内部固定的字符数组来存放 
* 当字符串长度大于等于16时，从堆上开辟空间

~~~C++
union _Bxty
{ // storage for small buffer or pointer to larger one
 value_type _Buf[_BUF_SIZE];
 pointer _Ptr;
 char _Alias[_BUF_SIZE]; // to permit aliasing
} _Bx;
~~~

这种设计虽然看似有些浪费空间，也确实在目前多数使用情况下字符串长度没有超过16，但是这样带来的好处就是一定程度上提高了效率，而且也不用纠结初始空间应该开多大，开太大浪费空间，太小又频繁扩容降低效率。

其次：**还有一个size_t字段保存字符串长度，一个size_t字段保存从堆上开辟空间总的容量 **

最后：**还有一个指针做一些其他事情。**

![string结构](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202308121726026.png "监视窗口")

监视窗口下，红框并不是string的真实结构，是编译器为了便于查看对其进行了优化，string的实际结构是黄框内的内容。

## g++下的结构

**G++下，string是通过写时拷贝实现的，string对象总共占4个字节，内部只包含了一个指针**，该指针将来指向一块堆空间，内部包含了如下字段： 

* 空间总大小 
* 字符串有效长度 
* 引用计数

~~~C++
struct _Rep_base
{
 size_type _M_length;
 size_type _M_capacity;
 _Atomic_word _M_refcount;
};
~~~

* 指向堆空间的指针，用来存储字符串。

# string类的模拟实现

模拟实现只须实现常用功能即可，参考如下：

~~~C++
class string
{
public:
	typedef char* iterator;
public:
	//构造函数
	//单字符、字符串以及string类
	string(const char* str = "");
	string(const string& s);
	//赋值
	string& operator=(const string& s);

	//迭代器
	iterator begin();
	const iterator begin() const;
	iterator end();
	const iterator end() const;

	//修改
	void push_back(char c);
	string& operator+=(char c);
	void append(const char* str);
	string& operator+=(const char* str);
	void clear();
	void swap(string& s);
	const char* c_str()const;

	//取出
	char& operator[](size_t index);
	const char& operator[](size_t index)const;

	//比较
	const bool operator<(const string& s) const;
	const bool operator<=(const string& s) const;
	const bool operator>(const string& s) const;
	const bool operator>=(const string& s) const;
	const bool operator==(const string& s) const;
	const bool operator!=(const string& s) const;

	//查找
	size_t find(char c, size_t pos = 0) const;
	size_t find(const char* str, size_t pos = 0) const;

	//插入
	string& insert(size_t pos, char c);
	string& insert(size_t pos, const char* str);

	//删除
	string& erase(size_t pos, size_t len = -1);

	//析构
	~string()
	{
		delete[] _str;
		_capacity = 0;
		_size = 0;
	}
private:
	char* _str;
	size_t _capacity;
	size_t _size;
};
~~~



详细代码参考[string类模拟实现](https://github.com/Yukii2333/Daily-Code/tree/main/Works/Work_cpp/2023_07_10_string)，此处主要来说明常遇见的问题：

## 深浅拷贝

### 浅拷贝

​		**浅拷贝：也称位拷贝，编译器只是将对象中的值拷贝过来。如果对象中管理资源，最后就会导致多个对象共 享同一份资源，当一个对象销毁时就会将该资源释放掉，而此时另一些对象不知道该资源已经被释放，以为 还有效，所以当继续对资源进项操作时，就会发生发生了访问违规。**

​		通俗来说，拿模拟实现的string来讲，经过拷贝后，两个对象里面的与用于指向字符串地址的指针会指向同一块空间，也就是将被拷贝的对象里的地址赋值给拷贝后的对象，而这样不管是在后续操作还是最终释放都会出现问题。而解决方式之一就是深拷贝。

### 深拷贝

​		深拷贝在拷贝内容时，对于堆栈上的数据不是只拷贝其地址，而是在堆上新开辟一块空间，再将需要的内容拷贝进去，经过深拷贝后，两个对象会各自拥有一块独立的空间。

### 写时拷贝

​		写时拷贝就是一种拖延症，是在浅拷贝的基础之上增加了引用计数的方式来实现的。 

​		引用计数：用来记录资源使用者的个数。在构造时，将资源的计数给成1，每增加一个对象使用该资源，就给 计数增加1，当某个对象被销毁时，先给该计数减1，然后再检查是否需要释放资源，如果计数为1，说明该 对象时资源的最后一个使用者，将该资源释放；否则就不能释放，因为还有其他对象在使用该资源。

详细内容可以参考：[写时拷贝](https://coolshell.cn/articles/12199.html)







