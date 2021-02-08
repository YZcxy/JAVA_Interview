# MQ

## 基本概念

Q：什么是消息队列？  
A：消息队列，是分布式系统中重要的组件。主要解决应用耦合，异步消息，流量削锋等问题。可实现高性能，高可用，可伸缩和最终一致性架构，是大型分布式系统不可缺少的中间件。
***
Q：消息队列由哪些角色组成？  
A：生产者 Producer 负责**生产消息**，消费者 Consumer 负责**消费消息**，消息代理 Broker 负责**存储消息和转发消息**。其中转发消息分为拉取 Pull 和推送 Push。
***
Q：消息队列有几种消费语义？  
A：一共有 3 种，分别如下：  
1. 消息至多被消费一次（At most once）：消息可能会丢失，但绝不重传。
2. 消息至少被消费一次（At least once）：消息可以重传，但绝不丢失。
3. 消息仅被消费一次（Exactly once）：每一条消息只被传递一次。
***
Q：消息队列有几种投递方式？分别有什么优缺点？  
A：消息队列有 push 推送和 pull 拉取两种投递方式。  
- push
  - 优点是即时性。
  - 缺点是消费者的消费能力不足，可能造成消息的堆积。
- pull
  - 优点是主动权在消费者手里，根据自己的消费速度进行拉取。
  - 消费方不知道什么时候可以拉取新消息，会造成消息延迟和忙等。

目前消息队列会基于 push + pull 的形式，Broker 仅仅告诉 Consumer 有新消息，具体的消息拉取由消费者自行决定。  
***

## RocketMQ

