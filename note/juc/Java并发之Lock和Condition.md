Java SDK 并发包内容很丰富，包罗万象，但是我觉得最核心的还是其对管程的实现。因为理论上利用管程，你几乎可以实现并发包里所有的工具类。在并发编程领域，有两大核心问题：一个是**互斥**，即同一时刻只允许一个线程访问共享资源；另一个是**同步**，即线程之间如何通信、协作。这两大问题，管程都是能够解决的。**Java SDK 并发包通过 Lock 和 Condition 两个接口来实现管程，其中 Lock 用于解决互斥问题，Condition 用于解决同步问题**。

<!--more-->

在介绍 Lock 的使用之前，有个问题需要你首先思考一下：Java 语言本身提供的 synchronized 也是管程的一种实现，既然 Java 从语言层面已经实现了管程了，那为什么还要在 SDK 里提供另外一种实现呢？难道 Java 标准委员会还能同意“重复造轮子”的方案？很显然它们之间是有巨大区别的。那区别在哪里呢？如果能深入理解这个问题，对你用好 Lock 帮助很大。下面我们就一起来剖析一下这个问题。

## 再造管程的理由

你也许曾经听到过很多这方面的传说，例如在 Java 的 1.5 版本中，synchronized 性能不如 SDK 里面的 Lock，但 1.6 版本之后，synchronized 做了很多优化，将性能追了上来，所以 1.6 之后的版本又有人推荐使用 synchronized 了。那性能是否可以成为“重复造轮子”的理由呢？显然不能。因为性能问题优化一下就可以了，完全没必要“重复造轮子”。

到这里，关于这个问题，你是否能够想出一条理由来呢？如果你细心的话，也许能想到一点。那就是我们前面在介绍死锁问题的时候，提出了一个**破坏不可抢占条件**方案，但是这个方案 synchronized 没有办法解决。原因是 synchronized 申请资源的时候，如果申请不到，线程直接进入阻塞状态了，而线程进入阻塞状态，啥都干不了，也释放不了线程已经占有的资源。但我们希望的是：

> 对于“不可抢占”这个条件，占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源，这样不可抢占这个条件就破坏掉了。

如果我们重新设计一把互斥锁去解决这个问题，那该怎么设计呢？我觉得有三种方案。

**1.能够响应中断**。synchronized 的问题是，持有锁 A 后，如果尝试获取锁 B 失败，那么线程就进入阻塞状态，一旦发生死锁，就没有任何机会来唤醒阻塞的线程。但如果阻塞状态的线程能够响应中断信号，也就是说当我们给阻塞的线程发送中断信号的时候，能够唤醒它，那它就有机会释放曾经持有的锁 A。这样就破坏了不可抢占条件了。

**2.支持超时**。如果线程在一段时间之内没有获取到锁，不是进入阻塞状态，而是返回一个错误，那这个线程也有机会释放曾经持有的锁。这样也能破坏不可抢占条件。

**3.非阻塞地获取锁**。如果尝试获取锁失败，并不进入阻塞状态，而是直接返回，那这个线程也有机会释放曾经持有的锁。这样也能破坏不可抢占条件。

这三种方案可以全面弥补 synchronized 的问题。到这里相信你应该也能理解了，这三个方案就是“重复造轮子”的主要原因，体现在 API 上，就是 Lock 接口的三个方法。详情如下：

```java
// 支持中断的 API
void lockInterruptibly() 
  throws InterruptedException;
// 支持超时的 API
boolean tryLock(long time, TimeUnit unit) 
  throws InterruptedException;
// 支持非阻塞获取锁的 API
boolean tryLock();
```

## 如何保证可见性

Java SDK 里面 Lock 的使用，有一个经典的范例，就是`try{}finally{}`，需要重点关注的是在 finally 里面释放锁。这个范例无需多解释，你看一下下面的代码就明白了。但是有一点需要解释一下，那就是可见性是怎么保证的。你已经知道 Java 里多线程的可见性是通过 Happens-Before 规则保证的，而 synchronized 之所以能够保证可见性，也是因为有一条 synchronized 相关的规则：synchronized 的解锁 Happens-Before 于后续对这个锁的加锁。那 Java SDK 里面 Lock 靠什么保证可见性呢？例如在下面的代码中，线程 T1 对 value 进行了 +=1 操作，那后续的线程 T2 能够看到 value 的正确结果吗？

