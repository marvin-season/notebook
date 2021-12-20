# nacos 配置中心

**Na**me**Co**nfigure**S**ervice

### 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```



### 名词解释

```properties
命名空间:  做配置隔离, 默认public
    1: 环境隔离:开发、测试、生产   -> 配置命名空间
        dev/test/prod
        	dev: ...
    2: 微服务隔离： 每个微服务一个命名空间， 使用配置分组来区分环境
配置集: 所有配置的集合
配置集ID: 配置id， -->data-id
配置分组: GROUP->DEFAULT_GROUP

```

### bootstrap.yml

```yaml
spring:
  main:
    allow-bean-definition-overriding: true
  application:
  	#  dataId的默认名称
    name: hc-customer-server
  cloud:
    nacos:
      #  服务配置地址
      server-addr: 119.29.109.198:8848
      #  注册中心
      discovery:
        server-addr: ${spring.cloud.nacos.server-addr}
        username: nacos
        password: cnxjkxq@2021nacos
        namespace: health-center

	  #  配置中心
      config:
        server-addr: ${spring.cloud.nacos.server-addr}
        namespace: health-center
        #  分组
        group: DEFAULT_GROUP
        # dataId的后缀
        file-extension: yml
        username: nacos
        password: cnxjkxq@2021nacos
        #  额外配置
        extension-configs:
          - data-id: datasource.yml
            refresh: true
            group: DEFAULT_GROUP

          - data-id: mp.yml
            refresh: true
            group: DEFAULT_GROUP

          - data-id: rpc.yml
            refresh: true
            group: DEFAULT_GROUP

  profiles:
    active: dev

```





