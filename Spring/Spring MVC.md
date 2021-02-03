# Spring MVC知识点

Q：Spring MVC 框架有什么用？  
A：Spring Web MVC 框架提供”模型-视图-控制器”( Model-View-Controller )架构和随时可用的组件，用于开发灵活且松散耦合的 Web 应用程序。  
***
Q：Spring MVC 有哪些核心组件？  
A：Spring MVC 一共有九大核心组件，最关键的只有 HandlerMapping + HandlerAdapter + HandlerExceptionResolver 。  

- MultipartResolver
- LocaleResolver
- ThemeResolver
- HandlerMapping
- HandlerAdapter
- HandlerExceptionResolver
- RequestToViewNameTranslator
- ViewResolver
- FlashMapManager

***
Q：DispatcherServlet 的工作流程是怎样的？  
A：步骤如下：  
1. 发送请求  
  用户向服务器发送 HTTP 请求，请求被 Spring MVC 的调度控制器 `DispatcherServlet` 捕获。
2. 映射处理器  
  `DispatcherServlet` 根据请求 URL ，调用 `HandlerMapping` 获得该 Handler 配置的所有相关的对象（包括 Handler 对象以及 Handler 对象对应的拦截器），最后以 `HandlerExecutionChain` 对象的形式返回。
3. 处理器适配  
  `DispatcherServlet` 根据获得的 Handler，选择一个合适的 `HandlerAdapter` 。（附注：如果成功获得 HandlerAdapter 后，此时将开始执行拦截器的 `preHandler()` 方法）。  
  提取请求 Request 中的模型数据，填充 Handler 入参，开始执行Handler（Controller)。 在填充Handler的入参过程中，根据你的配置，Spring 将帮你做一些额外的工作：数据的验证和转换。  
  Handler(Controller) 执行完成后，向 DispatcherServlet 返回一个 ModelAndView 对象。
4. 解析视图  
  根据返回的 ModelAndView ，选择一个适合的 ViewResolver（必须是已经注册到 Spring 容器中的 ViewResolver)，解析出 View 对象，然后返回给 DispatcherServlet。  
5. 渲染视图 + 响应请求  
  ViewResolver 结合 Model 和 View，来渲染视图，并写回给用户( 浏览器 )。  

![](https://blog-pictures.oss-cn-shanghai.aliyuncs.com/15300766829012.jpg)  

***
Q：Spring MVC 真正运行的流程？  
A：对于目前主流的架构，前后端已经进行分离了，所以 Spring MVC 只负责 Model 和 Controller 两块，而将 View 移交给了前端。所以，在上图中的步骤 ⑤ 和 ⑥ 两步，已经不在需要。  
在步骤 ③ 中，如果 Handler(Controller) 执行完后，如果判断方法有 @ResponseBody 注解，则直接将结果写回给用户( 浏览器 )。  
![](http://static.iocoder.cn/images/Spring/2022-02-21/01.png)
***
Q：`@RestController` 和 `@Controller` 有什么区别？  
A：`@RestController` 注解，在 `@Controller` 基础上，增加了 `@ResponseBody` 注解，更加适合目前前后端分离的架构下，提供 Restful API ，返回例如 JSON 数据格式。当然，返回什么样的数据格式，根据客户端的 "ACCEPT" 请求头来决定。
***
Q：Spring MVC 的 Controller 是不是单例？  
A：绝绝绝大多数情况下，Controller 是单例。那么，Controller 里一般不建议存在共享的变量。实际场景下，一般也不需要使用共享变量的情况。  
***
Q：什么是 Spring MVC 拦截器？
A：成功获得 `HandlerAdapter` 后，就会执行拦截器的方法，分为 `preHandle`、`postHandle`、`afterCompletion`。  
`preHandle` 按拦截器定义顺序调用。若任一拦截器返回 false ，则 Controller 方法不再调用。`postHandle` 和 `afterCompletion` 方法，按拦截器定义逆序调用。`postHandler` 在调用 Controller 方法之后执行。  
***
Q：Spring MVC 的拦截器和 Filter 过滤器有什么差别？
A：差异如下：  
- 功能相同：拦截器和 Filter都能实现相应的功能，谁也不比谁强。
- 容器不同：拦截器构建在 Spring MVC 体系中；Filter 构建在 Servlet 容器之上。
- 使用便利性不同：拦截器提供了三个方法，分别在不同的时机执行；过滤器仅提供一个方法，当然也能实现拦截器的执行时机的效果，就是麻烦一些。


