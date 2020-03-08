[toc]

# 前言

基于微服务的思想，构建在 B2C 电商场景下的项目实战。

## 架构图
* 整体的功能如下图：![功能图](https://github.com/liangchengcheng/spring-dubbo-hdsx-x2/blob/master/iamges/image_jiagou.png)

下面，我们会提供目前用到的中间件的管理平台。

**Swagger UI**

* 地址：http://ip:端口/swagger-ui.html
* 演示账号：无 / 无
* 用于展示 接口文档数据的界面。

**Dubbo Admin**

* 地址：http://dubbo-admin.shop.iocoder.com:18099
* 管理员账号：无需登陆


**Sentinel Console**

* 地址：http://sentinel.shop.iocoder.com:18099
* 账号：sentinel / sentinel


# 技术

## 项目结构

| 模块 | 名称 | 端口 | |
| --- | --- | --- | --- |
| `admin-web` | 【前端】管理后台 | HTTP 8080 | |
| `user-application` | 用户 HTTP 服务 | HTTP 18082 | |
| `product-application` | 商品 HTTP 服务 | HTTP 18081 | |
| `order-application` | 订单 HTTP 服务 | HTTP 18088 | |

-------

后端项目，目前的项目结构如下：

```Java
[-] xxx
  ├──[-] xxx-restful // 提供对外 HTTP API 。
  ├──[-] xxx-service-api // 提供 Dubbo 服务 API 。
  ├──[-] xxx-service-impl // 提供 Dubbo 服务 Service 实现。
```

考虑到大多数公司，无需拆分的特别细，并且过多 JVM 带来的服务器成本。所以目前的设定是：

* `xxx-service-impl` 内嵌在 `xxx-application` 中运行。
* MQ 消费者、定时器执行器，内嵌在 `xxx-service-impl` 中运行。

也就是说，一个 `xxx-application` 启动后，该模块就完整启动了。

## 搭建环境和启动流程

* 启动zookeeper，这里不做演示，使用默认端口即可，dubbo中间的服务注册中心就是zookeeper，我个人觉得nacos比较好。

* 启动dubbo的提供者服务， 再启动服务的消费者服务

* 启动sentinel `-Djava.net.preferIPv4Stack=true  -Dcsp.sentinel.api.port=8189   -Dproject.name=consumer-service -Dcsp.sentinel.dashboard.server=127.0.0.1:8080`

* 启动dubbo admin 的服务管理中心。


## 技术栈

### 后端

| 框架 | 说明 |  版本 |
| --- | --- | --- |
| [Spring Boot](https://spring.io/projects/spring-boot) | 应用开发框架 |   2.1.4 |
| [MySQL](https://www.mysql.com/cn/) | 数据库服务器 | 5.6 |
| [Druid](https://github.com/alibaba/druid) | JDBC 连接池、监控组件 | 1.1.16 |
| [MyBatis](http://www.mybatis.org/mybatis-3/zh/index.html) | 数据持久层框架 | 3.5.1 |
| [MyBatis-Plus](https://mp.baomidou.com/) | Mybatis 增强工具包 | 3.1.1 |
| [Redis](https://redis.io/) | key-value 数据库 | 暂未引入，等压测后，部分模块 |
| [Redisson](https://github.com/redisson/redisson) | Redis 客户端 | 暂未引入，等压测后，部分模块 |
| [Elasticsearch](https://www.elastic.co/cn/) | 分布式搜索引擎 | 6.7.1 |
| [Dubbo](http://dubbo.apache.org/) | 分布式 RPC 服务框架 | 2.7.1 |
| [RocketMQ](http://dubbo.apache.org/) | 消息中间件 | 4.3.2 |
| [Seata](https://github.com/seata/seata) | 分布式事务中间件 | 0.5.1 |
| [Zookeeper](http://zookeeper.apache.org/) | 分布式系统协调 | 3.4.9 作为注册中心 |
| [XXL-Job](http://www.xuxueli.com/xxl-job/) | 分布式任务调度平台 | 2.0.1 |
| [springfox-swagger2](https://github.com/springfox/springfox/tree/master/springfox-swagger2) | API 文档 | 2.9.2 |
| [swagger-bootstrap-ui](https://gitee.com/xiaoym/swagger-bootstrap-ui) | Swagger 增强 UI 实现 | 1.9.3 |

未来考虑引入

* [ ] 配置中心 Apollo
* [ ] 网关 Soul




### 监控

一般来说，监控会有三种方式：

* 1、Tracing ，我们采用 Apache SkyWalking
* 2、Logging ，我们采用 ELK
* 3、Metrics ，我们采用 Prometheus

| 框架 | 说明 |  版本 |
| --- | --- | --- |
| [SkyWalking](http://skywalking.apache.org/) | 分布式应用追踪系统 | 6.0.0 |
| [Prometheus](https://prometheus.io/) | 服务监控体系 | 2.9.2 |
| [Alertmanager](https://prometheus.io/docs/alerting/alertmanager/) | 告警管理器 | 0.17.0 |
| [Grafana](https://grafana.com/) | 仪表盘和图形编辑器 | 0.17.0 |

### 其它

* Jenkins 持续集成
* Nginx 服务器
* [ ] Docker 容器
* [ ] Nginx
