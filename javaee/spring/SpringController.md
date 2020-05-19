# Spring Controller


Spring Boot 内集成了 TomCat 服务器，通过控制层接收浏览器的 URL 请求进行操作并返回数据。

底层和浏览器的信息交互仍旧由 servlet 完成。

---

## 异常处理类

- `@ControllerAdvice`: 标注当前类为所有Controller类服务
- `@ExceptionHandler`: 标注当前方法处理异常（默认处理 RuntimeException ）
  `@ExceptionHandler(value = Exception.class)`: 处理所有异常
- `@ResponseBody`:将Controller类返回数据(通常为map)转码添加到response中(默认为json形式)


---


## Controller 配置

开发者可以通过以下两种方式配置 Controller 层：

1. 继承 WebMvcConfigurerAdapter 类（已过时）：使用默认配置，可重写以下方法。

2. 实现 WebMvcConfigurer 接口 / 继承 WebMvcConfigurationSupport 类：丢弃默认设置，开发者对以下方法全部重写。


```java
/** 解决跨域问题 **/
public void addCorsMappings(CorsRegistry registry) ;
/** 添加拦截器 **/
void addInterceptors(InterceptorRegistry registry);
/** 配置视图解析器 **/
void configureViewResolvers(ViewResolverRegistry registry);
/** 配置内容裁决选项 **/
void configureContentNegotiation(ContentNegotiationConfigurer configurer);
/** 视图跳转控制器 **/
void addViewControllers(ViewControllerRegistry registry);
/** 静态资源处理 **/
void addResourceHandlers(ResourceHandlerRegistry registry);
/** 默认静态资源处理器 **/
void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer);
```


### 跨域问题

配置如何处理跨域请求，否则返回数据会被浏览器拦截。

```java
    public void addCorsMappings(CorsRegistry registry) {
        //添加映射路径
        registry.addMapping("/**")
                //放行哪些原始域
                .allowedOrigins("*")
                //是否发送Cookie信息
                .allowCredentials(true)
                //放行哪些原始域(请求方式)
                .allowedMethods("GET","POST", "PUT", "DELETE")
                //放行哪些原始域(头部信息)
                .allowedHeaders("*")
                //暴露哪些头部信息（因为跨域访问默认不能获取全部头部信息）
                .exposedHeaders("Header1", "Header2");
    }
```


### 拦截器

- **拦截器(Interceptor)**

Java Web 中，在执行 Controller 方法前后对 Controller 请求进行拦截和处理。

依赖于 web 框架，在Spring 配置。在实现上基于 Java 的反射机制。

- **过滤器(Filter)**

Java Web中，在 request/response 传入 Servlet 前，过滤信息或设置参数。

依赖于 servlet 容器，在 web.xml 配置。在实现上基于函数回调。

**两者常用于修改字符编码、删除无用参数、登录校验等。Spring 框架中优先使用拦截器：功能接近、使用更加灵活。**


一个*：只匹配字符，不匹配路径（/）
两个**：匹配字符，和路径（/）

https://www.cnblogs.com/kangkaii/p/9023751.html


```java
@Configuration
public class WebSecurityConfiguration extends WebMvcConfigurerAdapter {


        //路径映射，已在Controller中配置
        /*@Override
        public void addViewControllers(ViewControllerRegistry registry) {
            registry.addViewController("/").setViewName("login");
            registry.addViewController("/index.html").setViewName("login");
            registry.addViewController("/main.html").setViewName("success");
            registry.addResourceHandler("/webjars/**") .addResourceLocations("classpath:/META-INF/resources/webjars/");
        }*/


        // Session key
        public final static String SESSION_KEY = "user";

        //装载拦截器
        @Bean
        public SecurityInterceptor getSecurityInterceptor() {
            return new SecurityInterceptor();
        }

        // 配置拦截器
        public void addInterceptors(InterceptorRegistry registry) {
            InterceptorRegistration addInterceptor = registry.addInterceptor(getSecurityInterceptor());
            // 排除配置
            addInterceptor.excludePathPatterns("/error","/login","/user/login");
            addInterceptor.excludePathPatterns("/asserts/**");
            addInterceptor.excludePathPatterns("/webjars/**");
            addInterceptor.excludePathPatterns("/public/**");
            // 拦截配置
            addInterceptor.addPathPatterns("/**");
        }

        // 定义拦截器
        private class SecurityInterceptor extends HandlerInterceptorAdapter {
            @Override
            public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
                    throws Exception {
                HttpSession session = request.getSession();
                if (session.getAttribute(SESSION_KEY) != null) return true;
                // 跳转登录页面（重定向）
                request.setAttribute("message","登录失败，请先输入用户名和密码。");
                request.getRequestDispatcher("login").forward(request,response);
                return false;
            }
        }
    }
```

