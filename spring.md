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

![][note] XML并非是配置元数据的唯一方式，Spring IoC容器与配置元数据的方式已经完全解耦，在今天，越来越
    多的开发者选择[基于java的配置](#312)来使用Spring应用。

　　有关如何使用其它方式配置Spring容器，可以参考如下章节：

* [基于标注的配置](#39)：从Spring2.5开始引入基于标注的配置。
* [基于Java的配置](#312)：从Spring3.0开始，基于Java配置的很多工程所提供的特性成为Spring框架的核心，
因此你可以选择使用java而不是XML来扩展应用。要使用这些功能，请参考
`@Configuration`,`@Bean`, `@Import`,`@DependsOn` 标注。

　　Spring配置包含至少一个且通常多于一个bean定义用于管理。基于XML的配置通过在顶级元素`<beans/>`
    下包含的`<bean/>`元素来表示这些beans。基于Java的配置则通过`@Configuration`类的`@Bean`标注方
    法来实现。

　　bean定义与组成应用的实际对象一一对应。通常情况下你只定义服务层对象，如数据访问对象（DAOs)，
    表现层对象 Struts `Action`实例，基础框架对象 Hinerate `SessionFactories`,JMS `Queues`等。
    而不定义具体领域对象，因为创建领域对象是DAOs和业务逻辑的责任。然而你也可以通过集成AspectJ来配置
    在IoC容器之外创建的对象。请参考[使用AspectJ在Spring中注入领域对象](#781)

　　下面的示例展示了XML配置框架：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="..." class="...">
		<!-- collaborators and configuration for this bean go here -->
	</bean>

	<bean id="..." class="...">
		<!-- collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions go here -->

</beans>
```

　　`id`属性是一个字符串，用来标识一个单独的bean定义。`class`属性定义了bean的类型和完整的类型名。
属性的值指向协作对象，用来指明协作对象的xml在本示例中未展示，详细请参考[依赖](#34)。

### 3.2.2 实例化容器

　　实例化Spring IoC容器非常简单。`ApplicationContext`的构建参数中的path或paths实际上就是
    资源字符串，容器使用它来加载配置元数据。这些资源字符串可以代表各种各样的资源，比如：文件系统、
    java `CLASSPATH`等等。

```
ApplicationContext context =  
    new ClassPathXmlApplicationContext(new String[]{"services.xml","daos.xml"});
```  

![][note] 在学习了Spring IoC容器之后，你也许会想深入了解Spring的资源抽象相关内容，这在[章节4，资源](#4)进行描述，
    它提供了一种方便的机制，用来读取一个输入流，输入流使用url形式描述。在特殊情况下，资源路径用来构建应用上下文，就
    如在[章节4.7 应用上下文和资源路径](#47)所描述的那样。  
　　下面的例子演示了服务层对象`(services.xml)`配置文件:
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

　　下面的例子演示了数据访问层对象`daos.xml`文件配置：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="accountD
		class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions for data access objects go here -->

</beans>
```  
　　在上述例子中，服务层由类`PetStoreServiceImpl`和两个数据访问对象`JapAccountDao`,`JpaItemDao`
    （基于JPA对象/关系映射标准）。`property name`元素表示JavaBean的属性名，而`ref`元素表示另一个
    bean定义的名称。而`id`和`ref`之间的这种关系表明了各个协作对象之间的依赖。有关对象依赖的细节请参
    考[依赖](#34)  

#### 基于XML描述的元数据的组合

　　如果bean的定义能够跨越多个XML文件，这将会非常有用。通常在你的架构中，一个逻辑层或模块由一个单独的xml
    配置文件来表示。  
　　你可以使用 application context的构造函数来加载这些XML片断，就如上节所述，这个构造函数可以传入
    多个`Resource`位置。另外，你也可以通过使用一个或多个`<import/>`元素来加载其它文件中的bean定义。
    如下所示：

```
<beans>
    <import resource="services.xml" />
    <import resource="resources/messageSource.xml" />
    <import resource="/resources/themeSource.xml" />

    <bean id="bean1" class="..." />
    <bean id="bean2" class="..." />
</beans>
```  

　　在上述例子中，从额外三个文件中引入了bean定义：`services.xml`,`messageSource.xml`,`themeSource.xml`。
    这些文件路径都是相对于引入这三个文件的bean定义文件设置的，所以`services.xml`必须放在和导入这些文件的
    xml位于同一个目录或相同的类路径，而`messageSource.xml`和`themeSource.xml`则要放在下一层的`resources`
    目录下。如你所见，路径开头的斜杠是被忽略的，但鉴于这些路径是相对的，所以最好根本就不要加斜杠。
    被引入的文件，包括顶层的`<beans/>`元素，一定要符合Spring xml bean定义模式。

![][note]我们不推荐使用相对路径"../"去引入父目录下的文件，即使这样做是合法的。这样的后果是我们创建了一个应用程序
    之外的依赖，尤其不推荐的是在"classpath" URLS中这样做（例如："classpath:../services.xml"）,在运行时应用会
    选择一个最近的classpath的根目录，并查找它的父目录。Classpath配置的改变会导致不同的、错误的目录。
　　  你也可以一直使用绝对路径，例如："file:C:/config/services.xml"或"classpath:/config/services.xml"。
    但你要明白，这样你就会把你的应用和一些特定的路径耦合在一起了。一种更好的做法是间接引用这种绝对路径，例如：
    通过使用"${...}"这种在运行时通过JVM系统参数解决的占位符。  

　　import指令是beans命名空间本身的特性，除了这种纯粹的bean定义之后，更多的特性可以通过选择Spring 提供的命名空间
    来实现，例如："context"和"util"命名空间。

#### Groovy Bean定义语言  

　　作为外部配置元数据的另一个例子，bean定义也可以通过Spring的Groovy bean定义语言实现，这可以从Grails框架得来。
    通常，你可以在一个如下构建的".groovy"文件中配置：
```
beans {
    dataSource(BasicDataSource) {
        driverClassName = "org.hsqldb.jdbcDriver"
        url = "jdbc:hsqldb:mem:grailsDB"
        username = "sa"
        password = ""
        settings = [mynew:"setting"]
    }
    sessionFactory(SessionFactory) {
        dataSource = dataSource
    }
    myService(MyService) {
        nestedBean = { AnotherBean bean -> 
                dataSource = dataSource
            }
    }
}
```
　　这种配置风格在很大程序上等同于XMLbean定义，甚至支持Spring的XML配置的命名空间.
    它同样允许通过 "importBeans"指令 导入XMLbean定义文件。
### 3.2.3 容器使用
　　`ApplicationContext`是高级工厂接口，能够用来维护不同beans及其依赖的注册表。
    你可以通过`T getBean(String name, CLass<T> requiredType)`方法来获取bean实例。
　　`ApplicationContext`可以让你通过下面的方式读取并访问它们:
```
// create and configure beans  
ApplicationContext context =  
    new ClassPathXmlApplicationContext("services.xml", "daos.xml");  
// retrive configured instance  
PetStoreService service = context.getBean("petStore", PetStoreService.class);  
// use configured instance  
List<String> userList = service.getUsernameList();
```
　　使用Groovy的配置，启动代码看起来类似，只是使用不同的上下文实现，此上下文实现是
    Groovy感知的(但也可以理解XML bean定义):
```
    ApplicationContext context =  
        new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```
　　最灵活的变体是`GenericApplicationContext`与读者代表结合使用，例如与
    `XmlBeanDefinitionReader`结合读取XML文件：
```
    GenericApplicationContext context = new GenericApplicationContext();
    new XmlBeanDefinitionReader(ctx).loadBeanDefinitions("services.xml","daos.xml");
    context.refresh();
```
　　`GroovyBeanDefinitionReader`结合读取groovy文件：
```
    GenericApplicationContext context = new GenericApplicationContext();
    new GroovyBeanDefinitionReader(ctx).loadBeanDefinitions("services.groovy", "daos.groovy");
    context.refresh();
```
　　如果需要，这读者委托可以混合使用并匹配到同一个`ApplicationContext`，从不同的配置源读取bean定义。
    你可以使用`getBean`方法来获取你的bean对应的实例。`ApplicationContext`接口也有许多其它方法用于获取
    bean，但最理想的情况就是你永远也用不到它们。实际上，你的应用代码根本就不应该使用`getBean()`方法，也
    不需要依赖Spring的API.例如，Spring的web应用集成框架提供了对于各种web组件的依赖注入，比如控制器和
    JSF管理的beans，这允许你通过元数据来声明一个特定bean的依赖。
## 3.3 Bean概览
　　Spring IoC容器管理一个或多个beans，这些beans通过你提供给容器的配置元数据来创建，比如XML格式的`<bean/>`
    定义。
　　在容器内部，这些bean定义用`BeanDefinition`对象表示，此对象包含以下元数据(或者更多)：
* 一个包限定的类名：通常是被定义bean的实现类；
* bean行为配置元素，用来说明bean在容器中的行为（作用域、生命周期回调等等）；
* bean完成工作需要的对其它bean的引用，这些引用也被称作依赖或合作者；
* 其它用来设置新创建对象的配置，例如：bean用来管理连接器的连接数，或者连接池最大数目。

　　这些配置会转换成它们组成的bean的各种属性
### 3.1.bean定义
　　|属性          |章节                              |
　　|--------------|--------------------____________:|
　　|class         |[3.3.2 初始化bean](#332)          |
## 3.16
    		


[figure31]:src/docs/images/container-magic.png
[note]:src/docs/images/note.png