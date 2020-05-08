> https://www.nowcoder.com/discuss/409450

最近打算换工作，对面是经验做一些总结，今后也是打算开启博客总结自己工作中遇到的一些问题分享给大家，算是一个开始吧！先说下整体面试下来的一些感受：

1.java基础知识真的要扎实，面试准备阶段不像考试有题可压，任何一个问题都有可能都会问到，所以，对自己负责，欺骗自己等于拿自己的事业开玩笑。

 2.大部分的面试官不是真的要问倒你，他们只是想看看你的解决思路和套路是否能够灵活多变，问到一个你不知道，你就说不知道了，那这个还怎么继续。所有的问题都有相通性，找到相似的场景扩展自己的思路。

 3.深入浅出！大部分的面试官都喜欢刨根接地的问，从简单的应用到底层原理再到某一个点，不要仅仅是知道了解，要有一定深度的学习

4.关于薪资，八仙过海各显神通，看你自己能力，只要你有能力，要多少还不是你自己说了算么！



## 有赞

### HashMap原理,put和resize过程

**1.HashMap原理：**

hashMap 是非线程安全的， hashMap 1.7的底层实现为数组（table[]）＋链表(LinkList-->Entry)，hashmap 1.8底层为数组+链表/红黑树（当链表长度到达阈值TREEIFY_THRESHOLD（默认为8）时，会转化为红黑树）

**2.put过程：**

①查看数组是否需要初始化

②根据key计算hashcode

③根据hashcode计算出桶位置

④遍历链表，查看key值与链表节点的key值是否相等，如果相等的话，那么进行覆盖旧值，并返回旧值。1.8的话需要先查看链表长度是否达到阈值，如果达到阈值，先进行红黑树转化然后再进行检查扩容。

⑤新增的时候需要检查是否需要扩容，需要扩容的话进行两倍扩容，扩容完成后进行插入新值。

**3.resize过程：**

resize扩容需要从四个方面来进行回答：
①.**什么时候触发resize?** 当容量超过当前容量（默认容量16）乘以负载因子（默认0.75）就会进行扩容，扩容大小为当前大小的两倍（扩展问题，为啥是两倍：通过限制length是一个2的幂数，h & (length-1)和h % length结果是一致的）。
②.**resize是如何hash的**：h & (length-1)
③**.resize是如何进行链表操作的**：使用头插法进行数据插入，每次新put的值放在头部
④.**并发操作下，链表是如何成环的**：HashMap的环：若当前线程此时获得ertry节点，但是被线程中断无法继续执行，此时线程二进入transfer函数，并把函数顺利执行，此时新表中的某个位置有了节点，之后线程一获得执行权继续执行，因为并发transfer，所以两者都是扩容的同一个链表，当线程一执行到e.next = new table[i] 的时候，由于线程二之前数据迁移的原因导致此时new table[i] 上就有ertry存在，所以线程一执行的时候，会将next节点，设置为自己，导致自己互相使用next引用对方，因此产生链表，导致死循环。

推荐阅读：

