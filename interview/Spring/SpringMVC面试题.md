### 1、Spring MVC 框架有什么用？

Spring Web MVC 框架提供 `模型-视图-控制器` 架构和随时可用的组件，用于开发灵活且松散耦合的 Web 应用程序。MVC 模式有助于分离应用程序的不同方面，如输入逻辑，业务逻辑和 UI 逻辑，同时在所有这些元素之间提供松散耦合。

### 2、描述一下 DispatcherServlet 的工作流程

DispatcherServlet(前端控制器) 的工作流程可以用一幅图来说明：

![图片](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-11-07/5ad1d046-80b2-4417-8753-e6af88d2bd9f_dispatcherServlet.png)

1. 向服务器发送 HTTP 请求，请求被前端控制器 DispatcherServlet 捕获。
2. DispatcherServlet 根据 servlet.xml 中的配置对请求的 URL 进行解析，得到请求资源标识符（URI）。然后根据该 URI，调用 HandlerMapping 获得该 Handler 配置的所有相关的对象（包括 Handler 对象以及 Handler 对象对应的拦截器），最后以HandlerExecutionChain 对象的形式返回。
3. DispatcherServlet 根据获得的Handler，选择一个合适的 HandlerAdapter。（附注：如果成功获得HandlerAdapter后，此时将开始执行拦截器的 preHandler(…)方法）。
4. 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)。在填充Handler的入参过程中，根据你的配置，Spring 将帮你做一些额外的工作：

- HttpMessageConveter：将请求消息（如 Json、xml 等数据）转换成一个对象，将对象转换为指定的响应信息。
- 数据转换：对请求消息进行数据转换。如`String`转换成`Integer`、`Double`等。
- 数据格式化：对请求消息进行数据格式化。如将字符串转换成格式化数字或格式化日期等。
- 数据验证：验证数据的有效性（长度、格式等），验证结果存储到`BindingResult`或`Error`中。

5. Handler(Controller)执行完成后，向 DispatcherServlet 返回一个 ModelAndView 对象；
6. 根据返回的ModelAndView，选择一个适合的 ViewResolver（必须是已经注册到 Spring 容器中的ViewResolver)返回给DispatcherServlet。
7. ViewResolver 结合Model和View，来渲染视图。
8. 视图负责将渲染结果返回给客户端。



### 3、介绍一下 WebApplicationContext

WebApplicationContext 是 ApplicationContext 的扩展。它具有 Web 应用程序所需的一些额外功能。它与普通的 ApplicationContext 在解析主题和决定与哪个servlet 关联的能力方面有所不同。



