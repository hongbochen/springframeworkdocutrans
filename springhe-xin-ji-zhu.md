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

简而言之，`BeanFactory`提供了配置框架和基本的功能，并且`ApplicationContext`添加了更多的企业特定的功能。`ApplicationContext`是`BeanFactory`的一个完备的超级，并且被唯一使用在描述Spring IoC容器的这一章节中。关于使用`BeanFactory`而不是`ApplicationContext`的更多信息，请查看`1.16小节的BeanFactory的介绍`。

在Spring中，那些组成你应用骨干的和那些被Spring IoC容器管理的对象被称为_beans_。一个Bean是一个被实例化的，组装的或其他被Spring IoC容器管理的对象。此外，简单的说，一个Bean就是你的应用程序中的对象中的一个。Beans和他们之间的依赖被映射到由容器使用的配置元数据中。

### 1.2 容器概览

接口`org.springframework.context.ApplicationContext`代表着Spring IoC容器并且负责实例化，配置和组装上述的beans。该容器使它的指令通过读取配置元数据作用于哪些对象去初始化，配置和组装。配置元数据表现在XML，Java注解或Java代码中。它允许您表达组成应用程度的对象以及这些对象之间的丰富的依赖性。

很多ApplicationContext接口的实现为开箱即用的。在独立的应用程序中，创建一个`ClassPathXmlApplicationContext`或`FileSystemXmlApplicationContext`的实例是通用的。虽然XML是定义配置元数据的原始格式，但是你可以通过提供少量的XML配置来指示容器使用Java注释或代码作为元数据格式，以声明性地支持这些附加的元数据格式。

在大多数的应用程序场景中，不需要显式的用户代码来实例化Spring IoC容器的一个或多个实例。例如，在一个Web应用场景中，在应用程序的web.xml文件中，一个简单的样板Web描述符XML通常就足够了。如果你正在使用基于Eclipse开发环境的[Spring工具套件](https://spring.io/tools/sts)，这个样板配置可以通过很少的鼠标点击或按键来简单的创建。

下面的图表是Spring如何工作的高层视图。你的应用程序是与配置元数据绑定在一起的，所以在`ApplicationContext`被创建和初始化之后，你将会有一个完成的配置完成的和可执行的系统或应用程序。

![](/assets/IoC容器.png)

#### 1.2.1 配置元数据

正如前面的图标显示，Spring IoC容器消费了一种配置元数据的形式；





