---

## Controller 实现

Controller 类需要使用 `@RestController` 或 `@Controller` 注解标注。

- `@Controller`：类中所有方法以 String 形式返回 classpath 路径下同名 html 页面。适用于 JSP/thymeleaf 等动态加载页面。

- `@RestController`：类中所有方法以 Map/List 等形式返回 JSON 数据。适用于前后端分离开发。

*`@Controller` 类中标注 `@ResponseBody` 的方法，可以起到和 `@RestController` 类相同的效果。*

### 请求映射

Controller 类中的方法使用 `@RequestMapping` 注解标注，就可以将指定 URL 请求映射到方法上处理。

```java
@RequestMapping(value = "/hello", method = RequestMethod.GET)     // 参数为 URL 路径和请求方式
@RequestMapping("/hello")                                         // 默认接收所有请求方式

@GetMapping("/hello")                                             // 简写形式的 GET 请求
@PostMapping("/hello")                                            // 简写形式的 POST 请求

// 灵活映射
@RequestMapping("/?/hello")                                       // ? 匹配单字符
@RequestMapping("/*/hello")`：                                    // * 匹配任意数量字符
@RequestMapping("/**/hello")：                                    // ** 匹配任意数量目录

@RequestMapping("/{ID}/hello")`                                   // {} 读取 URL 路径中的参数
```

Controller 类也可以通过 `@RequestMapping` 注解标注，表示路径下的 URL 请求在该类中寻找方法。

```java
@Controller
@RequestMapping("/speak")
public class SpeakController{
    @GetMapping("/hello")
    public String hello(){ return "hello"; } 
}
```


### 参数接收

对于请求 `/test?username=mrjoker&password=123456` ，Cotroller 方法有以下几种方式接收参数。

1. 直接获取参数

```java
@RequestMapping("/test")
public String test(String username, String password){
    return username + password;
}
```

2. 通过 HttpServletRequest 类来获取参数

```java
@RequestMapping("/test")
public String test(HttpServletRequest request){
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    return username + password;
}
```

3. 通过自定义对象来获取参数

```java
@RequestMapping("/test")
public String test(User user){
    String username = user.getUsername();
    String password = user.getPassword();
    return username + password;
}
```

4. 通过 RequestParam 注解来获取参数（实参值赋给形参）

```java
@RequestMapping("/test")
public String test(@RequestParam("username") String s1, @RequestParam("password") String s2){
    return s1 + s2;
}
```

5. 通过 PathVariable 注解来动态获取参数，对于请求 /test/mrjoker/123456

```java
@RequestMapping("/test/{username}/{password}")
public String test(@PathVariable("username") String s1, @PathVariable("password") String s2){
    return s1 + s2;
}
```

6. 通过 ModelAttribute 注解来获取其他方法返回值作为参数


https://blog.csdn.net/a532672728/article/details/78057218 传递参数

### 请求参数

GET 请求请求参数直接附着在 URL 中。而 POST 请求请求参数放置在请求体中，其请求参数有以下两种格式：

**Form Data 格式**
  
请求的 Content-Type 为 application/x-www-form-urlencoded

示例：`username=mrjoker&password=123456`

**Request Payload 格式**
  
请求的 Content-Type 为 application/json 或者 multipart/form-data

示例：`{"username":"mrjoker", "password":"123456"}`


AJAX 提交 POST 请求默认使用 Form Data 格式，Spring MVC 会自动解析到对应的 bean 中并获取参数。但对于 Request Payload 请求，则必须进行处理：


1. 前端解决方案 axios 库可以使用 qs 库将 json 对象转化为 Form Data 格式。
2. 后端解决方案 Spring Boot 在请求参数上加 `@RequestBody` 注解，将请求正文解析到对应的 bean 中获取参数。

