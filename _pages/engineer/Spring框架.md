---
date: 2023.08.13
---

> **题外话** 今天参加了一下米的笔试，好难，编程题一个也不会。切实感受了一下要想进大厂，leetcode刷题强度得跟上。
~~话说米算大厂吗？（狗头）~~

这两天需要用spring框架完善网关的控制台接口，就进一步熟悉了一下spring框架的内容。以下观点为《Spring实战（第四版&第五版）》结合官网文档提取出来的一些看法。
# Spring 与 Spring boot
Spring框架是为了解决Java企业级开发过于臃肿而提出的“轻量级”框架，核心思想为依赖注入（DI）和面向切面编程（AOP）。而spring boot是对spring本身的一次革命，从spring的视角对开发环节进行进一步简化，指导思想就是基于约定的自动配置。

从定义上讲，Spring boot是Spring框架的一部分，可以用来快速构建Spring应用，因为大多数配置都可以使用默认的约定配置。自定义配置也非常简单，只需要在yml文件中指定对应组件的参数即可。相比起手动定义Config类或xml文件来讲，在yml文件中提供配置是非常轻松的选择。
# Spring框架
开始真正使用Spring框架构建应用之前，还是得理解Spring的核心思想。
## DI
依赖注入（**Dependency Injection**）相比传统的构造类的方式来说减轻了类与其成员的耦合程度，类本身不需要负责成员的构造（实例化）只需描述类与类之间的依赖关系，由spring来进行依赖的注入。具体来说，任意一个POJO都可以是spring注入的组件（*这也正是Spring的强大之处*），定义依赖注入的方式只需要在@Config注解标注的类中指明各个@Bean组件的依赖关系。同样地，如果偏好使用xml文件进行定义，也需要在xml文件中定义bean和config。

配置完成之后，spring会生成容器存放这些bean，想要取用其中类时仅需加载配置好的spring上下文（**context**），并指定想要获得的bean即可（使用`getBean()`方法）。

spring依赖注入更强大之处在于可以不需要遵循上述显式注入的规则，而进行**隐式注入**。隐式注入需要使用@Component注解标注相应的组件，然后在配置类中指定需要扫描的包@ComponentScan就可以进行自动装配，而无需额外的@Config类或者xml文件。若在定义组件的过程中需要用到其他已经定义的bean，可以使用@Autowired让spring帮你获取依赖、自动装配。

## AOP
面向切面编程通俗地讲就是把一个应用划分为若干独立的切面，如安全、事务、并发、记录、业务，而每一个切面只需要关注自己的逻辑即可，实现应用组件最大程度的解耦，消除冗长的样板式代码。最终将不同的切面通过注解的方式进行组合，形成一个功能完整的类。作为使用者，目前在享受AOP带来的便捷，尚未自己定义切面进行编程。

*感觉上类的继承可以实现类似的效果，试想如果在C++中，可以定义类的构造函数和析构函数实现类似注解定义的自动化的操作*

# Spring应用
从现在的视角看，spring boot就是搭建一个spring应用的最佳实践，通过自动配置的方式快速构建起完整的spring应用。默认生成一个@SpringBootApplication启动类，这个类注解是三个注解的结合体：
- SpringBootConfiguration
- EnableAutoConfiguration
- ComponentScan

基于spring boot的自动配置，用Spring mvc构建一个web应用是非常轻松的
1. 需要定义@Controller类负责处理客户端请求
2. 需要通过模板（JSP/Thymeleaf）生成页面返回（传统）
3. 需要@Repository类管理持久化层的数据（基于JDBC）。

当然，Spring MVC支持的编写网页的方式远比上述灵活，有两个常用的拓展
- REST API：现在浏览器客户端能力已经远远超过渲染HTML的DOM tree，完全可以运行的复杂Javascript程序，如Vue、Angular、React等框架构成的js程序，这就允许服务器端不返回完整的web页面，而仅仅从服务器接受必要的数据（如json、xml），进一步通过执行Javascript生成html页面。因此服务器可以仅仅提供REST API，返回DO的json字符串即可。要做到这一点的简单做法就是为类加上@RestRequest注解，作用与将控制器@Controller方法的返回值类型定义为ResponseBody相同。
- JPA：持久化层不仅可以基于JDBC控制数据库操作，还可以用实现了java持久化接口API框架操作符合接口规范的数据。*此外，现在还非常流行[MyBatis](https://mybatis.org/mybatis-3/zh/index.html)持久化框架*。

以上是心得分享的全部内容，希望对你有帮助，欢迎邮件讨论。
---
