> 引用[他人]([http://www.tianxiaobo.com/2018/01/18/%E8%87%aa%E5%B7%B1%E5%8A%A8%E6%89%8B%E5%AE%9E%E7%8E%B0%E7%9A%84-Spring-IOC-%E5%92%8C-AOP-%E4%B8%8A%E7%AF%87/#comments](http://www.tianxiaobo.com/2018/01/18/自己动手实现的-Spring-IOC-和-AOP-上篇/#comments))文章并重新实践了一下。

<!--more-->

## 背景

我在大四实习的时候开始接触 J2EE 方面的开发工作，也是在同时期接触并学习 Spring 框架，到现在也有快有两年的时间了。不过之前没有仿写过 Spring IOC 和 AOP，只是宏观上对 Spring IOC 和 AOP 原理有一定的认识。所以为了更进一步理解 Spring IOC 和 AOP 原理。在工作之余，参考了一些资料和代码，动手实现了一个简单的 IOC 和 AOP，并实现了如下功能：

1. 根据 xml 配置文件加载相关 bean
2. 对 BeanPostProcessor 类型的 bean 提供支持
3. 对 BeanFactoryAware 类型的 bean 提供支持
4. 实现了基于 JDK 动态代理的 AOP
5. 整合了 IOC 和 AOP，使得二者可很好的协同工作

在实现自己的 IOC 和 AOP 前，我的想法比较简单，就是实现一个非常简单的 IOC 和 AOP，哪怕是几十行代码实现的都行。后来实现后，感觉还很有意思的。不过那个实现太过于简单，和 Spring IOC，AOP 相去甚远。后来想了一下，不能仅满足那个简单的实现，于是就有了这个仿写项目。相对来说仿写的代码要复杂了一些，功能也多了一点，看起来也有点样子的。尽管仿写出的项目仍然是玩具级，不过写仿写的过程中，还是学到了一些东西。总体上来说，收获还是很大的。在接下来文章中，我也将从易到难，实现不同版本的 IOC 和 AOP。好了，不多说了，开始干活。

## 简单的 IOC 和 AOP 实现

### 简单的 IOC

先从简单的 IOC 容器实现开始，最简单的 IOC 容器只需4步即可实现，如下：

1. 加载 xml 配置文件，遍历其中的标签
2. 获取标签中的 id 和 class 属性，加载 class 属性对应的类，并创建 bean
3. 遍历标签中的标签，获取属性值，并将属性值填充到 bean 中
4. 将 bean 注册到 bean 容器中

如上所示，仅需4步即可，是不是觉得很简单。好了，Talk is cheap, Show me the code. 接下来要上代码了。不过客官别急，上代码前，容我对代码结构做一下简单介绍：

```
SimpleIOC     // IOC 的实现类，实现了上面所说的4个步骤
SimpleIOCTest    // IOC 的测试类
Car           // IOC 测试使用的 bean
Wheel         // 同上 
ioc.xml       // bean 配置文件
```

容器实现类 SimpleIOC 的代码：

```java

public class SimpleIOC {

    private Map<String, Object> beanMap = Maps.newHashMap();

    public SimpleIOC(String location) throws Exception {
        loadBeans(location);
    }

    private void loadBeans(String location) throws Exception {
        //加载配置文件
        File file = new File(System.getProperty("user.dir")+"/" + location);
        if (!file.exists()) {
            throw new IOException("bean配置文件不存在");
        }


        InputStream inputStream = new FileInputStream(file);
        Document document = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(inputStream);
        NodeList childNodes = document.getDocumentElement().getChildNodes();

        if (childNodes.getLength() < 1) {
            throw new Exception("xml not exist child nodes");
        }

        for (int i = 0; i < childNodes.getLength(); i++) {
            Node item = childNodes.item(i);
            if (item instanceof Element) {
                Element element = (Element) item;
                String id = element.getAttribute("id");
                String className = element.getAttribute("class");

                //加载beanclass并创建bean
                Class beanClass = Class.forName(className);
                Object bean = beanClass.newInstance();

                //遍历property标签
                NodeList propertyNodes = element.getElementsByTagName("property");

                for (int j = 0; j < propertyNodes.getLength(); j++) {
                    Node propertyNode = propertyNodes.item(j);
                    if (propertyNode instanceof Element) {
                        Element propertyElement = (Element) propertyNode;
                        String name = propertyElement.getAttribute("name");
                        String value = propertyElement.getAttribute("value");

                        Field field = bean.getClass().getDeclaredField(name);
                        field.setAccessible(true);
                        if (!StringUtils.isEmpty(value)) {
                            field.set(bean, value);
                        } else {
                            String ref = propertyElement.getAttribute("ref");
                            if (StringUtils.isEmpty(ref)) {
                                throw new IllegalAccessException("ref not exist");
                            }
                            //填充引用字段
                            field.set(bean, getBean(ref));

                        }
                    }

                }
                //将实例注册到容器中
                registerBean(id, bean);
            }
        }
    }

    private void registerBean(String id, Object bean) {
        beanMap.put(id, bean);
    }

    public Object getBean(String ref) throws IllegalAccessException {
        Object o = beanMap.get(ref);
        if (o == null) {
            throw new IllegalAccessException("there is no bean with name :" + ref);
        }
        return o;
    }
}

```

容器测试使用的 bean 代码：

```java
@Data
public class Wheel {
    private String brand;
    private String specification ;
    
}
```



```java
@Data
public class Car {
    private String name;
    private String length;
    private String width;
    private String height;
    private Wheel wheel;
    

}
```

bean 配置文件 ioc.xml 内容:

```xml
<beans>
    <bean id="wheel" class="top.bcoder.test.service.spring.ioc.Wheel">
        <property name="brand" value="Michelin" />
        <property name="specification" value="265/60 R18" />
    </bean>

    <bean id="car" class="top.bcoder.test.service.spring.ioc.Car">
        <property name="name" value="Mercedes Benz G 500"/>
        <property name="length" value="4717mm"/>
        <property name="width" value="1855mm"/>
        <property name="height" value="1949mm"/>
        <property name="wheel" ref="wheel"/>
    </bean>
</beans>
```

IOC 测试类 SimpleIOCTest：

```java
public class SimpleIOCTest {
    @Test
    public void getBean() throws Exception {
       // String location = SimpleIOC.class.getClassLoader().getResource("ioc.xml").getFile();
        String location = "src/main/java/top/bcoder/test/service/spring/ioc/ioc.xml";
        SimpleIOC bf = new SimpleIOC(location);
        Wheel wheel = (Wheel) bf.getBean("wheel");
        System.out.println(wheel);
        Car car = (Car) bf.getBean("car");
        System.out.println(car);
    }
}
```

测试结果：

![](http://img.bcoder.top/2020.02.04.1/1.png)





![](http://img.bcoder.top/2020.02.04.1/2.png)

![](http://img.bcoder.top/2020.02.04.1/3.png)

![](http://img.bcoder.top/2020.02.04.1/4.png)

![](http://img.bcoder.top/2020.02.04.1/5.png)

![](http://img.bcoder.top/2020.02.04.1/6.png)