一个请求可以有多个 RequestParam，但只能有一个 RequestBody。如果 URL 内含有参数，两者也可以同时使用。

@RequestBody 可以直接以 String 接收前端传过来的 json 数据，也可以用对象自动解析前端传过来的 json 数据。对象里定义 List 属性，可用来接收多条 json 数据。

axios 中的 params与 data 传参的区别: params 传参，参数以 k=v&k=v 格式放置在 url 中传递。data传参，参数会收到Request Header中的 Content-Type 类型的影响 data 的参数会在 form表单中。


https://www.cnblogs.com/dw039/p/11104628.html


### 局部跨域

在方法上（@RequestMapping）或者在控制器（@Controller）上使用注解 @CrossOrigin，可以实现局部跨域.  

```java
    @RequestMapping("/hello")
    @ResponseBody
    @CrossOrigin("http://localhost:8080") 
    public String index( ){
        return "Hello World";
    }
```

使用 HttpServletResponse 对象添加响应头实现局部跨域。

```java
    @RequestMapping("/hello")
    @ResponseBody
    public String index(HttpServletResponse response){
        response.addHeader("Access-Control-Allow-Origin", "http://localhost:8080");
        // response.addHeader("Access-Control-Allow-Origin", "*");     全部放行
        return "Hello World";
    }
```
  

```java
//PersonController.java（web文件夹内）

@RestController
@RequestMapping("/superadmin")
public class PersonController {
    @Autowired
    private PersonService PersonService;

    /**
     * 获取所有信息
     *
     * @return
     */
    @RequestMapping(value = "/listPerson", method = RequestMethod.GET)
    private Map<String, Object> listPerson() {
        Map<String, Object> modelMap = new HashMap<String, Object>();
        List<Person> list = new ArrayList<Person>();
        // 获取列表
        list = PersonService.getPersonList();
        modelMap.put("PersonList", list);
        return modelMap;
    }

    /**
     * 通过ID获取信息
     *
     * @return
     */
    @RequestMapping(value = "/getPersonbyid", method = RequestMethod.GET)
    private Map<String, Object> getPersonById(Integer PersonId) {
        Map<String, Object> modelMap = new HashMap<String, Object>();
        // 获取信息
        Person Person = PersonService.getPersonById(PersonId);
        modelMap.put("Person", Person);
        return modelMap;
    }

    /**
     * 添加信息
     *
     * @param PersonStr
     * @param request
     * @return
     * @throws IOException
     * @throws JsonMappingException
     * @throws JsonParseException
     */
    @RequestMapping(value = "/addPerson", method = RequestMethod.POST)
    private Map<String, Object> addPerson(@RequestBody Person Person)
            throws JsonParseException, JsonMappingException, IOException {
        Map<String, Object> modelMap = new HashMap<String, Object>();
        // 添加区域信息
        modelMap.put("success", PersonService.addPerson(Person));
        return modelMap;
    }

    /**
     * 修改信息，主要修改名字
     *
     * @param PersonStr
     * @param request
     * @return
     * @throws IOException
     * @throws JsonMappingException
     * @throws JsonParseException
     */
    @RequestMapping(value = "/modifyPerson", method = RequestMethod.POST)
    private Map<String, Object> modifyPerson(@RequestBody Person Person)
            throws JsonParseException, JsonMappingException, IOException {
        Map<String, Object> modelMap = new HashMap<String, Object>();
        // 修改信息
        modelMap.put("success", PersonService.modifyPerson(Person));
        return modelMap;
    }

    @RequestMapping(value = "/removePerson", method = RequestMethod.GET)
    private Map<String, Object> removePerson(Integer PersonId) {
        Map<String, Object> modelMap = new HashMap<String, Object>();
        // 修改信息
        modelMap.put("success", PersonService.deletePerson(PersonId));
        return modelMap;
    }

}
```







#### 请求转发和重定向

1.请求转发（forward）:

当客户端（浏览器）向远程服务器发送一个URL请求后，服务器接收到请求后，会在服务器内部直接通过另外的一个URL获取资源并将此资源再响应给浏览器，请求转发整个过程是一次性的。
 
浏览器的URL地址仍然是原来的URL.
 
2.重定向（Redirect）:
    当客户端（浏览器）向服务器发送一个URL请求后，服务器会告诉浏览器，资源在另外一个URL地址上，此时浏览器会重新发送请求到新的资源地址。重定向发送了两次URL请求。
 
