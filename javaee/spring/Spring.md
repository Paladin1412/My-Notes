# Spring


## Spring

Spring 框架为开发 Java 应用程序提供了全面的基础架构支持。它包含一些很好的功能，如依赖注入和开箱即用的模块。

，Spring 就像一个大家族，有众多衍生产品例如 Boot，Security，JPA等等。但他们的基础都是Spring 的 IOC 和 AOP，IOC提供了依赖注入的容器，而AOP解决了面向切面的编程，然后在此两者的基础上实现了其他衍生产品的高级功能



Spring需要定义调度程序servlet，映射和其他支持配置。我们可以使用 web.xml 文件或Initializer类来完成此操作：
```java
public class MyWebAppInitializer implements WebApplicationInitializer {
  
    @Override
    public void onStartup(ServletContext container) {
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.setConfigLocation("com.pingfangushi");
          container.addListener(new ContextLoaderListener(context));
          ServletRegistration.Dynamic dispatcher = container
          .addServlet("dispatcher", new DispatcherServlet(context));
        dispatcher.setLoadOnStartup(1);
        dispatcher.addMapping("/");
    }
}
```
还需要将@EnableWebMvc注释添加到@Configuration类，并定义一个视图解析器来解析从控制器返回的视图：

```java
@EnableWebMvc
@Configuration
public class ClientWebConfig implements WebMvcConfigurer { 
   @Bean
   public ViewResolver viewResolver() {
      InternalResourceViewResolver bean
        = new InternalResourceViewResolver();
      bean.setViewClass(JstlView.class);
      bean.setPrefix("/WEB-INF/view/");
      bean.setSuffix(".jsp");
      return bean;
   }
}
```



## SpringBoot

Spring 的配置非常复杂，各种xml，properties处理起来比较繁琐。于是为了简化开发者的使用，Spring社区创造性地推出了Spring Boot，它遵循约定优于配置，极大降低了Spring使用门槛，但又不失Spring原本灵活强大的功能



Spring Boot 是 Spring 的扩展，一个轻量级，简化配置和开发流程的 web 整合框架。开发，测试和部署更加方便。

1. 不需要部署服务器，内嵌了如 Tomcat，Jetty 和 Undertow 这样的容器。
2. 不需要配置 XML 文件，将全部配置浓缩在一个 appliaction.yml 配置文件中。
3. 简化 Maven 配置。提供 POM 整合了 Spring 依赖项，引入核心依赖时会自引入其他依赖。
4. 提供了一些第三方功能。





在Spring Boot中，你会发现你引入的所有包都是starter形式，如：

spring-boot-starter-web-services，针对SOAP Web Services
spring-boot-starter-web，针对Web应用与网络接口
spring-boot-starter-jdbc，针对JDBC
spring-boot-starter-data-jpa，基于hibernate的持久层框架
spring-boot-starter-cache，针对缓存支持



