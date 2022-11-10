### 1、说说Spring里用了哪些设计模式?

`单例模式`：Spring 中的 Bean 默认情况下都是单例的。无需多说。

`工厂模式`：工厂模式主要是通过 BeanFactory 和 ApplicationContext 来生产 Bean 对象。

`代理模式`：最常见的 AOP 的实现方式就是通过代理来实现，Spring主要是使用 `JDK 动态代理`和 `CGLIB 代理`。

`模板方法模式`：主要是一些对数据库操作的类用到，比如 JdbcTemplate、JpaTemplate，因为查询数据库的建立连接、执行查询、关闭连接几个过程，非常适用于模板方法。



### 2、谈谈对IOC和AOP的理解？实现原理是什么？

`IOC` 叫做`控制反转`，指的是通过Spring来管理对象的**创建**、**配置**和**生命周期**，这样相当于把控制权交给了Spring，不需要人工来管理对象之间复杂的依赖关系，这样做的好处就是解耦。在Spring里面，主要提供了 [BeanFactory 和 ApplicationContext](https://blog.csdn.net/loli_kong/article/details/124809154) 两种 IOC 容器，通过他们来实现对 Bean 的管理。

`AOP` 叫做`面向切面编程`，他是一个编程范式，目的就是提高代码的模块性。Spring AOP 基于**动态代理**的方式实现，如果是实现了接口的话就会使用 JDK 动态代理，反之则使用 CGLIB 代理，Spring中 AOP 的应用主要体现在 `事务`、`日志`、`异常处理`等方面，通过在代码的前后做一些增强处理，可以实现对业务逻辑的隔离，提高代码的模块化能力，同时也是解耦。Spring主要提供了 **Aspect 切面**、**JoinPoint 连接点**、**PointCut 切入点**、**Advice 增强**等实现方式。



### 3、JDK动态代理和CGLIB代理有什么区别？

JDK 动态代理主要是针对类实现了某个接口，AOP 则会使用 JDK 动态代理。他基于反射的机制实现，生成一个实现同样接口的一个代理类，然后通过重写方法的方式，实现对代码的增强。

而如果某个类没有实现接口，AOP 则会使用 CGLIB 代理。他的底层原理是基于 asm 第三方框架，通过修改字节码生成成一个子类，然后重写父类的方法，实现对代码的增强。



### 4、FactoryBean 和 BeanFactory有什么区别？

BeanFactory 是 Bean 的工厂， ApplicationContext 的父类，IOC 容器的核心，负责生产和管理 Bean 对象。

FactoryBean 是 Bean，可以通过实现 FactoryBean 接口定制实例化 Bean 的逻辑，通过代理一个Bean对象，对方法前后做一些操作。



### 5、SpringBean的生命周期说说？

Spring Bean 生命周期简单概括为4个阶段：

1. 实例化，创建一个Bean对象

2. 填充属性，为属性赋值

3. 初始化

4. - 如果实现了`xxxAware`接口，通过不同类型的Aware接口拿到Spring容器的资源
   - 如果实现了BeanPostProcessor接口，则会回调该接口的`postProcessBeforeInitialzation`和`postProcessAfterInitialization`方法
   - 如果配置了`init-method`方法，则会执行`init-method`配置的方法

5. 销毁

6. - 容器关闭后，如果Bean实现了`DisposableBean`接口，则会回调该接口的`destroy`方法
   - 如果配置了`destroy-method`方法，则会执行`destroy-method`配置的方法

![](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/075f46d2-738b-434d-8806-8e7d7d2308c9_springbean的生命周期.jpg)

**spring bean 容器的生命周期流程如下：**

1. Spring 容器根据配置中的 bean 定义中实例化 bean
2. Spring 使用`依赖注入`填充所有属性，如 bean 中所定义的配置。
3. 如果 bean 实现 BeanNameAware 接口，则工厂通过传递 bean 的 ID 来调用 setBeanName()。
4. 如果 bean 实现 BeanFactoryAware 接口，工厂通过传递自身的实例来调用 setBeanFactory()。
5. 如果存在与 bean 关联的任何 BeanPostProcessors，则调用 preProcessBeforeInitialization() 方法。
6. 如果为 bean 指定了 init 方法（ <bean>的 init-method 属性），那么将调用它。
7. 最后，如果存在与 bean 关联的任何 BeanPostProcessors，则将调用 postProcessAfterInitialization() 方法。
8. 如果 bean 实现 DisposableBean 接口，当 spring 容器关闭时，会调用 destory()。
9. 如果为 bean 指定了 destroy 方法（ <bean>的 destroy-method 属性），那么将调用它。



### 6.Spring是怎么解决循环依赖的？

首先，Spring 解决循环依赖有两个前提条件：

1. 不全是构造器方式的循环依赖
2. 必须是单例

基于上面的问题，我们知道Bean的生命周期，本质上解决循环依赖的问题就是三级缓存，通过三级缓存提前拿到未初始化的对象。

第一级缓存：**用来保存实例化、初始化都完成的对象**

第二级缓存：**用来保存实例化完成，但是未初始化完成的对象**

第三级缓存：**用来保存一个对象工厂，提供一个匿名内部类，用于创建二级缓存中的对象**

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/62441f53-5d35-454d-809f-d2ded84df5c2_spring循环依赖01.jpg)

