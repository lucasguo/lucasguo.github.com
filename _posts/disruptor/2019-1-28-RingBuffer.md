---
layout: post
category : Disruptor
tagline: Disruptor
tags : [Disruptor, RingBuffer]
---
{% include JB/setup %}

[官方技术文档翻译目录](http://ifeve.com/disruptor/)
[该篇文档地址](http://ifeve.com/dissecting-disruptor-whats-so-special/)

## 取模方式
同hashmap，模数使用2的整数倍，然后可以使用位运算进行取模，速度更快
> num & (count - 1)

## 数据结构
使用了数组结构，相对于链表结构，由于元素内存地址连续相邻，可以被缓冲进同一缓冲行，能获得更好的速度。