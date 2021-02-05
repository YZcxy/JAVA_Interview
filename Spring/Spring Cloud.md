# Spring Cloud知识点

Q：Spring Cloud 核心功能是什么？
A：毫无疑问，Spring Cloud 可以说是目前微服务架构的最好的选择，涵盖了基本我们需要的所有组件，所以也被称为全家桶。Spring Cloud 主要提供了如下核心的功能：
- Distributed/versioned configuration 分布式/版本化的配置管理
- Service registration and discovery 服务注册与服务发现
- Routing 路由
- Service-to-service calls 端到端的调用
- Load balancing 负载均衡
- Circuit Breakers 断路器
- Global locks 全局锁
- Leadership election and cluster state 选举与集群状态管理
- Distributed messaging 分布式消息
***
Q：为什么要使用服务发现？  
A：简单来说，通过注册中心，调用方(Consumer)获得服务方(Provider)的地址，从而能够调用。  
当然，实际情况下，会分成两种注册中心的发现模式：  
- 客户端发现模式
- 服务端发现模式

在 Spring Cloud 中，我们使用前者，即客户端发现模式。  
***
Q：什么是Eureka？  
A：实现服务治理（服务注册与发现），由两个组件组成：Eureka 服务端和 Eureka 客户端。在应用启动时，Eureka 客户端向服务端注册自己的服务信息，同时将服务端的服务信息缓存到本地。客户端会和服务端周期性的进行心跳交互，以更新服务租约和服务信息。  
***
Q：Eureka 如何实现集群？  
A：为了使 Eureka Server 实现高可用，我们需要为它配置集群。这样当有一台 Eureka Server 有故障时，集群中的其他 Server 可以进行代替。Eureka 集群之中的 Node 通过 `P2P 通信`的方式共享注册表，以使得每个 Eureka Server 的注册表保持一致。本教程将在本地开启两台 Eureka Server 以测试集群的搭建。  
***
Q：Eureka 缓存机制是什么？  
A：Eureka 拥有二级缓存，一个只读缓存，一个读写缓存。如下图所示：  
![](http://svip.iocoder.cn/images/SpringCloud/2018_10_28/01.png)
***
Q：为什么要使用负载均衡？  
A：随着业务的发展，单台服务无法支撑访问的需要，于是搭建多个服务形成集群。负载平衡可以改善跨计算机，计算机集群，网络链接，中央处理单元或磁盘驱动器等多种计算资源的工作负载分布。那么随之要解决的是，每次请求，调用哪个服务，也就是需要进行负载均衡。  
目前负载均衡有两种模式：  
- 客户端模式
- 服务端模式

在 Spring Cloud 中，我们使用前者，即客户端模式。
***
Q：什么是Ribbon？  
A：主要提供客户侧的软件负载均衡算法。通过 Spring Cloud 的封装，可以让我们轻松地将面向服务的 REST 模版请求自动转换成客户端负载均衡的服务调用。  
![](http://static.iocoder.cn/36465fd7d91b3a4aeb3b28c3777649e6)
***
Q：Ribbon 有哪些负载均衡算法？  
A：一共有七种负载均衡策略：   

| 策略名                    | 策略声明                                                     | 策略描述                                                     | 实现说明                                                     |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BestAvailableRule         | public class BestAvailableRule extends ClientConfigEnabledRoundRobinRule | 选择一个最小的并发请求的server                               | 逐个考察Server，如果Server被tripped了，则忽略，在选择其中ActiveRequestsCount最小的server |
| AvailabilityFilteringRule | public class AvailabilityFilteringRule extends PredicateBasedRule | 过滤掉那些因为一直连接失败的被标记为circuit tripped的后端server，并过滤掉那些高并发的的后端server（active connections 超过配置的阈值） | 使用一个AvailabilityPredicate来包含过滤server的逻辑，其实就就是检查status里记录的各个server的运行状态 |
| WeightedResponseTimeRule  | public class WeightedResponseTimeRule extends RoundRobinRule | 根据响应时间分配一个weight，响应时间越长，weight越小，被选中的可能性越低。 | 一个后台线程定期的从status里面读取评价响应时间，为每个server计算一个weight。Weight的计算也比较简单responsetime 减去每个server自己平均的responsetime是server的权重。当刚开始运行，没有形成status时，使用roubine策略选择server。 |
| RetryRule                 | public class RetryRule extends AbstractLoadBalancerRule      | 对选定的负载均衡策略机上重试机制。                           | 在一个配置时间段内当选择server不成功，则一直尝试使用subRule的方式选择一个可用的server |
| RoundRobinRule            | public class RoundRobinRule extends AbstractLoadBalancerRule | roundRobin方式轮询选择server                                 | 轮询index，选择index对应位置的server                         |
| RandomRule                | public class RandomRule extends AbstractLoadBalancerRule     | 随机选择一个server                                           | 在index上随机，选择index对应位置的server                     |
| ZoneAvoidanceRule         | public class ZoneAvoidanceRule extends PredicateBasedRule    | 复合判断server所在区域的性能和server的可用性选择server       | 使用ZoneAvoidancePredicate和AvailabilityPredicate来判断是否选择某个server，前一个判断判定一个zone的运行性能是否可用，剔除不可用的zone（的所有server），AvailabilityPredicate用于过滤掉连接数过多的Server。 |

***
Q：为什么需要声明式调用？  
A：如果每个客户端都需要手动去写 HTTP 封装，会造成不必要的维护，有了声明式调用，开发人员可以更加关注业务的实现。  
***
Q：什么是Feign？  
A：Feign 的主要目标是将Java Http 客户端变得简单。 Feign的一个关键机制就是使用了动态代理。  
- 首先，如果你对某个接口定义了 @FeignClient 注解，Feign 就会针对这个接口创建一个动态代理。
- 接着你要是调用那个接口，本质就是会调用 Feign 创建的动态代理，这是核心中的核心。
- Feign的动态代理会根据你在接口上的 @RequestMapping 等注解，来动态构造出你要请求的服务的地址。
- 最后针对这个地址，发起请求、解析响应。 

![](http://static.iocoder.cn/6650aa32de0def76db0e4c5228619aef)
***
Q：Feign 和 Ribbon 的区别？
A：Ribbon 和 Feign 都是使用于调用用其余服务的，不过方式不同。
- 启动类用的注解不同。
  - Ribbon 使用的是 `@RibbonClient` 。
  - Feign 使用的是 `@EnableFeignClients` 。
- 服务的指定位置不同。
  - Ribbon 是在 `@RibbonClient` 注解上设置。
  - Feign 则是在定义声明方法的接口中用 @FeignClient 注解上设置。
- 调使用方式不同。
  - Ribbon 需要自己构建 Http 请求，模拟 Http 请求而后用 RestTemplate 发送给其余服务，步骤相当繁琐。
  - Feign 采使用接口的方式，将需要调使用的其余服务的方法定义成声明方法就可，不需要自己构建 Http 请求。不过要注意的是声明方法的注解、方法签名要和提供服务的方法完全一致。

***
Q：Feign 是怎么和 Ribbon、Eureka 整合的？  
A：如下图所示：  
![](http://static.iocoder.cn/252461fbb6d64d3dbc1914b7eadbfb86)
***
Q：为什么需要服务保障？  
A：在微服务架构中，我们将业务拆分成一个个的服务，服务与服务之间可以相互调用（RPC）。为了保证其高可用，单个服务又必须集群部署。由于网络原因或者自身的原因，服务并不能保证服务的 100% 可用，如果单个服务出现问题，调用这个服务就会出现网络延迟，此时若有大量的网络涌入，会形成任务累积，导致服务瘫痪，甚至导致服务“雪崩”。为了解决这个问题，就出现断路器模型。  
***
Q：什么是 Hystrix？  
A：Hystrix 是一个延迟和容错库，旨在隔离远程系统，服务和第三方库的访问点，当出现故障是不可避免的故障时，停止级联故障并在复杂的分布式系统中实现弹性。  
![](http://static.iocoder.cn/8848af2a2e093d0421d1c7113dedefc1)  
***
Q：Hystrix 隔离策略？  
A：Hystrix 有两种隔离策略：  
- 线程池隔离：tomcat 的线程会将请求的任务交给服务的内部线程池，tomcat的线程就可以去干别的事情去了，当服务自己线程池里面的线程执行完任务之后，就会将调用的结果返回给tomcat的线程，从而实现资源的隔离，当有大量并发的时候，服务内部的线程池的数量就决定了整个服务的并发度。如果服务A挂了，就不会造成大量的 tomcat 线程被服务 A 拖死，其他服务依然能够提供服务。整个系统不会受太大的影响。  
- 信号量隔离：信号量的资源隔离只是起到一个开关的作用，例如，服务X的信号量大小为10，那么同时只允许10个tomcat的线程(此处是tomcat的线程，而不是服务X的独立线程池里面的线程)来访问服务X，其他的请求就会被拒绝，从而达到限流保护的作用。  

总结：当请求的服务网络开销比较大的时候，或者是请求比较耗时的时候，我们最好是使用线程隔离策略，这样的话，可以保证大量的容器(tomcat)线程可用，不会由于服务原因，一直处于阻塞或等待状态，快速失败返回。而当我们请求缓存这些服务的时候，我们可以使用信号量隔离策略，因为这类服务的返回通常会非常的快，不会占用容器线程太长时间，而且也减少了线程切换的一些开销，提高了缓存服务的效率。  
***
Q：Hystrix 缓存机制？  
A：减少重复的请求数。在同一个用户请求的上下文中，相同依赖服务的返回数据始终保持一致。实现方式有两种：   
- 通过HystrixCommand类实现
  - 继承 HystrixCommand 或 HystrixObservableCommand，覆盖 `getCacheKey()` 方法，指定缓存的key，开启缓存配置。
  - 配置 HystrixRequestContextServletFilter
  - 继承 HystrixCommand 或 HystrixObservableCommand，在更新接口调用完成后，清空缓存。
- 使用CacheResultCacheRemove和CacheKey标注来实现缓存
  - 使用@CacheResult实现缓存功能
  - 使用@CacheResult和@CacheKey实现缓存功能
  - 使用@CacheRemove清空缓存

***
Q：什么是 Hystrix 断路器？  
A：Hystrix 断路器通过 HystrixCircuitBreaker 实现。HystrixCircuitBreaker 有三种状态 ：
- CLOSED ：关闭
- OPEN ：打开
- HALF_OPEN ：半开

![](http://static.iocoder.cn/images/Hystrix/2018_11_08/01.png)
- 红线 ：初始时，断路器处于 CLOSED 状态，链路处于健康状态。当满足如下条件，断路器从 CLOSED 变成 OPEN 状态：
  - 周期( 可配，HystrixCommandProperties.default_metricsRollingStatisticalWindow = 10000 ms )内，总请求数超过一定量( 可配，HystrixCommandProperties.circuitBreakerRequestVolumeThreshold = 20 ) 。
  - 错误请求占总请求数超过一定比例( 可配，HystrixCommandProperties.circuitBreakerErrorThresholdPercentage = 50% ) 。
- 绿线 ：断路器处于 OPEN 状态，命令执行时，若当前时间超过断路器开启时间一定时间( HystrixCommandProperties.circuitBreakerSleepWindowInMilliseconds = 5000 ms )，断路器变成 HALF_OPEN 状态，尝试调用正常逻辑，根据执行是否成功，打开或关闭熔断器【蓝线】。

***
Q：什么是 Hystrix 服务降级？  
A：在 Hystrix 断路器熔断时，可以调用一个降级方法，返回相应的结果。当然，降级方法需要配置和编码，如果不需要，也可以不写，也就是不会有服务降级的功能。
***
Q：为什么需要网关服务？  
A：API 网关可以提供一个单独且统一的 API 入口用于访问内部一个或多个 API。可以阻止将内部的敏感信息暴露给外部的客户端，为服务增加额外的安全层，可以支持混合通讯协议，降低微服务的复杂度，微服务模拟和虚拟化。  
***
Q：什么是Zuul？  
A：类似 Nginx ，反向代理的功能，不过 Netflix 自己增加了一些配合其他组件的特性。在微服务架构中，后端服务往往不直接开放给调用端，而是通过一个 API网关根据请求的 url ，路由到相应的服务。当添加API网关后，在第三方调用端和服务提供方之间就创建了一面墙，这面墙直接与调用方通信进行权限控制，后将请求均衡分发给后台服务端。  
***
Q：什么是配置中心？  
A：Spring Cloud Config 提供服务器端和客户端。服务器存储后端的默认实现使用 Git ，因此它轻松支持标签版本的配置环境，以及可以访问用于管理内容的各种工具。
***
Q：什么是链路追踪？  
A：在 Spring Cloud 中，skywalking 不仅仅能够透明的监控链路，还可以监控 JVM 等等。  
***
Q：Spring Cloud 所有整合？  
A：如下图：  
![](http://static.iocoder.cn/64e9a7827c76d38f899160da6f736ea2)

