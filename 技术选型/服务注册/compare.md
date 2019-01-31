|表格|B|C|D|E|
|:---|:---|---|---|---|
|A|B|C|D|E|


Eureka1.0架构存在的问题
---
>12
12312



* 订阅端拿到的是服务的全量的地址：这个对于客户端的内存是一个比较大的消耗，特别在多数据中心部署的情况下，某个数据中心的订阅端往往只需要同数据中心的服务提供端即可。
* pull模式：客户端采用周期性向服务端主动pull服务数据的模式（也就是客户端轮训的方式），这个方式存在实时性不足以及无谓的拉取性能消耗的问题。
* 一致性协议：Eureka集群的多副本的一致性协议采用类似“异步多写”的AP协议，每一个server都会把本地接收到的写请求（register/heartbeat/unregister/update）发送给组成集群的其他所有的机器（Eureka称之为peer），特别是hearbeat报文是周期性持续不断的在client->server->all peers之间传送；这样的一致性算法，导致了如下问题
    * 每一台Server都需要存储全量的服务数据，Server的内存明显会成为瓶颈。
    * 当订阅者却来越多的时候，需要扩容Eureka集群来提高读的能力，但是扩容的同时会导致每台server需要承担更多的写请求，扩容的效果不明显。
    * 组成Eureka集群的所有server都需要采用相同的物理配置，并且只能通过不断的提高配置来容纳更多的服务数据

### reference
* [阿里巴巴ConfigServer演变](https://nacos.io/en-us/blog/alibaba-configserver.html)