```java
class X {
  private final Lock rtl =
  new ReentrantLock();
  int value;
  public void addOne() {
    // 获取锁
    rtl.lock();  
    try {
      value+=1;
    } finally {
      // 保证锁能释放
      rtl.unlock();
    }
  }
}
```



答案必须是肯定的。**Java SDK 里面锁**的实现非常复杂，这里我就不展开细说了，但是原理还是需要简单介绍一下：它是**利用了 volatile 相关的 Happens-Before 规则**。Java SDK 里面的 ReentrantLock，内部持有一个 volatile 的成员变量 state，获取锁的时候，会读写 state 的值；解锁的时候，也会读写 state 的值（简化后的代码如下面所示）。也就是说，在执行 value+=1 之前，程序先读写了一次 volatile 变量 state，在执行 value+=1 之后，又读写了一次 volatile 变量 state。根据相关的 Happens-Before 规则：

1. **顺序性规则**：对于线程 T1，value+=1 Happens-Before 释放锁的操作 unlock()；
2. **volatile 变量规则**：由于 state = 1 会先读取 state，所以线程 T1 的 unlock() 操作 Happens-Before 线程 T2 的 lock() 操作；
3. **传递性规则**：线程 T1 的 value+=1 Happens-Before 线程 T2 的 lock() 操作。

```java
class SampleLock {
  volatile int state;
  // 加锁
  lock() {
    // 省略代码无数
    state = 1;
  }
  // 解锁
  unlock() {
    // 省略代码无数
    state = 0;
  }
}
```

所以说，后续线程 T2 能够看到 value 的正确结果。

## 什么是可重入锁

如果你细心观察，会发现我们创建的锁的具体类名是 ReentrantLock，这个翻译过来叫**可重入锁**，这个概念前面我们一直没有介绍过。**所谓可重入锁，顾名思义，指的是线程可以重复获取同一把锁**。例如下面代码中，当线程 T1 执行到 ① 处时，已经获取到了锁 rtl ，当在 ① 处调用 get() 方法时，会在 ② 再次对锁 rtl 执行加锁操作。此时，如果锁 rtl 是可重入的，那么线程 T1 可以再次加锁成功；如果锁 rtl 是不可重入的，那么线程 T1 此时会被阻塞。

除了可重入锁，可能你还听说过可重入函数，可重入函数怎么理解呢？指的是线程可以重复调用？显然不是，所谓**可重入函数，指的是多个线程可以同时调用该函数**，每个线程都能得到正确结果；同时在一个线程内支持线程切换，无论被切换多少次，结果都是正确的。多线程可以同时执行，还支持线程切换，这意味着什么呢？线程安全啊。所以，可重入函数是线程安全的。

```java
class X {
  private final Lock rtl =
  new ReentrantLock();
  int value;
  public int get() {
    // 获取锁
    rtl.lock();         ②
    try {
      return value;
    } finally {
      // 保证锁能释放
      rtl.unlock();
    }
  }
  public void addOne() {
    // 获取锁
    rtl.lock();  
    try {
      value = 1 + get(); ①
    } finally {
      // 保证锁能释放
      rtl.unlock();
    }
  }
}
```



## 公平锁与非公平锁

在使用 ReentrantLock 的时候，你会发现 ReentrantLock 这个类有两个构造函数，一个是无参构造函数，一个是传入 fair 参数的构造函数。fair 参数代表的是锁的公平策略，如果传入 true 就表示需要构造一个公平锁，反之则表示要构造一个非公平锁。

```java
// 无参构造函数：默认非公平锁
public ReentrantLock() {
    sync = new NonfairSync();
}
// 根据公平策略参数创建锁
public ReentrantLock(boolean fair){
    sync = fair ? new FairSync() 
                : new NonfairSync();
}
```

我们介绍过入口等待队列，锁都对应着一个等待队列，如果一个线程没有获得锁，就会进入等待队列，当有线程释放锁的时候，就需要从等待队列中唤醒一个等待的线程。如果是公平锁，唤醒的策略就是谁等待的时间长，就唤醒谁，很公平；如果是非公平锁，则不提供这个公平保证，有可能等待时间短的线程反而先被唤醒。

## 用锁的最佳实践