Q：RocketMQ 由哪些角色组成？  
A：如下图所示：  
![](http://static.iocoder.cn/images/RocketMQ/2019_11_12/01.png)
***
Q：RocketMQ 的整体流程是怎样的？  
A：如下图所示：  
![](http://static.iocoder.cn/images/RocketMQ/2019_11_12/02.png)
1. 启动 **Namesrv**，Namesrv 起来后监听端口，等待 Broker、Producer、Consumer 连上来，相当于一个路由控制中心。
2. **Broker** 启动，跟所有的 Namesrv 保持长连接，定时发送心跳包。
3. 收发消息前，先创建 Topic 。创建 Topic 时，需要指定该 Topic 要存储在哪些 Broker上。也可以在发送消息时自动创建 Topic。
4. Producer 发送消息。
5. Consumer 消费消息。
***
Q：Namesrv 的特性？  
A：Namesrv 用于存储 Topic、Broker 关系信息，功能简单，稳定性高。  
由于Broker 需要跟所有 Namesrv 连接，因此多个 Namesrv 之间相互没有通信，单台 Namesrv 宕机不影响其它 Namesrv 与集群。即使整个 Namesrv 集群宕机，已经正常工作的 Producer、Consumer、Broker 仍然能正常工作，但新起的 Producer、Consumer、Broker 就无法工作。   
Namesrv 压力不会太大，平时主要开销是在维持心跳和提供 Topic-Broker 的关系数据。
***
Q：Broker 的特性？  
A：主要特性如下几点：  
1. 高并发读写服务。
- 消息顺序写，所有 Topic 数据同时只会写一个文件，一个文件满1G ，再写新文件，真正的顺序写盘，使得发消息 TPS 大幅提高。
- 消息随机读，RocketMQ 尽可能让读命中系统 Pagecache ，因为操作系统访问 Pagecache 时，即使只访问 1K 的消息，系统也会提前预读出更多的数据，在下次读时就可能命中 Pagecache ，减少 IO 操作。
2. 负载均衡与动态伸缩。
- 负载均衡：Broker 上存 Topic 信息，Topic 由多个队列组成，队列会平均分散在多个 Broker 上，而 Producer 的发送机制保证消息尽量平均分布到所有队列中，最终效果就是所有消息都平均落在每个 Broker 上。
- 动态伸缩能力（非顺序消息）：Broker 的伸缩性体现在两个维度：Topic、Broker。
  - Topic 维度：假如一个 Topic 的消息量特别大，但集群水位压力还是很低，就可以扩大该 Topic 的队列数， Topic 的队列数跟发送、消费速度成正比。
  - Broker 维度：如果集群水位很高了，需要扩容，直接加机器部署 Broker 就可以。Broker 启动后向 Namesrv 注册，Producer、Consumer 通过 Namesrv 发现新Broker，立即跟该 Broker 直连，收发消息。
3. 高可用 & 高可靠。
- 高可用：集群部署时一般都为主备，备机实时从主机同步消息，如果其中一个主机宕机，备机提供消费服务，但不提供写服务。
- 高可靠：所有发往 Broker 的消息，有同步刷盘和异步刷盘机制。
  - 同步刷盘时，消息写入物理文件才会返回成功。
  - 异步刷盘时，只有机器宕机，才会产生消息丢失，Broker 挂掉可能会发生，但是机器宕机崩溃是很少发生的，除非突然断电。
4. Broker 与 Namesrv 的心跳机制。
- 单个 Broker 跟所有 Namesrv 保持心跳请求，心跳间隔为30秒，心跳请求中包括当前 Broker 所有的 Topic 信息。
- Namesrv 会反查 Broker 的心跳信息，如果某个 Broker 在 2 分钟之内都没有心跳，则认为该 Broker 下线，调整 Topic 跟 Broker 的对应关系。但此时 Namesrv 不会主动通知Producer、Consumer 有 Broker 宕机。也就说，只能等 Producer、Consumer 下次定时拉取 Topic 信息的时候，才会发现有 Broker 宕机。
***
Q：Producer 的特性？  
A：特性如下：  
1. 获得 Topic-Broker 的映射关系。
- Producer 启动时，也需要指定 Namesrv 的地址，从 Namesrv 集群中选一台建立长连接。如果该 Namesrv 宕机，会自动连其他 Namesrv ，直到有可用的 Namesrv 为止。
- 生产者每 30 秒从 Namesrv 获取 Topic 跟 Broker 的映射关系，更新到本地内存中。然后再跟 Topic 涉及的所有 Broker 建立长连接，每隔 30 秒发一次心跳。
- 在 Broker 端也会每 10 秒扫描一次当前注册的 Producer ，如果发现某个 Producer 超过 2 分钟都没有发心跳，则断开连接。
2. 生产者端的负载均衡。
- 生产者发送时，会自动轮询当前所有可发送的broker，一条消息发送成功，下次换另外一个broker发送，以达到消息平均落到所有的broker上。
***
Q：Consumer 的特性？  
A：特性如下：  
1. 获得 Topic-Broker 的映射关系。
- Consumer 启动时需要指定 Namesrv 地址，与其中一个 Namesrv 建立长连接。消费者每隔 30 秒从 Namesrv 获取所有Topic 的最新队列情况，这意味着某个 Broker 如果宕机，客户端最多要 30 秒才能感知。连接建立后，从 Namesrv 中获取当前消费 Topic 所涉及的 Broker，直连 Broker 。
- Consumer 跟 Broker 是长连接，会每隔 30 秒发心跳信息到Broker 。Broker 端每 10 秒检查一次当前存活的 Consumer ，若发现某个 Consumer 2 分钟内没有心跳，就断开与该 Consumer 的连接，并且向该消费组的其他实例发送通知，触发该消费者集群的负载均衡。
2. 消费者端的负载均衡。
- 消费者有两种消费模式：集群消费和广播消费。
  - 集群消费：一个 Topic 可以由同一个消费这分组( Consumer Group )下所有消费者分担消费。即一条消息只会投递到一个 Consumer Group 下面的一个实例。
  - 广播消费：每个消费者消费 Topic 下的所有队列。

***
Q：RocketMQ 提供了什么来保证顺序消息？  
A：提供了两种顺序级别：  
- 普通顺序消息 ：Producer 将相关联的消息发送到相同的消息队列。在集群异常情况（如某个 Broker 宕机或者重启）下，消息会出现短暂的乱序。
- 严格顺序消息 ：在【普通顺序消息】的基础上，Consumer 严格顺序消费。无论正常异常情况都能保证顺序，但是牺牲了分布式 Failover 特性，即 Broker 集群中只要有一台机器不可用，则整个集群都不可用，服务可用性大大降低。
***


