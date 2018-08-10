# Spring框架概览

Spring使得创建Java企业应用程序变得简单。它提供了你在一个企业环境中Java语言任何需要包含的东西，同时在Java虚拟机中支持Groovy和Kotlin作为可选的语言，并且很灵活的创建任何依赖于应用需要的框架。对于Spring框架5.0，Spring要求JDK 8+\(Java SE 8+\)，并且已经提供了对JDK1.9的箱外支持。

Spring支持大范围的应用场景。在一个大型企业中，应用经常存在很长时间，并且必须运行在JDK上，并且应用服务器的更新周期会超过开发者的控制。其他一些应用可能会以一个单一的jar文件和嵌入式的服务器运行，可能在一个云环境中。但是其他的可能是单独的应用（例如批处理或集成工作负载）是不需要服务器的。

Spring是开源的。他有一个大的并且活跃的社区，并且基于多种多样的实际世界的用例来提供持续的反馈。这已经帮助Spring成功的发展了很长时间。

## 1、"Spring"代表什么意思？

术语"Spring"在不通过的上下文中代表着不同的事物。他可以被用于指向Spring框架项目本身，这是他所有开始的项目。随着时间的推移，其他的Spring项目已经在Spring框架上进行构建。大多时候，当人们说"Spring"的时候，他们的意思是整个项目家族。该参考文档关注的基础是：Spring框架本身。

**Spring框架被分为多个模块。应用程序可以选择它需要哪个模块。中心的模块是核心容器，包括一个配置模块和一个依赖注入机制。除去那些，Spring框架提供了针对不同应用架构的基础支持，包括消息队列，事务数据和持久化，还有网页。他也包含基于Servlet的Spring MVC网页框架，同时也支持Spring WebFlux交互式网站框架。**

关于模块的一个注意事项：Spring框架的jar包允许部署到JDK9的模块路径中\("Jigsaw"\)。在允许Jigsaw的应用中使用时，Spring框架5的jar包与"Automatic-Module-Name"清单条目一起出现，该清单定义了稳定的语言层的模块名称（"spring.core"，"spring.context"等），这是与jar人工名称（这些jar遵守了使用"-"代替"."等的相同的明明范式）是分离的。当然，Spring框架的jar包在JDK 8和JDK 9的类路径上运行很好。

---

## 2、Spring和Spring框架的历史

Spring诞生自2003年，用于响应早期J2EE的复杂性。但是有些人认为Java EE和Spring是竞争关系，Spring实际上与Java EE是互补的。Spring编程模型并不接受Java EE的规格，它集成了从Java EE中精心挑选的个别规格。

* Servlet API
* WebSocket API
* Concurrency Utilities\(并发组件集\)
* JSON Binding API\(JSON绑定API\)
* Bean Validation
* JPA
* JMS
* 还有有必要的用于事务协调的JTA/JCA设置

Spring框架也支持依赖注入和通用注解特性，应用程序开发人员可能会选择这个特性去使用而不是由Spring框架提供的Spring特性机制。

对于Spring框架5.0，Spring要求Jave EE 7 级\(例如Servlet 3.1+，JPA 2.1+\)作为最低要求 - 但是同时，当在运行时发生的时候，提供了在Java EE 8级别的新API的盒外集成。这使得Spring完全兼容Tomcat 8和Tomcat 9，WebSphere 9 和 JBoss EAP 7。

随着时间的流逝，Java EE在应用开发中扮演的角色已经发生了转变。在Java EE和Spring的早期阶段，应用程序被创建并部署到应用程序服务器中。现在，随着Spring Boot的帮助，应用程序以设备和云友好的方式被创建，其实现方式为内置的Servlet容器和一些琐碎的改变。对于Spring框架5.0，一个WebFlux应用甚至不需要直接使用Servlet API，并且可以运行到不是Servlet容器的服务器中。

Spring继续创新和演化。除了Spring框架，有一些其他项目，例如Spring Boot，Spring Security，Spring Data，Spring Cloud，Spring Batch等其他。一定要记住每一个项目都有他自己的源码仓库，问题追踪和发行节奏。查看[spring.io/projects](https://spring.io/projects)获取完整的Spring项目列表。

## 3、设计思想

当你学习一个框架的时候，不仅仅要了解它做什么，还有了解它遵守的原则，这是非常重要的。这是Spring框架的指导原则：

* 在各个层面提供选择。Spring让您推迟设计决策尽可能的晚。例如，你可以通过配置而不是修改你的代码来转换持久化提供者。同许多其他基础设施问题和与第三方API集成是真实的。
* 兼容不同方面。