假设一个简单的循环依赖场景，A、B互相依赖。

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/8560eee3-078e-479a-8ed4-d927165aa335_spring循环依赖02.jpg)

A对象的创建过程：

1. 创建对象A，实例化的时候把A对象工厂放入三级缓存

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/11dd0b6d-441f-46e1-88f4-c26645b353ca_spring循环依赖03.jpg)

1. A注入属性时，发现依赖B，转而去实例化B
2. 同样创建对象B，注入属性时发现依赖A，依次从一级到三级缓存查询A，从三级缓存通过对象工厂拿到A，把A放入二级缓存，同时删除三级缓存中的A，此时，B已经实例化并且初始化完成，把B放入一级缓存。

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/d1c9dccf-3d25-482d-8112-848e8951dab1_spring循环依赖04.jpg)

1. 接着继续创建A，顺利从一级缓存拿到实例化且初始化完成的B对象，A对象创建也完成，删除二级缓存中的A，同时把A放入一级缓存
2. 最后，一级缓存中保存着实例化、初始化都完成的A、B对象

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/9f002320-3b8b-4d0e-818e-aa589b13921d_spring循环依赖05.jpg)

因此，由于把实例化和初始化的流程分开了，所以如果都是用构造器的话，就没法分离这个操作，所以都是构造器的话就无法解决循环依赖的问题了。



### 7、为什么要三级缓存？二级不行吗？

不可以，**主要是为了生成代理对象**。

因为三级缓存中放的是生成具体对象的**匿名内部类**，他可以生成代理对象，也可以是普通的实例对象。

使用三级缓存主要是**为了保证不管什么时候使用的都是一个对象**。

假设只有二级缓存的情况，往二级缓存中放的显示一个普通的Bean对象，`BeanPostProcessor`去生成代理对象之后，覆盖掉二级缓存中的普通Bean对象，那么多线程环境下可能取到的对象就不一致了。

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/b52074b6-f4e1-4d6e-8dc9-5a80bce03dcd_spring循环依赖06.jpg)

