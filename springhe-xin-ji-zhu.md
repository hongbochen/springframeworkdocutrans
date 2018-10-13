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

正如前面的图标显示，Spring IoC容器消费了一种配置元数据的形式；这个配置元数据表示应用程序开发人员告诉Spring容器如何在应用程序中实例化，配置和组装对象。

传统上，配置元数据是以简单直观的XML格式提供的，这篇章节主要使用它来传递Spring IoC容器的关键概念和特征。

> 基于XML的元数据不是唯一允许的配置元数据的形式。Spring IoC容器本身与实际写入配置元数据的格式完全分离。这些天，很多开发者选择_**基于Java的配置**_用于他们的Spring应用程序。

关于Spring容器中使用其他元数据格式的更多信息，请查看：

* 基于注解的配置：Spring 2.5引入了基于注解的配置元数据的支持。
* 基于Java的配置：从Spring 3.0开始，很多由Spring的Java配置项目提供的特征变成了核心Spring框架的一部分。因此，你可以在你的应用程序类之外通过使用Java而不是XML文件来定义beans。为了使用这些新的特征，请查看`@Configuration`，`@Bean`，`@Import`和`@DependsOn`注解。

Spring配置至少包含一个或超过一个容器必须要管理的bean定义。基于XML的配置元数据显示了这些beans在上层`<beans/>`元素中被配置成`<bean/>`元素。Java配置典型地在一个`@Configuration`类中使用`@Bean`注解方法。

这些bean定义对应于组成你的应用程序的实际对象。典型地就是你定义服务层对象，数据访问对象\(DAOs\)，表示对象，例如Struts `Action`实例，基础对象，例如Hibernate`SessionFactories`，JMS `Queues`等。通常情况下，容器中不配置细粒度域对象，因为通常创建和加载域对象是DAOs和业务逻辑的责任。然而，你可以使用带有AspectJ的Spring集成来配置在IoC容器控制之外被创建的对象。请查看[使用AspectJ来依赖注入Spring的域对象](/是)。

下列示例展示了基于XML配置元数据的基本结构：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        <!-- 在这里配置该bean的合作者和相关配置 -->
    </bean>

    <bean id="..." class="...">
        <!-- 在这里配置该bean的合作者和相关配置 -->

    </bean>

    <!-- 更多bean的配置 -->

</beans>
```

`id`属性是一个字符床，你可以使用该字符串来声明个人的bean定义。`class`属性定义bean的类型并且使用完全限定的类名。id属性的值指向协作对象。指向协作对象的XML没有在这个示例中展示出来；查看`依赖`获取更多信息。

#### 1.2.2 实例化一个容器

实例化一个Spring IoC容器是直截了当的。位置路径或提供给`AplicationContext`构造器的路径实际是资源字符串，它允许容器从外部资源，如本地文件系统，从Java `CLASSPATH`等中加载配置元数据。

```
ApplicationContext context = new ClassPathXmlApplicationContext("service.xml","daos.xml");
```

_在你学习Spring的IoC容器之后，你可能想要了解更多的关于Spring Resource的信息，正如在_`Resource`_中描述的，_`Resource`_提供了一个方便的机制用于从在URI语法中定义的位置中读取一个输入流。特别的，Resource路径被用于构建应用上下文，正如在_`Application contexts和Resouces paths`_中描述的。_

下面的示例展示了服务层对象\(services.xml\)配置文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```

下列示例展示了数据访问对象`daos.xml`文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for data access objects go here -->

</beans>
```

在前面的例子中，服务层包含类`PetStoreServiceImpl`，还有两个类型为`JpaAccountDao`和`JpaItemDao（基于JPA的对象/关系映射标准）`的数据访问对象。`property name`元素指向JavaBean属性的名称，`ref`指向另外bean定义的名称。`id`和`ref`的链接表示协作对象之间的依赖。想要了解配置一个对象依赖的更多细节，请查看[依赖](/上述)。

##### 组合基于XML配置的元数据

bean定义跨越多个XML文件是有用的。通常情况下，每一个独立的XML配置文件在你的架构中代表一个逻辑层或模块。

你可以使用应用上下文构造器从这些XML片段中加载bean定义。这个构造器采用多个`Resource`位置，就像在上一小节中展示的那样。相应替代的，使用一个或多个`<import/>`元素从其他一个或多个文件中加载bean定义。例如：

```
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```

在上面的示例中，额外的bean定义从三个文件中被加载上来：`services.xml`, `messageSource.xml`和`themeSource.xml`。在做importing的时候，所有位置路径都是与定义文件相对的，所以在做importing的时候，`service.xml`必须在相同的目录或作为文件的类路径的位置，然而`messageSource.xml`和`themeSource.xml`必须位于引入文件的位置下面的`resource`位置。正如你所看到的，主斜杠是被忽略的，但是考虑到这些路径都是相对的，不使用主斜杠是最好的形式。被引入文件的内容，包括顶层的`<bean/>`元素，根据Spring模式来说，都必须要是有效的XML的bean定义。

使用相对路径"../"在父目录中引入文件是可以的，但是不被推荐的。这样做在文件中也就是在当前应用外面创建了一个依赖。特别地，对于“classpath:”路径\(例如，"classpath:../service.xml"\)来说这样的引用是不被推荐的。





















































