# 核心技术

发行版5.0.8

---

_这一部分的指导文档覆盖了所有的完全集成到Spring框架中的技术。_

这些技术中首要的便是Spring框架的控制反转\(IoC\)容器。Spring框架的IoC容器的全面的处理是与Spring的面向切面编程\(AOP\)的彻底实现紧密相关的。Spring框架拥有他自己的AOP框架，该技术在概念上是很容器理解的，并且成功的解决了在Java企业编程中的AOP需求的80%的点。

该框架也提供了与AspectJ\(当前最丰富的-就特征而言-并且是在Java企业空间中最成熟的AOP实现\)的集成。

## 1、IoC容器

### 1.1、Spring IoC容器和beans的介绍

这篇章节覆盖了IoC原则的Spring框架的的实现。IoC也被称为依赖注入\(DI\)。这是对象定义他们依赖关系的过程，也就是说，其他对象仅仅通过构造器参数，工厂方法参数，或者是在它被构造或从一个工厂方法中返回后设置到对象实例上的属性来工作。当他创建bean的时候，容器接着注入这些依赖。这个过程从根本上是相反的，因此，名称“控制反转”，bean它本身控制实例或者是通过使用类的直接构造器或一个机制，例如服务定位器模式来定位他的依赖。

包`org.springframework.beans`和`org.springframework.context`是Spring框架的IoC容器的基础。`BeanFactory`接口提供了一个先进的配置机制能够管理任何类型的对象。`ApplicationContext`是BeanFactory的一个子接口。它增加了与Spring AOP特征的更加简单的集成；消息资源处理（用于国际化中），事件发布和应用层特定的上下文例如用于Web应用中的`WebApplicationContext。`

简而言之，BeanFactory提供了配置框架和基本的功能，并且ApplicationContext添加了更多的企业特定的功能。ApplicationContext是BeanFactory的一个完备的超级，