[spring为什么使用三级缓存而不是两级？](https://www.zhihu.com/question/445446018)



### 8、Spring事务传播机制有哪些？

1. **PROPAGATION_REQUIRED**：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，这也是通常我们的默认选择。
2. **PROPAGATION_REQUIRES_NEW**：创建新事务，无论当前存不存在事务，都创建新事务。
3. PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。
4. PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
5. PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。
6. PROPAGATION_MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。
7. PROPAGATION_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。‘



### 9、说说Spring Boot启动流程吧？

1. 准备环境，根据不同的环境创建不同的Environment
2. 准备、加载上下文，为不同的环境选择不同的Spring Context，然后加载资源，配置Bean
3. 初始化，这个阶段刷新Spring Context，启动应用
4. 最后结束流程





### 10、可以通过多少种方式完成依赖注入？

通常，依赖注入可以通过三种方式完成，即：

- 构造函数注入
- setter 注入
- 接口注入

在 Spring Framework 中，仅使用构造函数和 setter 注入。



### 11、区分构造函数注入和 setter 注入。

| 构造函数注入               | setter 注入                |
| :------------------------- | :------------------------- |
| 没有部分注入               | 有部分注入                 |
| 不会覆盖 setter 属性       | 会覆盖 setter 属性         |
| 任意修改都会创建一个新实例 | 任意修改不会创建一个新实例 |
| 适用于设置很多属性         | 适用于设置少量属性         |



### 12、spring 中有多少种 IOC 容器？

- BeanFactory - BeanFactory 就像一个包含 bean 集合的工厂类。它会在客户端要求时实例化 bean。
- ApplicationContext - ApplicationContext 接口扩展了 BeanFactory 接口。它在 BeanFactory 基础上提供了一些额外的功能。它会在启动时加载所有 bean。

**区分 BeanFactory 和 ApplicationContext。**

| BeanFactory                | ApplicationContext       |
| :------------------------- | :----------------------- |
| 它使用懒加载               | 它使用即时加载           |
| 它使用语法显式提供资源对象 | 它自己创建和管理资源对象 |
| 不支持国际化               | 支持国际化               |
| 不支持基于依赖的注解       | 支持基于依赖的注解       |



### 13、spring 提供了哪些配置方式?

- **基于 xml 配置**

bean 所需的依赖项和服务在 XML 格式的配置文件中指定。这些配置文件通常包含许多 bean 定义和特定于应用程序的配置选项。它们通常以 bean 标签开头。例如：

```xml
<bean id="studentbean" class="org.edureka.firstSpring.StudentBean"> 
    <property name="name" value="Edureka"></property>
</bean>
```

- **基于注解配置**

您可以通过在相关的类，方法或字段声明上使用注解，将 bean 配置为组件类本身，而不是使用 XML 来描述 bean 装配。默认情况下，Spring 容器中未打开注解装配。因此，您需要在使用它之前在 Spring 配置文件中启用它。例如：

```xml
<beans>
    <context:annotation-config/>
    <!-- bean definitions go here -->
</beans>
```

- **基于 Java API 配置**

Spring 的 Java 配置是通过使用 @Bean 和 @Configuration 来实现。

1. @Bean 注解扮演与 <bean /> 元素相同的角色。
2. @Configuration 类允许通过简单地调用同一个类中的其他 @Bean 方法来定义 bean 间依赖关系。

例如：

```java
@Configuration
public class StudentConfig {    
    @Bean    
    public StudentBean myStudent() {        
        return new StudentBean();    
    }
}
```



### 14、spring 支持集中 bean scope？

Spring bean 支持 5 种 scope：

- `Singleton`- 每个 Spring IoC 容器仅有一个单实例。
- `Prototype` - 每次请求都会产生一个新的实例。
- `Request` - 每一次 HTTP 请求都会产生一个新的实例，并且该 bean 仅在当前 HTTP 请求内有效。
- `Session` - 每一次 HTTP 请求都会产生一个新的 bean，同时该 bean 仅在当前 HTTP session 内有效。
- `Global-session` - 类似于标准的 HTTP Session 作用域，不过它仅仅在基于 portlet 的 web 应用中才有意义。Portlet 规范定义了全局 Session 的概念，它被所有构成某个 portlet web 应用的各种不同的 portlet 所共享。在 global session 作用域中定义的 bean 被限定于全局 portlet Session 的生命周期范围内。如果你在 web 中使用 global session 作用域来标识 bean，那么 web 会自动当成 session 类型来使用。

仅当用户使用支持 Web 的 ApplicationContext 时，最后三个才可用。



### 15、你用过哪些重要的 Spring 注解？

- @Controller - 用于 Spring MVC 项目中的控制器类。
- @Service - 用于服务类。
- @RequestMapping - 用于在控制器处理程序方法中配置 URI 映射。
- @ResponseBody - 用于发送 Object 作为响应，通常用于发送 XML 或 JSON 数据作为响应。
- @PathVariable - 用于将动态值从 URI 映射到处理程序方法参数。
- @Autowired - 用于在 spring bean 中自动装配依赖项。
- @Qualifier - 使用 @Autowired 注解，以避免在存在多个 bean 类型实例时出现混淆。
- @Scope - 用于配置 spring bean 的范围。
- @Configuration，@ComponentScan 和 @Bean - 用于基于 java 的配置。
- @Aspect，@Before，@After，@Around，@Pointcut - 用于切面编程（AOP）。



### 16、如何在 spring 中启动注解装配？

默认情况下，Spring 容器中未打开注解装配。因此，要使用基于注解装配，我们必须通过配置<context：annotation-config /> 元素在 Spring 配置文件中启用它。



### 17、@Resource和@Autowired注解

**共同点**

@Resource和@Autowired都可以作为注入属性的修饰，在接口仅有单一实现类时，两个注解的修饰效果相同，可以互相替换，不影响使用。

**不同点**

1、@Resource是JDK原生的注解，@Autowired是Spring2.5 引入的注解

2、@Resource有两个属性name和type。Spring将@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不指定name也不指定type属性，这时将通过反射机制使用byName自动注入策略。

@Autowired只根据type进行注入，不会去匹配name。如果涉及到type无法辨别注入对象时，那需要依赖@Qualifier或@Primary注解一起来修饰。



### 18、@Required 注解有什么用？

@Required 应用于 bean 属性 setter 方法。此注解仅指示必须在配置时使用 bean 定义中的显式属性值或使用自动装配填充受影响的 bean 属性。如果尚未填充受影响的 bean 属性，则容器将抛出 BeanInitializationException。

示例：

```java
public class Employee {    
    private String name;    
    @Required    
    public void setName(String name){        
        this.name=name;    
    }

    public string getName(){        
        return name;    
    }
}
```



### 19、spring DAO 有什么用？

Spring DAO 使得 JDBC，Hibernate 或 JDO 这样的数据访问技术更容易以一种统一的方式工作。这使得用户容易在持久性技术之间切换。它还允许您在编写代码时，无需考虑捕获每种技术不同的异常。



### 20、列举 spring 支持的事务管理类型

Spring 支持两种类型的事务管理：

1. `程序化事务管理`：在此过程中，在编程的帮助下管理事务。它为您提供极大的灵活性，但维护起来非常困难。
2. `声明式事务管理`：在此，事务管理与业务代码分离。仅使用注解或基于 XML 的配置来管理事务。



### 21、什么是 AOP？

`AOP`(Aspect-Oriented Programming), 即 面向切面编程, 它与 OOP( Object-Oriented Programming, 面向对象编程) 相辅相成, 提供了与 OOP 不同的抽象软件结构的视角.

在 OOP 中, 我们以类(class)作为我们的基本单元, 而 AOP 中的基本单元是 `Aspect(切面)`。

### 22、 AOP 中的 Aspect、Advice、Pointcut、JointPoint 和 Advice 参数分别是什么？

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/a027b690-1e3b-4f67-8c56-f4b58f9389d5_Spring-AOP-01.png)

