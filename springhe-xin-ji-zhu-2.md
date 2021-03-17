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

##### 构造器参数解析

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















