### 1.4、依赖

一个典型的企业级应用不仅仅包含一个对象（也就是在Spring说法中的bean）。即使最简单的应用也有一些一块协作的对象来作为一个连贯的应用展现给用户。下一小结展示了如何从单独定义一些bean定义到如何通过对象之间的合作实现一个完整的应用程序。

#### 1.4.1 依赖注入

依赖注入\(DI\)是对象定义他们的依赖\(也就是说，与该对象一快协作的对象\)的过程，该过程仅仅通过构造器参数，工厂方法参数，被构造器构造或工厂方法返回的实例设置属性来工作。当容器创建bean的时候，注入这些依赖。该过程与bean自己控制实例化或通过类或服务定位器模式直接构造依赖的方式正好是相反的，因此得名，控制反转。

使用DI规则编码更干净，当对象具有依赖关系时，解耦更有效。对象不用搜索他的依赖，并且不用知道依赖的位置或类。因此，你的类将使得测试更为简单，特别是当依赖在接口或基本抽象类中的时候，这允许在单元测试中使用存根或模拟实现。

DI主要有两种主要的变体：基于构造器的依赖注入和基于Setter方法的依赖注入。

##### 基于构造器的依赖注入

基于构造器的依赖注入是由容器调用一个带有多个参数的构造器实现的，构造器中的每一个参数都代表一个依赖。调用一个带有特定参数的`static`工厂方法来构建bean接近相同，这个讨论类似的处理构造函数和静态工厂方法的参数。下述的例子展示了一个只能通过使用构造器注入进行依赖注入的类：

```
public class SimpleMovieListener{
    // SimpleMovieListener有一个MovieFinder的依赖
    private MovieFinder movieFinder;

    // Spring容器能够注入一个MovieFinder的构造器
    public SimpleMovieListener(MovieFinder movieFinder){
        this.movieFinder = movieFinder;
    }

    // 实际使用被注入的MovieFinder的业务逻辑被省略...
}
```

注意，这个类并没有特别的地方。这就是一个没有依赖，没有容器特定的接口，基本类或注解的POJO\(Plain Ordinary Java Object，简单Java对象\)。

###### 构造器参数解析

构造器参数解析匹配通过使用参数类型进行。如果在bean定义的构造器参数中没有潜在的歧义存在，则在bean实例化的时候，定义在bean定义中的构造器参数的顺序，也是这些参数被提供给合适的构造器的顺序。考虑下面一个类：

```
package x.y;

public clas ThingOne{
    public ThingOne(ThingTwo thingTwo, ThingThree thingThree){
        // ...
    }
}
```

假设类`ThingTwo`和`ThingThree`没有继承关系，没有潜在的歧义存在。这样，下面的配置就是正常工作的，你不需要在`<constructor-arg/>`元素中明确的指定构造器参数的索引或类型。

```
<beans>
    <bean id="beanOne" class="x.y.ThingOne">
        <constructor-arg ref="beanTwo"/>
        <constructor-arg ref="beanThree"/>
    </bean>

    <bean id="beanTwo" class="x.y.ThingTwo"/>

    <bean id="beanThree" class="x.y.ThingThree"/>
</beans>
```

当另外一个bean被引用，并且类型是已知的，也是可以匹配的（和上述例子一样）。当一个简单的类型被使用，例如`<value>true</value>`，Spring不能决定这个值的类型，因此在没有帮助的情况下不能匹配这个类型。考虑下面的类：

```
package examples;

public class ExampleBean{
    // 计算最终答案的念书
    private int years;

    // 生命，宇宙或一切的答案
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer){
        this.year = year;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

###### 构造器参数类型匹配

在上述场景，如果通过使用`type`属性明确执行构造器参数的类型，容器能够使用类型匹配简单的类型。就像下面的例子一样：

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

###### 构造器参数索引

你可以使用`index`属性来明显的指定构造器参数的索引，就像下面的例子一样：

```
bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```

除了解析多个简单值的歧义，如果一个构造器由两个相同类型的参数，指定参数索引可以解决歧义。

> 构造器参数索引是从0开始的。

###### 构造器参数名称

你也可以使用构造器的参数名称来消除值歧义，就像下面的例子展示的：

```
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```

要注意，为了使得这个工作开箱即用，你的代码必须要使用debug模式进行编译，以使得Spring能够从构造器中找到参数名称。如果你不想或不能使你的代码在debug模式下进行编译，你可以使用`@ConstructorProperties`的JDK注解来明确的指定构造器参数的名称。下面使实例：

```
package examples;

public class ExampleBean{
    // 属性省略

    @ConstructorProperties({"years","ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer){
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

##### 基于Setter的依赖注入

基于Setter的依赖注入，是当调用一个无参构造器或一个没有参数的静态工厂方法实例化一个bean之后，容器调用bean的setter方法完成的。

下面的例子展示了一个仅仅能够使用单纯的setter注入来实现依赖注入的类。这个类是传统的Java。他是一个没有依赖，没有容器特定接口，没有基本类和注解的POJO。

```
public class SimpleMovieListener{
    // SimpleMovieListener有MovieFinder的依赖
    private MovieFinder movieFinder;

    // setter方法，因此Spring容易可以注入MovieFinder依赖
    public void setMovieFinder(MovieFinder movieFinder){
        this.movieFinder = movieFinder;
    }
}
```

`ApplicationContext`对他管理的bean支持基于构造器的依赖注入和基于setter方法的依赖注入。他也支持当一些依赖已经通过构造器方法注入之后，在通过setter方法进行依赖的注入。你可以在`BeanDefinition`表中配置依赖，这样你可以与`PropertyEditor`实例合作将属性从一种格式转换为另外的格式。然而，大多数Spring用户并不直接使用这个类，而是使用xml的bean定义，注解组件的方式（也就是被`@Component`,` @Controller`等注解的类）或者是在基于java的`@Configuration`类中的`@bean`方法。这些来源都可以在内部被转换为`BeanDefination`实例，然后用于加载整个Spring IoC容器实例。



> ### **基于构造器的依赖注入还是基于setter方法的依赖注入？**
>
> 既然你可以混合基于构造器的依赖注入和基于setter的依赖注入，对于强制性依赖使用基于构造器的依赖注入，对于可选的依赖使用setter方法或配置方法是一个很好的经验法则。注意在一个setter方法上使用@Require注解可以被用来使得这个属性成为一个必须的依赖。然而，最好使用带有参数程序验证的构造函数注入。
>
> Spring团队普遍倡导构造器注入，因为这使得你的应用程序组件实现为不可变的对象，并且确保需要的依赖不是null。除此之外，构造函数注入的组件总是以完全初始化的状态返回给客户机代码。作为提示，使用大量的构造器参数是一个很坏的编码方式，这暗示着这个类可能有很多责任，并且应该进行重构来更好的解决属性分离的问题。
>
> 基于Setter的依赖注入应该仅仅用在可选的依赖中，而这些依赖可以在类中被分配合理的默认值。因此在使用依赖的时候，非空检查是必要的。使用setter注入的一个好处就是使得对象比较容易在后面进行重新配置或重新注入。因此，通过jmxmbeans进行管理是setter注入的一个引人注目的用例。





























































































































