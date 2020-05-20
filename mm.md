+ 架构师筑基阶段
  + JVM底层原理与优化技巧
    + 运行时数据区域
      + 程序计数器PCR
      + java虚拟机栈
      + 本地方法栈
      + Java 堆
      + 方法区
  + 垃圾回收
    + 对象回收的判断
      + 引用计数算法
      + 可达性分析算法
    + 方法区回收
      + 垃圾回收效率较低
      + 回收内容
        + 废弃常量
        + 无用类
    + 垃圾收集算法
      + 标记清除算法(最基础算法)
      + 复制算法
      + 标记整理算法
      + 分代收集算法
    + 垃圾收集器
      + Serial(最基本的收集器)
      + ParNew收集器
      + Parallel Scavenge收集器
      + Serial Old收集器
      + Parallel Old收集器
      + CMS收集器
      + G1收集器
    + GC日志分析
  + 对象的创建
    + new 指令创建对象
    + 分配内存方式
    + 对象的内存布局
      + 对象头
      + 实例数据
      + 对齐填充
    + 对象的访问定位
      + 句柄访问  
      + 直接指针访问
  + 类加载机制
    + 类加载的生命周期
      + 加载
      + 连接
      + 初始化
      + 使用
      + 卸载
    + 类加载器
      + 双亲委派模型
      + 扩展类加载器
      + 应用程序类加载器
        + 负责加载ClassPath上指定的类库
      + 自定义类加载器
  + JVM性能调优实战
    + 常见的调优手段分析
    + GC调优实战
    + JVM调优实战
  + Java并发底层原理与源码实现
    + Happens-Before原则、重排序和顺序一致性
    + Synchronized
      + Synchronized的原理分析
      + 自旋锁、偏向锁、轻量级锁、重量级锁的概念与底层实现原理
    + Volatile的使用场景和底层实现原理
    + AbstractAueuedSynchronizer（AQS）底层源码分析
    + CAS的使用场景和底层实现原理
    + ThreadLocal的使用场景和底层源码分析
    + Java中的Lock底层源码分析
      + ReentrantLock
      + ReentrantReadWriteLock
      + Condition
    + 并发常用工具类
      + CyclicBarrier源码解析和使用场景
      + CountDownLatch源码解析和使用场景
      + Semphore源码解析和使用场景
      + Atomic系列源码解析
        + 基本类型的原子操作比如经典的AtomicBoolean、AtomicLnteger、AtomicLong
        + 数组类型的原子操作代表几个类AtomicIntegerArray、AtomicLongArray、AtomicReferenceArray
        + 引用类型的原子操作的典型AtomicReference、AtomicReferenceFieldUpdater......
    + 线程池源码
      + Fork Join源码解析和使用场景
      + CachedThreadPool源码和使用场景解析
      + FixedThreadPool源码和使用场景解析
      + SingleThreadExecutor源码和使用场景解析
      + ScheduleThreadPool源码和使用场景解析
  + Java集合类源码实现
    + JDK7中的HashMap源码解析
    + JDK7中的ConcurrentHashMap源码解析
    + 红黑树分析与应用详解
    + JDK8中的HashMap源码解析
    + JDK8中的ConcurrentHashMap源码解析
    + LinkedHashMap源码解析
    + ArrayList源码解析
  + JDK新特性
    + JDK8
      + Stream
        + 方法
          + Filter 过滤
          + Sort 排序
          + Map 映射
          + Match 匹配
            + 最终操作
          + Count 计数
            + 最终操作
          + Reduce 规约
            + 最终操作
        + 并行
          + stream()
          + parallelStream()
        + map
          + map.forEach
      + 函数式接口
      + Lambda 表达式
      + 方法引用
      + Date API
        + LocalTime 本地时间
        + LocalDateTime 本地日期时间
        + Timezones 时区
        + Clock 时钟
    + JDK9
      + modularity System 模块化系统
      + HTTP/2
        + java.net.http
      + JShell : 交互式 Java REPL
      + 不可变集合工厂方法
        + List.of()
        + Set.of()
        + Map.of()
        + Map.ofEntries()
        + UnsupportedOperationException
      + 私有接口方法
      + 多版本兼容 JAR
      + I/O 流新特性
    + JDK10
      + 局部变量的类型推断
      + GC改进和内存管理
      + 线程本地握手
      + 备用内存设备上的堆分配
      + 其他Unicode语言 - 标记扩展
      + 基于Java的实验性JIT编译器
      + 开源根证书
      + 根证书颁发认证（CA）
      + 将JDK生态整合单个存储库
      + 删除工具javah
    + JDK11
      + HTTP Client  
      + Java Flight Recorder (JFR)
      + JavaFX从JDK分离为独立模块
      + Project ZGC
    + JDK12
      + Shenandoah
      + Microbenchmark Suite
      + JVM常量API
      + 默认CDS档案
      + G1的优化
    + JDK13
      + Dynamic CDS Archives
      + ZGC
      + Reimplement the Legacy Socket API
      + Switch Expressions
      + Text Blocks