你已经知道，用锁虽然能解决很多并发问题，但是风险也是挺高的。可能会导致死锁，也可能影响性能。这方面有是否有相关的最佳实践呢？有，还很多。但是我觉得最值得推荐的是并发大师 Doug Lea《Java 并发编程：设计原则与模式》一书中，推荐的三个用锁的最佳实践，它们分别是：

> 1. 永远只在更新对象的成员变量时加锁
> 2. 永远只在访问可变的成员变量时加锁
> 3. 永远不在调用其他对象的方法时加锁

这三条规则，前两条估计你一定会认同，最后一条你可能会觉得过于严苛。但是我还是倾向于你去遵守，因为调用其他对象的方法，实在是太不安全了，也许“其他”方法里面有线程 sleep() 的调用，也可能会有奇慢无比的 I/O 操作，这些都会严重影响性能。更可怕的是，“其他”类的方法可能也会加锁，然后双重加锁就可能导致死锁。

**并发问题，本来就难以诊断，所以你一定要让你的代码尽量安全，尽量简单，哪怕有一点可能会出问题，都要努力避免。**

## 如何利用两个条件变量快速实现阻塞队列

一个阻塞队列，需要两个条件变量，一个是队列不空（空队列不允许出队），另一个是队列不满（队列已满不允许入队），这个例子我们前面在介绍管程的时候详细说过，这里就不再赘述。相关的代码，我这里重新列了出来，你可以温故知新一下。

```java
public class BlockedQueue<T>{
  final Lock lock =
    new ReentrantLock();
  // 条件变量：队列不满  
  final Condition notFull =
    lock.newCondition();
  // 条件变量：队列不空  
  final Condition notEmpty =
    lock.newCondition();
 
  // 入队
  void enq(T x) {
    lock.lock();
    try {
      while (队列已满){
        // 等待队列不满
        notFull.await();
      }  
      // 省略入队操作...
      // 入队后, 通知可出队
      notEmpty.signal();
    }finally {
      lock.unlock();
    }
  }
  // 出队
  void deq(){
    lock.lock();
    try {
      while (队列已空){
        // 等待队列不空
        notEmpty.await();
      }  
      // 省略出队操作...
      // 出队后，通知可入队
      notFull.signal();
    }finally {
      lock.unlock();
    }  
  }
}
```

不过，这里你需要注意，Lock 和 Condition 实现的管程，**线程等待和通知需要调用 await()、signal()、signalAll()**，它们的语义和 wait()、notify()、notifyAll() 是相同的。但是不一样的是，Lock&Condition 实现的管程里只能使用前面的 await()、signal()、signalAll()，而后面的 wait()、notify()、notifyAll() 只有在 synchronized 实现的管程里才能使用。如果一不小心在 Lock&Condition 实现的管程里调用了 wait()、notify()、notifyAll()，那程序可就彻底玩儿完了。

Java SDK 并发包里的 Lock 和 Condition 不过就是管程的一种实现而已，管程你已经很熟悉了，那 Lock 和 Condition 的使用自然是小菜一碟。下面我们就来看看在知名项目 Dubbo 中，Lock 和 Condition 是怎么用的。不过在开始介绍源码之前，我还先要介绍两个概念：同步和异步。

## 同步与异步

我们平时写的代码，基本都是同步的。但最近几年，异步编程大火。那同步和异步的区别到底是什么呢？**通俗点来讲就是调用方是否需要等待结果，如果需要等待结果，就是同步；如果不需要等待结果，就是异步**。

比如在下面的代码里，有一个计算圆周率小数点后 100 万位的方法`pai1M()`，这个方法可能需要执行俩礼拜，如果调用`pai1M()`之后，线程一直等着计算结果，等俩礼拜之后结果返回，就可以执行 `printf("hello world")`了，这个属于同步；如果调用`pai1M()`之后，线程不用等待计算结果，立刻就可以执行 `printf("hello world")`，这个就属于异步。

```java
// 计算圆周率小说点后 100 万位 
String pai1M() {
  // 省略代码无数
}
 
pai1M()
printf("hello world")
```

同步，是 Java 代码默认的处理方式。如果你想让你的程序支持异步，可以通过下面两种方式来实现：

1. 调用方创建一个子线程，在子线程中执行方法调用，这种调用我们称为异步调用；
2. 方法实现的时候，创建一个新的线程执行主要逻辑，主线程直接 return，这种方法我们一般称为异步方法。

