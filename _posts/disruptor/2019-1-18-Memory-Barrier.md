---
layout: post
category : Disruptor
tagline: Disruptor
tags : [Disruptor, Memory Barrier]
---
{% include JB/setup %}

[官方技术文档翻译目录](http://ifeve.com/disruptor/)
[该篇文档地址](http://ifeve.com/disruptor-memory-barrier/)

## 内存屏障
是一种CPU指令。
- 保证执行顺序，防止指令重排
- 分为读屏障和写屏障，读屏障保证该值已被更新到最新值，写屏障保证修改的值已刷新
- java的volatile关键字，在读取该字段前插入读屏障，在写入该字段后插入写屏障

源码在[com.lmax.disruptor.WorkProcessor](https://github.com/lucasguo/disruptor/blob/master/src/main/java/com/lmax/disruptor/WorkProcessor.java),已变更为使用cas实现

其中一块可以借鉴，使用AtomicBoolean判断是否已运行，而不使用synchronize做同步