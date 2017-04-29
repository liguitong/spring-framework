# 3. IoC容器
## 3.1 	Spring IoC容器及beans简介
        本章主要讲述Spring框架的IoC（控制反转）实现。IoC也称作DI（依赖注入）。
    它是指这样一种过程：一个对象的所有依赖都将通过构造参数、工厂方法参数或此对象的属性来定义，
    之后容器会在创建此对象时注入这些依赖。这和对象自身通过直接使用构造函数或类似于Service Locator
    这种机制来创建依赖是完全相反的，这也是控制反转的由来。
        org.springframework.beans和org.springframewok.context两个包是Spring框架IoC容器的基础。
    BeanFactory接口提供了一种高级配置机制，可以用来管理任意对象。而ApplicationContext是BeanFactory
    的子接口，它的特性使得与Spring的AOP功能集成更加容易，这些特性包括：用于国际化处理的消息资源处理、
    事件发布及应用层特定上下文，例如用于web应用程序中的WebApplicationContext。
