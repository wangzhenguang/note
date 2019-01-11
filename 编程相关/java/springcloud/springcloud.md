# SpringCloud

| 微服务           | 技术实现                             |
| ---------------- | ------------------------------------ |
| 服务开发         | Springboot、Spring                   |
| 服务配置与管理   | Archaius、Diamond                    |
| 服务注册与发现   | Eureka、Consul、Zookeeper等          |
| 服务调用         | Rest、RPC、gRPC                      |
| 服务熔断器       | Hystrix、Envoy等                     |
| 负载均衡         | Ribbon、Nginx等                      |
| 服务接口调用     | Feign等                              |
| 消息队列         | Kafka、RabbitMQ、ActiveMQ等          |
| 服务配置中心管理 | SpringCloudConfig、Chef等            |
| 服务路由         | Zuul等                               |
| 服务监控         | Zabbix、Nagios、Metrics、Spectator等 |
| 全链路追踪       | Zipkin、Brave、Dapper等              |
| 服务部署         | Docker、OpenStack、Kubernetes等      |
| 数据流操作开发包 | SpringCloud Stream                   |
| 事件消息总线     | Spring Cloud Bus                     |



springloaded devtools 热部署











#### eureka info配置

maven 设置访问资源文件

instance-id 配置

访问路径显示ip

关闭自我保护机制

```yaml
eureka:
	server:
		enable-self-preservation: false
	
```

#### 服务发现

### 集群搭建

## 



## ribbon

#### rule

默认提供

| 名称                      | 规则                                                         |
| ------------------------- | ------------------------------------------------------------ |
| RoundRobinRule            | 轮询                                                         |
| RandomRule                | 随机                                                         |
| AvailabilityFilteringRule | 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，还有并发的连接数量超过阈值的服务，然后对剩余的服务按照轮询策略访问 |
| WeightedResponseTimeRule  | 更具平均响应时间计算所有服务的权重，响应时间越快服务权重越大。被选中的几率越大。 刚启动时，会先使用轮询策略。等统计信息足够再自行切换 |
| RetryRule                 | 先按照轮询策略获取服务。如果服务失败则在指定时间内进行重试，获取可用服务。 |
| BestAvailableRule         | 先过滤掉故障的服务，然后选一个并发量最小的服务               |
| ZoneAvoidanceRule         | 默认规则，判断server所在区域的性能和服务的可用性选择服务器。 |



自定义rule配置



## feign使用

服务熔断

### 服务降级

## ZUUL

依赖包

注解

配置 路由映射规则

## Config

访问配置的几种方式：

 