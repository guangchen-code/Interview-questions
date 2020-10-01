# SSM 面试专题

## SpringMVC

### 1、什么是 SpringMVC

答：SpringMVC 是 Spring 的一个模块，基于 MVC 的一个框架，无需中间整合层来整合。

### 2、Spring MVC 的优点

答：

01. 它是基于组件技术的. 全部的应用对象, 无论控制器和视图, 还是业务对象之类的都是 Java 组件. 并且和 Spring 提供的其他基础结构紧密集成
02. 不依赖于 Servlet API(目标虽是如此, 但是在实现的时候确实是依赖于 Servlet 的)
03. 可以任意使用各种视图技术, 而不仅仅局限于 JSP
04. 支持各种请求资源的映射策略
05. 它应是易于扩展的

### 3、SpringMVC 工作原理

答：

01. 客户端发送请求到 DispatcherServlet
02. DispatcherServlet 查询 handlerMapping 找到处理请求的 Controller
03. Controller 调用业务逻辑后，返回 ModelAndView
04. DispatcherServlet 查询 ModelAndView，找到指定视图
05. 视图将结果返回到客户端

### 4、SpringMVC 流程

答：

01. 用户发送请求至前端控制器 DispatcherServlet。
02. DispatcherServlet 收到请求调用 HandlerMapping 处理器映射器。
03. 处理器映射器找到具体的处理器 (可以根据 xml 配置、注解进行查找)，生成处理器对象及处理器拦截器 (如果有则生成) 一并返回给 DispatcherServlet。
04. DispatcherServlet 调用 HandlerAdapter 处理器适配器。
05. HandlerAdapter 经过适配调用具体的处理器 (Controller，也叫后端控制器)。
06. Controller 执行完成返回 ModelAndView。
07. HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet。
08. DispatcherServlet 将 ModelAndView 传给 ViewReslover 视图解析器。
09. ViewReslover 解析后返回具体 View。
10. DispatcherServlet 根据 View 进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet 响应用户。

### 6、SpringMVC 的控制器是不是单例模式, 如果是, 有什么问题, 怎么解决

答：是单例模式, 所以在多线程访问的时候有线程安全问题, 不要用同步, 会影响性能的, 解决方案是在控制器里面不能写字段。

### 7、如果你也用过 Struts2 简单介绍下 SpringMVC 和 Struts2 的区别有哪些

答：

01. SpringMVC 的入口是一个 Servlet 即前端控制器，而 Struts2 入口是一个 Filter 过虑器。
02. SpringMVC 是基于方法开发 (一个 url 对应一个方法)，请求参数传递到方法的形参，可以设计为单例或多例 (建议单例)，Struts2 是基于类开发，传递参数是通过类的属性，只能设计为多例。
03. Struts2 采用值栈存储请求和响应的数据，通过 OGNL 存取数据，SpringMVC 通过参数解析器是将 request 请求内容解析，并给方法形参赋值，将数据和视图封装成 ModelAndView 对象，最后又将 ModelAndView 中的模型数据通过 request 域传输到页面。JSP 视图解析器默认使用 JSTL。

### 8、SpingMVC 中的控制器的注解一般用那个, 有没有别的注解可以替代

答：一般用 @Conntroller 注解, 表示是表现层, 不能用用别的注解代替。

### 9、 @RequestMapping 注解用在类上面有什么作用

答：是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

### 10、怎么样把某个请求映射到特定的方法上面、

答：直接在方法上面加上注解 @RequestMapping, 并且在这个注解里面写上要拦截的路径。

### 11、如果在拦截请求中, 我想拦截 get 方式提交的方法, 怎么配置

答：可以在 @RequestMapping 注解里面加上 method=RequestMethod. GET

### 12、怎么样在方法里面得到 Request, 或者 Session

答：直接在方法的形参中声明 request, SpringMVC 就自动把 request 对象传入。

### 13、我想在拦截的方法里面得到从前台传入的参数, 怎么得到

答：直接在形参里面声明这个参数就可以, 但必须名字和传过来的参数一样。

### 14、如果前台有很多个参数传入, 并且这些参数都是一个对象的, 那么怎么样快速得到这个对象

答：直接在方法中声明这个对象, SpringMVC 就自动会把属性赋值到这个对象里面。

### 15、SpringMVC 中函数的返回值是什么

答：返回值可以有很多类型, 有 String, ModelAndView, 当一般用 String 比较好。

### 16、SpringMVC 怎么样设定重定向和转发的

答：

在返回值前面加 "forward:" 就可以让结果转发, 譬如 `forward:user.do?name=method4`
在返回值前面加 "redirect:" 就可以让返回值重定向, 譬如 `redirect:http://www.baidu.com`

### 17、SpringMVC 用什么对象从后台向前台传递数据的

答：通过 ModelMap 对象, 可以在这个对象里面用 put 方法, 把对象加到里面, 前台就可以通过 EL 表达式拿到。

### 18、SpringMVC 中有个类把视图和数据都合并的一起的, 叫什么

答：叫 ModelAndView。

### 19、怎么样把 ModelMap 里面的数据放入 Session 里面

答：可以在类上面加上 @SessionAttributes 注解, 里面包含的字符串就是要放入 session 里面的 key

### 20、SpringMVC 怎么和 AJAX 相互调用的

答：

通过 Jackson 框架就可以把 Java 里面的对象直接转化成 Js 可以识别的 Json 对象。

具体步骤如下 ：

01. 加入 Jackson.jar
02. 在配置文件中配置 json 的映射
03. 在接受 Ajax 方法里面可以直接返回 Object,List 等, 但方法前面要加上 @ResponseBody 注解

### 21、当一个方法向 AJAX 返回特殊对象, 譬如 Object, List 等, 需要做什么处理

答：要加上 @ResponseBody 注解

### 22、SpringMVC 里面拦截器是怎么写的

答：有两种写法, 一种是实现接口, 另外一种是继承适配器类, 然后在 SpringMVC 的配置文件中配置拦截器即可：

``` XML
<!-- 配置 SpringMVC 的拦截器 -->
<mvc:interceptors>

    <!-- 配置一个拦截器的 Bean 就可以了 默认是对所有请求都拦截 -->
    <bean id="myInterceptor" class="com.et.action.MyHandlerInterceptor"></bean>

    <!-- 只针对部分请求拦截 -->
    <mvc:interceptor>
    <mvc:mapping path="/modelMap.do" />
        <bean class="com.et.action.MyHandlerInterceptorAdapter" />
    </mvc:interceptor>
</mvc:interceptors>
```

### 23、讲下 SpringMVC 的执行流程

答：系统启动的时候根据配置文件创建 Spring 的容器, 首先是发送 HTTP 请求到核心控制器 disPatherServlet，Spring 容器通过映射器去寻找业务控制器，使用适配器找到相应的业务类，在进业务类时进行数据封装，在封装前可能会涉及到类型转换，执行完业务类后使用 ModelAndView 进行视图转发，数据放在 Model 中，用 Map 传递数据进行页面显示。