##Spring Boot是什么

　　[Spring官方网站](http://spring.io/)本身使用Spring框架开发，随着功能以及业务逻辑的日益复杂，应用伴随着大量的XML配置文件以及复杂的Bean依赖关系。随着Spring 3.0的发布，Spring IO团队逐渐开始摆脱XML配置文件，并且在开发过程中大量使用“约定优先配置”（convention over configuration）的思想来摆脱Spring框架中各类繁复纷杂的配置（即时是Java Config）。

　　[Spring Boot](http://projects.spring.io/spring-boot/)正是在这样的一个背景下被抽象出来的开发框架，它本身并不提供Spring框架的核心特性以及扩展功能，只是用于快速、敏捷地开发新一代基于Spring框架的应用程序。也就是说，它并不是用来替代Spring的解决方案，而是和Spring框架紧密结合用于提升Spring开发者体验的工具。同时它集成了大量常用的第三方库配置（例如Jackson, JDBC, Mongo, Redis, Mail等等），Spring Boot应用中这些第三方库几乎可以零配置的开箱即用（out-of-the-box），大部分的Spring Boot应用都只需要非常少量的配置代码，开发者能够更加专注于业务逻辑。

　　**总的来说**

* 集中式配置（application.properties）+注解，大大简化了开发流程;
* 内嵌的Tomcat和Jetty容器，可直接打成jar包启动，无需提供Java war包以及繁琐的Web配置 ;
* 提供了Spring各个插件的基于Maven的pom模板配置，开箱即用，便利无比;
* 可以在任何你想自动化配置的地方，实现可能 ;
* 提供更多的企业级开发特性，如何系统监控，健康诊断，权限控制 ;
* 无冗余代码生成和XML强制配置;
* 提供支持强大的Restfult风格的编码，非常简洁

##Spring Boot注解
　　Spring最开始是为了解决EJB等大型企业框架对应用程序的侵入性，因此大量依靠配置文件来“非侵入式”得给POJO增加功能，然而，从Spring 3.x开始，Spring被外界最为诟病的一点就是配置繁多，号称“配置地狱”，各种xml文件，出了问题非常难排查。从Spring 4.x开始，Spring.io提供了三种方式编织Bean：

* 利用注解：隐式配置，例如：@Autowired、@Bean、@Component等，通过注解来简化xml文件。
* 利用Java文件：显示配置，比xml配置的优势是具备类型安全
* 利用传统的xml配置文件
####注解清单

1. @ResponseBody 
用该注解修饰的函数，会将结果直接填充到HTTP的响应体中，一般用于构建RESTful的api；

2. @Controller 
用于定义控制器类，在spring 项目中由控制器负责将用户发来的URL请求转发到对应的服务接口（service层）；

3. @RestController 
@ResponseBody和@Controller的合集；

4. @RequestMapping 
提供路由信息，负责URL到Controller中的具体函数的映射；

5. @EnableAutoConfiguration 
Spring Boot自动配置（auto-configuration）：尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库”。你可以将@EnableAutoConfiguration或者@SpringBootApplication注解添加到一个@Configuration类上来选择自动配置。如果发现应用了你不想要的特定自动配置类，你可以使用@EnableAutoConfiguration注解的排除属性来禁用它们。

6. @ComponentScan 
表示将该类自动发现（扫描）并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。

7. @Configuration 相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类——可以使用@ImportResource注解加载xml配置文件。

8. @SpringBootApplication 
相当于@EnableAutoConfiguration、@ComponentScan和@Configuration的合集；

9. @Import 
用来导入其他配置类；

10. @ImportResource 
用来加载xml配置文件；

11. @Autowired 
自动导入依赖的bean；

12. @Service 
一般用于修饰service层的组件；

13. @Repository 
使用@Repository注解可以确保DAO或者repositories提供异常转译，这个注解修饰的DAO或者repositories类会被ComponetScan发现并配置，同时也不需要为它们提供XML配置项。

###配置

　　Spring Boot最大的特色是“约定优先配置”，大量的默认配置对开发者十分的友好。但是在实际的应用开发过程中，默认配置不可能满足所有场景，同时用户也需要配置一些必须的配置项——例如数据库连接信息。Spring Boot的配置系统能够让开发者快速的覆盖默认约定，同时支持Properties配置文件和YAML配置文件两种格式，默认情况下Spring Boot加载类路径上的application.properties或application.yml文件，例如：

``` 
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

YAML格式更加简洁：

```
spring:
  datasource:
    url: jdbc:mysql://localhost/test
    username: dbuser
    password: dbpass
    driver-class: com.mysql.jdbc.Driver
```

一旦发现这些信息，Spring Boot就会根据它们创建DataSource对象。另一个常见的配置场景是Web应用服务器：

```
 Server settings (ServerProperties)
server:
  port: 8080
  address: 127.0.0.1
  sessionTimeout: 30
  contextPath: /
```

   Tomcat specifics
 ``` 
tomcat:
    accessLogEnabled: false
    protocolHeader: x-forwarded-proto
    remoteIpHeader: x-forwarded-for
    basedir:
    backgroundProcessorDelay: 30 # secs
```
　　通过port和address可以修改服务器监听的地址和端口，sessionTimeout配置session过期时间（再也不用修改web.xml了，因为它根本不存在）。同时如果在生产环境中使用内嵌Tomcat，当然希望能够配置它的日志、线程池等信息，这些现在都可以通过Spring Boot的属性文件配置，而不再需要再对生产环境中的Tomcat实例进行单独的配置管理了。
  
####参考资料
* [Spring Boot——开发新一代Spring Java应用](https://www.tianmaying.com/tutorial/spring-boot-overview)
* [Spring Boot中的注解](http://www.tuicool.com/articles/bQnMra)