+ 架构师成长阶段
    + Spring的底层原理与源码实现
      + AspectJ和springAop，aspectj的静态织入
      + JDK动态代理的源码分析，JDK是如何操作字节码
      + spring通过cglib完成AOP，cglib如果完成方法拦截
      + AnnotationAwareAspectJAutoProxyCreator如何完成代理织入的
      + BeanDefinition的定义，在spring体系当中beanDefinition的和bean的产生过程，sping当中的各种BeanDefinition的作用
      + BeanDefinition有什么作用？如果来改变一个bean的行为、spring当中有哪些扩展点开源来修改beanDefinition
      + BeanDefinitionRegistry的作用，源码分析、哪些开源框架利用了这个类
      + BeanNameGenerator如何改变beanName的生成策略、如何自己写一个beanName的生成策略
      + BeanPostProcessor如何插手bean的实例化过程、经典的应用场景有哪些？spring内部哪里用到了这个接口
      + BeanFactoryPostProcessor和BeanPostProcessor的区别、经典应用场景、spring内部如何把他应用起来的
      + BeanDefinitionRegistryPostProcessor和BeanFactoryPostProcessor的关系已经区别，spring底层如何调用他们
      + ConfigurationClassPostProcessor这个类如何完成bean的扫描，如何完成@Bean的扫描、如何完成对@Import的解析
      + @Imoprt的三种类型，普通类、配置类、ImportSelector；spring在底层源码当中如何来解析这三种import的
      + 如何利用ImportSelector来完成对spring的扩展？你所用的其他框架或者技术说明地方体现了这个类的使用
      + @Configuration这注解为什么可以不加？加了和不加的区别，底层为什么使用cglib
      + @Bean的方法是如何保证单例的？如果不需要单例需要这么配置？为什么需要这么配置
      + springFacoryBean和BeanFacory的区别，有哪些经典应用场景？spring的factoryMethod的经典应用场景？
      + ImportBeanDefinitionRegistrar这个接口的作用，其他主流框架如何利用这个类来完成和spring的结合的？
      + spring是什么时候来执行后置处理器的？有哪些重要的后置处理器，比如CommonAnnotationBeanPostProcessor
      + CommonAnnotationBeanPostProcessor如何来完成spring初始化方法的回调。spring内部的各种Procesor的作用分别是什么
      + spring和springBoot当中的各种@Enablexxxx的原理是什么？如何自己实现一个？比如动态开启某某些自定义功能
      + spring如何来完成bean的循环依赖并且实例化的，什么是spring的IOC容器，怎么通过源码来理解？
      + Bean的实例化过程，源码中的两次gegetSingleton的不同和相比如SpringMvc的源码分析等等......
    + Spring Boot的底层原理与源码实现
      + Spring Boot的源码分析和基本应用、利用springmvc的知识模拟和手写一个springboot
      + springmvc的零配置如何实现的？利用servelt3.0的哪些新知识？在springmvc中如何内嵌一个tomcat，如何把web.xml去掉
      + springboot当中的监听器和设计模式中观察者模式的关系、模拟java当中的事件驱动编程模型
      + springboot的启动流程分析、springboot如何初始化spring的context？如何初始化DispacterServlet的、如何启动tomcat的
      + springboot的配置文件类型、配置文件的语法、配置文件的加载顺序、模拟springboot的自动配置
      + springboot的日志系统、springboot如何设计他的日志系统的，有什么优势？如何做到统一日志的？
    + Mybatis的底层原理与源码实现
      + mybatis优缺点、spring 与mybatis 集成、mybaits单独使用
      + Config、Sql配置、Mapper配置、有几种注册mapper的方法，优先级如何？
      + mybaits的一级缓存、二级缓存、mybatis的二级缓存为什么是鸡肋？
      + 通用mapper的实现、mybaits编写sql语句的三种方式
      + 如何利用mybaits的源码来扩展一个mybaits的插件，比如扩展一个适合你公司的分页插件
      + @MapperScan的源码分析？mapperScan如何生效的？
      + mybatis如何扩展spring的扫描器的、mybatis扫描完之后如何利用FactoryBean的？
      + mybaits底层如何把一个代理对象放到spring容器中？用到了spring的哪些知识？
      + mybaits和spring的核心接口ImportBeanDefinitionRegistrar之间千丝万缕的关系
      + 从原来来说明mybaits的一级缓存为什么会失效？spring为什么把他失效？有没有办法解决？
      + 从mybatis来分析mybatis的执行流程、mybaits的sql什么时候缓存的？缓存在哪里？
      + mybaits当中的方法名为什么需要和mapper当中的id一致？从源码来说明
    + Shiro的底层原理与源码实现
      + Shiro中进行身份认证的底层源码解析
      + Shiro中权限验证的底层源码解析
      + Shiro中会话管理底层源码解析
      + Shiro中加密功能底层源码解析
    + Mysql的底层原理与优化技巧
      + Mysql底层原理
        + 索引
          + InnoDb行格式，Mysql中的一行数据到底是怎么存储的？
          + 什么是B+树？B+树和B树的区别是什么？
          + InnoDd索引底层原理与MyISAM索引底层原理有什么区别？
          + 建立索引时该考虑些什么问题？
          + Mysql中是如何利用B+树这种数据结构来构造索引的？
        + 索引优化
          + Mysql中的查询优化器会帮助程序员做哪些事情？是如何工作的？
          + Explain语句执行后的结果每个字段分别代表什么意思？每个字段的每个值分别代表什么情况？
          + 如何优化JOIN查询？JOIN查询的底层原理是什么？
          + 如何优化子查询？子查询的底层原理是什么？
          + 如何优化order by, group by ,limit语句查询？
        + 事务
          + Mysql是如何实现事务的？Mysql中为什么有事务隔离级别？每种事务隔离级别代表什么意思？Mysql中是如何实现事务隔离级别的？
          + MVCC是什么？底层原理是什么？
          + ReadView是什么？底层原理是什么？
        + 锁
          + Mysql中有哪些锁类型？每种锁代表什么意思？Mysql是如何实现“锁”的？
          + Mysql中什么情况下会出现死锁？如何避免？Mysql针对死锁进行了哪些优化和处理？
      + Mysql集群
        + mysql主从复制实战
        + mysql主主复制实战
        + haproxy+keepalived高可用
        + MySQL NDB Cluster
      + Mysql调优实战
        + 服务器调优手段分析
        + 慢查询优化策略
        + Sql调优实战
