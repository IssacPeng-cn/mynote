### 1.kafka 可以脱离 zookeeper 单独使用吗？为什么？

kafka 不能脱离 zookeeper 单独使用，因为 kafka 使用 zookeeper 管理和协调 kafka 的节点服务器。

### 2.kafka 有几种数据保留的策略？

kafka 有两种数据保存策略：按照过期时间保留和按照存储的消息大小保留。

### 3.kafka 同时设置了 7 天和 10G 清除数据，到第五天的时候消息达到了 10G，这个时候 kafka 将如何处理？

这个时候 kafka 会执行数据清除工作，时间和大小不论那个满足条件，都会清空数据。

### 4.什么情况会导致 kafka 运行变慢？

• cpu 性能瓶颈

• 磁盘读写瓶颈

• 网络瓶颈

### 5.使用 kafka 集群需要注意什么？

• 集群的数量不是越多越好，最好不要超过 7 个，因为节点越多，消息复制需要的时间就越长，整个群组的吞吐量就越低。

• 集群数量最好是单数，因为超过一半故障集群就不能用了，设置为单数容错率更高。