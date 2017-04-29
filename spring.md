# 3. IoC容器
## 3.1 	Spring IoC容器及beans简介
    本章主要讲述Spring框架的IoC（控制反转）实现。IoC也称作DI（依赖注入）。它是指这样一种过程：一个对象的所有依赖都将通过构造参数、工厂方法参数或此对象的属性来定义，之后容器会在创建此对象时注入这些依赖。这和对象自身通过直接使用构造函数或类似于Service Locator这种机制来创建依赖是完全相反的，这也是控制反转的由来。
Org.springframework.beans和org.springframewok.context