## Dubbo 源码分析

其实在编程领域，异步的场景还是挺多的，比如 TCP 协议本身就是异步的，我们工作中经常用到的 RPC 调用，**在 TCP 协议层面，发送完 RPC 请求后，线程是不会等待 RPC 的响应结果的**。可能你会觉得奇怪，平时工作中的 RPC 调用大多数都是同步的啊？这是怎么回事呢？

其实很简单，一定是有人帮你做了异步转同步的事情。例如目前知名的 RPC 框架 Dubbo 就给我们做了异步转同步的事情，那它是怎么做的呢？下面我们就来分析一下 Dubbo 的相关源码。

对于下面一个简单的 RPC 调用，默认情况下 sayHello() 方法，是个同步方法，也就是说，执行 service.sayHello(“dubbo”) 的时候，线程会停下来等结果。

```java
DemoService service = 初始化部分省略
String message = service.sayHello("dubbo");
System.out.println(message);
```

如果此时你将调用线程 dump 出来的话，会是下图这个样子，你会发现调用线程阻塞了，线程状态是 TIMED_WAITING。本来发送请求是异步的，但是调用线程却阻塞了，说明 Dubbo 帮我们做了异步转同步的事情。通过调用栈，你能看到线程是阻塞在 DefaultFuture.get() 方法上，所以可以推断：Dubbo 异步转同步的功能应该是通过 DefaultFuture 这个类实现的。

