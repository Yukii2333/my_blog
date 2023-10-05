---
title: C++map&set
date: 2023-10-01 19:27:12
categories: C++
tags:
 - map
 - set
 - STL
 - AVLTree
 - RBTree
description:
cover:
---

# 键值对

**用来表示具有一一对应关系的一种结构，该结构中一般只包含两个成员变量key和value，key代表键值，value表示与key对应的信息**，比如英文互译字典里的英文与中文的对应关系，亦或者学号与学生间的对应关系，且key（学号）所对应的value（学生）是唯一的。

# 关联式容器

关联式容器也是用来存储数据的，与序列容器（vector、list、dequeue等）即线性序列的数据结构不同的是，其**内部存储的是<key,value>结构的键对值，在数据检索时比序列式容器效率更高。**

~~~C++
//SGI-STL中关于键对值的定义
template <class T1, class T2>
struct pair {
  typedef T1 first_type;
  typedef T2 second_type;

  T1 first;
  T2 second;
  pair() : first(T1()), second(T2()) {}
  pair(const T1& a, const T2& b) : first(a), second(b) {}
}
~~~

# 树形结构的关联式容器

根据使用场景的不同，STL中实现了两种不同结构的管理式容器，**树形结构与哈希结构**。**树形结构包括：map、set、multimap、multiset**。这四容器的共同点是**使用平衡搜索树（红黑树）作为底层**，容器中的元素是一个有序的序列。

## set

