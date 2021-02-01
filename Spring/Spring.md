# Spring 知识点

## Spring Framework

Q：什么是 Spring Framework？  
A：Spring 是一个开源应用框架，旨在降低应用程序开发的复杂度。  
- 它是轻量级、松散耦合的。
- 它具有分层体系结构，允许用户选择组件，同时还为 J2EE 应用程序开发提供了一个有凝聚力的框架。
- 它可以集成其他框架，如 Spring MVC、Hibernate、MyBatis 等，所以又称为框架的框架( 粘合剂、脚手架 )。
***
Q：Spring Framework 中有多少个模块，它们分别是什么？  
A：如下图所示：  
![](http://static.iocoder.cn/images/Spring/2018-12-24/01.jpg)
***
Q：Spring 框架有什么好处？  
A：优点如下：  
- DI ：Dependency Injection(DI) 方法，使得构造器和 JavaBean、properties 文件中的依赖关系一目了然。
- 轻量级：与 EJB 容器相比较，IoC 容器更加趋向于轻量级。这样一来 IoC 容器在有限的内存和 CPU 资源的情况下，进行应用程序的开发和发布就变得十分有利。
- 面向切面编程(AOP)： Spring 支持面向切面编程，同时把应用的业务逻辑与系统的服务分离开来。
- 集成主流框架：Spring 并没有闭门造车，Spring 集成了已有的技术栈，比如 ORM 框架、Logging 日期框架、J2EE、Quartz 和 JDK Timer ，以及其他视图技术。
- 模块化：Spring 框架是按照模块的形式来组织的。由包和类的命名，就可以看出其所属的模块，开发者仅仅需要选用他们需要的模块即可。
- 便捷的测试：要测试一项用Spring开发的应用程序十分简单，因为测试相关的环境代码都已经囊括在框架中了。更加简单的是，利用 JavaBean 形式的 POJO 类，可以很方便的利用依赖注入来写入测试数据。
- Web 框架：Spring 的 Web 框架亦是一个精心设计的 Web MVC 框架，为开发者们在 Web 框架的选择上提供了一个除了主流框架比如 Struts 、过度设计的、不流行 Web 框架的以外的有力选项。
- 事务管理：Spring 提供了一个便捷的事务管理接口，适用于小型的本地事物处理（比如在单 DB 的环境下）和复杂的共同事物处理（比如利用 JTA 的复杂 DB 环境）。
- 异常处理：Spring 提供一个方便的 API ，将特定技术的异常(由JDBC, Hibernate, 或 JDO 抛出)转化为一致的、Unchecked 异常。
***
Q：Spring 框架中都用到了哪些设计模式？  
A：Spring 框架中使用到了大量的设计模式，下面列举了比较有代表性的：  
- 代理模式 — 在 AOP 和 remoting 中被用的比较多。
- 单例模式 — 在 Spring 配置文件中定义的 Bean **默认**为单例模式。
- 模板方法 — 用来解决代码重复的问题。比如 RestTemplate、JmsTemplate、JdbcTemplate 。
- 前端控制器 — Spring 提供了 DispatcherServlet 来对请求进行分发。
- 视图帮助(View Helper) — Spring 提供了一系列的 JSP 标签，高效宏来辅助将分散的代码整合在视图里。
- 依赖注入 — 贯穿于 BeanFactory / ApplicationContext 接口的核心理念。
- 工厂模式 — BeanFactory 用来创建对象的实例。
***

## Spring IoC

Q：什么是 Spring IoC 容器？  
A：Spring 框架的核心是 Spring IoC 容器。容器创建 Bean 对象，将它们装配在一起，配置它们并管理它们的完整生命周期。  

- Spring 容器使用**依赖注入**来管理组成应用程序的 Bean 对象。
- 容器通过读取提供的**配置元数据** Bean Definition 来接收对象进行实例化，配置和组装的指令。
- 该配置元数据 Bean Definition 可以通过 XML，Java 注解或 Java Config 代码提供。
***
Q：什么是依赖注入？  
A：在依赖注入（Dependency Injection）中，你**不必主动、手动创建对象**，但必须描述**如何创建**它们。  

- 你不是直接在代码中将组件和服务连接在一起，而是描述配置文件中哪些组件需要哪些服务。
- 然后，再由 IoC 容器将它们装配在一起。
***
Q：可以通过多少种方式完成依赖注入？  
A：有三种方式，在 Spring Framework 中，仅使用构造函数和 setter 注入这两种方式。     

- 接口注入 Interface Injection
- setter 注入 Setter Injection
- 构造函数注入 Setter Injection

实际场景中，setting 使用得较多。  

***
Q：Spring 中有多少种 IoC 容器？  
A：Spring 提供了两种 IoC 容器，分别是 `BeanFactory`、`ApplicationContext` 。  
**ApplicationContext 接口扩展了 BeanFactory 接口**，它在 BeanFactory 基础上提供了一些额外的功能。内置如下功能：  

- MessageSource ：管理 message ，实现国际化等功能。
- ApplicationEventPublisher ：事件发布。
- ResourcePatternResolver ：多资源加载。
- EnvironmentCapable ：系统 Environment（profile + Properties）相关。
- Lifecycle ：管理生命周期。
- Closable ：关闭，释放资源
- InitializingBean：自定义初始化。
- BeanNameAware：设置 beanName 的 Aware 接口。
BeanFactory 也被称为低级容器，而 ApplicationContext 被称为高级容器。  
***
Q：请介绍下常用的容器？  
A：常用容器如下：   

- BeanFactory 最常用的是 `XmlBeanFactory` ：根据 XML 文件中定义的内容，创建相应的 Bean。
- ApplicationContext 最常用的有：
  - `ClassPathXmlApplicationContext`：从 ClassPath 中的 XML 配置文件中读取上下文。
  - `FileSystemXmlApplicationContext`：有文件系统中的 XML 配置文件读取上下文。
  - `XmlWebApplicationContext`：由 Web 应用中的 XML文件读取上下文。
  - `ConfigServletWebServerApplicationContext`：StringBoot 主要使用这种。

***
Q：IoC 的一些好处？  
A：好处如下：  

- 它将最小化应用程序中的代码量。
- 它以最小的影响和最少的侵入机制促进松耦合。
- 它支持即时的实例化和延迟加载 Bean 对象。
- 它将使您的应用程序易于测试，因为它不需要单元测试用例中的任何单例或 JNDI 查找机制。
***
Q：简述 Spring IoC 的实现机制？  
A：简单来说，Spring 中的 IoC 的实现原理，就是工厂模式加反射机制。代码如下：  
```java
interface Fruit {

     public abstract void eat();
     
}
class Apple implements Fruit {

    public void eat(){
        System.out.println("Apple");
    }
    
}
class Orange implements Fruit {
    public void eat(){
        System.out.println("Orange");
    }
}

class Factory {

    public static Fruit getInstance(String className) {
        Fruit f = null;
        try {
            f = (Fruit) Class.forName(className).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return f;
    }
    
}

class Client {

    public static void main(String[] args) {
        Fruit f = Factory.getInstance("io.github.dunwu.spring.Apple");
        if(f != null){
            f.eat();
        }
    }
    
}
```
***
Q：Spring 框架中有哪些不同类型的事件？  
A：Spring 的 ApplicationContext 提供了支持事件和代码中监听器的功能。  
我们可以创建 Bean 用来监听在 ApplicationContext 中发布的事件。如果一个 Bean 实现了 ApplicationListener 接口，当一个ApplicationEvent 被发布以后，Bean 会自动被通知。示例代码如下：  
```java
public class AllApplicationEventListener implements ApplicationListener<ApplicationEvent> {  
    
    @Override  
    public void onApplicationEvent(ApplicationEvent applicationEvent) {  
        // process event  
    }
    
}
```
1. 上下文更新事件（ContextRefreshedEvent）：该事件会在ApplicationContext 被初始化或者更新时发布。也可以在调用ConfigurableApplicationContext 接口中的 #refresh() 方法时被触发。
2. 上下文开始事件（ContextStartedEvent）：当容器调用ConfigurableApplicationContext 的 #start() 方法开始/重新开始容器时触发该事件。
3. 上下文停止事件（ContextStoppedEvent）：当容器调用 ConfigurableApplicationContext 的 #stop() 方法停止容器时触发该事件。
4. 上下文关闭事件（ContextClosedEvent）：当ApplicationContext 被关闭时触发该事件。容器被关闭时，其管理的所有单例 Bean 都被销毁。
5. 请求处理事件（RequestHandledEvent）：在 We b应用中，当一个HTTP 请求（request）结束触发该事件。
***
Q：Spring支持自定义事件吗？  
A：支持，可以通过扩展 ApplicationEvent 类来开发自定义的事件。实现如下：  
1. 示例自定义的事件的类，代码如下：  
```java
public class CustomApplicationEvent extends ApplicationEvent{  

    public CustomApplicationEvent(Object source, final String msg) {  
        super(source);
    }  

}
```
2. 为了监听这个事件，还需要创建一个监听器。示例代码如下：
```java
public class CustomEventListener implements ApplicationListener<CustomApplicationEvent> {

    @Override  
    public void onApplicationEvent(CustomApplicationEvent applicationEvent) {  
        // handle event  
    }
    
}
```
3. 之后通过 ApplicationContext 接口的 `publishEvent(Object event)` 方法，来发布自定义事件。示例代码如下：
```java
// 创建 CustomApplicationEvent 事件
CustomApplicationEvent customEvent = new CustomApplicationEvent(applicationContext, "Test message");
// 发布事件
applicationContext.publishEvent(customEvent);
```
## Spring Bean

Q：什么是 Spring Bean ？  
A：定义如下：  
- Bean 由 Spring IoC 容器实例化，配置，装配和管理。
- Bean 是基于用户提供给 IoC 容器的配置元数据 Bean Definition 创建。
***
Q：Spring 有哪些配置方式？  
A：单纯从 Spring Framework 提供的方式，一共有三种：  
1. XML 配置文件。  
  Bean 所需的依赖项和服务在 XML 格式的配置文件中指定。这些配置文件通常包含许多 bean 定义和特定于应用程序的配置选项。它们通常以 bean 标签开头。例如：
```xml
<bean id="studentBean" class="org.edureka.firstSpring.StudentBean">
    <property name="name" value="Edureka"></property>
</bean>
```
2. 注解配置。  
  您可以通过在相关的类，方法或字段声明上使用注解，将 Bean 配置为组件类本身，而不是使用 XML 来描述 Bean 装配。默认情况下，Spring 容器中未打开注解装配。因此，您需要在使用它之前在 Spring 配置文件中启用它。例如：
```xml
<beans>
<context:annotation-config/>
<!-- bean definitions go here -->
</beans>
```
3. Java Config 配置。
  Spring 的 Java 配置是通过使用 `@Bean` 和 `@Configuration` 来实现。`@Bean` 注解扮演与 `<bean>` 元素相同的角色。`@Configuration` 类允许通过简单地调用同一个类中的其他 `@Bean` 方法来定义 Bean 间依赖关系。  
```java
@Configuration
public class StudentConfig {
    @Bean
    public StudentBean myStudent() {
        return new StudentBean();
    }
}
```
***
Q：Spring 支持几种 Bean Scope ？  
A：Spring Bean 支持 5 种 Scope ，实际常用的只有 Singleton 和 Prototype 两种级别。  

- Singleton - 每个 Spring IoC 容器仅有一个单 Bean 实例。**默认**
- Prototype - 每次请求都会产生一个新的实例。
- Request - 每一次 HTTP 请求都会产生一个新的 Bean 实例，并且该 Bean 仅在当前 HTTP 请求内有效。
- Session - 每一个的 Session 都会产生一个新的 Bean 实例，同时该 Bean 仅在当前 HTTP Session 内有效。
- Application - 每一个 Web Application 都会产生一个新的 Bean ，同时该 Bean 仅在当前 Web Application 内有效。
***
Q：Spring Bean 在容器的**生命周期**是什么样的？  
A：Spring Bean 的初始化流程如下：  

- 实例化 Bean 对象
  - Spring 容器根据配置中的 Bean Definition(定义)中实例化 Bean 对象。
  - Spring 使用依赖注入填充所有属性，如 Bean 中所定义的配置。
- Aware 相关的属性，注入到 Bean 对象
  - 如果 Bean 实现 BeanNameAware 接口，则工厂通过传递 Bean 的 beanName 来调用 `setBeanName(String name)`  方法。
  - 如果 Bean 实现 BeanFactoryAware 接口，工厂通过传递自身的实例来调用 `setBeanFactory(BeanFactory beanFactory)` 方法。
- 调用相应的方法，进一步初始化 Bean 对象
  - 如果存在与 Bean 关联的任何 BeanPostProcessor 们，则调用 `preProcessBeforeInitialization(Object bean, String beanName)` 方法。
  - 如果 Bean 实现 InitializingBean 接口，则会调用 `#afterPropertiesSet()` 方法。
  - 如果为 Bean 指定了 init 方法（例如 `<bean>`  的 init-method 属性），那么将调用该方法。
  - 如果存在与 Bean 关联的任何 BeanPostProcessor 们，则将调用 `postProcessAfterInitialization(Object bean, String beanName)` 方法。

![](http://static.iocoder.cn/images/Spring/2018-12-24/08.png)
***
Q：什么是 Spring 的内部 bean？   
A：只有将 Bean **仅用作**另一个 Bean 的属性时，才能将 Bean 声明为内部 Bean。  

- 为了定义 Bean，Spring 提供基于 XML 的配置元数据在 `<property>`或`<constructor-arg>` 中提供了 `<bean>`元素的使用。
- 内部 Bean 总是匿名的，并且它们总是作为原型 Prototype 。  
***
Q：什么是 Spring 装配？  
A：当 Bean 在 Spring 容器中组合在一起时，它被称为装配或 Bean 装配。Spring 容器需要知道需要什么 Bean 以及容器应该如何使用依赖注入来将 Bean 绑定在一起，同时装配 Bean 。   
***
Q：自动装配有哪些方式？
A：Spring 容器能够自动装配 Bean 。也就是说，可以通过检查 BeanFactory 的内容让 Spring 自动解析 Bean 的协作者。  
自动装配的不同模式：  
- no - 这是默认设置，表示没有自动装配。应使用显式 Bean 引用进行装配。
- byName - 它根据 Bean 的名称注入对象依赖项。它匹配并装配其属性与 XML 文件中由相同名称定义的 Bean 。
- 【**最常用**】byType - 它根据类型注入对象依赖项。如果属性的类型与 XML 文件中的一个 Bean 类型匹配，则匹配并装配属性。
- 构造函数 - 它通过调用类的构造函数来注入依赖项。它有大量的参数。
- autodetect - 首先容器尝试通过构造函数使用 autowire 装配，如果不能，则尝试通过 byType 自动装配。
***
Q：解释什么叫延迟加载？   
A：默认情况下，容器启动之后会将所有作用域为单例的 Bean 都创建好，但是有的业务场景我们并不需要它提前都创建好。此时，我们可以在Bean 中设置 `lzay-init = "true"` 。  

- 这样，当容器启动之后，作用域为单例的 Bean ，就不在创建。
- 而是在获得该 Bean 时，才真正在创建加载。
***
Q：Spring 框架中的单例 Bean 是线程安全的么？  
A：Spring 框架并没有对单例 Bean 进行任何多线程的封装处理。当然实际上，大部分的 Spring Bean 并没有可变的状态(比如Serview 类和 DAO 类)，所以相对线程安全。  
如果你的 Bean 有多种状态的话，就需要自行保证线程安全。最浅显的解决办法，就是将多态 Bean 的作用域( Scope )由 Singleton 变更为 Prototype 。  
***
Q：Spring 如何解决循环依赖？  
A：Spring 只解决 scope 为 singleton 的循环依赖。对于 scope 为 prototype 的 bean ，Spring 无法解决，直接抛出 BeanCurrentlyInCreationException 异常。对于构造器的循环依赖，Spring 也是无法解决的，只能抛出 BeanCurrentlyInCreationException 异常表示循环依赖，能解决基于 field 属性的循环依赖。  
- 首先 A 完成初始化第一步并将自己提前曝光出来（通过 ObjectFactory 将自己提前曝光），在初始化的时候，发现自己依赖对象 B，此时就会去尝试 get(B)，这个时候发现 B 还没有被创建出来。
- 然后 B 就走创建流程，在 B 初始化的时候，同样发现自己依赖 C，C 也没有被创建出来。
- 这个时候 C 又开始初始化进程，但是在初始化的过程中发现自己依赖 A，于是尝试 get(A)，这个时候由于 A 已经添加至缓存中（一般都是添加至三级缓存 singletonFactories ），通过 ObjectFactory 提前曝光，所以可以通过 `ObjectFactory.getObject()` 方法来拿到 A 对象，C 拿到 A 对象后顺利完成初始化，然后将自己添加到一级缓存中。
- 回到 B ，B 也可以拿到 C 对象，完成初始化，A 可以顺利拿到 B 完成初始化。到这里整个链路就已经完成了初始化过程了。
***
Q：什么是基于注解的容器配置？  
A：不使用 XML 来描述 Bean 装配，开发人员通过在相关的类，方法或字段声明上使用注解将配置移动到组件类本身。它可以作为 XML 设置的替代方案。Spring 的 Java 配置是通过使用 @Bean 和 @Configuration 来实现。
***
Q：如何在 Spring 中启动注解装配？  
A：默认情况下，Spring 容器中未打开注解装配。要使用基于注解装配，我们必须通过配置 `<context：annotation-config />` 元素在 Spring 配置文件中启用它。

## Spring AOP

Q：什么是 AOP ？  
A：AOP(Aspect-Oriented Programming)，即面向切面编程, 它与 OOP( Object-Oriented Programming, 面向对象编程) 相辅相成， 提供了与 OOP 不同的抽象软件结构的视角。  
- 在 OOP 中，以类( Class )作为基本单元
- 在 AOP 中，以切面( Aspect )作为基本单元。
***
Q：什么是 Aspect ？  
A：Aspect 由 PointCut 和 Advice 组成。它既包含了横切逻辑的定义，也包括了连接点的定义。
Spring AOP 就是负责实施切面的框架，它将切面所定义的横切逻辑编织到切面所指定的连接点中。
AOP 的工作重心在于如何将增强编织目标对象的连接点上, 这里包含两个工作:  
- 如何通过 PointCut 和 Advice 定位到特定的 JoinPoint 上。  
- 如何在 Advice 中编写切面代码。  
可以简单地认为, 使用 `@Aspect` 注解的类就是切面。  
***
Q：什么是 JoinPoint ?  
A：JoinPoint ，切点，程序运行中的一些时间点, 例如：  
- 一个方法的执行。  
- 或者是一个异常的处理。  
***
Q：什么是 PointCut ？  
A：简单来说，PointCut 是匹配 JoinPoint 的条件。Advice 是和特定的 PointCut 关联的，并且在 PointCut 相匹配的 JoinPoint 中执行。即 Advice => PointCut => JoinPoint 。  
在 Spring 中, 所有的方法都可以认为是 JoinPoint ，但是我们并不希望在所有的方法上都添加 Advice 。而 PointCut 的作用，就是提供一组规则(使用 AspectJ PointCut expression language 来描述) 来匹配 JoinPoint ，给满足规则的 JoinPoint 添加 Advice 。  
***
Q：关于 JoinPoint 和 PointCut 的区别？  
A：在 Spring AOP 中，**所有的方法执行都是 JoinPoint** 。而 **PointCut 是一个描述信息**，它修饰的是 JoinPoint ，通过 PointCut ，我们就可以确定哪些 JoinPoint 可以被织入 Advice 。  
Advice 是在 JoinPoint 上执行的，而 PointCut 规定了哪些 JoinPoint 可以执行哪些 Advice 。  
***
Q：什么是 Advice ？  
A：Advice ，通知。特定 JoinPoint 处的 Aspect 所采取的动作称为 Advice 。Spring AOP 使用一个 Advice 作为拦截器，在 JoinPoint “周围”维护一系列的拦截器。  
***
Q：有哪些类型的 Advice？  
A：有如下五种类型：  
- Before：这些类型的 Advice 在 JoinPoint 方法之前执行，并使用 `@Before` 注解标记进行配置。
- After Returning：这些类型的 Advice 在连接点方法正常执行后执行，并使用 `@AfterReturning` 注解标记进行配置。
- After Throwing：这些类型的 Advice 仅在 JoinPoint 方法通过抛出异常退出并使用 `@AfterThrowing` 注解标记配置时执行。
- After Finally：这些类型的 Advice 在连接点方法之后执行，无论方法退出是正常还是异常返回，并使用 `@After` 注解标记进行配置。
- Around：这些类型的 Advice 在连接点之前和之后执行，并使用 `@Around` 注解标记进行配置。
***
Q：什么是 Target ？  
A：Target ，织入 Advice 的目标对象。目标对象也被称为 Advised Object 。因为 Spring AOP 使用运行时代理的方式来实现 Aspect ，因此 Advised Object 总是一个代理对象(Proxied Object) 。  
注意, Advised Object 指的不是原来的对象，而是织入 Advice 后所产生的代理对象。  
***
Q：AOP 有哪些实现方式？  
A：实现 AOP 的技术，主要分为两大类：  
1. 静态代理：指使用 AOP 框架提供的命令进行编译，从而在编译阶段就可生成 AOP 代理类，因此也称为编译时增强。  
2. 动态代理 - 在运行时在内存中“临时”生成 AOP 动态代理类，因此也被称为运行时增强。目前 Spring 中使用了两种动态代理库：**JDK 动态代理 和 CGLIB**。