![](http://img.bcoder.top/2020.01.27.2/1.png)

不过为了理清前后关系，还是有必要分析一下调用 DefaultFuture.get() 之前发生了什么。DubboInvoker 的 108 行调用了 DefaultFuture.get()，这一行很关键，我稍微修改了一下列在了下面。这一行先调用了 request(inv, timeout) 方法，这个方法其实就是发送 RPC 请求，之后通过调用 get() 方法等待 RPC 返回结果。

```java
public class DubboInvoker{
  Result doInvoke(Invocation inv){
    // 下面这行就是源码中 108 行
    // 为了便于展示，做了修改
    return currentClient 
      .request(inv, timeout)
      .get();
  }
}
```

DefaultFuture 这个类是很关键，我把相关的代码精简之后，列到了下面。不过在看代码之前，你还是有必要重复一下我们的需求：当 RPC 返回结果之前，阻塞调用线程，让调用线程等待；当 RPC 返回结果后，唤醒调用线程，让调用线程重新执行。不知道你有没有似曾相识的感觉，这不就是经典的等待 - 通知机制吗？这个时候想必你的脑海里应该能够浮现出管程的解决方案了。有了自己的方案之后，我们再来看看 Dubbo 是怎么实现的。

```java
// 创建锁与条件变量
private final Lock lock 
    = new ReentrantLock();
private final Condition done 
    = lock.newCondition();
 
// 调用方通过该方法等待结果
Object get(int timeout){
  long start = System.nanoTime();
  lock.lock();
  try {
	while (!isDone()) {
	  done.await(timeout);
      long cur=System.nanoTime();
	  if (isDone() || 
          cur-start > timeout){
	    break;
	  }
	}
  } finally {
	lock.unlock();
  }
  if (!isDone()) {
	throw new TimeoutException();
  }
  return returnFromResponse();
}
// RPC 结果是否已经返回
boolean isDone() {
  return response != null;
}
// RPC 结果返回时调用该方法   
private void doReceived(Response res) {
  lock.lock();
  try {
        response = res;
    if (done != null) {
      done.signal();
    }
  } finally {
    lock.unlock();
  }
}
```

当 RPC 结果返回时，会调用 doReceived() 方法，这个方法里面，调用 lock() 获取锁，在 finally 里面调用 unlock() 释放锁，获取锁后通过调用 signal() 来通知调用线程，结果已经返回，不用继续等待了。

至此，Dubbo 里面的异步转同步的源码就分析完了，有没有觉得还挺简单的？最近这几年，工作中需要异步处理的越来越多了，其中有一个主要原因就是有些 API 本身就是异步 API。例如 websocket 也是一个异步的通信协议，如果基于这个协议实现一个简单的 RPC，你也会遇到异步转同步的问题。现在很多公有云的 API 本身也是异步的，例如创建云主机，就是一个异步的 API，调用虽然成功了，但是云主机并没有创建成功，你需要调用另外一个 API 去轮询云主机的状态。如果你需要在项目内部封装创建云主机的 API，你也会面临异步转同步的问题，因为同步的 API 更易用。

## 总结

Java SDK 并发包里的 Lock 接口里面的每个方法，你可以感受到，都是经过深思熟虑的。除了支持类似 synchronized 隐式加锁的 lock() 方法外，还支持超时、非阻塞、可中断的方式获取锁，这三种方式为我们编写更加安全、健壮的并发程序提供了很大的便利。希望你以后在使用锁的时候，一定要仔细斟酌。

除了并发大师 Doug Lea 推荐的三个最佳实践外，你也可以参考一些诸如：减少锁的持有时间、减小锁的粒度等业界广为人知的规则，其实本质上它们都是相通的，不过是在该加锁的地方加锁而已。你可以自己体会，自己总结，最终总结出自己的一套最佳实践来。



Lock&Condition 是管程的一种实现，所以能否用好 Lock 和 Condition 要看你对管程模型理解得怎么样。管程的技术前面我们已经专门用了一篇文章做了介绍，你可以结合着来学，理论联系实践，有助于加深理解。

Lock&Condition 实现的管程相对于 synchronized 实现的管程来说更加灵活、功能也更丰富。



## 课后思考

1.你已经知道 tryLock() 支持非阻塞方式获取锁，下面这段关于转账的程序就使用到了 tryLock()，你来看看，它是否存在死锁问题呢？

```java
class Account {
  private int balance;
  private final Lock lock
          = new ReentrantLock();
  // 转账
  void transfer(Account tar, int amt){
    while (true) {
      if(this.lock.tryLock()) {
        try {
          if (tar.lock.tryLock()) {
            try {
              this.balance -= amt;
              tar.balance += amt;
            } finally {
              tar.lock.unlock();
            }
          }//if
        } finally {
          this.lock.unlock();
        }
      }//if
    }//while
  }//transfer
}
```

答：本意是通过破坏不可抢占条件来避免死锁问题，但是它的实现中有一个致命的问题，那就是： while(true) 没有 break 条件，从而导致了死循环。除此之外，这个实现虽然不存在死锁问题，但还是存在活锁问题的，解决活锁问题很简单，只需要随机等待一小段时间就可以了。(A，B两账户相互转账，各自持有自己lock的锁，都一直在尝试获取对方的锁，形成了活锁)

修复后的代码如下所示，我仅仅修改了两个地方，一处是转账成功之后 break，另一处是在 while 循环体结束前增加了`Thread.sleep(随机时间)`。

```java
class Account {
  private int balance;
  private final Lock lock
          = new ReentrantLock();
  // 转账
  void transfer(Account tar, int amt){
    while (true) {
      if(this.lock.tryLock()) {
        try {
          if (tar.lock.tryLock()) {
            try {
              this.balance -= amt;
              tar.balance += amt;
              // 新增：退出循环
              break;
            } finally {
              tar.lock.unlock();
            }
          }//if
        } finally {
          this.lock.unlock();
        }
      }//if
      // 新增：sleep 一个随机时间避免活锁
      Thread.sleep(随机时间);
    }//while
  }//transfer
}
```

这个思考题里面的 while(true) 问题还是比较容易看出来的，**但不是所有的 while(true) 问题都这么显而易见的**，很多都隐藏得比较深。



2.DefaultFuture 里面唤醒等待的线程，用的是 signal()，而不是 signalAll()，你来分析一下，这样做是否合理呢？

答：思考题是关于 signal() 和 signalAll() 的，Dubbo 最近已经把 signal() 改成 signalAll() 了，我觉得用 signal() 也不能说错，但的确是**用 signalAll() 会更安全**。我个人也倾向于使用 signalAll()，因为我们写程序，不是做数学题，而是在搞工程，工程中会有很多不稳定的因素，更有很多你预料不到的情况发生，所以不要让你的代码铤而走险，尽量使用更稳妥的方案和设计。Dubbo 修改后的相关代码如下所示：

```java
// RPC 结果返回时调用该方法   
private void doReceived(Response res) {
  lock.lock();
  try {
    response = res;
    done.signalAll();
  } finally {
    lock.unlock();
  }
}
```