1. `Aspect` - Aspect 是一个实现交叉问题的类，例如事务管理。方面可以是配置的普通类，然后在 Spring Bean 配置文件中配置，或者我们可以使用 Spring AspectJ 支持使用 @Aspect 注解将类声明为 Aspect。
2. `Advice` - Advice 是针对特定 JoinPoint 采取的操作。在编程方面，它们是在应用程序中达到具有匹配切入点的特定 JoinPoint 时执行的方法。您可以将 Advice 视为 Spring 拦截器（Interceptor）或 Servlet 过滤器（filter）。
3. `Advice Arguments` - 我们可以在 advice 方法中传递参数。我们可以在切入点中使用 args() 表达式来应用于与参数模式匹配的任何方法。如果我们使用它，那么我们需要在确定参数类型的 advice 方法中使用相同的名称。
4. `Pointcut` - Pointcut 是与 JoinPoint 匹配的正则表达式，用于确定是否需要执行 Advice。Pointcut 使用与 JoinPoint 匹配的不同类型的表达式。Spring 框架使用 AspectJ Pointcut 表达式语言来确定将应用通知方法的 JoinPoint。
5. `JoinPoint` - JoinPoint 是应用程序中的特定点，例如方法执行，异常处理，更改对象变量值等。在 Spring AOP 中，JoinPoint 始终是方法的执行器。