+ 架构师拓展阶段
    + 响应式编程
        + Spring Webflux的底层原理与源码
          + steam流的编程概念、流的创建和流的操作、以及什么叫做并行流
          + reactive steam流的基本概念和主要接口以及运行原理的分析
          + 异步servlet的概念和例子、 RouterFunction的运行机制和原理
          + spring webFlux Reactive Core的技术讲解比如Logging和HttpHandler
          + DispatcherHandler、webflux的配置、统一异常处理、result统一处理
          + View Resolution、webflux的视图裁决、Web Security、http缓存
          + WebClient的主要方法、retrieve()方法、exchange()、过滤器和同步使用技巧
          + spring webflux和WebSocket实战讲解
        + 响应式开发框架Akka的基本应用与原理
          + Akka中的设计理念，为什么需要Akka
          + Akka的核心-Actor模型是什么？为什么经典？
          + Akka支持哪些持久化机制？如何持久化的？
          + Akka中的监控与容错机制详解
          + Akka的集群模式详解
    + 网络编程
        + Tomcat底层原理与源码、调优方案
          + Tomcat整体架构设计
          + Tomcat中“HTTP长连接”的实现原理与源码分析
          + Tomcat中关于解析HTTP请求行、请求头、情头体的源码分析
          + Tomcat中关于分块传输（chunk）请求体的源码分析
          + Tomcat中响应一个请求的原理与源码分析
          + Tomcat中利用BIO处理请求的源码分析
          + Tomcat中利用NIO处理请求的源码分析
          + Tomcat中异步Servlet实现的源码分析
          + Tomcat是如何做到“打破双亲委派的”？Tomcat中自定义类加载器的应用与源码解析
          + Tomcat中的四大容器处理请求的源码分析
          + Tomcat启动过程与解析配置文件源码解析
          + Tomcat性能调优实战
        + Netty的应用与底层原理源码
          + netty的整体架构实现、netty的模块分析、netty对于大数据的传输、压缩和解压缩
            + netty复合缓冲和其他缓冲的原理分析和各自的使用场景、计数原子和AtomicIntegerFieldUpdater
          + netty的HTTP支持、netty如何实现tomcat的web容器功能、netty对socket的实现
          + netty和RPC的原理分析、netty和websocket的原理分析、生命周期的理解、服务端怎么实现的
          + RPC框架分析、什么是RPC，主流RPC框架的使用和原理分析、如何实现自己的RPC框架
          + netty当中的IO模型分析、NIO的在netty当中的体现、nio的Scattering和Gathering的原理分析
          + NIO的重要API讲解、NIO的模型原理、NIO的零copy如何实现的、NIO的buffer和channel的应用和原理
          + selector的源码深入分析、nio的网络编程、nio的堆外内存使用，文件通道的深入使用和理解
          + 如何利用nett来实现一个高性能的弹幕系统、比如利用netty模拟实现一个斗鱼的弹幕功能
          + netty的线程模型解析、netty的编码解码框架解析、netty自定义协议和TCP粘包拆包的问题如何解决
            + netty初始化流程总结及Channel与ChannelHandlerContext、channel注册的原理、channel选择器工厂和轮询算法及注册底层实现
        + Nginx高并发场景应用与底层实现原理、调优方案
          + Nginx简介、安装、配置
          + Nginx反向代理基本使用
          + Nginx反向代理高级使用
          + Nginx反向代理底层原理解析
          + 利用Nginx实现动静分离
          + Nginx负载均衡策略与底层原理详解
        + Devops技术
          + Linux
            + Linux原理、启动、目录介绍、linux的网络安全配置
            + Linux运维常用命令、Linux用户与权限介绍、shell脚本编写
          + Git
            + 动手搭建Git客户端与服务端、Git的核心命令
            + Git企业应用、git的原理，git底层指针介绍、gitlab
          + Maven
            + 搭建Nexus私服、maven的插件介绍、maven插件的使用
            + 整体认知maven的体系结构、maven核心命令、maven的pom配置体系
          + Docker
            + Docker的基础用法以及Docker镜像的基本操作
            + 程序员如何利用Dockerfile格式、Dockerfile命令以及docker build构建镜像
            + 容器技术入门、Docker容器基本操作、容器虚拟化网络概述以及Docker的容器网络是怎样的？
            + Compose和Dockerfile的区别是什么？Compose的配置文件以及使用Compose运行容器、Docker的实战应用
            + Docker的三大核心概念：镜像（Images）、容器（Containers）、仓库服务注册器（Registry）他们分别是什么？
            + 什么是Docker、为什么要使用他、和开发有什么关系？能否带来便捷、Docker 简介、入门，Docker的架构是怎样的？
          + Kubernetes
            + Kubernetes入门简介
            + kubernetes集群搭建
            + Kubernetes容器网络
            + Kubernetes容器持久化存储
            + kubernetes作业管理和容器编排
          + 系统可持续集成
            + Jenkins的安装与介绍
            + Jenkins流水线(Pipeline)原理介绍
            + Jenkins自动化构建、测试与部署实战
          + 系统监控
            + 开源监控系统prometheus的介绍与实战
            + 跨平台的开源的度量分析和可视化工具Grafana的介绍与实战