浏览器上面的URL已经换位了新的资源请求地址。

https://www.cnblogs.com/javaxiaobu/p/11149151.html

---

### Spring Boot配置HTTPS

####生成SSL证书

https://www.cnblogs.com/benwu/articles/4891758.html

JDK提供证书管理工具: JDK\bin\keytool.exe 

<font size =2 color = green>Tomcat使用Java提供的密码库，通过Java的Keytool工具生成JKS等格式的证书文件。
Apache使用OpenSSL提供的密码库，生成PEM、KEY、CRT等格式的证书文件。</font>

**cmd命令(JDK\bin目录打开)**

密钥库: 保存密钥和对应的证书。【证书只含有公钥】

<font size =2 color = blond>genkeypair 生成密钥对（非对称加密算法）
genseckey 生成密钥（对称加密算法）
</font>
创建名为tomcat的密钥对以及自签名的证书，放入mykeystore密钥库中（不存在则创建）
`keytool -genkeypair -alias "tomcat" -keyalg "RSA" -validity 180 -keypass "123456" -keystore "D:\mykeystore.keystore" -storetype PKCS12 -storepass `



- alias 证书别名
- keyalg 加密算法，生成密钥对默认RSA
- keysize 密钥键长，RSA默认2048
- validity 证书有效期，默认90
- keypass 证书密码
- keystore 密钥库路径，默认创建在用户目录下
- storetype 密钥库类型，默认JKS
- storepass 密钥库密码

查看密钥库
`keytool -list -v -alias tomcat -keystore "D:\mykeystore.keystore" -storepass 123456 `

将生成的证书文件拷贝到项目resources目录下


####修改全局配置文件

application.properties格式

```properties
server.port = 8443
server.ssl.key-store = classpath:mykeystore.keystore
server.ssl.key-store-password = 123456
server.ssl.keyStoreType = PKCS12
server.ssl.keyAlias = tomcat
```

设置SSL后，默认使用HTTPS协议。访问localhost:8443，会出现证书安全提示，强行进入即可。
<font size =2 color = brown>【未付费注册，不被数字认证机构CA认可：会被浏览器标记为不安全】</font>

如果将服务器端口号设置成443端口,即https的默认访问端口,那么在进行https访问的时候可以不带端口号直接访问。

**修改入口文件**

如果访问http://localhost:8443，则提示需要TLS。

》》将http连接自动转换为https连接

```java
@Configuration
public class TestSslApplication {
    //servlet容器，自己写的bean会覆盖自动配置的bean？
    @Bean
    public ServletWebServerFactory servletContainer() {
        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
        tomcat.addAdditionalTomcatConnectors(createStandardConnector());
        return tomcat;
    }

　　// 新建connecter监听80端口，并重定向至8443
    private Connector createStandardConnector() {
        Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
        connector.setScheme("http");
        connector.setPort(80);
        connector.setSecure(false);
        connector.setRedirectPort(8443);
        return connector;
    }

}

```


重新配置Servlet容器(Tomcat)

自动配置类ServletWebServerFactoryAutoConfiguration会读取bean

https://www.jianshu.com/p/017a7f40efff

**衍生：Spring Boot是如何启动Tomcat的？**

tomcat最顶层容器是Server，代表着整个服务器，一个Server包含多个Service。Service主要包括多个Connector和一个Container。Connector用来处理连接相关的事情，并提供Socket到Request和Response相关转化。Container用于封装和管理Servlet，以及处理具体的Request请求。

---

main方法： 实例化SpringApplication，执行run方法

run方法：  
    配置属性、获取监听器，初始化输入参数、配置环境，输出banner
    创建上下文、预处理上下文、刷新上下文、再刷新上下文：context

refreshApplicationContext方法：通过ServletWebServerFactory接口定义了getwebServer方法，通过其创建webServer并返回（创建时做了两件重要的事情：把Connector对象添加到tomcat中，配置引擎）【TomcatServletWebServerFactory是接口其中一个实现类】

TomcatwebServer类中，规定了Tomcat服务器的启动和关闭方法。


Spring Boot启动过程主要做了以下几件事情：

在SpringBoot中启动tomcat的工作在刷新上下文这一步

而tomcat的启动主要是实例化两个组件：Connector、Container
