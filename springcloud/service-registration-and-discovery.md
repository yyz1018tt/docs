> 在实际分布式架构中，应用不能总是知道其他服务的确切位置。服务注册中心(such as [Netflix Eureka](https://github.com/Netflix/eureka), or a sidecar solution, such as [HashiCorp Consul](https://www.consul.io/))可以提供帮助。Spring Cloud 为流行的注册中心(such as [Eureka](https://spring.io/projects/spring-cloud-netflix), [Consul](https://spring.io/projects/spring-cloud-consul), [Zookeeper](https://spring.io/projects/spring-cloud-zookeeper), and even [Kubernetes'](https://spring.io/projects/spring-cloud-kubernetes) built-in system)提供了 `DiscoveryClient` 实现。还有  [Spring Cloud Load Balancer](https://spring.io/guides/gs/spring-cloud-loadbalancer/) 帮你在服务实例之间实现负载均衡。

# 服务注册与发现

顾名思义，分布式系统中服务注册与发现主要解决的就是两个问题：

- 服务注册：服务实例将自身服务信息注册到注册中心。服务信息包括服务主机的网络位置（ IP 和提供服务的  Port），以及暴露服务自身状态以及访问协议等信息。
- 服务发现：服务实例请求注册中心获取所依赖服务信息。服务实例通过注册中心，获取到注册到其中的服务实例的信息，通过这些信息去请求它们提供的服务。

实际调用过程中，服务消费者 通过注册中心发现 服务提供者 注册的服务通讯地址和端口，然后实现 RPC(远程过程调用)。

# CAP

CAP 原则，指的是在一个分布式系统中，`Consistency`(一致性)、`Availability`(可用性)、`Partition Tolerance`(分区容错性)，不能同时成立。

- 一致性：它要求在同一时刻点，分布式系统中的所有数据备份都处于同一状态。
- 可用性：在系统集群的一部分节点宕机后，系统依然能够响应用户的请求。
- 分区容错性：在网络区间通信出现失败，系统能够容忍。

> 一般来讲，基于网络的不稳定性，分布容错是不可避免的，所以我们默认 CAP 中的 P 总是成立的。
>
> 一致性的强制数据统一要求，必然会导致在更新数据时部分节点处于被锁定状态，此时不可对外提供服务，影响了服务的可用性，反之亦然。因此一致性和可用性不能同时满足。
>
> Eureka 满足了其中的 AP(高可用性)，Consul 和 Zookeeper 满足了其中的 CP(一致性)。

# Eureka

应用内，集成在应用中，不需要依赖其他的软件服务

默认开启自我保护模式，即在在短时间内丢失过多服务的心跳时，那么这个服务不会立马被注销。

AP 保证服务的可用性。Eureka 多实例采用互相注册的方式，当一台服务宕机，客户端请求会自动切换到新的Eureka Server 节点上。

# Zookeeper

需要安装额外的软件，对应用侵入性小

CP 保证任何时候的数据一致。

集群环境需要选举Leader，当Leader宕机时，需要重新选举Leader，而在选举Leader的过程中，服务是不可用的。

# Consul

 Go开发 需要安装额外的软件对应用侵入性小

CP 保证任何时候的数据一致。集群类似Zookeeper

# 三者总结：

| 对比项      | Eureka   | Zookeeper | Consul   |
| ----------- | -------- | --------- | -------- |
| 侵入性      | 应用内部 | 应用外    | 应用外   |
| CAP原则     | AP       | CP        | CP       |
| 访问协议    | HTTP     | TCP       | HTTP/DNS |
| 开发语言    | Java     | Java      | Go       |
| SpringCloud | 集成     | 集成      | 集成     |

