---
layout: post
title: "foreach和for在PHP中数组遍历的区别"
description: ""
category: 
tags: [数组，循环遍历，效率]
---
{% include JB/setup %}

##0.背景

数组在PHP语言中利用非常的广泛，最常用的数据处理就是数组的遍历。但是对于数组的遍历的效率和顺序，PHP是如何做做到和考究的呢，为此，做了一个简单的调研。

##1.数组的简单实现

PHP内部的数组是通过HashTable来进行实现的，且哈希表的冲突解决办法是用传统的拉链法进行解决。也就是如果两个不同的key哈希值相同的话，它会被放到同一个hash值下面一个单链表中去。就是因为这样的设计，php中数组的元素取值时间复杂度正常情况下是O(1)。在最坏情况下就会退化为单链表的遍历。这取决于hash函数的设计。

下面看看PHP中hashtable的结构体设计：

{% highlight php %}
<?php
typedef struct _hashtable {
uint nTableSize;        /* 散列表大小, Hash值的区间 */   
uint nTableMask;        /* 等于nTableSize -1, 用于快速定位 */  
uint nNumOfElements;    /* HashTable中实际元素的个数 */  
ulong nNextFreeElement; /* 下个空闲可用位置的数字索引 */  
Bucket *pInternalPointer;   /* 内部位置指针, 会被reset, current这些遍历函数使用 */   
Bucket *pListHead;      /* 头元素, 用于线性遍历 */  
Bucket *pListTail;      /* 尾元素, 用于线性遍历 */  
Bucket **arBuckets;     /* 实际的存储容器 */  
dtor_func_t pDestructor;/* 元素的析构函数(指针) */  
zend_bool persistent;  
unsigned char nApplyCount; /* 循环遍历保护 */  
zend_bool bApplyProtection;   
} HashTable;  

typedef struct bucket {
ulong h;                        /* 数字索引/hash值 */  
uint nKeyLength;                /* 字符索引的长度 */  
void *pData;                    /* 数据 */  
void *pDataPtr;                 /* 数据指针 */  
struct bucket *pListNext;               /* 下一个元素, 用于线性遍历 */  
struct bucket *pListLast;       /* 上一个元素, 用于线性遍历 */  
struct bucket *pNext;                   /* 处于同一个拉链中的下一个元素 */  
struct bucket *pNext;                   /* 处于同一拉链中的上一个元素 */  
char arKey[1]; /* 节省内存,方便初始化的技巧 */  
} Bucket;  
?>    
{% endhighlight %}  

首先是hashtable的定义，Bucket就是存放最终元素的一个桶。在hashtable中定义中可以看到几个遍历hashtable的指针（头尾指针和当前位置指针）。然后就是具体bucket的定义，其中pListNext和pListLast用于标识整体hash表中的上一个和下一个位置元素（主要用于遍历使用），而pNext和pNext主要用于指示存放在相同hashbucket中的冲突元素的前一个和后一个的指针。主要用于查找使用。

通过下面一个结构原理图，我们就可以看出整体的结构。

<img src="hash.png" alt="xxxxxx" style="width:600px;"/>

##2.for和foreach的对标
首先是循环遍历的对比，foreach的遍历直接通过 hashtable中定义的pInternalPointer指针来进行遍历。而如果使用for循环遍历的话，则是根据key的值来逐个hash查找，这其中需要计算hash值和冲突查找，所以效率相对会比较低。

另外对于遍历还存在另外一个问题：
{% highlight php %}
 
<?php
$arr = array(1,2,3,4,5);
foreach ($arr as $v) {
//可以获取
}
 
while (list($key, $v) = each($arr)) {
//获取不到
}
?>   
{% endhighlight %}   

在每次foreach遍历元素的时候，都会reset hashtable中定义的pInternalPointer指针。所以会从头遍历。
但是while循环中却什么元素也拿不到，因为它不会去调整这个指针，所以什么元素也拿不到，因为经历过foreach的遍历，指针已经移动到最后一个元素了。

{% highlight php %}
<?php  
$arr[2] = 'huixinchen';  
$arr[1]  = 2007;  
$arr[0]  = 2008;  
foreach ($arr as $key => $val) {  
//现在结果又是什么?  
}  
?>    
{% endhighlight %}   

因为foreach的遍历是根据pListNext和pListLast指针进行遍历的，而这个指针是根据元素插入的顺序进行便利的，所以遍历的顺序就是插入的顺序，而不是根据索引的顺序进行遍历。


