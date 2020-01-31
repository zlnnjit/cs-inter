经常会有同学来问我，我的数据库占用空间太大，我把一个最大的表删掉了一半的数据，怎么表文件的大小还是没变？

那么今天，我就和你聊聊数据库表的空间回收，看看如何解决这个问题。

这里，我们还是针对MySQL中应用最广泛的InnoDB引擎展开讨论。一个InnoDB表包含两部分，即：表结构定义和数据。在MySQL 8.0版本以前，表结构是存在以.frm为后缀的文件里。而MySQL 8.0版本，则已经允许把表结构定义放在系统数据表中了。因为表结构定义占用的空间很小，所以我们今天主要讨论的是表数据。

接下来，我会先和你说明为什么简单地删除表数据达不到表空间回收的效果，然后再和你介绍正确回收空间的方法。

<!--more-->





![](http://img.bcoder.top/2020.01.31.8/1.png)

![](http://img.bcoder.top/2020.01.31.8/2.png)

![](http://img.bcoder.top/2020.01.31.8/3.png)

![](http://img.bcoder.top/2020.01.31.8/4.png)

![](http://img.bcoder.top/2020.01.31.8/5.png)

![](http://img.bcoder.top/2020.01.31.8/6.png)

![](http://img.bcoder.top/2020.01.31.8/7.png)

![](http://img.bcoder.top/2020.01.31.8/8.png)

![](http://img.bcoder.top/2020.01.31.8/9.png)

![](http://img.bcoder.top/2020.01.31.8/10.png)

![](http://img.bcoder.top/2020.01.31.8/11.png)

![](http://img.bcoder.top/2020.01.31.8/12.png)

![](http://img.bcoder.top/2020.01.31.8/13.png)

![](http://img.bcoder.top/2020.01.31.8/14.png)

![](http://img.bcoder.top/2020.01.31.8/15.png)

![](http://img.bcoder.top/2020.01.31.8/16.png)

![](http://img.bcoder.top/2020.01.31.8/17.png)

![](http://img.bcoder.top/2020.01.31.8/18.png)

![](http://img.bcoder.top/2020.01.31.8/19.png)

![](http://img.bcoder.top/2020.01.31.8/20.png)

![](http://img.bcoder.top/2020.01.31.8/21.png)

![](http://img.bcoder.top/2020.01.31.8/22.png)

![](http://img.bcoder.top/2020.01.31.8/23.png)

![](http://img.bcoder.top/2020.01.31.8/24.png)

![](http://img.bcoder.top/2020.01.31.8/25.png)

![](http://img.bcoder.top/2020.01.31.8/26.png)

![](http://img.bcoder.top/2020.01.31.8/27.png)

![](http://img.bcoder.top/2020.01.31.8/28.png)