[HashMap? ConcurrentHashMap? 相信看完这篇没人能难住你！](https://crossoverjie.top/2018/07/23/java-senior/ConcurrentHashMap/#comments)

[HashMap工作原理和扩容机制](https://blog.csdn.net/u014532901/article/details/78936283)



### 线程池有哪些类型

①FixedThreadPool:创建可重用固定线程数的线程池。

```java
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```



②SingleThreadPool:创建只有一个线程的线程池。

```java
    public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>(),
                                    threadFactory));
    }
```



③CachedThreadPool:一个可根据需要创建新线程的线程池，**如果现有线程没有可用的，则创建一个新线程并添加到池中，如果有被使用完但是还没销毁的线程，就复用该线程。终止并从缓存中移除那些已有 60 秒钟未被使用的线程。因此，长时间保持空闲的线程池不会使用任何资源。**

```java
    public static ExecutorService newCachedThreadPool(ThreadFactory threadFactory) {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>(),
                                      threadFactory);
    }
```



④ScheduledThreadPool：创建一个线程池，它可安排在给定延迟后运行命令或者定期地执行。

拓展：为啥不推荐使用①②③类型类来创建新的线程池？因为允许请求的队列长度为Integer.MAX_VALUE，可能会累积大量的请求，从而导致OOM。





###  ConcurrentHashMap分段锁原理，java8和java7实现的区别

 **1.ConcurrentHashMap分段锁原理:**

ConcurrentHashMap采用了分段锁技术，其中Segement继承了RecentLock，当ConcurrentHashMap进行get、put操作时，均是同步的。各个Segement之间的get、put操作可以进行并发，即当一个线程访问ConcurrentHashMap的Segement时，不会影响对其他Segement的访问。

**2.java7的实现？**

java7采用数组+链表的底层数据结构实现。

**3.java8的实现？**

由于java7的实现在链表查询遍历元素的时候，时间复杂度为O(n)，当一个Segement的链表元素过多时，性能表现不是很好，因此Java8采用数据+链表/红黑树的方式实现。当链表的长度大于阈值8时，会转化成红黑树。**其中抛弃了原有的 Segment 分段锁，而采用了 `CAS + synchronized` 来保证并发安全性。**

总的来说三点区别：

数组+链表--->数据+链表/红黑树

分段锁RecentLock--->CAS + synchronized

HashEntry--->Node

推荐阅读：

[HashMap? ConcurrentHashMap? 相信看完这篇没人能难住你！](https://crossoverjie.top/2018/07/23/java-senior/ConcurrentHashMap/#comments)



### B-树和B+树区别，数据库索引原理

 **1.B-树和B+树区别**

二叉搜索树，主要特点：所有节点最多有两个儿子，并且所有节点只能存储一个关键字，同时，当前节点的左指针指向比关键字自己小的节点，当前节点的右指针指向比自己关键字大的节点。

B-树和B树是一个概念，是多路搜索树（**相比于二叉搜索树，IO次数更少**）。B-树的特性：

1. 关键字集合分布在整颗树中；
2. 任何一个关键字出现且只出现在一个结点中；
3. 搜索有可能在非叶子结点结束；
4. 其搜索性能等价于在关键字全集内做一次二分查找；

其最底搜索性能为O(logN)



B+树是B-树的变体，也是一种多路搜索树    B+的特性：

​    1.所有关键字都出现在叶子结点的链表中（稠密索引），且链表中的关键字恰好

是有序的；

​    2.不可能在非叶子结点命中；

​    3.非叶子结点相当于是叶子结点的索引（稀疏索引），叶子结点相当于是存储

（关键字）数据的数据层；

​    4.更适合文件索引系统；



**B+树的优势：**

- 单一节点存储更多的元素，使得查询的IO次数更少。
- 所有查询都要查找到叶子节点，查询性能稳定。
- 所有叶子节点形成有序链表，便于范围查询。

B树--->MongoDB  B+树--->MySQL

推荐阅读：

[B树，B-树和B+树、B*树的区别](https://blog.csdn.net/qq_22613757/article/details/81218741)

[漫画：什么是B-树？](https://mp.weixin.qq.com/s?__biz=MzI2NjA3NTc4Ng==&mid=2652079363&idx=1&sn=7c2209e6b84f344b60ef4a056e5867b4&chksm=f1748ee6c60307f084fe9eeff012a27b5b43855f48ef09542fe6e56aab6f0fc5378c290fc4fc&scene=0&pass_ticket=75GZ52L7yYmRgfY0HdRdwlWLLEqo5BQSwUcvb44a7dDJRHFf49nJeGcJmFnj0cWg#rd)

[漫画：什么是B+树？](https://blog.csdn.net/qq_26222859/article/details/80631121)

spring生命周期，几种scope区别，aop实现有哪几种实现，接口代理和类代理会有什么区别



**2.数据库索引原理**

MyISAM索引实现：MyISAM引擎使用B+Tree作为索引结构，叶节点的data域存放的是数据记录的地址。

Innodb索引实现：

第一个重大区别是InnoDB的数据文件本身就是索引文件。MyISAM索引文件和数据文件是分离的，索引文件仅保存数据记录的地址。而在InnoDB中，表数据文件本身就是按B+Tree组织的一个索引结构，这棵树的叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键，因此InnoDB表数据文件本身就是主索引。

第二个与MyISAM索引的不同是InnoDB的辅助索引data域存储相应记录主键的值而不是地址。换句话说，InnoDB的所有辅助索引都引用主键作为data域。

推荐阅读：

[数据库索引原理及优化](https://blog.csdn.net/suifeng3051/article/details/52669644)



### 组合索引怎么使用？最左匹配的原理

**1.组合索引怎么使用？最左匹配的原理**

例如组合索引（a,b,c），组合索引的生效原则是 

从前往后依次使用生效，如果中间某个索引没有使用，那么断点前面（**范围值也算断点，orderby不算断点，用到索引**）的索引部分起作用，断点后面的索引没有起作用；



**2.最左匹配的原理**

以**最左边的为起点**任何连续的索引都能匹配上



推荐阅读：

[组合索引的使用效果的总结](https://www.cnblogs.com/hongmoshui/p/10429842.html)



### Spring生命周期

Bean 的生命周期概括起来就是 **4 个阶段**：

1. 实例化（Instantiation）
2. 属性赋值（Populate）
3. 初始化（Initialization）
   1. 初始化前：
      1. 检查 Aware 相关接口并设置相关依赖(BeanNameAware、BeanClassLoaderAware、BeanFactoryAware)
      2. BeanPostProcessor 前置处理
   2. 初始化中：
      1. 若实现 InitializingBean 接口，调用 afterPropertiesSet() 方法
      2. 若配置自定义的 init-method方法，则执行
   3. 初始化后：
      1. BeanPostProceesor 后置处理
4. 销毁（Destruction）
   1. 若实现 DisposableBean 接口，则执行 destory()方法
   2. 若配置自定义的 detory-method 方法，则执行



推荐阅读：

[如何记忆 Spring Bean 的生命周期](https://juejin.im/post/5e4791a7f265da5715630629)

[Spring Bean的生命周期（非常详细）](https://www.cnblogs.com/zrtqsk/p/3735273.html)



### Spring几种scope区别？

1.singleton:Spring的IOC容器中只有一个实例bean，该值为scope的默认值

2.prototype：每次getBean时都会创建一个新的实例

3.request:每次请求都会创建一个实体bean

4.session：每次session请求时都会创建一个实体bean

5.globalsession:每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。



推荐阅读：

[Spring中的scope配置和@scope注解](https://blog.csdn.net/Tracycater/article/details/54019223)





### Spring AOP实现有哪几种实现，接口代理和类代理会有什么区别?

Spring AOP有两种实现，均为动态代理：

1.JDK动态代理：基于反射进行动态代理，核心类是InvocationHandker类和Proxy类，被代理的类必须实现接口

2.CGLIB动态代理：被代理类无需实现接口，主要实现MethodInterceptor接口即可实现代理



Spring AOP如果代理的类存在接口，优先使用JDK动态代理，否则使用CGLIB动态代理。



推荐阅读：

[Spring AOP实现原理](https://juejin.im/post/5af3bd6f518825673954bf22#heading-4)



### 斐波拉契数列非递归实现

```java
public static int fib(int n) {
    if(n<=2)return 1;
    int first=1;
    int second=1;
    int sum=0;
    while(n>2) {
        sum=first+second;
        first=second;
        second=sum;
        n--;
    }    
    return second;
} 
```



### 短URL实现

自增序列算法、 摘要算法

推荐阅读：

[短网址(short URL)系统的原理及其实现](https://hufangyun.com/2017/short-url/)





## 今日头条

### JVM内存模型

1.程序计数器：线程私有，用来程序跳转，流程控制

2.方法区（1.8叫元数据区）：线程共享，用于存储类信息、常量、静态变量等信息

3.Java虚拟机栈：线程私有，用于方法调用**Java 虚拟机栈会出现两种错误：StackOverFlowError 和 OutOfMemoryError**

4.堆：线程私有，主要的内存区域，存储对象实例，垃圾回收主要针对这一块。

5.本地方法栈：线程共享，本地方法被执行的时候，在本地方法栈也会创建一个栈帧，用于存放该本地方法的局部变量表、操作数栈、动态链接、出口信息。

参考：[京东18届一年半经验社招.md#运行时数据区域内存模型必考](https://gitee.com/zlnnjit/cs-inter/blob/master/note/interview/京东18届一年半经验社招.md#运行时数据区域内存模型必考)



### G1和CMS垃圾回收器

CMS收集器：**是一种以获取最短回收停顿时间为目标的收集器**。

**过程：**

①初始标记：标记GC Roots能直接关联到的对象，需要在safepoint位置暂停所有执行线程。---->STW

②并发标记：进行GC Roots Tracing，遍历完从root可达的所有对象。该阶段与工作线程并发执行。

③重新标记：修正并发标记期间因用户程序继续运作而导致标记产生表动的那一部分对象的标记记录。需要在safepoint位置暂停所有执行线程。--->STW

④并发清除： 开启用户线程，同时 GC 线程开始对为标记的区域做清扫。

**优点：**

并发收集、低停顿。

**缺点：**

①CMS收集器对CPU资源非常敏感。

②CMS收集器无法处理浮动垃圾（Floating Garbage）。

③CMS收集器是基于标记-清除算法，该算法缺点都有：标记和清除效率低/产生大量不连续的内存碎片。

④停顿时间是不可预期的。



G1收集器：**重新定义了堆空间，打破了原有的分代模型，将堆划分为一个个区域。这么做的目的是在进行收集时不必在全堆范围内进行，这是它最显著的特点。**

**过程：**

①初始标记：标记GC Roots 可以直接关联的对象，该阶段需要线程停顿但是耗时短。---->STW

②并发标记：寻找存活的对象，可以与其他程序并发执行，耗时较长。

③最终标记：并发标记期间用户程序会导致标记记录产生变动（好比一个阿姨一边清理垃圾，另一个人一边扔垃圾）虚拟机会将这段时间的变化记录在Remembered Set Logs 中。最终标记阶段会向Remembered Set合并并发标记阶段的变化。这个阶段需要线程停顿，也可以并发执行---->STW

④筛选回收：对每个Region的回收成本进行排序，按照用户自定义的回收时间来制定回收计划

**优点：**

①空间整合：G1使用Region独立区域概念，G1利用的是标记复制法，不会产生垃圾碎片

②分代收集：G1可以自己管理新生代和老年代

③并行于并发：G1可以通过机器的多核来并发处理 STW停顿，减少停顿时间，并且可不停顿java线程执行GC动作，可通过并发方式让GC和java程序同时执行。

④可预测停顿：G1除了追求停顿时间，还建立了可预测停顿时间模型，能让制定的M毫秒时间片段内，消耗在垃圾回收器上的时间不超过N毫秒

**缺点：**

G1 需要记忆集 (具体来说是卡表)来记录新生代和老年代之间的引用关系，这种数据结构在 G1 中需要占用大量的内存，可能达到整个堆内存容量的 20% 甚至更多。而且 G1 中维护记忆集的成本较高，带来了更高的执行负载，影响效率。

**按照《深入理解Java虚拟机》作者的说法，CMS 在小内存应用上的表现要优于 G1，而大内存应用上 G1 更有优势，大小内存的界限是6GB到8GB。**

推荐阅读：

[弄明白CMS和G1，就靠这一篇了](https://yuanrengu.com/2020/4c889127.html)

[g1和cms区别](https://blog.csdn.net/u010310183/article/details/102790573)





### wait/await和sleep区别

- 两者最主要的区别在于：**sleep 方法没有释放锁，而 wait 方法释放了锁** 。
- 两者都可以暂停线程的执行。
- wait 通常被用于线程间交互/通信，sleep 通常被用于暂停执行。
- wait() 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 notify() 或者 notifyAll() 方法。sleep() 方法执行完成后，线程会自动苏醒。或者可以使用 wait(long timeout)超时后线程会自动苏醒。





### Spring生命周期，几种scope区别

参考：

[Spring生命周期](#Spring生命周期)

[Spring几种scope区别？](#Spring几种scope区别？)



### Linux常用指令

top/free/df/mv/cp/sed/scp......



### 线程池底层实现原理

参考：

[京东18届一年半经验社招.md#核心线程池threadpoolexecutor的参数必考](https://gitee.com/zlnnjit/cs-inter/blob/master/note/interview/京东18届一年半经验社招.md#核心线程池threadpoolexecutor的参数必考)





## 网易

### RPC原理

**1.为什么会出现RPC?**

RPC(Remote Procedure Call Protocol)——远程过程调用协议。

一般来说，自己写程序然后本地调用，这种程序的特点是服务的消费方和提供方。当我们进入公司时，面对的很可能就是成千上万的服务提供方，这时候就需要使用RPC来进行远程服务调用。RPC将原来的本地调用转变为调用远端的服务器上的方法，给系统的处理能力和吞吐量带来了近似于无限制提升的可能。

**2.RPC的组成**

客户端：服务的调用方

客户端存根：存放服务端的地址消息，再将客户端的请求参数打包成网络消息，然后通过网络远程发送给服务方。

服务端：真正的服务提供者。

服务端存根：接收客户端发送过来的消息，将消息解包，并调用本地的方法。

**3.其他问题**

RPC的过程？

如何做到透明化远程服务调用？动态代理，把本地调用代理成网络调用

如何进行服务发布？Zookeeper

如何进行序列化与反序列化？Protobuf、Thrift、Avro

如何进行通信？NIO--->Netty



推荐阅读：

[RPC原理解析](https://www.cnblogs.com/swordfall/p/8683905.html)

[你应该知道的RPC原理](https://www.cnblogs.com/LBSer/p/4853234.html)





### HashMap原理

参考[HashMap原理,put和resize过程](#HashMap原理,put和resize过程)



### Redis缓存回收机制

**1.Redis缓存回收机制**

数据过期：

①定时删除策略：Redis启动一个定时器监控所有的key,一旦有过期的话就进行删除（遍历所有key,非常耗费CPU）

②惰性删除策略：获取key的时候判断是否过期， 过期则进行删除

Redis采用的方式:①(随机抓取一部分key进行检测)+②



内存淘汰：

①noeviction：当内存不足以容纳新写入数据时，新写入操作会报错。（Redis 默认策略）

②allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 Key。（LRU推荐使用）

③allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个 Key。

④volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的 Key。这种情况一般是把 Redis 既当缓存，又做持久化存储的时候才用。 

⑤volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个 Key。

⑥volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的 Key 优先移除。不推荐。如果没有对应的键，则回退到noeviction策略。



参考：

[京东18届一年半经验社招.md#redis的数据过期策略必考](https://github.com/zlnnjit/cs-inter/blob/master/note/interview/京东18届一年半经验社招.md#redis的数据过期策略必考)

推荐阅读：

[Redis内存回收策略](https://www.jianshu.com/p/6a5eb0ddf57b)



### Redis主从同步

**1.主从复制作用**

①数据冗余

②故障恢复（服务冗余）

③负载均衡

④读写分离（主节点写操作、从节点读操作）



**2.主从复制过程**

①连接建立阶段

+ 步骤1：保存主节点信息
+ 步骤2：建立socket连接
+ 步骤3：发送ping命令
+ 步骤4：身份验证
+ 步骤5：发送从节点端口信息

②数据同步阶段

从节点向主节点发送psync命令

根据主从节点当前状态的不同，可以分为全量复制和部分复制

③命令传播阶段

主从节点进入命令传播阶段；在这个阶段主节点将自己执行的写命令发送给从节点，从节点接收命令并执行，从而保证主从节点数据的一致性。



**3.介绍全量复制和部分复制**

全量复制：用于初次复制或其他无法进行部分复制的情况，将主节点中的所有数据都发送给从节点，是一个非常重型的操作。

部分复制：用于网络中断等情况后的复制，只将中断期间主节点执行的写命令发送给从节点，与全量复制相比更加高效。需要注意的是，如果网络中断时间过长，导致主节点没有能够完整地保存中断期间执行的写命令，则无法进行部分复制，仍使用全量复制。



**4.主从复制缺点**

故障恢复无法自动化；写操作无法负载均衡；存储能力受到单机的限制。



推荐阅读：

[深入学习Redis（3）：主从复制](https://www.cnblogs.com/kismetv/p/9236731.html)



### Redis哨兵机制

**1.为什么会有哨兵机制？**

在主从复制的基础上，哨兵实现了自动化的故障恢复。



**2.哨兵机制作用？**

- 监控（Monitoring）：哨兵会不断地检查主节点和从节点是否运作正常。
- 自动故障转移（Automatic failover）：当主节点不能正常工作时，哨兵会开始自动故障转移操作，它会将失效主节点的其中一个从节点升级为新的主节点，并让其他从节点改为复制新的主节点。
- 配置提供者（Configuration provider）：客户端在初始化时，通过连接哨兵来获得当前Redis服务的主节点地址。
- 通知（Notification）：哨兵可以将故障转移的结果发送给客户端。



**3.哨兵机制节点组成？**

它由两部分组成，哨兵节点和数据节点：

- 哨兵节点：哨兵系统由一个或多个哨兵节点组成，哨兵节点是特殊的redis节点，**不存储数据**。
- 数据节点：主节点和从节点都是数据节点。



**4.哨兵机制原理？**

（1）定时任务：每个哨兵节点维护了3个定时任务。定时任务的功能分别如下：通过向主从节点发送info命令获取最新的主从结构；通过发布订阅功能获取其他哨兵节点的信息；通过向其他节点发送ping命令进行心跳检测，判断是否下线。

（2）主观下线：在心跳检测的定时任务中，如果其他节点超过一定时间没有回复，哨兵节点就会将其进行主观下线。顾名思义，主观下线的意思是一个哨兵节点“主观地”判断下线；与主观下线相对应的是客观下线。

（3）客观下线：哨兵节点在对主节点进行主观下线后，会通过sentinel is-master-down-by-addr命令询问其他哨兵节点该主节点的状态；如果判断主节点下线的哨兵数量达到一定数值，则对该主节点进行客观下线。

需要特别注意的是，客观下线是主节点才有的概念；如果从节点和哨兵节点发生故障，被哨兵主观下线后，不会再有后续的客观下线和故障转移操作。

（4）选举领导者哨兵节点：当主节点被判断客观下线以后，各个哨兵节点会进行协商，选举出一个领导者哨兵节点，并由该领导者节点对其进行故障转移操作。

监视该主节点的所有哨兵都有可能被选为领导者，选举使用的算法是**Raft算法**；Raft算法的基本思路是先到先得：即在一轮选举中，哨兵A向B发送成为领导者的申请，如果B没有同意过其他哨兵，则会同意A成为领导者。选举的具体过程这里不做详细描述，一般来说，哨兵选择的过程很快，谁先完成客观下线，一般就能成为领导者。

（5）**故障转移：选举出的领导者哨兵，开始进行故障转移操作，该操作大体可以分为3个步骤：**

- 在从节点中选择新的主节点：选择的原则是，首先过滤掉不健康的从节点；然后选择优先级最高的从节点(由slave-priority指定)；如果优先级无法区分，则选择复制偏移量最大的从节点；如果仍无法区分，则选择runid最小的从节点。
- 更新主从状态：通过slaveof no one命令，让选出来的从节点成为主节点；并通过slaveof命令让其他节点成为其从节点。
- 将已经下线的主节点(即6379)设置为新的主节点的从节点，当6379重新上线后，它会成为新的主节点的从节点。



**4.哨兵机制缺点**

写操作无法负载均衡；存储能力受到单机的限制。（Redis集群解决了该情况）

推荐阅读：

[深入学习Redis（4）：哨兵](https://www.cnblogs.com/kismetv/p/9609938.html)



### Zookeeper锁是如何实现的

一般使用Curator进行使用Zookeeper锁，例如有两个客户端A和客户端B,首先A先在锁节点下创建例如01子节点的锁，然后再获取节点信息，发现自己的01节点排名第一，那么就获得锁。

客户端B也需要获取锁，现在锁节点下创建例如02的子节点，然后再获取锁节点信息，发现锁节点信息为[01,02],并不排第一，因此获取不到锁，客户端B会在他的顺序节点的**上一个顺序节点加一个监听器。**

当客户端A使用完锁，删除01节点，客户端B获取到01删除的监听，然后发现自己的02节点排名第一，那么就获取到锁。

推荐阅读：

[七张图彻底讲清楚ZooKeeper分布式锁的实现原理](https://juejin.im/post/5c01532ef265da61362232ed)





### 分布式缓存读写不一致问题

参考：[京东18届一年半经验社招.md#redis与mysql双写一致性方案](https://github.com/zlnnjit/cs-inter/blob/master/note/interview/%E4%BA%AC%E4%B8%9C18%E5%B1%8A%E4%B8%80%E5%B9%B4%E5%8D%8A%E7%BB%8F%E9%AA%8C%E7%A4%BE%E6%8B%9B.md#redis%E4%B8%8Emysql%E5%8F%8C%E5%86%99%E4%B8%80%E8%87%B4%E6%80%A7%E6%96%B9%E6%A1%88)

推荐阅读：

[Redis与Mysql双写一致性方案解析](https://zhuanlan.zhihu.com/p/59167071)



### Java线程你是怎么使用的

①如何创建线程：

+ Thread
+ Runnable
+ Callable
+ Executors工具类
+ ThreadPoolExecutor对象

②主要讲线程池

### 数据库是如何调优的

1.数据表加合适的索引

2.针对执行计划进行优化

3.根据慢sql进行优化

4.加缓存

5.参数调优

推荐阅读：

[知乎：有哪些常见的数据库优化方法？](https://www.zhihu.com/question/36431635)



### git rebase命令发生了什么

rebase命令可以帮我们把整个提交历史变成干净清晰的一条线。

[彻底搞懂 Git-Rebase](http://jartto.wang/2018/12/11/git-rebase/?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)



