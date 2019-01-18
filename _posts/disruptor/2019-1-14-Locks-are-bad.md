---
layout: post
category : Disruptor
tagline: Disruptor
tags : [Disruptor, Lock]
---
{% include JB/setup %}

[官方技术文档翻译目录](http://ifeve.com/disruptor/)
[该篇文档地址](http://ifeve.com/locks-are-bad/)

## 锁对性能的影响

> Disruptor论文中讲述了我们所做的一个实验。这个测试程序调用了一个函数，该函数会对一个64位的计数器循环自增5亿次。当单线程无锁时，程序耗时300ms。如果增加一个锁（仍是单线程、没有竞争、仅仅增加锁），程序需要耗时10000ms，慢了两个数量级。更令人吃惊的是，如果增加一个线程（简单从逻辑上想，应该比单线程加锁快一倍），耗时224000ms。使用两个线程对计数器自增5亿次比使用无锁单线程慢1000倍。并发很难而锁的性能糟糕。

### 代码测试
- 无锁
```
    static volatile  int counter = 0;
    static int countLoop = 2000000000;

    public static void main(String[] args) throws InterruptedException {
        long startTime = System.currentTimeMillis();

        for ( int i  = 1; i <= countLoop; i++) {
            increaseCounter();
        }
        System.out.println(counter);
        System.out.println(System.currentTimeMillis() - startTime);
    }

    private static void increaseCounter() {
        counter ++;
    }
```


2000000000
16963


- 加锁
```
    static volatile  int counter = 0;
    static int countLoop = 2000000000;

    public static void main(String[] args) throws InterruptedException {
        long startTime = System.currentTimeMillis();

        for ( int i  = 1; i <= countLoop; i++) {
            increaseCounter();
        }
        System.out.println(counter);
        System.out.println(System.currentTimeMillis() - startTime);
    }

    private static **synchronized** void increaseCounter() {
        counter ++;
    }
```

> JDK1.8
2000000000
49824

> JDK1.6
2000000000
59111

- 双线程
```
    static volatile  int counter = 0;
    static int countLoop = 2000000000;

    public static void main(String[] args) throws InterruptedException {
        long startTime = System.currentTimeMillis();

        Runnable runner = () -> {
            for (int i = 1; i <= countLoop / 2; i++) {
                increaseCounter();
            }
        };
        Thread t1 = new Thread(runner);
        Thread t2 = new Thread(runner);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(counter);
        System.out.println(System.currentTimeMillis() - startTime);
    }

    private static synchronized void increaseCounter() {
        counter ++;
    }
```

2000000000
63471

- CAS
```
    static AtomicInteger counter = new AtomicInteger();
    static int countLoop = 2000000000;

    public static void main(String[] args) throws InterruptedException {
        long startTime = System.currentTimeMillis();

        Runnable runner = () -> {
            for (int i = 1; i <= countLoop / 2; i++) {
                increaseCounter();
            }
        };
        Thread t1 = new Thread(runner);
        Thread t2 = new Thread(runner);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(counter);
        System.out.println(System.currentTimeMillis() - startTime);
    }

    private static void increaseCounter() {
        counter.incrementAndGet();
    }
```

2000000000
22359

## 队列的劣势
多线程情况下，队列头指针和尾指针会产生冲突。
