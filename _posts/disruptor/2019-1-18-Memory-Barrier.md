---
layout: post
category : Disruptor
tagline: Disruptor
tags : [Disruptor, Padding]
---
{% include JB/setup %}

[官方技术文档翻译目录](http://ifeve.com/disruptor/)
[该篇文档地址](http://ifeve.com/disruptor-cacheline-padding/)

## 缓存结构

![](/images/CPUCache.png)

寄存器以及1/2级缓存为每个核独享，3级缓存和主内存在各个核间共享。32位和64位的区分在于CPU的寄存器长度。

~~现在主流64位处理器，寄存器长度也就64位。缓存被分割为一个个缓存行，缓存行的长度与寄存器长度一致，一般也是64位。Java中Long型为8位，所以一个缓存行容纳8个Long型数字。~~

CPU为64**位**，缓存行为64**字节**，1字节（Byte）=8位（bit），两者没有关联。一个Long型为8字节。

## 为何需要填充缓存行
环形队列用的是数组，头指针和尾指针在离得较近的时候可能处于同一缓存行里。但是头指针和尾指针一般是由不同线程处理的（头指针由消费者处理移动，尾指针由提供者处理移动）。不同线程跑在不同的CPU核里。当消费者修改了头指针后，这个缓存行会被置为无效，引发其他CPU核里的该缓存行也被置为无效，尽管在其他核中未修改该缓存行。

![](/images/FalseSharingReadTail.png)

缓存行经常被无效刷新引发很多无效操作，减慢操作速度。所以需要将头指针和尾指针分散在不同的缓存行里，这里使用了填充缓存行技术。
> 源码参考com.lmax.disruptor.RingBuffer类

```
    public static final long INITIAL_CURSOR_VALUE = Sequence.INITIAL_VALUE;
    protected long p1, p2, p3, p4, p5, p6, p7;
```

INITIAL_CURSOR_VALUE为真实需要的值，p1~p7仅用于占坑，使得整个对象正好占据一个缓存行。

## 填充缓存行性能代码比较
```
public class PaddingTest {
    private static int arrayLength = 4;
    private static int loopCount = 200000000;
    private static PaddingItem[] paddingItems = new PaddingItem[arrayLength];

    static {
        for (int i = 0; i < arrayLength; i++) {
            paddingItems[i] = new PaddingItem();
        }
    }
    public static void main(String[] args) throws InterruptedException {

        Thread[] threads = new Thread[arrayLength];

        for ( int i = 0; i < arrayLength; i++) {
            final int counter = i;
            threads[i] = new Thread(() -> {
                for (long j = 0; j < loopCount; j++) {
                    paddingItems[counter].value = j;
                }
            });
        }

        Long timeBegin = System.nanoTime();

        for ( int i = 0; i < arrayLength; i++) {
            threads[i].start();
        }

        for ( int i = 0; i < arrayLength; i++) {
            threads[i].join();
        }

        System.out.println("Running time: " + (System.nanoTime() - timeBegin));

    }

    static class PaddingItem {
        public volatile Long value = 0L;
        // 注释改行获得为填充的测试效果
        public Long p1, p2, p3, p4, p5, p6, p7;
    }

}
```

### 结果：
- 填充
Running time: 10507268058
Running time: 11907021800
Running time: 12659525350
- 未填充
Running time: 13122762171
Running time: 13275903759
Running time: 13805552933

### 结论和注意事项：
- 填充后速度会略快，但影响不会相当大，在不极端榨取性能的情况下并不用考虑。
- PaddingItem的value必须要加volatile关键字，才能将该属性放到主内存中，施加内存屏障，才有测试效果。
- 要使用System.nanoTime()，精度才够，而不是使用System.currentTimeMillis()。