[set文档](https://legacy.cplusplus.com/reference/set/set/)

大致要点：

* set是K类型的关联式容器，其中的key就是其中的value
* value（即key）是唯一存在于该容器中的
* set按照其内部的默认比较对象来进行存放数据
* set相较于unordered_set（哈希结构的K类型关联容器）访问单个元素更慢，但set允许根据顺序对元素进行迭代
* set的底层是红黑树

需要注意的是：

* set中的value即是key，不需要构建键值对
* 使用set的迭代器进行遍历能得到有序序列
* set中默认按照小于来进行元素的比较
* set中查找元素的时间复杂度为log(2N)
* set中不支持修改value值，因为value值作为key值进行使用
* set中不能插入重复元素，因而可以用于去重以及排序

### 模板参数列表

![模板参数列表](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310012003412.png "模板参数列表")

T：set中存放的元素类型，实际在底层存储的是<value,value>的键值对

Compare：set中用于比较的仿函数，默认按小于进行比较

Alloc：set使用STL中的空间配置器管理空间（*暂时可以先不用管*）

### set的构造

| [函数声明](https://legacy.cplusplus.com/reference/set/set/set/) | 功能介绍                          |
| ------------------------------------------------------------ | --------------------------------- |
| set (const key_compare& comp = key_compare(),const allocator_type& alloc = allocator_type()); | 构造空的set                       |
| set (InputIterator first,  InputIterator last,const key_compare& comp = key_compare(),const allocator_type& alloc = allocator_type()); | 用(first,last)区间中的元素构造set |
| set (const set& x)                                           | set的拷贝构造                     |

### set的迭代器

| 函数名称                                                     | 功能介绍                                                 |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| [begin](https://legacy.cplusplus.com/reference/set/set/begin/) | 返回set中的起始位置的迭代器                              |
| [end](https://legacy.cplusplus.com/reference/set/set/end/)   | 返回set中最后一个元素后面的迭代器                        |
| [rbegin](https://legacy.cplusplus.com/reference/set/set/rbegin/) | 返回set第一个元素的反向迭代器，即end                     |
| [rend](https://legacy.cplusplus.com/reference/set/set/rend/) | 返回set最后一个元素下一个位置的反向迭代器， 即begin      |
| [cbegin](https://legacy.cplusplus.com/reference/set/set/cbegin/) | 返回set中起始位置元素的const迭代器                       |
| [cend](https://legacy.cplusplus.com/reference/set/set/cend/) | 返回set中最后一个元素后面的const迭代器                   |
| [crbegin](https://legacy.cplusplus.com/reference/set/set/crbegin/) | 返回set第一个元素的反向const迭代器，即cend               |
| [crend](https://legacy.cplusplus.com/reference/set/set/crend/) | 返回set最后一个元素下一个位置的反向const迭代器，即cbegin |

*set拥有双向迭代器*

### set的容器

| 函数名称                                                     | 功能介绍                                  |
| ------------------------------------------------------------ | ----------------------------------------- |
| [empty](https://legacy.cplusplus.com/reference/set/set/empty/) | 检测set是否为空，空返回true，否则返回true |
| [size](https://legacy.cplusplus.com/reference/set/set/size/) | 返回set中有效元素的个数                   |

### set的修改操作

| 函数名称                                                     | 功能介绍                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [insert](https://legacy.cplusplus.com/reference/set/set/insert/) | 在set中插入元素x，实际插入的是构成的键值对，如果插入成功，返回<该元素在set中的位置，true>,如果插入失败，说明x在set中已经存在，返回<该元素在set中的位置，false> |
| [erase](https://legacy.cplusplus.com/reference/set/set/erase/) | 删除set中position位置上的元素/删除set中值为x的元素，返回删除的元素的个数/删除set中[first, last)区间中的元素 |
| [swap](https://legacy.cplusplus.com/reference/set/set/swap/) | 交换set中的元素                                              |
| [clear](https://legacy.cplusplus.com/reference/set/set/clear/) | 将set中的元素清空                                            |
| [find](https://legacy.cplusplus.com/reference/set/set/find/) | 返回set中值为x的元素的位置，未找到返回end()                  |
| [count](https://legacy.cplusplus.com/reference/set/set/count/) | 返回set中值为x的元素的个数                                   |

*有一些主要是用于multiset，从此处也能看出其可以存储重复元素*

### set的实际使用

~~~C++
//set的使用需要包含头文件
#include<set>
void test()
{
    int array[] = { 1, 3, 5, 7, 9, 2, 4, 6, 8, 0, 1, 3, 5, 7, 9, 2, 4, 6, 8, 0 };
    //用数组来初始化一个set对象
    set<int> s(array, array + sizeof(array) / sizeof(array));
    //观察size是否与数组相同
    cout << s.size() << endl;
    //全部遍历一遍看是否实现去重（范围for）
    for (auto& e : s)
    {
        cout << e << " ";
    }
    cout << endl;
    //用反向迭代器便利一遍
    for (auto it = s.rbegin(); it != s.rend(); ++it)
    {
        cout << *it << " ";
    }
    cout << endl;
    //输出key值为3的个数
    cout << s.count(3) << endl;
}
~~~

## map

[map文档](https://legacy.cplusplus.com/reference/map/map/)

大致要点：

* map是K&V类型的关联容器，存储值是由key与value构成的键值对
* map中key通常用于排序与唯一标识元素，value中存储与此键值key对应的内容
* key与value类型可以不同，其构成键对值的方式是使用`pair`

​													*typedef pair value_type;*

* map内部的比较也是按照键值key来进行比较
* 同样的，map访问单个元素相较于unordered_map慢一些，但map允许根据顺序对元素进行迭代
* map支持随机访问，即`[]`，在[]中输入key就能找到与key对应的value
* map底层也是红黑树

### 模板参数列表

![模板参数列表](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310041546223.png "模板参数列表")

key：键值对中的key

T：键值对中的value

Compare：map中用于比较的仿函数，按照key值来比较，默认按小于进行比较

Alloc：map使用STL中的空间配置器管理空间（*暂时可以先不用管*）

### map的构造

| [函数声明](https://legacy.cplusplus.com/reference/map/map/map/) | 功能介绍                          |
| ------------------------------------------------------------ | --------------------------------- |
| map (const key_compare& comp = key_compare(), const allocator_type& alloc = allocator_type()); | 构造空的map                       |
| map (InputIterator first, InputIterator last, const key_compare& comp = key_compare(), const allocator_type& alloc = allocator_type()); | 用(first,last)区间中的元素构造map |
| map (const map& x);                                          | map的拷贝构造                     |

### map的迭代器

| 函数名称                                                     | 功能介绍                                                 |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| [begin](https://legacy.cplusplus.com/reference/map/map/begin/) | 返回map中的起始位置的迭代器                              |
| [end](https://legacy.cplusplus.com/reference/map/map/end/)   | 返回map中最后一个元素后面的迭代器                        |
| [rbegin](https://legacy.cplusplus.com/reference/map/map/rbegin/) | 返回map第一个元素的反向迭代器，即end                     |
| [rend](https://legacy.cplusplus.com/reference/map/map/rend/) | 返回map最后一个元素下一个位置的反向迭代器， 即begin      |
| [cbegin](https://legacy.cplusplus.com/reference/map/map/cbegin/) | 返回map中起始位置元素的const迭代器                       |
| [cend](https://legacy.cplusplus.com/reference/map/map/cend/) | 返回map中最后一个元素后面的const迭代器                   |
| [crbegin](https://legacy.cplusplus.com/reference/map/map/crbegin/) | 返回map第一个元素的反向const迭代器，即cend               |
| [crend](https://legacy.cplusplus.com/reference/map/map/crend/) | 返回map最后一个元素下一个位置的反向const迭代器，即cbegin |

*map拥有双向迭代器*

### map的容器以及访问

| 函数名称                                                     | 功能介绍                                  |
| ------------------------------------------------------------ | ----------------------------------------- |
| [empty](https://legacy.cplusplus.com/reference/map/map/empty/) | 检测map是否为空，空返回true，否则返回true |
| [size](https://legacy.cplusplus.com/reference/map/map/size/) | 返回map中有效元素的个数                   |
| [operator[]](https://legacy.cplusplus.com/reference/map/map/operator[]/) | 返回map中key对应的value                   |

*[]的运算符重载，当map内存在输入的key时，就返回其对应的value，否则就`insert(key,T())`，然后再返回其value*

*还存在一个跟[]操作类似的函数`at`，`at(key)`与`[key]`一样，只是在不存在输入key值时会直接抛异常*

### map的修改操作

| 函数名称                                                     | 功能介绍                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [insert](https://legacy.cplusplus.com/reference/map/map/insert/) | 在map中插入元素x，实际插入的是构成的键值对，如果插入成功，返回<该元素在set中的位置，true>,如果插入失败，说明x在map中已经存在，返回<该元素在set中的位置，false> |
| [erase](https://legacy.cplusplus.com/reference/map/map/erase/) | 删除map中position位置上的元素/删除map中值为x的元素，返回删除的元素的个数/删除map中[first, last)区间中的元素 |
| [swap](https://legacy.cplusplus.com/reference/map/map/swap/) | 交换map中的元素                                              |
| [clear](https://legacy.cplusplus.com/reference/map/map/clear/) | 将map中的元素清空                                            |
| [find](https://legacy.cplusplus.com/reference/map/map/find/) | 返回map中key值为x的元素的位置，未找到返回end()               |
| [count](https://legacy.cplusplus.com/reference/map/map/count/) | 返回map中值为x的元素的个数                                   |

*count对于map与set都只会返回0或者1*

### map的实际应用

~~~C++
//map的使用需要包含头文件
#include<map>
#include<string>
#include<vector>
//统计单词出现的次数
void test()
{
    //列表初始化，后续会提及
    vector<string> words{ "key","value","value","code","hello","value","world" };
    map<string, int> m;
    //pair是STL内的一种存储键值对的结构
    //make_pair(k,v)是一个函数，能返回k与v的pair键值对
    //还可以使用pair<string,int>("xxx",x)即匿名对象来插入
    m.insert(make_pair("key", 1));
    m.insert(make_pair("code", 1));
    m.insert(make_pair("value", 1));
    for (auto& e : m)
    {
        cout << e.first << " " << e.second << endl;
    }
    cout << endl;
    //使用[]计数以及插入
    for (auto& e : words)
    {
        m[e]++;
    }
    for (auto& e : m)
    {
        cout << e.first << " " << e.second << endl;
    }
    cout << endl;
    for (auto it = m.rbegin(); it != m.rend(); ++it)
    {
        cout << it->first << " " << it->second << endl;
    }
    cout << m.count("value") << endl;
}
~~~

##  multiset

[multiset文档](https://legacy.cplusplus.com/reference/set/multiset/)

大致要点：

* multiset是按照特定顺序存储元素的容器，其中元素是可以重复的
* multiset与set一样，是K类型的关联式容器，其中的key就是其中的value
* value（即key）是能复数存在于该容器中的
* multiset按照其内部的默认比较对象来进行存放数据
* multiset相较于unordered_multiset访问单个元素更慢，但multiset允许根据顺序对元素进行迭代
* multiset底层结构为红黑树

需要注意的是：

* multiset中的value即是key，不需要构建键值对
* 与set的区别是，multiset中的元素可以重复，set是中value是唯一的
* 使用multiset的迭代器进行遍历能得到有序序列
* multiset中的元素不能修改
* multiset中查找元素的时间复杂度为log(2N)
* multiset可用于排序

### multiset的使用

*multiset与set接口大部分都是相同的，就不再赘述，仅展示不同之处*

~~~C++
#include<set>
void test()
{
    int array[] = { 2, 1, 3, 9, 6, 0, 5, 8, 4, 7 };
    multiset<int> s(array, array + sizeof(array) / sizeof(array[0]));
    for (auto& e : s)
    {
        cout << e << " ";
    }
    cout << endl;
    //允许重复元素的存在，因而不能进行去重
    s.insert(array, array + sizeof(array) / sizeof(array[0]));
    for (auto& e : s)
    {
        cout << e << " ";
    }
    cout << endl;
}
~~~

## multimap

[multimap文档](https://legacy.cplusplus.com/reference/map/multimap/)

大致要点：

* multimap是K&V类型的关联容器，存储值是由key与value构成的键值对
* multimap中key通常用于排序与唯一标识元素，value中存储与此键值key对应的内容
* key与value类型可以不同，其构成键对值的方式是使用`pair`
* multimap内部的比较也是按照键值key来进行比较
* multimap访问单个元素相较于unordered_multimap慢一些，但multimap允许根据顺序对元素进行迭代
* multimap底层也是红黑树

**multimap与map的区别就是允许重复key值，以及未重载operator[]操作（因为key值不唯一）**

*使用上也是包含map头文件，除此以外没有什么大的区别*

## 在OJ中的应用

### [前K个高频单词](https://leetcode.cn/problems/top-k-frequent-words/description/)

~~~C++
class Solution {
public:
	//仿函数
    //默认的比较方式不能满足需求就需要自行传入
    class Greater
    {
    public:
        bool operator()(const pair<string,int>& p1, const pair<string,int>& p2)
        {
            return (p1.second > p2.second) || (p1.second == p2.second && p1.first<p2.first);
        }
    };
    //排序+统计次数
    vector<string> topKFrequent(vector<string>& words, int k) {
        map<string,int> count_map;
        for(auto& e : words)
        {
            count_map[e]++;
        }
        vector<pair<string,int>> count_v(count_map.begin(),count_map.end());
        sort(count_v.begin(),count_v.end(),Greater());
        vector<string> rt;
        for(int i = 0; i < k; ++i)
        {
            rt.push_back(count_v[i].first);
        }
        return rt;
    }
};
~~~

### [两个数的交集](https://leetcode.cn/problems/intersection-of-two-arrays/description/)

~~~C++
class Solution {
public:
    //去重+排序
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        //去重
        set<int> s1;
        for (auto e : nums1)
        {
            s1.insert(e);
        }
        set<int> s2;
        for (auto e : nums2)
        {
            s2.insert(e);
        }
        //set排过序，依次比较，小的一定不是交集，相等的是交集
        auto it1 = s1.begin();
        auto it2 = s2.begin();
        vector<int> ret;
        while (it1 != s1.end() && it2 != s2.end())
        {
            if (*it1 < *it2)
            {
                it1++;
            }
            else if (*it2 < *it1)
            {
                it2++;
            }
            else
            {
                ret.push_back(*it1);
                it1++;
                it2++;
            }
        }
        return ret;
    }
~~~

# 底层结构

前面的几种容器**其底层都是按照二叉搜索树来实现的**，但是一般的二叉搜索树有其自身的缺陷，假如往树中插入的元素有序或者接近有序，二叉搜索树就会退化成单支树，时间复杂度会退化成O(N)，因此map、set等关联式容器的底层结构是对二叉树进行了平衡处理，即采用平衡树来实现。

## AVL树

### AVL树的概念

二叉搜索树虽可以缩短查找的效率，但如果**数据有序或接近有序二叉搜索树将退化为单支树，查找元素相当于在顺序表中搜索元素，效率低下**。因此，两位俄罗斯的数学家G.M.Adelson-Velskii 和E.M.Landis在1962年发明了一种解决上述问题的方法：**当向二叉搜索树中插入新节点后，如果能保证每个节点的左右子树高度之差的绝对值不超过1**(需要对树中的节点进行调整)，即可降低树的高度，从而减少平均搜索长度。

一棵AVL树或者是空树，或者是具有以下性质的二叉搜索树：

* **它的左右子树都是AVL树**
* **左右子树高度之差(简称平衡因子)的绝对值不超过1(-1/0/1)**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310041712096.png" alt="AVL树" title="AVL树" style="zoom:50%;" />

**如果一棵二叉搜索树是高度平衡的，它就是AVL树。如果它有n个节点，其高度可保持在O(log_2 n)，搜索时间复杂度O(log_2 n)。**

### AVL数的节点定义

~~~C++
template<class K, class V>
struct AVLTreeNode
{
	typedef AVLTreeNode<K, V> Node;

	std::pair<K, V> kv_;
    //引入父节点指针的节点，对于后续操作更易于理解
	AVLTreeNode<K, V>* left_;
	AVLTreeNode<K, V>* right_;
	AVLTreeNode<K, V>* parent_;
	//平衡因子（balancing factor）
	//右子树高度-左子树高度
	int bf_;

	AVLTreeNode(const std::pair<K, V> kv)
		:kv_(kv)
		,left_(nullptr)
		,right_(nullptr)
		,parent_(nullptr)
		,bf_(0)
	{}
};
~~~

### AVL树的插入

AVL树就是在二叉搜索树的基础上引入了平衡因子，因此AVL树也可以看成是二叉搜索树。那么AVL树的插入过程可以分为两步：

1. **按照二叉搜索树的方式插入新节点**
2. **调整节点的平衡因子**

~~~C++
bool Insert(const std::pair<K, V>& kv)
{
    //先跟普通二叉搜索树一样进行插入
	if (root_ == nullptr)
	{
		root_ = new Node(kv);
		return true;
	}
	Node* cur = root_;
	Node* parent = root_;
	while (cur != nullptr)
	{
		parent = cur;
		if (kv.first < cur->kv_.first)
		{
			cur = cur->left_;
		}
		else if (kv.first > cur->kv_.first)
		{
			cur = cur->right_;
		}
		else
		{
			return false;
		}
	}
	
    //三个指针都需要更新
	cur = new Node(kv);
	if (kv.first < parent->kv_.first)
	{
		parent->left_ = cur;
	}
	else
	{
		parent->right_ = cur;
	}
	cur->parent_ = parent;
	
    //与普通二叉树不同的是引入了平衡因子
	//更新平衡因子
    //从最底层到祖先整条路径都需要更新
    //在原本平衡因子的基础上，左子树插入就--，右子树插入就++
	while (parent != nullptr)
	{
		if (cur == parent->left_)
		{
			parent->bf_--;
		}
		else
		{
			parent->bf_++;
		}

		if (parent->bf_ == 0)
		{
			break;
		}
        //向上更新
		else if (abs(parent->bf_) == 1)
		{
			cur = parent;
			parent = parent->parent_;
		}
        //平衡因子的绝对值<=1
        //左右子树高度差大于1时，需要进行调整，即旋转
		else if (abs(parent->bf_) == 2)
		{
			//旋转操作
		}
	}
}
~~~

### AVL树的旋转

旋转主要分为两种类型，每种类型右可以分为两种小类

#### 单旋

##### 右单旋

即**新插入的节点位于较高左子树的左侧——左左**，此时就需要右单旋

![右单旋](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051023910.png "右单旋")

结合图看下面的代码：

~~~C++
void RotateR(Node* parent)
{
    //位置关系如下
    //			parent
    //		cur			
    //curleft  curright
    //要将curright放置于parent的left
    //parent放置于cur的right
    //然后更新平衡因子，经过单旋的平衡因子都会为0
    //然后再看先前parent是否是root，若是root则将root更新为cur
	Node* cur = parent->left_;
	Node* curRight = cur->right_;

	cur->right_ = parent;
	parent->left_ = curRight;

	if (curRight)
	{
		curRight->parent_ = parent;
	}

	Node* pParent = parent->parent_;
	parent->parent_ = cur;
    //默认是将root的parent置空，判断此处是否为空来确定是否为root节点
	cur->parent_ = pParent;
	if (pParent)
	{
		if (pParent->left_ == parent)
		{
			pParent->left_ = cur;
		}
		else
		{
			pParent->right_ = cur;
		}
	}
	else
	{
		root_ = cur;
	}

	parent->bf_ = cur->bf_ = 0;
}
~~~

##### 左单旋

即**新插入的节点位于较高右子树的右侧——右右**，此时就需要左单旋

*建议如右单旋那样，先画图，再结合代码理解*

~~~C++
void RotateL(Node* parent)
{
    //位置关系如下
    //		parent
    //			cur			
    //	curleft  	curright
    //要将curleft放置于parent的right
    //parent放置于cur的left
    //然后更新平衡因子，经过单旋的平衡因子都会为0
    //然后再看先前parent是否是root，若是root则将root更新为cur
	Node* cur = parent->right_;
	Node* curLeft = cur->left_;
	
	cur->left_ = parent;
	parent->right_ = curLeft;
	

	if (curLeft)
	{
		curLeft->parent_ = parent;
	}

	Node* pParent = parent->parent_;
	parent->parent_ = cur;
    //默认是将root的parent置空，判断此处是否为空来确定是否为root节点
	cur->parent_ = pParent;

	if (pParent)
	{
		if (pParent->left_ == parent)
		{
			pParent->left_ = cur;
		}
		else
		{
			pParent->right_ = cur;
		}
	}
	else
	{
		root_ = cur;
	}

	parent->bf_ = cur->bf_ = 0;
}
~~~

#### 双旋

##### 左右双旋

即**新节点插入较高左子树的右侧---左右：先左单旋再右单旋**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051101274.png" alt="左右双旋" title="左右双旋" style="zoom:50%;" />

结合图再看下面的代码：

~~~C++
void RotateLR(Node* parent)
{
	Node* cur = parent->left_;
	Node* curRight = cur->right_;
	int bf = curRight->bf_;
    //直接复用已有的单旋即可
	RotateL(cur);
	RotateR(parent);
    //可插入的位置除了b以外还有c，两处都是左右双旋
    //但是最后的结果（平衡因子）不太一样
    //cur在70位置，其right的bf=1，说明在c插入，bf=-1说明在b插入
	if (bf == -1)
	{
		cur->bf_ = 0;
		parent->bf_ = 1;
		curRight->bf_ = 0;
	}
	else if (bf == 1)
	{
		cur->bf_ = -1;
		parent->bf_ = 0;
		curRight->bf_ = 0;
	}
    // 0 0 0的情况如下
    //		parent
    //cur
    //	 newnode
    //左旋再右旋后平衡因子为0 0 0
	else if (bf == 0)
	{
		cur->bf_ = 0;
		parent->bf_ = 0;
		curRight->bf_ = 0;
	}
}
~~~

##### 右左双旋

即**新节点插入较高右子树的左侧---右左：先右单旋再左单旋**

~~~C++
void RotateRL(Node* parent)
{
	Node* cur = parent->right_;
	Node* curLeft = cur->left_;
	int bf = curLeft->bf_;
    //直接复用已有的单旋
	RotateR(cur);
	RotateL(parent);
	if (bf == -1)
	{
		cur->bf_ = 1;
		parent->bf_ = 0;
		curLeft->bf_ = 0;
	}
	else if (bf == 1)
	{
		cur->bf_ = 0;
		parent->bf_ = -1;
		curLeft->bf_ = 0;
	}
    //		parent
    //				cur
    //		    newnode
	else if (bf == 0)
	{
		cur->bf_ = 0;
		parent->bf_ = 0;
		curLeft->bf_ = 0;
	}
}
~~~

#### 旋转的区分

假如以pParent为根的子树不平衡，即pParent的平衡因子为2或者-2，分以下情况考虑 

1. pParent的平衡因子为 2，说明pParent的右子树高，设pParent的右子树的根为pSubR
   * 当pSubR的平衡因子为1时，执行左单旋
   * 当pSubR的平衡因子为-1时，执行右左双旋 
2. pParent的平衡因子为-2，说明pParent的左子树高，设pParent的左子树的根为pSubL
   * 当pSubL的平衡因子为-1是，执行右单旋
   * 当pSubL的平衡因子为1时，执行左右双旋

旋转完成后，原pParent为根的子树个高度降低，已经平衡，不需要再向上更新。

### AVL树的验证

AVL树是在二叉搜索树的基础上加入了平衡性的限制，因此要验证AVL树，可以分两步：

1. **验证其为二叉搜索树**

如果中序遍历得到一个有序序列，就说明为二叉搜索树

2. **验证其为平衡树**
   * 每个节点子树高度差的绝对值不超过1
   * 节点的平衡因子是否计算正确

~~~C++
//求高度
int Hight(Node* root)
{
	if (root == nullptr)
	{
		return 0;
	}
	int leftHight = Hight(root->left_);
	int rightHight = Hight(root->right_);
	return leftHight > rightHight ? leftHight + 1 : rightHight + 1;
}
//判断是否是AVL树
bool IsBalance(Node* root)
{
    //空树也是AVL树
	if (root == nullptr)
	{
		return true;
	}
    //计算root节点的平衡因子：即root左右子树的高度差
	int leftHight = Hight(root->left_);
	int rightHight = Hight(root->right_);
    //如果计算出的平衡因子与root的平衡因子不相等，或者root平衡因子的绝对值超过1，则一定不是AVL树
	if ((rightHight - leftHight) != root->bf_)
	{
		std::cout << "平衡因子异常：" << "key:" << root->kv_.first << std::endl;
		return false;
	}
    //root的左和右如果都是AVL树，则该树一定是AVL树
	return IsBalance(root->left_) && IsBalance(root->right_);
}
~~~

### AVL树的删除

*留个坑等之后填*

如需实现可以参考《算法导论》或《数据结构-用面向对象方法与C++描述》殷人昆版。

### AVL树的性能

AVL树是一棵绝对平衡的二叉搜索树，其要求每个节点的左右子树高度差的绝对值都不超过1，这样可以保证查询时高效的时间复杂度，即`log2(N)`。但是如果要**对AVL树做一些结构修改的操作，性能非常低下**，比如：插入时要维护其绝对平衡，旋转的次数比较多，**更差的是在删除时，有可能一直要让旋转持续到根的位置。**因此：如果需要一种查询高效且有序的数据结构，而且数据的个数为静态的(即不会改变)，可以考虑AVL树，但**一个结构经常修改，就不太适合**。

*[具体实现](https://github.com/Yukii2333/Daily-Code/tree/main/Works/Work_cpp/2023_09_07_AVLTree)*

## 红黑树

### 红黑树的概念

**红黑树，是一种二叉搜索树**，但在每个结点上**增加一个存储位表示结点的颜色，可以是Red或Black**。 通过**对任何一条从根到叶子的路径上各个结点着色方式的限制，确保没有一条路径会比其他路径长出两倍**，因而是**接近平衡**的。

![红黑树](https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051130659.svg "红黑树")

**红黑树的特性：**

1. **每个结点不是红色就是黑色** 
2. **根节点是黑色的** 
3. **如果一个节点是红色的，则它的两个孩子结点是黑色的** 
4. **对于每个结点，从该结点到其所有后代叶结点的简单路径上，均包含相同数目的黑色结点** 
5. **每个叶子结点都是黑色的(此处的叶子结点指的是空结点)**

### 红黑树节点的定义

~~~C++
//结点颜色
enum Colour
{
	RED,
	BLACK
};

template<class K, class V>
struct RBTreeNode
{
	typedef RBTreeNode<K, V> Node;

	std::pair<K, V> kv_;
	Colour cor_;
	RBTreeNode<K, V>* left_;
	RBTreeNode<K, V>* right_;
	RBTreeNode<K, V>* parent_;


	RBTreeNode(const std::pair<K, V> kv)
		:kv_(kv)
		,cor_(RED)
		,left_(nullptr)
		,right_(nullptr)
		,parent_(nullptr)
	{}
};
~~~

### 红黑树的插入操作

红黑树是在二叉搜索树的基础上加上其平衡限制条件，因此红黑树的插入可分为两步：

1. **按照二叉搜索的树规则插入新节点**

~~~C++
bool Insert(std::pair<K,V> kv)
{
	if (root_ == nullptr)
	{
		root_ = new Node(kv);
		root_->cor_ = BLACK;
		return true;
	}
	Node* cur = root_;
	Node* parent = root_;
	while (cur != nullptr)
	{
		parent = cur;
		if (kv.first < cur->kv_.first)
		{
			cur = cur->left_;
		}
		else if (kv.first > cur->kv_.first)
		{
			cur = cur->right_;
		}
		else
		{
			return false;
		}
	}
	//同样的，前面的步骤也是一般二叉搜索树的插入操作
    //然后创建节点，更改指针关系
	cur = new Node(kv);
	cur->cor_ = RED;
	if (kv.first < parent->kv_.first)
	{
		parent->left_ = cur;
	}
	else
	{
		parent->right_ = cur;
	}
	cur->parent_ = parent;
	//
    //...颜色调整以及树结构调整
    //
    
    //根节点颜色可能被修改，需要修改为黑色
    root_->cor_= BLACK;
	return true;
}
~~~

2. 检测新节点插入后，红黑树的性质是否造到破坏

因为**新节点的默认颜色是红色**，因此：如果其双**亲节点的颜色是黑色，没有违反红黑树任何性质，则不需要调整**；但当**新插入节点的双亲节点颜色为红色时，就违反了性质三不能有连在一起的红色节点**，此时需要对红黑树分情况来讨论：

**约定:cur为当前节点，p为父节点，g为祖父节点，u为叔叔节点**

* 情况一: **cur为红，p为红，g为黑，u存在且为红**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051841419.png" alt="情况一" title="情况一" style="zoom:50%;" />

*可能是一颗完整的树，也可能是一颗子树*

进行处理后：

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051844903.png" alt="处理后" title="处理后" style="zoom:50%;" />

**如果g是根节点，则需要变为黑色。如果不是根节点，父节点为黑色，则结束调整；若父节点为红色则继续向上调整。**

* 情况二: **cur为红，p为红，g为黑，u不存在/u存在且为黑**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051849020.png" alt="情况二" title="情况二" style="zoom:50%;" />

处理后：

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051855319.png" alt="处理后" title="处理后" style="zoom:50%;" />



**从p节点处进行右单旋，然后将p节点变为黑色，再将其两个子节点变为红色，结束调整。**

*另一种右单旋与之类似就不过多介绍了*

u节点不存在时，说明cur是新插入的节点；当u节点存在时，其一定为黑色，且p节点原先也是黑色，只是向上调整的过程中改变了颜色。

* **情况三: cur为红，p为红，g为黑，u不存在/u存在且为黑**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051907591.png" alt="情况三" title="情况三" style="zoom:50%;" />

对p节点进行右单旋并不能达到需求，但是很容易发现，**对p进行左单旋后，就变成了情况二**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051909060.png" alt="左单旋后" title="进行左单旋" style="zoom:50%;" />

然后对cur节点进行情况二的操作，即**对cur节点进行右单旋，然后将cur变为黑色，其两个子节点变为红色，结束调整。**

<img src="https://raw.githubusercontent.com/Yukii2333/Images/main/blog_image/202310051913520.png" alt="处理后" title="处理后" style="zoom:50%;" />

完整的插入如下：

~~~C++
bool Insert(std::pair<K,V> kv)
{
	if (root_ == nullptr)
	{
		root_ = new Node(kv);
		root_->cor_ = BLACK;
		return true;
	}
	Node* cur = root_;
	Node* parent = root_;
	while (cur != nullptr)
	{
		parent = cur;
		if (kv.first < cur->kv_.first)
		{
			cur = cur->left_;
		}
		else if (kv.first > cur->kv_.first)
		{
			cur = cur->right_;
		}
		else
		{
			return false;
		}
	}
	//同样的，前面的步骤也是一般二叉搜索树的插入操作
    //然后创建节点，更改指针关系
	cur = new Node(kv);
	cur->cor_ = RED;
	if (kv.first < parent->kv_.first)
	{
		parent->left_ = cur;
	}
	else
	{
		parent->right_ = cur;
	}
	cur->parent_ = parent;
	//不满足情况需要持续调整
    //结束条件为cur->parent为空(整棵树已经调整完毕)或者符合红黑树要求
	while (parent && parent->cor_ == RED)
	{
		Node* grandparent = parent->parent_;
		
		if (parent == grandparent->left_)
		{
			Node* uncle = grandparent->right_;
            //情况一
			if (uncle && uncle->cor_ == RED)
			{
				uncle->cor_ = parent->cor_ = BLACK;
				grandparent->cor_ = RED;

				//一次变色后，接着从grandparent的位置开始处理,而不是parent
				//若是parent开始处理会遇到grandparent为空但进行访问的情况
				//况且三个节点都已经变色处理，从parent开始也没有意义
				cur = grandparent;
				parent = cur->parent_;
			}
			else
			{
                //情况二
				if (cur == parent->left_)
				{
					RotateR(grandparent);
					parent->cor_ = BLACK;
					grandparent->cor_ = RED;
				}
                //情况三
				else
				{
					RotateL(parent);
					RotateR(grandparent);
					cur->cor_ = BLACK;
					grandparent->cor_ = RED;
				}
				break;
			}
		}
		else
		{
			Node* uncle = grandparent->left_;
             //情况一
			if (uncle && uncle->cor_ == RED)
			{
				uncle->cor_ = parent->cor_ = BLACK;
				grandparent->cor_ = RED;

				cur = grandparent;
				parent = cur->parent_;
			}
			else
			{
                 //情况二
				if (cur == parent->right_)
				{
					RotateL(grandparent);
					parent->cor_ = BLACK;
					grandparent->cor_ = RED;
				}
                 //情况三
				else
				{
					RotateR(parent);
					RotateL(grandparent);
					cur->cor_ = BLACK;
					grandparent->cor_ = RED;
				}
				break;
			}
		}
	}
	root_->cor_= BLACK;
	return true;
}
~~~

### 红黑树的旋转

红黑树的旋转与AVL树的旋转无异，仅需直接复用即可（需要去掉其中平衡因子的修改）

### 红黑树的验证

红黑树的检测分为两步： 

1. **检测其是否满足二叉搜索树(中序遍历是否为有序序列)** 
2. **检测其是否满足红黑树的性质**
   * 无连续的红色节点
   * 每条路径的黑色节点相同

~~~C++
bool IsBalance(Node* root)
{
	if (root == nullptr)
	{
		return true;
	}
	if (root->cor_ != BLACK)
	{
		return false;
	}
    //选取黑色节点的基准值
    //随机一条路径即可
	int benchmark = 0;
	Node* cur = root;
	while (cur)
	{
		if (cur->cor_ == BLACK)
			++benchmark;

		cur = cur->left_;
	}
	return CheakColour(root, 0, benchmark);
}
//benchmark为基准值，每条路径上的黑色节点个数相同
bool CheakColour(Node* root, int blackNum, int benchmark)
{
	if (root == nullptr)
	{
		if (blackNum != benchmark)
			return false;

		return true;
	}
	if (root->cor_ == BLACK)
	{
		++blackNum;
	}
	else if (root->cor_ == RED && root->parent_ && root->parent_->cor_ == RED)
	{
		std::cout << "出现连续红色节点！" << "cur_key:" << root->kv_.first << std::endl;
		return false;
	}
	return CheakColour(root->left_, blackNum, benchmark)
		&& CheakColour(root->right_, blackNum, benchmark);
}
~~~

### 红黑树的删除

*留个坑*

### 红黑树的性能

红黑树同样也是一颗二叉搜索树，但由于**通过颜色的限制实现最长路径不超过最短路径的两倍，使得不会退化为链表类似的结构，而且没有AVL树那般要求绝对的平衡，在插入以及调整的效率上是优于AVL树的**，虽然会使得层数比AVL树高个一两层，但这种差距对于CPU而言已经是可以忽略不计了，**查询效率也是O(log2N)。**

*[具体实现](https://github.com/Yukii2333/Daily-Code/tree/main/Works/Work_cpp/2023_09_10_RBTree)*

*由于篇幅过长，map与set的具体实现就分到下一篇了*
