---
layout: post
category : 算法
tagline: skip list
tags : [数据结构,skip list]
---
{% include JB/setup %}

### 结构
Skip List是有序结构，一种空间换时间的数据结构。
每个节点上都有随机的冗余数，冗余量的多少决定该节点数据能到达第几层。
![](/images/skiplist1.png)


### 查找
查找过程：
先从最顶级找起，判断落在顶层的哪个区间里，然后往下跳，在下一层的这个区间找，直到在某一层命中或者找到最后一层都不存在则该元素不存在。
![](/images/skiplist2.png)
因为上层节点都是跳着找，所以叫skip list，查找方法有点类似于二分查找法，基本操作的时间复杂度为O(logn)（包括插入、删除、查找）。

### 对比
与平衡树比，占用更多空间，而且实际查找对象等操作会比平衡树不稳定。
但是在指定了要取第几位的元素的时候，skip list更有利。

### Redis ZSet
Redis zset的底层实现为skip list而不是balanced tree，因为在redis的应用场景下，skip list和balanced tree差别没那么大，而skip list更便于实现和调试。
>1) They are not very memory intensive. It's up to you basically. Changing parameters about the probability of a node to have a given number of levels will make then less memory intensive than btrees.
>2) A sorted set is often target of many ZRANGE or ZREVRANGE operations, that is, traversing the skip list as a linked list. With this operation the cache locality of skip lists is at least as good as with other kind of balanced trees.
>3) They are simpler to implement, debug, and so forth. For instance thanks to the skip list simplicity I received a patch (already in Redis master) with augmented skip lists implementing ZRANK in O(log(N)). It required little changes to the code.
>                       https://stackoverflow.com/questions/45115047/why-redis-sortedset-uses-skip-list-instead-of-balanced-tree
