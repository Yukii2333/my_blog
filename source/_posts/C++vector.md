---
title: C++vector
date: 2023-08-17 14:39:44
categories: C++
tags:
 - C++
 - STL
 - vector
description:
cover:
---

# 初步了解vector

## 关于vector

[相关文档](https://legacy.cplusplus.com/reference/vector/vector/)

1. vector是表示可变大小数组的序列容器。 
2. 就像数组一样，vector也采用的连续存储空间来存储元素。也就是意味着可以采用下标对vector的元素 进行访问，和数组一样高效。但是又不像数组，它的大小是可以动态改变的，而且它的大小会被容器自 动处理。 
3. 本质讲，vector使用动态分配数组来存储它的元素。当新元素插入时候，这个数组需要被重新分配大小 为了增加存储空间。其做法是，分配一个新的数组，然后将全部元素移到这个数组。就时间而言，这是 一个相对代价高的任务，因为每当一个新的元素加入到容器的时候，vector并不会每次都重新分配大 小。 
4. vector分配空间策略：vector会分配一些额外的空间以适应可能的增长，因为存储空间比实际需要的存 储空间更大。不同的库采用不同的策略权衡空间的使用和重新分配。但是无论如何，重新分配都应该是 对数增长的间隔大小，以至于在末尾插入一个元素的时候是在常数时间的复杂度完成的。 
5. 因此，vector占用了更多的存储空间，为了获得管理存储空间的能力，并且以一种有效的方式动态增 长。 
6. 与其它动态序列容器相比（deque, list and forward_list）， vector在访问元素的时候更加高效，在末 尾添加和删除元素相对高效。对于其它不在末尾的删除和插入操作，效率更低。比起list和forward_list 统一的迭代器和引用更好。

## vector的使用

主要还是参照[相关文档](https://legacy.cplusplus.com/reference/vector/vector/)

### 常用接口

#### vector的定义

| [构造函数声明](https://legacy.cplusplus.com/reference/vector/vector/vector/) | 接口说明                 |
| ------------------------------------------------------------ | ------------------------ |
| vector()                                                     | 无参构造                 |
| vector（size_type n, const value_type& val = value_type()）  | 构造并初始化n个val       |
| vector (const vector& x)                                     | 拷贝构造                 |
| vector (InputIterator first, InputIterator last)             | 使用迭代器进行初始化构造 |

~~~C++
void test()
{
    vector<int> v1;
    vector<int> v2(3,4);
    vector<int> v3(v2);
    vector<int> v4(v2.begin(),v2.end());
}
~~~

#### vector迭代器

| 迭代器（iterator）                                           | 接口说明                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [begin()](https://legacy.cplusplus.com/reference/vector/vector/begin/)+[end()](https://legacy.cplusplus.com/reference/vector/vector/end/) | 获取第一个数据位置的iterator/const_iterator， 获取最后一个数据的下一个位置的iterator/const_iterator |
| [rbgin()](https://legacy.cplusplus.com/reference/vector/vector/rbegin/)+[rend()](https://legacy.cplusplus.com/reference/vector/vector/rend/) | 获取最后一个数据位置的reverse_iterator，获取第一个数据前一个位置的 reverse_iterator |

![迭代器位置示意](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202308171501056.png "迭代器位置示意")

~~~C++
void test()
{
    vector<int> v(5,6);
    vector<int>::iterator it = v.begin();
    while(it != v.end())
    {
        cout<<it<<" ";
        it++;
    }
}
~~~

#### vector容量

| 接口名称                                                     | 接口说明             |
| ------------------------------------------------------------ | -------------------- |
| [size](https://legacy.cplusplus.com/reference/vector/vector/size/) | 获取数据个数         |
| [capacity](https://legacy.cplusplus.com/reference/vector/vector/capacity/) | 获取容量大小         |
| [empty](https://legacy.cplusplus.com/reference/vector/vector/empty/) | 判断是否为空         |
| [resize](https://legacy.cplusplus.com/reference/vector/vector/resize/) | 改变vector的size     |
| [reserve](https://legacy.cplusplus.com/reference/vector/vector/reserve/) | 改变vector的capacity |

* capacity的代码在vs和g++下分别运行会发现，**vs下capacity是按1.5倍增长的，g++是按2倍增长的**。 这个问题经常会考察，不要固化的认为，vector增容都是2倍，具体增长多少是根据具体的需求定义 的。vs是PJ版本STL，g++是SGI版本STL。 
* reserve只负责开辟空间，如果确定知道需要用多少空间，reserve可以缓解vector增容的代价缺陷问 题。 
* resize在开空间的同时还会进行初始化，影响size。

~~~C++
void VecotorExpandtest()
{
    size_t sz;
 	vector<int> v;
 	sz = v.capacity();
 	cout << "making v grow:\n";
 	for (int i = 0; i < 100; ++i)
 	{
 		v.push_back(i);
 		if (sz != v.capacity())
 		{
 			sz = v.capacity();
 			cout << "capacity changed: " << sz << '\n';
 		}
 	}
}
~~~

~~~C++
void test()
{
	vector<int> v;
	for (int i = 1; i < 10; i++)
    {
        v.push_back(i);
    }
	v.resize(5);
	v.resize(8, 100);
	v.resize(12);
	cout << "v contains:";
	for (size_t i = 0; i < v.size(); i++)
    {
        cout << ' ' << v[i];
    }
	cout << '\n';
}
~~~

#### vecotor增删查改

| 接口名称                                                     | 接口说明                       |
| ------------------------------------------------------------ | ------------------------------ |
| [push_back](https://legacy.cplusplus.com/reference/vector/vector/push_back/) | 尾插                           |
| [pop_back](https://legacy.cplusplus.com/reference/vector/vector/pop_back/) | 尾删                           |
| [find](https://legacy.cplusplus.com/reference/algorithm/find/) | 查找（并不是vector的成员函数） |
| [insert](https://legacy.cplusplus.com/reference/vector/vector/insert/) | 在position之前插入val          |
| [erase](https://legacy.cplusplus.com/reference/vector/vector/erase/) | 删除position位置的数据         |
| [swap](https://legacy.cplusplus.com/reference/vector/vector/swap/) | 交换两个vector的数据空间       |
| [oparetor[]](https://legacy.cplusplus.com/reference/vector/vector/operator[]/) | 像数组一样访问                 |

~~~C++
void TestVector4()
{
	vector<int> v;
	v.push_back(1);
	v.push_back(2);
	v.push_back(3);
	v.push_back(4);

	auto it = v.begin();
	while (it != v.end()) 
	{
		cout << *it << " ";
		++it;
	}
	cout << endl;

	v.pop_back();
	v.pop_back();

	it = v.begin();
	while (it != v.end()) 
	{
		cout << *it << " ";
		++it;
	}
	cout << endl;
}
~~~

### vector的迭代器失效问题

**迭代器的主要作用就是让算法能够不用关心底层数据结构，其底层实际就是一个指针，或者是对指针进行了封装**

比如：**vector的迭代器就是原生态指针T* **。因此**迭代器失效，实际就是迭代器底层对应指针所指向的 空间被销毁了，而使用一块已经被释放的空间**，造成的后果是程序崩溃(即**如果继续使用已经失效的迭代器， 程序可能会崩溃**)。

对于vector可能会导致其迭代器失效的操作有：

1. **会引起其底层空间改变的操作，都有可能是迭代器失效**，比如：resize、reserve、insert、assign、 push_back等。

2. 指定位置元素的删除操作--**erase**

3. 与vector类似，string在插入+扩容操作+erase之后，迭代器也会失效

**解决迭代器失效的方法：迭代器失效解决办法：在使用前，对迭代器重新赋值即可**

阅读文档可以发现，这类会产生迭代器失效的接口都会有返回值，而目的就是对迭代器重新赋值，避免迭代器失效。



# vector模拟实现

基本实现功能以及实现方式参考：[Vector](https://github.com/Yukii2333/Daily-Code/tree/main/Works/Work_cpp/2023_07_15_vector)

## 注意事项

### 在模拟实现的erase中使用memcpy

1. memcpy是内存的二进制格式拷贝，将一段内存空间中内容原封不动的拷贝到另外一段内存空间中 
2. 如果拷贝的是自定义类型的元素，memcpy既高效又不会出错，但如果拷贝的是自定义类型元素，并且 自定义类型元素中涉及到资源管理时，就会出错，因为memcpy的拷贝实际是浅拷贝。

**如果对象中涉及到资源管理时，千万不能使用memcpy进行对象之间的拷贝，因为memcpy是 浅拷贝，否则可能会引起内存泄漏甚至程序崩溃。**





