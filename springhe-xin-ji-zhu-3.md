##### 依赖关系的处理过程

容器按照下面的方式运行依赖项处理：

* `ApplicationContext`是用描述所有bean的配置元数据进行创建和初始化的。配置元数据可以使用XML，Java编码或注解进行指定。
* 对于每一个bean，他的依赖以属性，构造器参数或静态工厂方法（如果你用它替代普通的构造函数）的参数来表达。当bean真正被创建的时候，这些依赖被提供给bean。
* 在容器中的每一个属性或构造器参数都是一个被设置的值或指向另外一个bean的引用的真实定义。
* 作为值每一个属性或构造器参数都将从其指定的格式转换为该属性或构造器参数的实际类型。默认情况下，Spring可以将以String的值转换为所有的类型，例如int, long, String, boolean等等。

在容器被创建的时候，Spring容器会验证每一个bean的配置。但是，bean在被真实创建之前，bean的属性是不被设置的。当容器被创建的时候，单例作用域的或被设置为预实例化的（默认）的bean也会被创建。作用域被定义在`Bean Scopes`。因此，bean只有在被需要的时候，才会被创建。一个bean的创建默认会导致一系列的bean被创建，因为bean的依赖和依赖的依赖等都会被创建并且赋值。请注意，这些依赖直接的解析不匹配将会最晚出现-也就是说，在第一个创建受影响的bean的时候。

> ## 循环依赖
>
> 如果你使用构造器依赖注入，有可能会创建一个无法处理的循环依赖的场景。
>
> 例如：类A通过构造器注入需要一个类B的实例，类B通过构造器注入需要一个类A的实例。如果你为类A和B配置bean以便相互注入，Spring IoC容器在运行的时候检测到循环依赖，则会抛出`BeanCurrentlyInCreationException。`
>
> 一种可能的处理方式便是编辑一些类的源码使其变为通过setter进行配置，而不是使用构造器注入。二者选一，避免构造器注入或只使用setter注入。也就是说，虽然不是推荐的方式，但你仍然可以使用setter注入来配置循环依赖。
>
> 与典型案例（没有循环依赖的问题）不同，bean A和bean B之间的循环依赖问题迫使一个bean在完全初始化完成之前被注入到另外一个bean中。

通常情况下，你可以信任Spring会做正确的事情。它在容器加载期间检测配置问题，例如对不存在的bean的引用和循环依赖问题。当bean被真正创建的时候，Spring尽可能晚的设置属性和解决依赖。这就意味着，当你请求一个对象的时候，如果创建那个对象或他的依赖存在问题，已经被正确加载的容器可能产生一个异常。例如bean生成一个缺失或未验证属性的异常结果。某些配置问题的这种延迟可见性是ApplicationContext实现默认情况下的预实例化单例bean的原因。在实际需要这些bean之前，需要花费一些时间和内存来创建他们，在ApplicationContext被创建的时候而不是之后，你就会发现配置问题。你仍然可以重写这些默认的行为，因此单例bean懒实例化，而不是预实例化。

如果没有循环依赖存在，当一个或多个协作的bean被注入到一个依赖的bean的时候，每一个协作的bean在注入到依赖的bean之前都是被完全配置好的。这就意味着，如果bean A有对bean B的依赖，Spring IoC容器在调用bean A的setter方法之前，完全配置好bean B。换句话说，bean被实例化\(如果不是预实例化单例\)，他的依赖被设置，然后相关的生命周期方法（例如配置方法或初始化bean的回调方法）被调用。

##### 依赖注入的例子

下面的例子使用XML配置元数据用于基于setter的依赖注入。Spring XML配置文件的一小部分指定bean定义，如下所示：

```
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- setter injection using the nested ref element -->
    <property name="beanOne">
        <ref bean="anotherExampleBean"/>
    </property>

    <!-- setter injection using the neater ref attribute -->
    <property name="beanTwo" ref="yetAnotherBean"/>
    <property name="integerProperty" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```

下面的例子展示了相应的ExampleBean类：

```
public class ExampleBean{
    private AnotherBean beanOne;

    private YetAnotherBean beanTwo;

    private int i;

    public void setBeanOne(AnotherBean beanOne){
        this.beanOne = beanOne;
    }

    public void setBeanTwo(YetAnotherBean beanTwo){
        this.beanTwo = beanTwo;
    }

    public void setIntegerProperty(int i){
        this.i = i;
    }
}
```

在上述的例子中，setter方法被声明用于匹配XML文件中指定的属性。下面的例子使用基于构造器的依赖注入：

```
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- constructor injection using the nested ref element -->
    <constructor-arg>
        <ref bean="anotherExampleBean"/>
    </constructor-arg>

    <!-- constructor injection using the neater ref attribute -->
    <constructor-arg ref="yetAnotherBean"/>

    <constructor-arg type="int" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```

下面的雷子展示了相应的ExampleBean类：

```
public class ExampleBean{
    private AnotherBean beanOne;

    private YetAnotherBean beanTwo;

    public ExampleBean(AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i){
        this.beanOne = anotherBean;
        this.beanTwo = yetAnotherBean;
        this.i = i;
    }
}
```

在bean定义中指定的构造器参数被用做ExampleBean的构造器的参数。

现在，考虑一个例子的变体，不是使用构造器，Spring被告知调用一个静态工厂方法来返回一个对象的实例。

```
<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance">
    <constructor-arg ref="anotherExampleBean"/>
    <constructor-arg ref="yetAnotherBean"/>
    <constructor-arg value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```

下面的例子展示了相应的ExampleBean类：

```
public class ExampleBean {

    // a private constructor
    private ExampleBean(...) {
        ...
    }

    // a static factory method; the arguments to this method can be
    // considered the dependencies of the bean that is returned,
    // regardless of how those arguments are actually used.
    public static ExampleBean createInstance (
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {

        ExampleBean eb = new ExampleBean (...);
        // some other operations...
        return eb;
    }
}
```

静态工厂方法的参数有&lt;constructor-arg /&gt;元素提供，实际上与构造器实际被使用一样。由静态工厂方法返回的类的类型不必与包含静态工厂方法的类的类型相同（虽然在例子中是这样的）。一个实例（非静态）工厂方法可以以基本相同的方式使用（除了使用factory-bean属性而不是class属性之外），因为在这里不在讨论这些细节。

