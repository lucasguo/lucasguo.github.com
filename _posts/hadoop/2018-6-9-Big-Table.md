---
layout: post
category : Hadoop
tagline: Big Table
tags : [Hadoop,Table]
---
{% include JB/setup %}

[原文](https://research.google.com/archive/bigtable-osdi06.pdf)

[翻译](http://dblab.xmu.edu.cn/post/google-bigtable/)

![](/images/big-table.jpg)

三维可以指定一个值

(rowkey,columnname,timestamp) -> value

- rowkey
存储时会按字典顺序排序。Google在用域名做key时是倒叙的，例如com.baidu.www。这样能保证相同的域名的连续性而不会因为二级域名等原因被隔离。

- columnname
列名由列族和子列名构成。列族的好处：
1. 从列族里取出来的数据就可以构成一个对象
2. 可以按列族做权限控制
3. 列族的内容可以存在同一节点或物理上接近的几个节点上，加快读取速度

- timestamp
在插入数据时会自动生成，以当前系统毫秒为单位。用于做版本控制，以及垃圾回收。可以指定数据只保存几份历史记录或是某个时间段后的记录。