+ 架构师成型阶段
  + 分布式
    + 分布式理论
      + CAP理论、BASE理论
      + 2PC协议、3PC协议、XA协议
      + Paxos算法、Raft算法、Zab协议
    + 分布式架构中必备Zookeeper源码解析
      + 分布式系统介绍以及Zookeeper快速入门与基本使用
      + Zookeeper各个客户端框架的对比和使用
      + Zookeeper客户端与服务端交互流程源码解析
      + Zookeeper单机模式与集群模式处理请求源码解析
      + Zookeeper集群模式下的请求处理流程源码解析（ZK是如何保证数据一致性的？）
      + Zookeeper领导者选举介绍以及源码解析
      + 使用Zookeeper实现分布式锁以及分布式配置中心的实战
    + 阿里重磅打造的微服务框架Dubbo源码解析
      + Dubbo框架全面介绍及基本使用快速入门
      + Dubbo中负载均衡、服务路由、集群容错等等高级功能使用
      + Dubbo与Spring整合源码分析，通过注解与通过xml
      + Dubbo的扩展机制-SPI源码解析
      + Dubbo服务导出(服务注册与服务暴露)源码解析
      + Dubbo服务引入、服务目录源码解析
      + Dubbo服务调用与容错源码解析
      + 手写模拟Dubbo开源项目实战
    + 分布式消息队列
      + RocketMq底层源码解析
        + RocketMQ快速入门、集群部署
        + RocketMQ事物消息、批量发送消息、广播模式
        + RocketMQ消息生产者、消息消费者、有序消息生产者、顺序消费
      + Kafka底层原理解析
        + kafka基本介绍及 kafka仅仅只是消息中间件吗？
        + kafka系统参数及优化
        + kafka分区再均衡及偏移量提交
        + kafka实战确保消息一致且不丢失
      + Rabbitmq底层原理解析
        + RabbitMQ环境安装&amp;amp;amp;amp;RabbitMQ整体架构与消息流转&amp;amp;amp;amp;交换机详解
        + 消息如何保障 100% 的投递成功方案&amp;amp;amp;amp;企业消息幂等性概念及业界主流解决方案
        + Confirm确认消息详解&amp;amp;amp;amp;Return返回消息详解&amp;amp;amp;amp;消费端的限流策略&amp;amp;amp;amp;消费端ACK与重回队列机制
        + SpringAMQP用户管理组件-RabbitAdmin应用&amp;amp;amp;amp;SpringAMQP消息模板组件-RabbitTemplate实战
        + SpringAMQP消息容器-SimpleMessageListenerContainer详解&amp;amp;amp;amp;SpringAMQP消息适配器-MessageListenerAdapter使用
        + RabbitMQ与SpringBoot2.0整合实战&amp;amp;amp;amp;RabbitMQ与Spring Cloud Stream整合实战
        + RabbitMQ集群架构模式&amp;amp;amp;amp;RabbitMQ集群镜像队列构建实现可靠性存储&amp;amp;amp;amp;RabbitMQ集群整合负载均衡基础组件HaProxy_
    + 分布式锁
      + 利用Mysql实现分布式锁实战与优缺点
      + 利用Zookeeper实现分布式锁实战与优缺点
      + 利用Redis实现分布式锁实战与优缺点
      + 分布式锁的使用场景
    + 分布式事务Seata
      + 手写一个2PC分布式事物框架
      + 如何用消息队列解决分布式事务
      + 什么是TCC，TCC与其他方案优缺点对比
      + 阿里开源框架Seata的基本使用
      + 阿里开源框架Seata的底层原理与源码解析
    + 分布式定时任务
      + Elastic-Job作业分片
      + Elastic-Job事件追踪
      + Elastic-Job作业运行状态监听
    + 分布式搜索引擎ElasticSearch
      + Elasticsearch入门介绍安装与基本api的使用
      + Elasticsearch高级查询及搜索系统实战
      + Elasticsearch搜索底层原理解析
      + Elasticsearch集群搭建与底层原理解析
      + 分布式日志系统ELK
        + 什么是Elastic Stack? 什么是ELK？
        + Kibana介绍以及使用
        + FileBeat使用与原理解析
        + LogStash使用与原理解析
        + 新一代ELK日志系统搭建
    + 分布式全局ID
      + 雪花算法详解与优缺点
      + 滴滴开源框架Tinyid源码解析
      + 百度开源组件Uidgenerator源码解析
      + 美团开源框架Leaf源码解析
    + 分库分表
      + Apache ShardingSphere
        + 数据分片
        + 读写分离
        + springboot整合实战
      + Mycat
        + 分库分表场景介绍
        + Mycat原理解析
        + 分库分表实战
    + 分布式缓存Redis
      + redis单机版
        + redis安装及redis基本api、redis scan等高级命令
        + redis持久化rdb,aof混合持久化，及优缺点对比
        + redis配置文件解读、redis单线程但是高性能
        + redis单机版缺陷、以及如何解决这种缺陷的案例分析
      + redis主从复制与哨兵模式
        + redis主从复制原理、什么时候增量复制，什么时候全量复制
        + 主从复制实战、哨兵模式原理、哨兵模式实战
        + 哨兵缺陷和cluster集群的优点、对比分析如何选择
      + redis高可用集群
        + redis cluster基本原理、rediscluster实战
        + rediscluster扩容与缩容、客户端操作Rediscluster原理
      + redis应用场景实战
        + 使用缓存常见问题及解决方案、缓存穿透、缓存击穿、缓存雪崩、无底洞问题
        + redis实现分布式锁、redlock、setex、setnx
        + redis在实践中的一些常见问题以及优化思路（包含linux内核参数优化）
      + 分布式文件存储Mongodb
        + MongoDB入门介绍、bson与json对比
        + MongoDB内嵌型数据结构、常用操作命令
        + MongoDB索引介绍、全文索引、复合索引
        + 节应用场景分析、和实战小项目
      + 分布式数据库
        + PingCAP开源分布式数据库TiDB原理介绍与实战
        + 阿里开源的分布式数据库OceanBase原理介绍与实战
  + 微服务
    + Spring Cloud Netflix
      + Eureka的源码分析服务注册和服务发现以及心跳机制和保护机制，对比eureka与zookeeper，什么是CAP原则？
      + Ribbon源码分析和客服端负载均衡，客户端负载均衡？服务端负载均衡？ Ribbon核心组件IRule以及重写IRule
      + Fegin源码分析和声明式服务调用，Fegin负载均衡，Fegin如何与Hystrix结合使用？ 有什么问题？
      + Hystrix实现服务限流、降级，大型分布式项目服务雪崩如何解决？ 服务熔断到底是什么？一线公司的解决方案
      + HystrixDoashboard如何实现自定义接口降级、监控数据、数据聚合等等
      + Zuul统一网关详解、服务路由、过滤器使用等，从源头来拦截掉一些不良请求
      + 分布式配置中心Config详解，如何与github或是其他自定义的git平台结合、比如gitlab
      + 分布式链路跟踪详解，串联调用链，,让Bug无处可藏，如何厘清微服务之间的依赖关系？如何跟踪业务流的处理顺序？
    + Spring Cloud Alibaba
      + Sentinel中流量控制、熔断降级的底层原理实现
      + Nacos动态服务发现、配置管理的底层原理实现和服务管理平台使用
      + RocketMQ：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务
      + Dubbo：Apache Dubbo是一款高性能 Java RPC 框架
      + Seata：阿里巴巴开源的易于使用的高性能微服务分布式事务解决方案
      + Alibaba Cloud ACM：一款在分布式架构环境中对应用配置进行集中管理和推送的应用配置中心产品
      + Alibaba Cloud OSS: 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务
      + Alibaba Cloud SchedulerX: 阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务
      + Alibaba Cloud SMS: 覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道
    + 服务网格（Service Mesh）
      + 什么是Service Mesh？Service Mesh是微服务时代的TCP协议
      + Service Mesh的底层架构，包含哪些组件？底层的工作原理是什么？
      + 什么样的企业需要使用Service Mesh？如何最大化利用Service Mesh?

