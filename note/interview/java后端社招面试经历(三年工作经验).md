https://www.nowcoder.com/discuss/409450





最近打算换工作，对面是经验做一些总结，今后也是打算开启博客总结自己工作中遇到的一些问题分享给大家，算是一个开始吧！先说下整体面试下来的一些感受：

1.java基础知识真的要扎实，面试准备阶段不像考试有题可压，任何一个问题都有可能都会问到，所以，对自己负责，欺骗自己等于拿自己的事业开玩笑。

 2.大部分的面试官不是真的要问倒你，他们只是想看看你的解决思路和套路是否能够灵活多变，问到一个你不知道，你就说不知道了，那这个还怎么继续。所有的问题都有相通性，找到相似的场景扩展自己的思路。

 3.深入浅出！大部分的面试官都喜欢刨根接地的问，从简单的应用到底层原理再到某一个点，不要仅仅是知道了解，要有一定深度的学习

4.关于薪资，八仙过海各显神通，看你自己能力，只要你有能力，要多少还不是你自己说了算么！



# 有赞

### hashMap原理,put和resize过程

**1.hashMap原理：**

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





# 今日头条

### JVM内存模型

1.程序计数器：线程私有，用来程序跳转，流程控制

2.方法区（1.8叫元数据区）：线程共享，用于存储类信息、常量、静态变量等信息

3.Java虚拟机栈：线程私有，用于方法调用**Java 虚拟机栈会出现两种错误：StackOverFlowError 和 OutOfMemoryError**

4.堆：线程私有，主要的内存区域，存储对象实例，垃圾回收主要针对这一块。

5.本地方法栈：线程共享，本地方法被执行的时候，在本地方法栈也会创建一个栈帧，用于存放该本地方法的局部变量表、操作数栈、动态链接、出口信息。

参考：[京东18届一年半经验社招.md#运行时数据区域内存模型必考](https://gitee.com/zlnnjit/cs-inter/blob/master/note/interview/京东18届一年半经验社招.md#运行时数据区域内存模型必考)



### G1和CMS垃圾回收器

G1收集器：**是一种以获取最短回收停顿时间为目标的收集器**。

**过程：**

①初始标记：GC Roots---->STW

②并发标记：GC Roots Tracing

③重新标记：修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录--->STW

④并发清除： 开启用户线程，同时 GC 线程开始对为标记的区域做清扫。

**缺点：**

①



推荐阅读：

[弄明白CMS和G1，就靠这一篇了](https://yuanrengu.com/2020/4c889127.html)





await和sleep区别

spring生命周期，几种scope区别

linux常用指令

java多线程，线程池的选型，为什么要选这个，底层实现原理