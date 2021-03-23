#### 1.4.2 依赖和配置详情

正如上一小节提到的那样，您可以将bean属性和构造函数参数定义为对其他托管bean（协作者）的引用或内联定义的值。为了这个目的，Spring的基于XML的配置元数据在`<property/>`和`<contructor-arg/>`元素中支持子元素类型。

##### 直接值（基本数据类型，String等）

&lt;property/&gt;元素的alue属性指定一个属性或构造器参数作为用于用户可读的变量。Spring转换服务被用来转换这些数值从一个字符串转到属性或参数的真是类型。下面的例子展示了设置一个bean的不同的值：

```
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- results in a setDriverClassName(String) call -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="misterkaoli"/>
</bean>
```

下面的例子使用p-namespace来使用更多简洁的XML配置：

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="root"
        p:password="misterkaoli"/>

</beans>
```

前面的XML是更加简洁的。然而，打字错误是在运行时发现的，而不是在设计的时候发现的，除非当你创建一个bean定义的时候，你使用一个支持自动属性完善的IDE。这样的IDE帮助是高度推荐的。

你也可以配置一个`java.util.Properties`实例，就行下面这样：

```
<bean id="mappings"
    class="org.springframework.context.support.PropertySourcesPlaceholderConfigurer">

    <!-- typed as a java.util.Properties -->
    <property name="properties">
        <value>
            jdbc.driver.className=com.mysql.jdbc.Driver
            jdbc.url=jdbc:mysql://localhost:3306/mydb
        </value>
    </property>
</bean>
```

Spring容器通过使用JavaBeans的`propertyEditor`机制将`<value/>`元素中的文本转换为一个`java.util.Properties`实例。这是一个很好的快捷方式，是Spring团队在其中的几个地方中，有几个地方支持使用嵌套的`<value/>`元素而不是`value`属性样式。

###### idref元素

`idref`元素仅仅是一个通过在容器中传递另外一个bean的id到`<contructor-arg/>`或`<property/>`元素来放错的方式。下面的例子展示了如何使用它：

```
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```

上述bean定义代码段在运行时与下面的代码段是一样的：

```
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean"/>
</bean>
```

第一种形式比第二种形式更好，因为使用`idref`标签使得容器在部署时能够验证那个被引用的，被命名的bean是否存在。在第二种形式中，在被传递到`client `bean的`targetName`属性的值是不运行程序验证的。拼写错误（很有可能是致命错误）仅仅在`client `bean在实际被初始化的时候被发现。如果client bean是原型bean，只有在容器部署很长一段时间，才能发现此打字错误和由此产生的异常。

> 注意：
>
> 在`idref`元素中的`local`属性在4.0 beans XSD中已经不再支持，因为它不再提供比常规bean引用更高的值。当更新到4.0架构的时候，更高你现存在的`idref local`引用为`idref bean`。

&lt;idref/&gt;带来值的通用的地方就是在一个ProxyFactoryBean的bean定义中的AOP拦截器的配置。当你指定拦截器名称的时候，使用&lt;idref/&gt;可以防止误拼拦截器ID。













