+ 架构师实战阶段
  + 熟悉高并发场景下的架构设计解决方案
  + 如何对亿级流量进行限流
  + 热点数据高并发读如何做
  + 千万级并发的秒杀系统如何设计
  + 超大型微服务架构中服务降级如何处理
+ 熟悉大规模分布式架构中的各种场景解决方案
  + 分布式链路追踪如何做
  + 分布式锁如何选型
  + 分库分表如何预估数据量与分片规则
  + 高并发下接口幂等性该如何设计
  + 分布式ID该如何生成
  + 分布式Session如何做
  + 分布式调度框架该如何设计
+ 掌握大厂秒杀架构设计与解决方案
  + 阿里双十一系统是如何设计的？
  + 京东618系统是如何设计的？
  + 12306抢票系统是如何设计的？
  + 微信红包系统是如何设计的？
+ 千万级并发互联网实战项目
  + 系统模块
    + 商品模块
    + 订单模块
    + 会员模块
    + 库存模块
    + 交易模块
    + 购物车模块
    + 物流模块
    + 积分模块
    + 支付模块
    + 清算模块
  + 技术要点
    + 利用Spring Cloud作为主开发框架，同时也会用到Dubbo
    + 使用RabbitMq作为消息队列进行异步调用、解耦、削峰
    + 使用不同的Redis集群模式解决特定的场景问题
    + 多套Mysql集群架构分析与实战
    + 利用Mycat针对不同业务场景的分库分表实战
    + 使用美团开源框架Leaf作为分布式全局ID的生成器
    + 利用阿里开源的Seata框架进行分布式事务管理
    + 利用Jenkins、Gitlab、Docket等实现自动化项目构建与部署
    + 利用Kubernetes实现系统的自动扩缩容
    + 利用Elastic Search作为搜索引擎
    + 使用ELK作为日志系统
    + 性能监控
      + 监控系统Prometheus使用详解
      + 可视化的系统监控平台Grafana使用详解
  + 项目调优实战
    + 如何解决系统响应慢的问题？该如何分析
    + 结合业务场景设置合适的JVM参数
    + 如何根据GC日志分析线上重大问题
    + 针对访问量大的表该如何正确的建立索引与维护索引
    + 如何充分提高Tomcat的并发量与吞吐量
    + 如何充分提高Nginx的并发量与吞吐量
    + Linux服务器该如何优化，有哪些优化措施

+ 架构师延伸阶段
  + 大数据技术
    + Hadoop概述及生态圈介绍
    + Yarn的发展、架构、原理分析
    + Yarn与mesos比较
    + 分布式文件系统HDFS应用与原理分析
    + MapReduce应用与原理分析
    + 数据仓库Hive应用与原理分析
    + Hbase的应用与原理分析
    + Zookeeper的底层原理详解
    + Kafka的底层原理详解
    + Sparks生态圈
    + 数据挖掘算法
  + Python
    + Python简介
    + Python基础语法
    + Python函数
    + 文件IO
    + 面向对象编程
    + 异常处理
    + 多线程和多进程
    + 并发和异步IO
    + 函数式编程
    + 科学计算
      + numpy
      + pandas
    + 图像处理
      + PIL
      + OpenCV
  + 数据结构与算法
    + 算法入门
    + 快速排序、冒泡排序
    + 贪心算法
    + 动态规划算法
    + 树论基础
    + 二叉搜索树与BTree
    + 字典树与哈夫曼编码
    + DFS算法
    + BFS算法
    + 最短路径算法
    + 二分查找
    + 高级索引算法