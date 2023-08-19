---
title: C++list
date: 2023-08-19 15:56:56
categories: C++
tags:
 - C++
 - STL
 - vector
description:
cover:
---

# 初步了解list

## list的介绍

[list文档](https://legacy.cplusplus.com/reference/list/list/)

* list是可以在常数范围内在任意位置进行插入和删除的序列式容器，并且该容器可以前后双向迭代。 

* list的**底层是双向链表结构**，双向链表中每个元素存储在互不相关的独立节点中，在节点中通过指针指向 其前一个元素和后一个元素。 

* list与forward_list非常相似：最主要的不同在于**forward_list是单链表**，只能朝前迭代，已让其更简单高 效。 

* 与其他的序列式容器相比(array，vector，deque)，list通常在任意位置进行插入、移除元素的执行效率 更好。 

* 与其他序列式容器相比，**list和forward_list最大的缺陷是不支持任意位置的随机访问**，比如：要访问list 的第6个元素，必须从已知的位置(比如头部或者尾部)迭代到该位置，在这段位置上迭代需要线性的时间 开销；list还需要一些额外的空间，以保存每个节点的相关联信息(对于存储类型较小元素的大list来说这 可能是一个重要的因素)

## list常用接口

### list的构造

| [构造函数](https://legacy.cplusplus.com/reference/list/list/list/) | 接口说明                            |
| ------------------------------------------------------------ | ----------------------------------- |
| list (size_type n, const value_type& val = value_type())     | 构造的list中包含n个值为val的元素    |
| list()                                                       | 构造空的list                        |
| list (const list& x)                                         | 拷贝构造函数                        |
| list (InputIterator first, InputIterator last)               | 用[first, last)区间中的元素构造list |

~~~C++
void test()
{
    list<int> s1;
    list<int> s2(3,4);
    list<int> s3(s2);
    list<int> s4(s2.begin(),s2.end());
}
~~~

### list的迭代器

| 接口名称                                                     | 接口说明                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [begin](https://legacy.cplusplus.com/reference/list/list/begin/)+[end](https://legacy.cplusplus.com/reference/list/list/end/) | 返回第一个元素的迭代器+返回最后一个元素下一个位置的迭代器    |
| [rbegin](https://legacy.cplusplus.com/reference/list/list/rbegin/)+[rend](https://legacy.cplusplus.com/reference/list/list/rend/) | 返回第一个元素的reverse_iterator,即end位置，返回最后一个元素下一个位置的 reverse_iterator,即begin位置 |

list是带头的双向循环链表，因而最后一个元素的下一个就是头结点，而反向迭代器仅需复用整正向迭代器即可（譬如：rbegin就返回end，rend就返回begin）

~~~C++
void test()
{
    list<int> s;
    s.push_back(1);
    s.push_back(2);
    s.push_back(3);
    s.push_back(4);
    s.push_back(5);
    
    list<int>::iterator it = s.begin();
    while(it != s.end())
    {
        cout<<it<<" "；
            it++;
    }
    cout<<endl;
    
}
~~~

### list的容量

| 接口名称                                                     | 接口说明                                    |
| ------------------------------------------------------------ | ------------------------------------------- |
| [empty](https://legacy.cplusplus.com/reference/list/list/empty/) | 检测list是否为空，是返回true，否则返回false |
| [size](https://legacy.cplusplus.com/reference/list/list/size/) | 返回list中有效节点的个数                    |

由于链表是在堆上申请不连续的空间作为节点，因而不需要向数组那样提前开辟空间，容量取决于改程序能从内存中申请多大的空间，因而就没有了容量的概念，也就没有了扩容之类的接口

~~~C++
void test()
{
    list<int> s;
    if(s.empty())
    {
        s.push_back(1);
    	s.push_back(2);
    	s.push_back(3);
    	s.push_back(4);
    	s.push_back(5);
    }
    cout<<s.size()<<endl;
}
~~~

### list的增删查改

| 接口名称                                                     | 接口说明                                |
| ------------------------------------------------------------ | --------------------------------------- |
| [front](https://legacy.cplusplus.com/reference/list/list/front/) | 返回list的第一个节点中值的引用          |
| [back](https://legacy.cplusplus.com/reference/list/list/back/) | 返回list的最后一个节点中值的引用        |
| [push_front](https://legacy.cplusplus.com/reference/list/list/push_front/) | 在list首元素前插入值为val的元素         |
| [push_back](https://legacy.cplusplus.com/reference/list/list/push_back/) | 在list尾部插入值为val的元素             |
| [pop_front](https://legacy.cplusplus.com/reference/list/list/pop_front/) | 删除list中第一个元素                    |
| [pop_back](https://legacy.cplusplus.com/reference/list/list/pop_back/) | 删除list中最后一个元素                  |
| [insert](https://legacy.cplusplus.com/reference/list/list/insert/) | 在list position 位置中插入值为val的元素 |
| [erase](https://legacy.cplusplus.com/reference/list/list/erase/) | 删除list position位置的元素             |
| [swap](https://legacy.cplusplus.com/reference/list/list/swap/) | 交换两个list中的元素                    |
| [clear](https://legacy.cplusplus.com/reference/list/list/clear/) | 清空list中的有效元素                    |

~~~C++
void test()
{
    int arr[] = {1,2,3,4};
    list<int> l(arr,arr+szieof(arr)/sizeof(arr[0]));
    
    auto pos = ++l.begin();
    l.insert(pos,0);
    
    l.push_front(0);
    l.push_back(5);
    for(auto e : l)
    {
        cout<<e<<" ";
    }
    cout<<endl;
    
    l.pop_front();
    l.pop_back();
    for(auto e : l)
    {
        cout<<e<<" ";
    }
    cout<<endl;
    
    if(!l.empty())
    {
        cout<<l.size()<<endl;
        l.clear();
    }
    if(l.empty())
    {
        cout<<"clear!"<<endl;
    }
}
~~~

### list的迭代器失效

此处**可将迭代器暂时理解成类似于指针**，迭代器失效即**迭代器所指向的节点的无效，即该节点被删除了**。因为list的底层结构为带头结点的双向循环链表，因此**在list中进行插入时是不会导致list的迭代器失效的，只有在删除时才会失效**，并且**失效的只是指向被删除节点的迭代器，其他迭代器不会受到影响**。

# list的模拟实现

详细实现参考：[list模拟实现](https://github.com/Yukii2333/Daily-Code/tree/main/Works/Work_cpp/2023_07_18_list)

## 注意事项

* 就如上面所说，list的迭代器分为正向迭代器和反向迭代器，但是实际上代码底层只实现了正向迭代器，而反向迭代器只是复用了正向迭代器而已。

* 由于list并不是像vector那样连续的空间，因而迭代器的移动并不是简单的地址++或--，而是由一个节点指向另一个节点，当然只须重载运算符即可，但是当全部都实现在list类的内部时会发现一件事，即使重载了运算符也不能改变当先的指针，因为要进行++的是节点而不是该类（大致就是需要++的对象不同，自己亲身实现一下就能理解了）。

~~~C++
	//List的迭代器类
    template<class T, class Ref, class Ptr>
    class ListIterator
    {
        typedef ListNode<T> Node;
        typedef ListNode<T>* PNode;
        typedef ListIterator<T, Ref, Ptr> Self;
    public:
        PNode _pNode;

        ListIterator(PNode pNode = nullptr)
            :_pNode(pNode)
        {}
        ListIterator(const Self& l)
            :_pNode(l._pNode)
        {}
        T& operator*()
        {
            return _pNode->_val;
        }

        T* operator->()
        {
            return &_pNode->_val;
        }
        //前置++
        Self& operator++()
        {
            _pNode = _pNode->_pNext;
            return *this;
        }
        //后置++
        Self operator++(int)
        {
            Self tmp(*this);
            _pNode = _pNode->_pNext;
            return tmp;
        }
        Self& operator--()
        {
            _pNode = _pNode->_pPre;
            return *this;
        }
        Self& operator--(int)
        {
            Self tmp(*this);
            _pNode = _pNode->_pPre;
            return tmp;
        }
        bool operator!=(const Self& l)
        {
            return _pNode != l._pNode;
        }
        bool operator==(const Self& l)
        {
            return _pNode == l._pNode;
        }
    };
~~~