### 23、什么是通知（Advice）？

特定 JoinPoint 处的 Aspect 所采取的动作称为 Advice。Spring AOP 使用一个 Advice 作为拦截器，在 JoinPoint “周围”维护一系列的拦截器。

### 24、 有哪些类型的通知（Advice）？

- `Before` - 这些类型的 Advice 在 joinpoint 方法之前执行，并使用 @Before 注解标记进行配置。
- `After Returning` - 这些类型的 Advice 在连接点方法正常执行后执行，并使用@AfterReturning 注解标记进行配置。
- `After Throwing` - 这些类型的 Advice 仅在 joinpoint 方法通过抛出异常退出并使用 @AfterThrowing 注解标记配置时执行。
- `After (finally)` - 这些类型的 Advice 在连接点方法之后执行，无论方法退出是正常还是异常返回，并使用 @After 注解标记进行配置。
- `Around` - 这些类型的 Advice 在连接点之前和之后执行，并使用 @Around 注解标记进行配置。

### 25、指出在 spring aop 中 concern 和 cross-cutting concern 的不同之处。

concern 是我们想要在应用程序的特定模块中定义的行为。它可以定义为我们想要实现的功能。

cross-cutting concern 是一个适用于整个应用的行为，这会影响整个应用程序。例如，日志记录，安全性和数据传输是应用程序几乎每个模块都需要关注的问题，因此它们是跨领域的问题。

### 26、 AOP 有哪些实现方式？

实现 AOP 的技术，主要分为两大类：

- `静态代理` - 指使用 AOP 框架提供的命令进行编译，从而在编译阶段就可生成 AOP 代理类，因此也称为编译时增强；

  - `编译时编织`（特殊编译器实现）

  - `类加载时编织`（特殊的类加载器实现）。

- `动态代理` - 在运行时在内存中“临时”生成 AOP 动态代理类，因此也被称为运行时增强。

  - `JDK 动态代理`

  - `CGLIB`

### 27、 Spring AOP and AspectJ AOP 有什么区别？

Spring AOP 基于`动态代理`方式实现；AspectJ 基于`静态代理`方式实现。

Spring AOP 仅支持方法级别的 PointCut；提供了完全的 AOP 支持，它还支持属性级别的 PointCut。



### 28、如何理解 Spring 中的代理？

将 Advice 应用于目标对象后创建的对象称为代理。在客户端对象的情况下，目标对象和代理对象是相同的。

```java
Advice + Target Object = Proxy
```



### 29、什么是编织（Weaving）？

为了创建一个 advice 对象而链接一个 aspect 和其它应用类型或对象，称为编织（Weaving）。在 Spring AOP 中，编织在运行时执行。请参考下图：

![](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/a8bf91c4-14bd-4199-895a-d510169a83d3_编织.png)

