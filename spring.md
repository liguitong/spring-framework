# 3. IoC容器
## 3.1 Spring IoC容器及beans简介

　　　　本章主要讲述Spring框架的IoC（控制反转）实现。IoC也称作DI（依赖注入）。
    它是指这样一种过程：一个对象的所有依赖都将通过构造参数、工厂方法参数或此对象的属性来定义，
    之后容器会在创建此对象时注入这些依赖。这和对象自身通过直接使用构造函数或类似于Service Locator
    这种机制来创建依赖是完全相反的，这也是控制反转的由来。

　　　　`org.springframework.beans`和`org.springframewok.context`两个包是Spring框架IoC容器的基础。
    `BeanFactory`接口提供了一种高级配置机制，可以用来管理任意对象。而`ApplicationContext是BeanFactory`
    的子接口，它的特性使得与Spring的AOP功能集成更加容易，这些特性包括：用于国际化处理的消息资源处理、
    事件发布及应用层特定上下文，例如用于web应用程序中的`WebApplicationContext`。

　　　　总之`BeanFactory`类提供了配置框架和基础功能，而`ApplicationContext`增加了更多了企业特定功能。
    `ApplicationContext`类是`BeanFactory`的超集，它在本章中也特定用来描述Sping的IoC容器。
	关于`ApplicationContext`的更多描述，请见[3.16 The BeanFactory](#316)

　　　　在Spring中，构成应用程序的主干和由Sping IoC容器管理的对象被称为`beans`。`bean`是指由Spring 
	IoC 容器进行实例化、组装或着管理的对象。另外，`bean`只是应用程序中诸多对象之一。`Beans`及它们的
	依赖反映在容器的元数据配置中。
## 3.2 容器简介

　　　　`org.springframework.context.ApplicationContext`接口代表了Spring的IoC容器，并负责实例化和
	组装上述的`beans`。容器通过读取配置元数据来决定对什么对象进行实例化、配置及组装。配置元数据通常由XML
	Java标注或Java代码表示。你可以用它来表示组成你应用程序的各个对象及彼此之间的依赖关系。

　　　　`ApplicationContext`接口的几个实现类也与Spring一同发布。在单独的应用程序中，通过会创建
	`ClassPathXmlApplicationContext`和`FileSystemXmlApplicationContext`。除了使用XML这种传统的
	配置元数据的方式外，你还可以使用Java标注或代码作为配置元数据的方式，而这只需要通过少量的XML配置来显
	示使用这些配置方式。

　　　　在大多数据应用场景中，不需要通过代码来显示的实例化Spring IoC容器。例如，在web 应用程序中，通过
    在web.xml中八行左右的样例代码就足够了。
        (参见[3.15.4 便利的Web应用程序的ApplicationContext初始化](#3154))
        如果您正在使用Eclipse驱动的Spring Tool Suite来开发，那这个样例只需要简单的点几下鼠标或敲击键
    盘就足够了。

　　　　下图展示了Spring工作的上层视图。应用程序中的类是与配置元数据绑定的，因此在`ApplicationContext`
    创建和初始化之后，你就拥有一个完全配置化的可执行系统和应用。
### Figure 3.1. Spring IoC容器
![][figure31]

## 3.2.1 配置元数据

　　　　如上图所示，Spring IoC容器通过配置元数据工作。通过配置元数据，开发者可以告诉Spring容器如何去实
    例化、配置和组装应用中的对象。

　　　　元数据通常以传统的简单直观的XML格式配置，本章中大多数时候都通过这种方式来展示Sping IoC容器的关
    键概念和功能。

![][note]
## 3.16
    		


[figure31]:src/docs/images/container-magic.png
[note]:srce/docs/images/note.png