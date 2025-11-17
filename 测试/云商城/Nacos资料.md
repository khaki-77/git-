# SpringCloudAlibaba Nacos

> lecture：波波老师

课程内容：

1. Nacos
2. Gateway
3. Seata
4. Sentinel



必要的前置基础：

1. Spring
2. Maven
3. SpringBoot
4. 有SpringCloudNetflix基础更好，如果没有也可以学习。





# 一、微服务介绍

## 1. 什么是微服务

&emsp;&emsp;2014年，Martin Fowler（**马丁·福勒** ） 提出了微服务的概念，定义了微服务是由以单一应用程序构成的小服务，自己拥有自己的进程与轻量化处理，服务依业务功能设计，以全自动的方式部署，与其他服务使用 HTTP API 通信。同时服务会使用最小的规模的集中管理能力，服务可以用不同的编程语言与数据库等组件实现。

![WeChat13cb5e0a22944a3c85e6d91ca10babbc.png](img/a544f9b87c934ffe9fc994129e4bc5e2.png)

&emsp;&emsp;**马丁·福勒**是敏捷联盟的成员，于2001年，同其他16名合著者一起协助创作了“敏捷软件开发宣言”。他负责维护一个bliki网站---一种blog和wiki的混合衍生物，他还使控制反转(Inversion of Control)“依赖注入模式(Dependency Injection)”一词得到普及。

## 2.架构的演变

​        https://blog.csdn.net/qq_38526573/article/details/89576363

&emsp;&emsp;随着互联网的发展，网站应用的规模也不断的扩大，进而导致系统架构也在不断的进行变化，从互联网早起到现在，系统架构大体经历了下面几个过程：

![WeChatb55c37f0bb53be8957590c529bb21e83.png](img/18122ec67e5c4f2ca5579288c42c42cf.png)

1. **单体应用架构**

&emsp;&emsp;把所有功能都集中在一个应用中，统一部署，开发成本、部署成本和维护成本低

* 优点：项目架构简单，适合用户量少的项目，开发成本低，项目部署在一个节点上，维护方便。
* 缺点：功能集中在一个工程中，对于大型项目比一开发和维护，项目模块紧耦合，单点容错率低，无法对不同的模块功能进行针对性的优化和水平拓展

2. **垂直应用架构**

&emsp;&emsp;所谓垂直应用架构，其实就是把之前的单体应用拆分成多个应用，以提升效率，比如电商系统可以拆分成：电商系统、后台系统、CMS系统

* 优点：项目拆分实现了流量分担，解决了并发问题，而且可以针对不同模块进行优化和水平拓展，同时不同的系统之间不会互相影响，提高容错率
* 缺点：系统之间互相存在，无法进行相互调用，系统之间互相独立，会造成一部分功能的冗余

3. **分布式架构**

&emsp;&emsp;随着业务的增加，在垂直应用架构中冗余的业务代码越来越多，就需要将冗余的部分抽取出来，统一做成业务层单独处理，变成一个单独的服务，控制层调用不同的业务层服务就能完成不同的业务功能，具体表现就是一个项目拆分成表现层和服务层两个部分，服务层中包含业务逻辑，表现层只需要处理和页面的交互，业务逻辑都是调用服务层的服务来实现，这就是分布式架构。

* 优点：抽取公共的功能作为服务层，提高代码复用行。
* 缺点：系统间耦合度变高，调用关系错综复杂，难以维护。

4. **SOA架构**

&emsp;&emsp;分布式架构中的缺点就是调用复杂，而且当服务越来越多，或者当某一个服务压力过大需要水平拓展和负载均衡，对于资源调度和治理就需要用到治理中心SOA（Service Oriented Architecture）为核心来解决，同时治理中心还可以帮助我们解决服务之间协议不同的问题。

* 优点：使用治理中心（ESB\dubbo）解决了服务见调用关系的自动调节
* 缺点：服务间会有依赖关系，一旦某个环节出错会影响较大（服务雪崩），服务关系复杂，运维、测试部署困难。

5. **微服务架构**

&emsp;&emsp;微服务架构在某种程度上面架构SOA继续发展的下一步，它更加强调服务的“彻底拆分”，目的就是提高效率，微服务架构中，每个服务必须独立部署同时互不影响，微服务架构更加轻巧，轻量级。

微服务架构与SOA架构的不同

1. 微服务架构比SOA架构会更加的精细，让专业的人去做专业的。
2. 目的是提高效率每个服务之间互不影响，微服务架构中，每个服务需要独立部署
3. SOA架构中可能数据库存储会发生共享，微服务强调每个服务都是单独数据库，保证每个服务之间互不影响。
4. 微服务项目架构比SOA架构更加适合与互联网公司迅捷开发、快速迭代版本，因为粒度非常精细。

为何使用微服务呢？

![WeChatc690c21a12ed31985403c3be5446e01c.png](img/6e5c1179855b42d4ad8f472ea9ed2f1b.png)

微服务就像集群作战，可以提升性能

![WeChate3645eab5fa6b9086451566cb00ebadb.png](img/7b30c17619c4446aa1f9cd9c8ac98d35.png)

微服务架构：

![WeChat16099fb7486aa74bf6f6c13e79467e67.png](img/cdfcdadc0c2a450e869d841d8522e196.png)

* 优点：服务原子化拆分，独立打包、部署和升级，保证每个微服务清晰的任务划分，利于扩展
* 缺点：分布式系统开发的技术成本高（容错、分布式事务等），复杂性更高，每个微服务进行分布式独立部署，当进行模块调用的时候，分布式将会变得更加麻烦。

## 3.Spring Cloud 发展历史

**Spring Cloud netflix -> Spring Cloud Alibaba**

1. 2020-12-22日**Spring** 官方博客宣布，`Spring Cloud 2020.0.0`正式发布。`2020.0.0`是第一个使用新的版本号命名方案的**Spring Cloud** 发行版本。在此之前**Spring Cloud** 使用英国伦敦地铁站的命名方式来命名一个大版本（`train version`），如果不按照新的版本号命名的话，本次的版本号应该是Ilford。
2. 更新版本没有什么大惊小怪的，但是本次更新却正式开启了**Spring Cloud Netflix** 体系的终结进程。**Netflix** 公司是目前微服务落地中最成功的公司。它开源了诸如**Eureka** 、**Hystrix** 、**Zuul** 、**Feign** 、**Ribbon** 等等广大开发者所知微服务套件，统称为**Netflix OSS** 。在当时**Netflix OSS** 成为微服务组件上事实的标准。但是在2018年**Netflix** 公司宣布其核心组件**Hystrix** 、**Ribbon** 、**Zuul** 、**Eureka** 等进入**维护状态** ，不再进行新特性开发，只修BUG。这直接影响了**Spring Cloud** 项目的发展路线，**Spring** 官方不得不采取了应对措施，在2019年的在 **SpringOne 2019** 大会中，**Spring Cloud** 宣布 **Spring Cloud Netflix项目进入维护模式** ，并在2020年移除相关的**Netflix OSS** 组件。
3. Spring Cloud Alibaba 成为了主流

![image.png](img/06e4ffcc7d254c14951de86c71ed0d07.png)

![image.png](https://fynotefile.oss-cn-zhangjiakou.aliyuncs.com/fynote/1396/1631511529000/6462169e77c844ea94afb0ec3ff8e5ff.png)

## 4. Spring Cloud Alibaba

https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md

### 4.1 简介

&emsp;&emsp;Spring Cloud Alibaba 致力于提供微服务开发的一站式解决方案。此项目包含开发分布式应用微服务的必需组件，方便开发者通过 Spring Cloud 编程模型轻松使用这些组件来开发分布式应用服务。

&emsp;&emsp;依托 Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud 应用接入阿里微服务解决方案，通过阿里中间件来迅速搭建分布式应用系统。

### 4.2 主要功能

* **服务限流降级** ：默认支持 WebServlet、WebFlux, OpenFeign、RestTemplate、Spring Cloud Gateway, Zuul, Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。
* **服务注册与发现** ：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。
* **分布式配置管理** ：支持分布式系统中的外部化配置，配置更改时自动刷新。
* **消息驱动能力** ：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。
* **分布式事务** ：使用 @GlobalTransactional 注解， 高效并且对业务零侵入地解决分布式事务问题。
* **阿里云对象存储** ：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
* **分布式任务调度** ：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。
* **阿里云短信服务** ：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

### 4.3 组件介绍

* **[Sentinel]** ：阿里巴巴源产品，把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。
* **[Nacos]** ：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。
* **[RocketMQ]** ：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务。
* **[Dubbo]** ：Apache Dubbo™ 是一款高性能 Java RPC 框架。
* **[Seata]** ：阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案。
* **[Alibaba Cloud OSS]** : 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。
* **[Alibaba Cloud SchedulerX]**: 阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。
* **[Alibaba Cloud SMS]** : 覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。



### 4.3 版本说明

https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E

![image-20231114141441010](../../../../../课程更新录制/15-SpringCloudAlibaba/01-Nacos专题/01-笔记课件/img/image-20231114141441010.png)

课程中使用的版本

- SpringCloud:2020.0.1
- SpringCloudAlibaba:2021.1
- SpringBoot:2.4.2
- NacosServer:2.2.3

# 二、Nacos基础篇

## 1.Nacos介绍

​	https://nacos.io/docs/next/overview/

​	Nacos（Naming Configuration Service） 是一个易于使用的动态服务发现、配置和服务管理平台，用于构建云原生应用程序

​	**服务发现是微服务架构中的关键组件之一**。Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

​	Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以`服务`为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

1. Nacos = **注册中心+配置中心组合**

2. Nacos支持几乎所有主流类型的“服务”的发现、配置和管理，常见的服务如下：

   Kubernetes Service

   gRPC & Dubbo RPC Service

   Spring Cloud RESTful Service



**为什么需要用Nacos**

​	为何使用注册中心呢？我们以入住酒店的前台为例子，稍微加以解释。先设想一个没有服务前台的酒店，客人入住需要自己寻找合适居住的房间，客人不知道每个房间的情况，无法确定那个房间是打扫干净可以入住，客人只能逐个房间寻找，如果遇到已经居住房客的房间一定很尴尬，显然这是不正常的情况。正常的情况是酒店会安排服务台，酒店打扫干净可以入住的房间会登记注册到服务台，这样客人来住店，只需要在前台就可以查找到可以入住的房间，这样就无需等待快速的入住。显然，服务器提供发注册和发现机制可以让房客快速找到合适的房间，快速解决入住问题。

![image-20210915144412020](img/image-20210915144412020.png)



​	采用微服务以后，软件微服务组件各自独立，但最终还要组合为一个整体作为一个软件系统服务于最终客户，这时软件组件之间也需要彼此通讯，彼此调用方法。微服务架构内部发起通讯调用方法的一方成为“**服务消费者**”，提供远程方法调用的服务器称为“**服务提供者**”，往往为了提高系统性能，会提供多个服务器作为**服务提供者**，此时**服务消费者**找到**服务提供者**的过程，就类似于用户在找房间的过程。为了帮助**服务消费者**快速的发现**服务提供者**，在微服务框架中都会引入**注册中心。注册中心**类似于酒店的前台，提供在软件服务的注册和发现功能，**服务提供者**会先在注册中心进行**注册**，声明可以对外提供服务，而**服务消费者**只需要在注册中心就可以快速**发现**找到可以使用的服务，快速使用服务。注册中心实现了服务提供和服务消费的快速撮合功能。

![image-20240727134732457](img/image-20240727134732457.png)

## 2. Nacos的下载和安装

官网网址：https://nacos.io/zh-cn/index.html

![image-20210915113531807](img/image-20210915113531807.png)

官网文档网址：https://nacos.io/zh-cn/docs/quick-start.html

注意：使用官网推荐的稳定版本：

![image-20210915152238853](img/image-20210915152238853.png)

下载地址：https://github.com/alibaba/nacos/releases





下载之后安装过程

1. 解压以后找到bin目录

![image-20210915163508347](img/image-20210915163508347.png)

2. 执行指令：

   ### Linux/Unix/Mac

   启动命令(standalone代表着单机模式运行，非集群模式):

   ```
   sh startup.sh -m standalone
   ```

   ### Windows

   启动命令(standalone代表着单机模式运行，非集群模式):

   ```
   startup.cmd -m standalone
   ```

3. 结果：

   ![image-20231114154204883](img/image-20231114154204883.png)

4. 得到结果以后为了验证是否成功开启Nacos，我们需要访问：http://localhost:8848/nacos

![image-20210915163952277](img/image-20210915163952277.png)

5. 出现此界面表示已经成功启动Nacos，默认的账号密码是：nacos/nacos

![image-20210915164224183](img/image-20210915164224183.png)

6. 到这里就是成功开启了Nacos服务了。



**Nacos持久化配置**

Nacos默认自带嵌入式数据库derby，所以我们每次创建一个Nacos实例就会有一个derby，当有多个Nacos节点的时候，就会出现一致性问题，所以Nacos支持了外部数据库统一数据管理MySql。

官网地址：https://nacos.io/zh-cn/docs/deployment.html

修改application.properties文件。把MySQL的配置放开

![image-20231114211259849](img/image-20231114211259849.png)



找到Nacos安装目录下的conf目录中的Sql脚本，然后在数据库中进行执行，先创建db-nacos的数据库

![image-20231114211536856](img/image-20231114211536856.png)

然后重启服务即可

## 3.注册中心

​	Nacos可以直接提供注册中心（Eureka）+配置中心（Config），所以它的好处显而易见，我们在前面成功安装和启动了Nacos以后就可以发现Nacos本身就是一个小平台，它要比之前的Eureka更加方便，不需要我们在自己做配置。

​	服务发现是微服务架构中的关键组件之一。在这样的架构中，手动为每个客户端配置服务列表可能是一项艰巨的任务，并且使得动态扩展极其困难。Nacos Discovery 帮助您自动将您的服务注册到 Nacos 服务器，Nacos 服务器会跟踪服务并动态刷新服务列表。此外，Nacos Discovery 将服务实例的一些元数据，如主机、端口、健康检查 URL、主页等注册到 Nacos。

​	学习任何知识我们都需要从它的官方文档入手，所以我们直接来看官网给我们提供的文档：https://spring.io/projects/spring-cloud-alibaba#learn

![image-20240727135118646](img/image-20240727135118646.png)



### 3.1 创建父项目

&emsp;&emsp;首先创建一个Maven的父项目。用来聚合管理。并声明了SpringCloud和SpringCloudAlibaba的版本

```xml
<properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
    <spring-cloud-alibaba-version>2021.1</spring-cloud-alibaba-version>
    <spring-cloud.version>2020.0.1</spring-cloud.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>${spring-cloud-alibaba-version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 3.2 服务提供者

&emsp;&emsp;然后创建一个服务提供者的模块。父项目为上面创建的项目，同样是一个SpringBoot项目。pom.xml中的完整依赖信息如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.boge</groupId>
    <artifactId>NacosProviderExample9001</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>NacosProviderExample9001</name>
    <parent>
        <groupId>com.boge</groupId>
        <artifactId>SpringCloudExample</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.4.2</spring-boot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${spring-boot.version}</version>
                <configuration>
                    <mainClass>com.boge.provider.AppProviderStart</mainClass>
                    <skip>true</skip>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>

```

然后在`application.yml`中添加nacos的配置信息

```yml
server:
  port: 9001
spring:
  application:
    name: nacos-provider
  cloud:
    discovery:
      server-addr: 127.0.0.1:8848

management:
  endpoint:
    web:
      exposure:
        include:'*'
```

然后在启动类中添加`@EnableDiscoveryClient`注解

```java
@EnableDiscoveryClient
@SpringBootApplication
public class AppProviderStart {

    public static void main(String[] args) {
        SpringApplication.run(AppProviderStart.class, args);
    }

}
```

然后添加提供的服务接口

```java
@RestController
public class DemoController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/bobo")
    public String getServerPort(){
        return "Hello Nacos Discovery"+serverPort;
    }
}
```

注意需要在父项目中标记：

```
<modules>
    <module>NacosProviderExample9001</module>
</modules>
```

启动服务我们就可以在Nacos中服务列表中看到注册的服务信息

![image-20231114160511315](img/image-20231114160511315.png)

### 3.3 服务消费者

&emsp;&emsp;同样的我们再创建一个服务消费者的SpringBoot项目。并添加相关的依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.boge</groupId>
    <artifactId>NacosConsumerExample9101</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>NacosConsumerExample9101</name>
    <description>Demo project for Spring Boot</description>
    <parent>
        <groupId>com.boge</groupId>
        <artifactId>SpringCloudExample</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.4.2</spring-boot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${spring-boot.version}</version>
                <configuration>
                    <mainClass>com.boge.consumer.AppConsumerStart</mainClass>
                    <skip>true</skip>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>

```

然后在属性文件中添加对应的配置信息

```yml
server:
  port: 9101
spring:
  application:
    name: nacos-consumer
  cloud:
    discovery:
      server-addr: localhost:8848
service-url:
  nacos-user-service: http://nacos-provider
```

同样在父项目中记得添加关系

```xml
<modules>
    <module>NacosProviderExample9001</module>
    <module>NacosConsumerExample9101</module>
</modules>
```

在主启动类中一样添加对应的注解

```java
@EnableDiscoveryClient
@SpringBootApplication
public class AppConsumerStart {

    public static void main(String[] args) {
        SpringApplication.run(AppConsumerStart.class, args);
    }

}
```

启动消费服务后一样可以在Nacos服务列表中看到注册的服务信息

![image-20231114161654623](img/image-20231114161654623.png)



### 3.4 服务调用

&emsp;&emsp;上面我们创建了服务的提供者和服务的消费者。但是都只是完成了服务的nacos注册。并没有串联起来。在我们使用的SpringCloudAlibaba2021版本中移除了`Ribbon`，因为`Ribbon`已经停止更新维护了。使用`loadBalancer`作用新的负载均衡器。我们需要添加新的依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
        </dependency>
```

然后需要配置`RestTemplate`来实现远程调用

```java
@EnableDiscoveryClient
@SpringBootApplication
public class AppConsumerStart {

    public static void main(String[] args) {
        SpringApplication.run(AppConsumerStart.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

}
```

然后在服务调用的接口中的获取RestTemplate对象。并完成相关的逻辑

```java
@RestController
public class DemoController {

    @Autowired
    private RestTemplate restTemplate;

    /**
     * 消费者去访问具体服务，这种写法可以实现
     * 配置文件和代码的分离
     */
    @Value("${service-url.nacos-user-service}")
    private String serverURL;


    @GetMapping(value = "consumer/nacos")
    public String getDiscovery(){
        System.err.println(serverURL);
        return restTemplate.getForObject(serverURL+"/bobo",String.class);
    }
}
```

说明：getForObject方法的参数的含义

**第一个参数url表示被调用的目标Rest接口位置**

		1. url的第一部分是在Nacos中注册的服务提供者名称，如果多个服务提供者注册相同名称，Ribbon会自动寻找其中一个服务提供者，并且调用接口方法。这个就是负载均衡功能。
		2. url后半部是控制器的请求路径。

**第二个参数是返回值类型**

1. JavaBean类型或者JavaBean数组类型，如果控制器返回的是List集合，需要使用数组类型接收。

**第三个参数是可变参数**

1. 是传递给url的动态参数，使用参数时候需要在url上需要使用{1}、{2}、{3}进行参数占位，这样传递的参数就会自动替换占位符。

启动服务后访问测试：http://localhost:9101/consumer/nacos

![image-20231114164016863](img/image-20231114164016863.png)

看到上面的返回信息说明访问成功了。



### 3.5 OpenFeign服务调用

&emsp;&emsp;OpenFegin是一个声明式的服务调用组件。本质上是封装的Ribbon实现的。

![image-20231114195737749](img/image-20231114195737749.png)

然后添加相关的依赖

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```

然后创建对应的Feign接口

```java 
@FeignClient(name = "nacos-provider")
public interface ProviderService {

    @GetMapping(value = "/bobo")
    public String getServerPort();
}
```

然后在启动类中添加`@EnableFeignClients(basePackages = "com.boge.consumer.feign")`注解。指定我们存放Feign接口的位置

```java
@EnableFeignClients(basePackages = "com.boge.consumer.feign")
@EnableDiscoveryClient
@SpringBootApplication
public class AppConsumerStart {

    public static void main(String[] args) {
        SpringApplication.run(AppConsumerStart.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

}
```

然后在controller中通过OpenFeign实现服务的远程调用

```java
@RestController
public class DemoController {


    @Autowired
    private ProviderService providerService;


    @GetMapping(value = "consumer/nacos2")
    public String getDiscovery2(){
        System.err.println("--->"+serverURL);
        return providerService.getServerPort();
    }

}
```

启动服务后访问测试：http://localhost:9101/consumer/nacos2

![image-20231114210101727](img/image-20231114210101727.png)



### 3.6 注册中心对比



| 服务注册与发现框架 | CAP模型 | 控制台管理 | 社区活跃度      |
| ------------------ | ------- | ---------- | --------------- |
| Eureka             | AP      | 支持       | 低(2.x版本闭源) |
| Zookeeper          | CP      | 不支持     | 中              |
| Consul             | CP      | 支持       | 高              |
| Nacos              | AP/CP   | 支持       | 高              |

#### CAP模型

​	计算机专家 埃里克·布鲁尔（Eric Brewer）于 2000 年在 ACM 分布式计算机原理专题讨论会（简称：PODC）中提出的分布式系统设计要考虑的三个核心要素：

- 一致性（Consistency）：同一时刻的同一请求的实例返回的结果相同，所有的数据要求具有强一致性(Strong Consistency)
- 可用性（Availability）：所有实例的读写请求在一定时间内可以得到正确的响应
- 分区容错性（Partition tolerance）：在网络异常（光缆断裂、设备故障、宕机）的情况下，系统仍能提供正常的服务

&emsp;&emsp;以上三个特点就是CAP原则（又称CAP定理），但是三个特性不可能同时满足，所以分布式系统设计要考虑的是在满足P（分区容错性）的前提下选择C（一致性）还是A（可用性），即：CP或AP



![image-20240801131219916](img/image-20240801131219916.png)





#### CP原则

>  一致性 + 分区容错性原则

​	CP 原则属于强一致性原则，要求所有节点可以查询的数据随时都要保持一致（同步中的数据不可查询），即：若干个节点形成一个逻辑的共享区域，某一个节点更新的数据都会立即同步到其他数据节点之中，当数据同步完成后才能返回成功的结果，但是在实际的运行过程中网络故障在所难免，如果此时若干个服务节点之间无法通讯时就会出现错误，从而牺牲了以可用性原则（A），例如关系型数据库中的事务。



#### AP原则

>  可用性原则 + 分区容错性原则

​	AP原则属于弱一致性原则，在集群中只要有存活的节点那么所发送来的所有请求都可以得到正确的响应，在进行数据同步处理操作中即便某些节点没有成功的实现数据同步也返回成功，这样就牺牲一致性原则（C 原则）。

​	使用场景：对于数据的同步一定会发出指令，但是最终的节点是否真的实现了同步，并不保证，可是却可以及时的得到数据更新成功的响应，可以应用在网络环境不是很好的场景中。



#### Nacos的AP/CP

​	Nacos无缝支持一些主流的开源生态，同时再阿里进行Nacos设计的时候重复的考虑到了市场化的运作（市面上大多都是以单一的实现形式为主，例如：Zookeeper使用的是 CP、而 Eureka采用的是AP），在Nacos中提供了两种模式的动态切换。

![image-20231115152651339](img/image-20231115152651339.png)



#### Nacos 何时选择切换模式

> 1. 一般来说，如果不需要储存服务界别的信息且服务实例通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。如Spring Cloud 和 Dubbo，都适用于AP模式，AP模式为了服务的可用性减弱了一致性，因此AP模式下只支持注册`临时实例`。
>
> 2. 如果需要在服务级别编辑或者储存配置信息，那么CP是必须的，K8S服务和DNS服务则是用于CP模式。CP模式下则支持注册`持久化实例`，此时则是以Raft协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。
>
> 3. 切换命令（默认是AP）：
>
>    ```shell
>    curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP'
>    ```
>
> 



注意：临时和持久化的区别主要在`健康检查`失败后的表现，持久化实例健康检查失败后会被标记成不健康，而临时实例会直接从列表中被删除。



## 4.配置中心

### 4.1 基础配置

#### 4.1.1 添加依赖

&emsp;&emsp;我们需要添加Nacos作为配置中心的依赖信息。

```xml
<dependency> 
    <groupId> com.alibaba.cloud </groupId> 
    <artifactId> spring-cloud-starter-alibaba-nacos-config </artifactId> 
</dependency>
```



#### 4.1.2 配置信息

&emsp;&emsp;然后我们需要在`bootstrap.yaml`中配置配置中心的相关信息。注意的是这里我们要配置两个，因为Nacos同SpringCloud-config一样，在项目初始化时，要保证先从配置中心进行配置拉取，拉取配置之后，才能保证项目的正常启动。

​	springboot中配置文件的加载是存在优先级顺序的，bootstrap优先级高于application

​	分别要配置的是，这里bootstrap.yml配置好了以后，作用是两个，第一个让3377这个服务注册到Nacos中，第二个作用就是去Nacos中去读取指定后缀为yaml的配置文件：

```yaml
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
```

application.yml

```yaml
spring:
  profiles:
    active: dev # 表示开发环境
```



#### 4.1.3 主启动类

&emsp;&emsp;在主启动类上添加对应的注解

```java 
@EnableDiscoveryClient
@SpringBootApplication
public class AppConfigStart {

    public static void main(String[] args) {
        SpringApplication.run(AppConfigStart.class, args);
    }

}
```



#### 4.1.4 业务类

​	这里的@RefreshScope实现配置自动更新，意思为如果想要使配置文件中的配置修改后不用重启项目即生效，可以使用@RefreshScope配置来实现

```java
@RestController
@RefreshScope //支持Nacos的动态刷新功能
public class ConfigClientController {

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/config/info")
    public String getConfigInfo(){
        return configInfo;
    }

}
```



#### 4.1.5 Nacos的配置规则

​	在 Nacos Spring Cloud 中，`dataId` 的完整格式如下（详情可以参考官网 https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html）：

```yml
${prefix}-${spring.profiles.active}.${file-extension}
```

> 1. `prefix` 默认为 `spring.application.name` 的值，也可以通过配置项 `spring.cloud.nacos.config.prefix`来配置。
> 2. `spring.profiles.active` 即为当前环境对应的 profile，注意：**当 `spring.profiles.active` 为空时，对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 `${prefix}.${file-extension}`**（不能删除）
> 3. `file-exetension` 为配置内容的数据格式，可以通过配置项 `spring.cloud.nacos.config.file-extension` 来配置。目前只支持 `properties` 和 `yaml` 类型。
> 4. 通过 Spring Cloud 原生注解 `@RefreshScope` 实现配置自动更新：
> 5. 所以根据官方给出的规则我们最终需要在Nacos配置中心添加的配置文件的名字规则和名字为：
>
> ${spring.application.name}-${spring.profiles.active}.${file-extension}
>
> nacos-config-client-dev.yaml
>
> 微服务名称-当前环境-文件格式



![image-20231123144003276](img/image-20231123144003276.png)



#### 4.1.6 配置创建

&emsp;&emsp;然后在nacos中添加相关的配置信息

![image-20231123144100805](img/image-20231123144100805.png)

![image-20231123144131524](img/image-20231123144131524.png)

```yaml
config: 
    info: nacos config center,version = 1
```

然后在配置中心就会看到刚刚发布的配置

![image-20231123144230836](img/image-20231123144230836.png)



#### 4.1.7 自动配置更新

&emsp;&emsp;修改Nacos配置，不需要重启项目即可自动刷新.

![image-20231123144304419](img/image-20231123144304419.png)

修改版本号为2，点击发布

![image-20231123144320948](img/image-20231123144320948.png)

测试访问即可，注意在SpringBoot2.4之后需要显示的添加如下的依赖。bootstrap.yaml 才会生效

```xml
<!-- SpringBoot2.4之后要让 bootstrap.yaml 文件生效我们需要添加该依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

### 4.2.综合应用

#### 4.2.1 DataID

&emsp;&emsp;在实际的开发中我们需要面对各种不同的环境。比如开发环境。测试环境和生产环境。不同的环境下我们的配置信息是有差异的。这时我们就可以通过`spring.profile.active`来指定不同的环境来对应不同的配置文件。

比如我们上面的案例是`dev`开发环境。那么对应的配置文件的名称就是`nacos-config-client-dev.yaml`.然后我们把对应的环境切换到`test`测试环境。那么对应的配置文件的名称就是`nacos-config-client-test.yaml`.然后我们在nacos中创建该配置文件

![image-20231130141249298](img/image-20231130141249298.png)



![image-20231130141307779](img/image-20231130141307779.png)

项目的`spring.profile.active`也需要变动下

![image-20231130141346340](img/image-20231130141346340.png)

然后测试访问

![image-20231130141410055](img/image-20231130141410055.png)



#### 4.2.2 Group

&emsp;&emsp;上面我们通过DataID来配置我们的配置信息。这种场景是把项目中的所有信息都配置在了一个配置文件中。这个会造成配置信息非常多。不便于管理这时我们可以根据配置信息的类别通过`Group`来对配置信息进行分组。我们可以把配置信息中的`MyBatis`的和`数据库`的相关配置信息都单独的区分出来。在创建配置文件的时候指定分组即可。我们前面创建的使用的是默认分组`DEFAULT_GROUP`.

`MYBATIS`组： 注意：DataID还是一致的

![image-20231130142727152](img/image-20231130142727152.png)



`DB`组

![image-20231130142753954](img/image-20231130142753954.png)

然后在项目中我们指定对应的分组即可

![image-20231130143513964](img/image-20231130143513964.png)

测试访问即可：

![image-20231130143558773](img/image-20231130143558773.png)

#### 4.2.3 namespace

&emsp;&emsp; 上面的`group`方案可以基于配置信息的内容做划分。但是在实际的项目环境中。项目环境是比较复杂的。不同的项目不同的模块会有很多相似或者交叉的。这时我们可以通过`namespace`来实现环境之间的隔离。

&emsp;&emsp;namespace默认是`public`,这个我们不能删除。我们直接新建两个namespace： OA和CRM
![image-20231130144037938](img/image-20231130144037938.png)

![image-20231130144121651](img/image-20231130144121651.png)

上面的命名空间的ID我们是用的自动生成的即可。

在CRM的命名空间下创建配置文件

![image-20231130144714590](img/image-20231130144714590.png)

![image-20231130144651577](img/image-20231130144651577.png)

然后在OA的命名空间下创建配置文件

![image-20231130144824858](img/image-20231130144824858.png)

然后在代码中完成相关的配置

![image-20231130144903476](img/image-20231130144903476.png)

然后在`bootstrap.yaml`中配置命名空间

```yaml
server:
  port: 7777
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        group: MYBATIS # 指定group
        namespace: 4445ef5b-1851-40ee-851d-8a811de16134 # 指定 namespace
```

测试即可看到期望的结果：

![image-20231130145122701](img/image-20231130145122701.png)



#### 4.2.4 配置拆分

&emsp;&emsp;上面的讲解中我们的`DataID`还是统一的。我们可以通过`group`的方式来拆分不同的配置信息。当然我们还可以通过`配置拆分`的形式。我们定义不同类型的配置文件。比如：

![image-20231130150855215](img/image-20231130150855215.png)

然后在项目中我们特别指导改配置信息即可

```yaml
server:
  port: 7777
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        # group: prod # 指定group
        namespace: 4445ef5b-1851-40ee-851d-8a811de16134 # 指定 namespace
        # 可以配置多个配置集
        extension-configs[0]: # 配置拆解
          data-id: mybatis-config.yaml
          group: prod
          refresh: true
```

访问测试：

![image-20231130151021934](img/image-20231130151021934.png)

搞定！



#### 4.2.5 三者的关系

&emsp;&emsp;命名空间（Namespace）、配置分组（Group）和配置集ID（Data ID）是Nacos中的三个概念，用于进行资源管理和配置管理。

它们的区别和作用如下：

    命名空间（Namespace）：
        定义：用于隔离和管理不同的资源，每个命名空间有唯一的标识符。
        作用：资源隔离、权限控制、环境隔离。
    
    配置分组（Group）：
        定义：用于对配置进行分类管理，每个配置分组有唯一的标识符。
        作用：配置分类、配置隔离、动态刷新。
    
    配置集ID（Data ID）：
        定义：用于唯一标识一个配置集，每个配置集有唯一的Data ID。
        作用：配置集的唯一标识、配置集的分类管理、配置集的动态刷新。

&emsp;&emsp;在使用Nacos时，可以根据具体的需求和场景，合理使用命名空间、配置分组和配置集ID来进行资源和配置的管理。



## 5.Nacos集群配置

### 5.1 集群结构

&emsp;&emsp;Nacos的集群环境我们采用这种结构：3个Nacos注册中心+1个MySql



![image-20231130205426131](img/image-20231130205426131.png)

### 5.2 Nacos集群

&emsp;&emsp;我们在windows上安装3个Nacos节点。分配配置相关信息

application.properties: 持久化数据到mysql中

![image-20231201144027169](../../../../../课程更新录制/15-SpringCloudAlibaba/01-Nacos专题/01-笔记课件/img/image-20231201144027169.png)

修改 `cluster.conf.example`为`cluster.conf`然后在里面写上相关的集群节点信息

```conf
#2023-12-01T14:34:17.038
192.168.1.111:8848
192.168.1.111:8868
192.168.1.111:8888
```

这里注意，我们在修改端口的时候一定要有一定的偏移量，因为Nacos2.0本身新增了占用端口，所以我们在设置端口号的时候注意要避开，不要占用端口，以下是官网截图：

![image-20231201144157965](img/image-20231201144157965.png)

三个节点服务。注意不要放在有中文的目录下

![image-20231201144236408](img/image-20231201144236408.png)

然后分配启动三个服务即可

![image-20231201144304539](img/image-20231201144304539.png)

然后分配登录测试

![image-20231201144341843](img/image-20231201144341843.png)

然后把我们前面写的服务提供者和服务消费者注册到nacos中

![image-20231201144423285](img/image-20231201144423285.png)

然后可以看到相关的注册信息

![image-20231201144542742](img/image-20231201144542742.png)



### 5.3 Nginx负载均衡

&emsp;&emsp;上面我们配置了3个nacos的服务节点。具体调用哪个节点来注册我们可以通过nginx更好的来实现管理。我们下载个windows 版本的nginx：http://nginx.org/en/download.html

![image-20231201202050803](img/image-20231201202050803.png)

修改nginx.conf文件。添加`负载均衡`的配置

```conf
#user  nobody;
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;

    keepalive_timeout  65;
	
	upstream nacos {
		server 192.168.1.111:8848;
		server 192.168.1.111:8868;
		server 192.168.1.111:8888;
	}

    #gzip  on;

    server {
        listen       80;
        server_name  192.168.1.111;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass http://nacos;
        }
    }
}

```

然后通过`start nginx` 来启动nginx服务

![image-20231201202242174](img/image-20231201202242174.png)

然后启动成功。同时我们可以在服务中也通过nginx来负载。

![image-20231201202310669](img/image-20231201202310669.png)

启动后发现服务也正常的注册成功了

![image-20231201202341382](img/image-20231201202341382.png)



# 三、Nacos源码篇

前置的基础知识：

1. SpringBoot自动装配原理
2. Spring的IOC、AOP这些核心知识
3. 相关的设计模式的基础
4. Java核心基础知识



## 1.环境准备

### 1.1 服务端构建

​	为了更方便的调试源码信息。我们可以从官方GitHub中下载下来服务端的源码信息。在本地编译后启动服务即可。

https://github.com/alibaba/nacos/tree/2.2.3

#### 1.1.1 下载源码

​	下载源码到本地。然后解压到非中文的目录下

![image-20240328130444870](img/image-20240328130444870.png)

​	然后在目录下找到对应的编译命令

![image-20240328130616186](img/image-20240328130616186.png)

​	解压进入目录中进行maven编译

```pom
mvn -Prelease-nacos -Dmaven.test.skip=true clean install -U
```

![image-20240328130707753](img/image-20240328130707753.png)

#### 1.1.2 启动Nacos服务

​	编译成功后我们就可以通过IDEA来打开nacos的源码。找到`nacos-console`服务启动

![image-20240328130821680](img/image-20240328130821680.png)

​	第一次启动会报错我们需要设置下单机的启动模式：-Dnacos.standalone=true

![image-20240328130902417](img/image-20240328130902417.png)

​	到此源码编译的版本就启动成功了。我们的服务注册和发现就可以使用这个服务来完成了

![image-20240412142025574](img/image-20240412142025574.png)

### 1.2 服务提供者

​	服务提供者的案例代码还是用的之前讲解Nacos应用的时候创建的案例。版本关系如下：

* SpringCloudAlibaba: 2021.1
* SpringCloud: 2020.0.1
* SpringBoot: 2.4.2



相关的配置

```yaml
server:
  port: 9001

spring:
  application:
    name: nacos-provider
  cloud:
    discovery:
      server-addr: 192.168.1.111:8848

management:
  endpoint:
    web:
      exposure:
        include:'*'
```

启动服务后正常注册到Nacos配置中心中

![image-20240412142353091](../../../../../课程更新录制/15-SpringCloudAlibaba/源码篇课程/01-Nacos/01-课件资料/img/image-20240412142353091.png)



### 1.3 服务消费者

​	服务消费者和服务提供者一样，版本信息相同

* SpringCloudAlibaba: 2021.1
* SpringCloud: 2020.0.1
* SpringBoot: 2.4.2

配置信息如下

```yaml
server:
  port: 9101
spring:
  application:
    name: nacos-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

management:
  endpoint:
    web:
      exposure:
        include:'*'
        
service-url:
  nacos-user-service: http://nacos-provider
```

在启动类中添加Ribbon的配置

```java
@EnableDiscoveryClient
@SpringBootApplication
public class NacosConsumer9101Application {

    public static void main(String[] args) {
        SpringApplication.run(NacosConsumer9101Application.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

​	对应的控制器的业务

```java
@RestController
public class UserController {

    @Value("${service-url.nacos-user-service}")
    private String serverURL;

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/helloConsumer")
    public String helloProvider(){
        System.out.println("------消费者接口执行了-----");
        // 通过RestTemplate 实现 服务的发现和调用
        return restTemplate.getForObject(serverURL+"/hello",String.class);
    }
}
```

​	启动服务消费者后。消费者首先也会在nacos中注册的

![image-20240412143321843](img/image-20240412143321843.png)

​	同时可以完成服务消费者和服务提供者的调用关系：

![image-20240412144024394](img/image-20240412144024394.png)

### 1.4 配置中心

​	配置中心的案例同样使用的是前面应用课程中使用的代码。版本和上面的是一样的。

需要在添加配置中心的依赖

```xml
 <dependency>
    <groupId> com.alibaba.cloud </groupId>
    <artifactId> spring-cloud-starter-alibaba-nacos-config </artifactId>
</dependency>
```

相关案例代码在课程附件资料中获取

## 2. 注册中心

### 2.1 服务注册原理

#### 2.1.1 服务提供者

​	首先我们来看看Nacos的服务提供者的服务注册的逻辑。这块我们需要根据`spring-cloud-starter-alibaba-nacos-discovery:2021.1`中的`spring.factories`中提供的配置类`NacosServiceRegistryAutoConfiguration` 

![image-20240412150015617](img/image-20240412150015617.png)

进来后找到`NacosServiceRegistry` 的注入方法

```java
    @Bean
    public NacosServiceRegistry nacosServiceRegistry(NacosDiscoveryProperties nacosDiscoveryProperties) {
        // NacosServiceRegistry 对象会完成 服务提供者服务的注册功能
        return new NacosServiceRegistry(nacosDiscoveryProperties);
    }
```

进入`NacosServiceRegistry`中找到`register`方法。这个是注册功能的核心方法

```java
    public void register(Registration registration) {
        if (StringUtils.isEmpty(registration.getServiceId())) {
            log.warn("No service to register for nacos client...");
        } else {
            // 获取需要注册的服务的相关信息
            NamingService namingService = this.namingService();
            String serviceId = registration.getServiceId();
            String group = this.nacosDiscoveryProperties.getGroup();
            // 完成需要注册实例对象的封装
            Instance instance = this.getNacosInstanceFromRegistration(registration);

            try {
                namingService.registerInstance(serviceId, group, instance);
                log.info("nacos registry, {} {} {}:{} register finished", new Object[]{group, serviceId, instance.getIp(), instance.getPort()});
            } catch (Exception var7) {
                log.error("nacos registry, {} register failed...{},", new Object[]{serviceId, registration.toString(), var7});
                ReflectionUtils.rethrowRuntimeException(var7);
            }

        }
    }
```

可以先进入到`getNacosInstanceFromRegistration`中看看 需要注册实例对象的封装

```java 
    private Instance getNacosInstanceFromRegistration(Registration registration) {
        Instance instance = new Instance();
        instance.setIp(registration.getHost()); // IP
        instance.setPort(registration.getPort()); // 端口
        // 服务的权重
        instance.setWeight((double)this.nacosDiscoveryProperties.getWeight());
        // 集群名称
        instance.setClusterName(this.nacosDiscoveryProperties.getClusterName());
        // 服务是否启用
        instance.setEnabled(this.nacosDiscoveryProperties.isInstanceEnabled());
        // 服务相关的元数据
        instance.setMetadata(registration.getMetadata());
        // 设置否临时实例
        instance.setEphemeral(this.nacosDiscoveryProperties.isEphemeral());
        return instance;
    }
```

再回到`NacosServiceRegistry`中查看`namingService.registerInstance(...)`的执行

```java
@Override
public void registerInstance(String serviceName, String groupName, Instance instance) throws NacosException {
    // 检测实例是否合法  代码比较简单 进去看看即可
    NamingUtils.checkInstanceIsLegal(instance);
    // groupName@@serviceName 把这两个信息通过@@组合成一个姓的字符串
    String groupedServiceName = NamingUtils.getGroupedName(serviceName, groupName);
    // 如果是临时实例
    if (instance.isEphemeral()) {
        // 构建心跳服务的对象
        BeatInfo beatInfo = beatReactor.buildBeatInfo(groupedServiceName, instance);
        // 会开启定期发送心跳服务的定时任务
        beatReactor.addBeatInfo(groupedServiceName, beatInfo);
    }
    // 通过代理对象来注册服务
    serverProxy.registerService(groupedServiceName, groupName, instance);
}
```

​	在上面的方法中针对当前服务的类型做出不同的操作，临时实例会发送心跳服务。而持久化的实例就不会发起心跳服务了，进入`serverProxy.registerService(..)`继续查看

```java
    public void registerService(String serviceName, String groupName, Instance instance) throws NacosException {
        
        NAMING_LOGGER.info("[REGISTER-SERVICE] {} registering service {} with instance: {}", namespaceId, serviceName,
                instance);
        // 1. 需要注册到Nacos的为什么位置 namespaceId serviceName groupName 由这三个来确定
        // 2. 服务提供者的信息 ip port weight ....
        final Map<String, String> params = new HashMap<String, String>(16);
        params.put(CommonParams.NAMESPACE_ID, namespaceId);
        params.put(CommonParams.SERVICE_NAME, serviceName);
        params.put(CommonParams.GROUP_NAME, groupName);
        params.put(CommonParams.CLUSTER_NAME, instance.getClusterName());
        params.put("ip", instance.getIp());
        params.put("port", String.valueOf(instance.getPort()));
        params.put("weight", String.valueOf(instance.getWeight()));
        params.put("enable", String.valueOf(instance.isEnabled()));
        params.put("healthy", String.valueOf(instance.isHealthy()));
        params.put("ephemeral", String.valueOf(instance.isEphemeral()));
        params.put("metadata", JacksonUtils.toJson(instance.getMetadata()));
        // 发起服务 UtilAndComs.nacosUrlInstance 获取服务端接受服务注册的接口
        // /nacos/v1/ns/instance  POST方式提交
        reqApi(UtilAndComs.nacosUrlInstance, params, HttpMethod.POST);
        
    }
```

获取的接口地址: /nacos/v1/ns/instance

![image-20240412163341888](img/image-20240412163341888.png)

​	再进去跟踪`reqApi`方法就是具体的服务发送的相关的功能了

![image-20240412163538093](img/image-20240412163538093.png)

​	这块就自行跟踪。

#### 2.1.2 服务端注册

​	我们在上面可以看到服务提供者发起请求访问的是服务端的这个地址`/nacos/v1/ns/instance`,那么对应的我们就来看看这个方法是怎么处理请求服务注册的。以及最终的服务信息是保存在哪的。处理这个请求的方法在`nacos-naming`的controllers 中。

![image-20240412163846431](img/image-20240412163846431.png)

在这个方法中会先获取客户端提供的相关的服务信息，获取`namespaceId`,`serviceName`同时会检测service的格式`defaultName@@serviceName`和客户端处理形成对应，然后获取需要注册的服务对象信息`Instance`.

在`getInstanceOperator().registerInstance(namespaceId, serviceName, instance)`中会完成具体的注册功能。当然`客户端服务健康检测`也在这里。

```java
    @Override
    public void registerInstance(String namespaceId, String serviceName, Instance instance) throws NacosException {
        // 检测 instance 的合法性
        NamingUtils.checkInstanceIsLegal(instance);
        // 实例的状态  临时 或者 永久
        boolean ephemeral = instance.isEphemeral();
        // 获取客户端Id  address+'#'+ ephemeral
        String clientId = IpPortBasedClient.getClientId(instance.toInetAddr(), ephemeral);
        // 当服务提供者注册服务后要做的如何做服务的心态检测
        createIpPortClientIfAbsent(clientId);
        // Service 就是我们要注册的服务
        Service service = getService(namespaceId, serviceName, ephemeral);
        clientOperationService.registerInstance(service, instance, clientId);
    }
```

createIpPortClientIfAbsent(clientId)：这个方法在服务健康检测的时候需要关注。

clientOperationService.registerInstance(service, instance, clientId)：做客户端实例的注册操作，先进入`ClientOperationServiceProxy`判断当前实例的状态

```java
private ClientOperationService chooseClientOperationService(final Instance instance) {
    return instance.isEphemeral() ? ephemeralClientOperationService : persistentClientOperationService;
}
```

进入临时实例的处理，ephemeralClientOperationService

```java
    @Override
    public void registerInstance(Service service, Instance instance, String clientId) throws NacosException {
        NamingUtils.checkInstanceIsLegal(instance);
        // 获取Service 对象
        Service singleton = ServiceManager.getInstance().getSingleton(service);
        if (!singleton.isEphemeral()) {
            throw new NacosRuntimeException(NacosException.INVALID_PARAM,
                    String.format("Current service %s is persistent service, can't register ephemeral instance.",
                            singleton.getGroupedServiceName()));
        }
        Client client = clientManager.getClient(clientId);
        // 判断合法性
        if (!clientIsLegal(client, clientId)) {
            return;
        }
        // 需要 注册的信息
        InstancePublishInfo instanceInfo = getPublishInfo(instance);
        client.addServiceInstance(singleton, instanceInfo);
        client.setLastUpdatedTime();
        client.recalculateRevision();
        // 发布一个 客户端的注册事件
        NotifyCenter.publishEvent(new ClientOperationEvent.ClientRegisterServiceEvent(singleton, clientId));
        NotifyCenter
                .publishEvent(new MetadataEvent.InstanceMetadataEvent(singleton, instanceInfo.getMetadataId(), false));
    }
```

上面的方法封装了需要注册的信息，然后发布了一个客户端的注册事件。然后找到对应的事件处理器`ClientServiceIndexesManager`.进入到对应的`onEvent(...)`方法中

```java
    @Override
    public void onEvent(Event event) {
        if (event instanceof ClientEvent.ClientDisconnectEvent) {
            // 处理客户端断开
            handleClientDisconnect((ClientEvent.ClientDisconnectEvent) event);
        } else if (event instanceof ClientOperationEvent) {
            // 处理客户端操作
            handleClientOperation((ClientOperationEvent) event);
        }
    }
```

对应的是`ClientOperationEvent`事件。进入后看到了我们熟悉的事件

```java
    private void handleClientOperation(ClientOperationEvent event) {
        Service service = event.getService();
        String clientId = event.getClientId();
        if (event instanceof ClientOperationEvent.ClientRegisterServiceEvent) {
            // 这个就是处理 客户端 注册的事件
            addPublisherIndexes(service, clientId);
        } else if (event instanceof ClientOperationEvent.ClientDeregisterServiceEvent) {
            removePublisherIndexes(service, clientId);
        } else if (event instanceof ClientOperationEvent.ClientSubscribeServiceEvent) {
            addSubscriberIndexes(service, clientId);
        } else if (event instanceof ClientOperationEvent.ClientUnsubscribeServiceEvent) {
            removeSubscriberIndexes(service, clientId);
        }
    }
```

addPublisherIndexes的处理逻辑

```java
// 注册的服务会注册在这个 ConcurrentMap 中
private final ConcurrentMap<Service, Set<String>> publisherIndexes = new ConcurrentHashMap<>();

private void addPublisherIndexes(Service service, String clientId) {
        // publisherIndexes 存储了需要注册的服务信息
        publisherIndexes.computeIfAbsent(service, key -> new ConcurrentHashSet<>());
        publisherIndexes.get(service).add(clientId);
    	// 发布一个 服务改变的 事件 
        NotifyCenter.publishEvent(new ServiceEvent.ServiceChangedEvent(service, true));
    }
```

​	到这儿我们就可以看到注册的服务是保存在`publisherIndexes`中。同时会发布一个服务改变的事件。这个就和其他的功能模块串联起来了。



### 2.2 心跳服务原理

​	Nacos中针对临时实例是通过心跳检测服务来确认服务是否可用的。在服务提供者注册服务之前会做一个服务的类型检测。如果是临时服务就会开启定时任务。定期发送心跳服务给Nacos服务端。

NacosNamingService中的方法

```Java
@Override
public void registerInstance(String serviceName, String groupName, Instance instance) throws NacosException {
    NamingUtils.checkInstanceIsLegal(instance);
    String groupedServiceName = NamingUtils.getGroupedName(serviceName, groupName);
    // 这块做 实例类型的判断
    if (instance.isEphemeral()) {
        // 构建需要发送的心跳服务的信息
        BeatInfo beatInfo = beatReactor.buildBeatInfo(groupedServiceName, instance);
        // 在这个方法中会完成定时任务的处理
        beatReactor.addBeatInfo(groupedServiceName, beatInfo);
    }
    serverProxy.registerService(groupedServiceName, groupName, instance);
}
```

进入到`addBeatInfo`方法中查看

```Java
public void addBeatInfo(String serviceName, BeatInfo beatInfo) {
    NAMING_LOGGER.info("[BEAT] adding beat: {} to beat map.", beatInfo);
    String key = buildKey(serviceName, beatInfo.getIp(), beatInfo.getPort());
    BeatInfo existBeat = null;
    //fix #1733
    if ((existBeat = dom2Beat.remove(key)) != null) {
        existBeat.setStopped(true);
    }
    dom2Beat.put(key, beatInfo);
    // 这个是关键。开启了一个定时任务。
    executorService.schedule(new BeatTask(beatInfo), beatInfo.getPeriod(), TimeUnit.MILLISECONDS);
    MetricsMonitor.getDom2BeatSizeMonitor().set(dom2Beat.size());
}
```

在上面开启了一个间隔15秒的定时任务。beatInfo.getPeriod()这块在前面获取BeatInfo的时候有设置

![image-20240806155123805](img/image-20240806155123805.png)

![image-20240806155139155](img/image-20240806155139155.png)

调度执行的任务是`BeatTask`.所以直接进入这个类中。找到`run`方法

```java
@Override
        public void run() {
            // 状态判断
            if (beatInfo.isStopped()) {
                return;
            }
            // 获取下次发送心跳服务的时间
            long nextTime = beatInfo.getPeriod();
            try {
                // 发送心跳服务，和前面注册服务的逻辑一样
                JsonNode result = serverProxy.sendBeat(beatInfo, BeatReactor.this.lightBeatEnabled);
                // 获取心跳服务的间隔时间
                long interval = result.get("clientBeatInterval").asLong();
                boolean lightBeatEnabled = false;
                if (result.has(CommonParams.LIGHT_BEAT_ENABLED)) {
                    lightBeatEnabled = result.get(CommonParams.LIGHT_BEAT_ENABLED).asBoolean();
                }
                BeatReactor.this.lightBeatEnabled = lightBeatEnabled;
                if (interval > 0) {
                    nextTime = interval;
                }
                // 心跳服务的状态码
                int code = NamingResponseCode.OK;
                if (result.has(CommonParams.CODE)) {
                    code = result.get(CommonParams.CODE).asInt();
                }
                // 如果心跳服务发送 响应 没有找到服务 则重新发起服务注册操作
                if (code == NamingResponseCode.RESOURCE_NOT_FOUND) {
                    Instance instance = new Instance();
                    instance.setPort(beatInfo.getPort());
                    instance.setIp(beatInfo.getIp());
                    instance.setWeight(beatInfo.getWeight());
                    instance.setMetadata(beatInfo.getMetadata());
                    instance.setClusterName(beatInfo.getCluster());
                    instance.setServiceName(beatInfo.getServiceName());
                    instance.setInstanceId(instance.getInstanceId());
                    instance.setEphemeral(true);
                    try {
                        // 注册服务
                        serverProxy.registerService(beatInfo.getServiceName(),
                                NamingUtils.getGroupName(beatInfo.getServiceName()), instance);
                    } catch (Exception ignore) {
                    }
                }
            } catch (NacosException ex) {
                NAMING_LOGGER.error("[CLIENT-BEAT] failed to send beat: {}, code: {}, msg: {}",
                        JacksonUtils.toJson(beatInfo), ex.getErrCode(), ex.getErrMsg());
                
            }
            // 继续 下一次的心跳服务 调度
            executorService.schedule(new BeatTask(beatInfo), nextTime, TimeUnit.MILLISECONDS);
        }
    }
```

​	到这儿就是服务提供者做的心跳服务的处理。同时发送的心跳服务的服务器的访问接口是`/nacos/v1/ns/instance/beat`.

![image-20240806161156162](img/image-20240806161156162.png)

​	然后我们来看下服务端是怎么接收并处理心跳服务的。在服务端的`InstanceController`中的`beat`方法

```java
@CanDistro
    @PutMapping("/beat")
    @Secured(action = ActionTypes.WRITE)
    public ObjectNode beat(HttpServletRequest request) throws Exception {
        
        ObjectNode result = JacksonUtils.createEmptyJsonNode();
        result.put(SwitchEntry.CLIENT_BEAT_INTERVAL, switchDomain.getClientBeatInterval());
        
        String beat = WebUtils.optional(request, "beat", StringUtils.EMPTY);
        RsInfo clientBeat = null;
        if (StringUtils.isNotBlank(beat)) {
            clientBeat = JacksonUtils.toObj(beat, RsInfo.class);
        }
        String clusterName = WebUtils
                .optional(request, CommonParams.CLUSTER_NAME, UtilsAndCommons.DEFAULT_CLUSTER_NAME);
        String ip = WebUtils.optional(request, "ip", StringUtils.EMPTY);
        int port = Integer.parseInt(WebUtils.optional(request, "port", "0"));
        if (clientBeat != null) {
            if (StringUtils.isNotBlank(clientBeat.getCluster())) {
                clusterName = clientBeat.getCluster();
            } else {
                // fix #2533
                clientBeat.setCluster(clusterName);
            }
            ip = clientBeat.getIp();
            port = clientBeat.getPort();
        }
        String namespaceId = WebUtils.optional(request, CommonParams.NAMESPACE_ID, Constants.DEFAULT_NAMESPACE_ID);
        String serviceName = WebUtils.required(request, CommonParams.SERVICE_NAME);
        NamingUtils.checkServiceNameFormat(serviceName);
        Loggers.SRV_LOG.debug("[CLIENT-BEAT] full arguments: beat: {}, serviceName: {}, namespaceId: {}", clientBeat,
                serviceName, namespaceId);
        BeatInfoInstanceBuilder builder = BeatInfoInstanceBuilder.newBuilder();
        builder.setRequest(request);
        // 处理客户端提交的心跳服务
        int resultCode = getInstanceOperator()
                .handleBeat(namespaceId, serviceName, ip, port, clusterName, clientBeat, builder);
        result.put(CommonParams.CODE, resultCode);
        result.put(SwitchEntry.CLIENT_BEAT_INTERVAL,
                getInstanceOperator().getHeartBeatInterval(namespaceId, serviceName, ip, port, clusterName));
        result.put(SwitchEntry.LIGHT_BEAT_ENABLED, switchDomain.isLightBeatEnabled());
        return result;
    }
```

上面代码中的核心是`getInstanceOperator().handleBeat(...)`方法。

```java
@Override
    public int handleBeat(String namespaceId, String serviceName, String ip, int port, String cluster,
            RsInfo clientBeat, BeatInfoInstanceBuilder builder) throws NacosException {
        Service service = getService(namespaceId, serviceName, true);
        String clientId = IpPortBasedClient.getClientId(ip + InternetAddressUtil.IP_PORT_SPLITER + port, true);
        IpPortBasedClient client = (IpPortBasedClient) clientManager.getClient(clientId);
        // 如果 客户端 为空 基于心跳提供的信息创建新的 服务来注册
        if (null == client || !client.getAllPublishedService().contains(service)) {
            if (null == clientBeat) {
                // 心跳服务提供的 服务 在 服务列表中没有找到
                return NamingResponseCode.RESOURCE_NOT_FOUND;
            }
            Instance instance = builder.setBeatInfo(clientBeat).setServiceName(serviceName).build();
            registerInstance(namespaceId, serviceName, instance);
            client = (IpPortBasedClient) clientManager.getClient(clientId);
        }
        if (!ServiceManager.getInstance().containSingleton(service)) {
            throw new NacosException(NacosException.SERVER_ERROR,
                    "service not found: " + serviceName + "@" + namespaceId);
        }
        if (null == clientBeat) {
            // 构建新的 ClientBeat对象
            clientBeat = new RsInfo();
            clientBeat.setIp(ip);
            clientBeat.setPort(port);
            clientBeat.setCluster(cluster);
            clientBeat.setServiceName(serviceName);
        }
        ClientBeatProcessorV2 beatProcessor = new ClientBeatProcessorV2(namespaceId, clientBeat, client);
        // 处理心跳信息
        HealthCheckReactor.scheduleNow(beatProcessor);
        client.setLastUpdatedTime();
        return NamingResponseCode.OK;
    }
```

处理心跳服务的方法是`HealthCheckReactor.scheduleNow(beatProcessor)`方法。核心是`beatProcessor`对象中的`run`方法。

```java
@Override
    public void run() {
        if (Loggers.EVT_LOG.isDebugEnabled()) {
            Loggers.EVT_LOG.debug("[CLIENT-BEAT] processing beat: {}", rsInfo.toString());
        }
        String ip = rsInfo.getIp();
        int port = rsInfo.getPort();
        String serviceName = NamingUtils.getServiceName(rsInfo.getServiceName());
        String groupName = NamingUtils.getGroupName(rsInfo.getServiceName());
        Service service = Service.newService(namespace, groupName, serviceName, rsInfo.isEphemeral());
        HealthCheckInstancePublishInfo instance = (HealthCheckInstancePublishInfo) client.getInstancePublishInfo(service);
        // 检查 心跳提供的服务 和 找到的 注册的服务信息是否一致
        if (instance.getIp().equals(ip) && instance.getPort() == port) {
            if (Loggers.EVT_LOG.isDebugEnabled()) {
                Loggers.EVT_LOG.debug("[CLIENT-BEAT] refresh beat: {}", rsInfo);
            }
            instance.setLastHeartBeatTime(System.currentTimeMillis());
            if (!instance.isHealthy()) { // 如果服务不是健康的
                instance.setHealthy(true); // 更新状态为健康的
                Loggers.EVT_LOG.info("service: {} {POS} {IP-ENABLED} valid: {}:{}@{}, region: {}, msg: client beat ok",
                        rsInfo.getServiceName(), ip, port, rsInfo.getCluster(), UtilsAndCommons.LOCALHOST_SITE);
                NotifyCenter.publishEvent(new ServiceEvent.ServiceChangedEvent(service)); // 发布服务状态改变事件
                NotifyCenter.publishEvent(new ClientEvent.ClientChangedEvent(client)); // 发布客户端状态改变事件
                // 发布 服务健康状态改变事件
                NotifyCenter.publishEvent(new HealthStateChangeTraceEvent(System.currentTimeMillis(),
                        service.getNamespace(), service.getGroup(), service.getName(), instance.getIp(),
                        instance.getPort(), true, "client_beat"));
            }
        }
    }
```

​	上面的逻辑也很简单。如果服务是一致的同时如果服务在服务端已经标注为不健康的情况下会修正服务为健康状态。同时会发布相关的事件。



### 2.3 注册服务健康检查

​	服务在注册的时候也会完成服务的健康检查。这块的具体路径我们来看看。

 

```Java
@Override
public void registerInstance(String namespaceId, String serviceName, Instance instance) throws NacosException {
    NamingUtils.checkInstanceIsLegal(instance);
    
    boolean ephemeral = instance.isEphemeral();
    String clientId = IpPortBasedClient.getClientId(instance.toInetAddr(), ephemeral);
    // 当服务提供者注册服务后要做的如何做服务的心态检测
    createIpPortClientIfAbsent(clientId);
    // Service 就是我们要注册的服务
    Service service = getService(namespaceId, serviceName, ephemeral);
    clientOperationService.registerInstance(service, instance, clientId);
}
```

在注册实例的方法中`createIpPortClientIfAbsent`这个方法会完成相关的操作。

```Java
private void createIpPortClientIfAbsent(String clientId) {
    // 如果当前注册的服务 之前没有记录信息
    if (!clientManager.contains(clientId)) {
        ClientAttributes clientAttributes;
        if (ClientAttributesFilter.threadLocalClientAttributes.get() != null) {
            clientAttributes = ClientAttributesFilter.threadLocalClientAttributes.get();
        } else {
            clientAttributes = new ClientAttributes();
        }
        // 测试客户端的连接
        clientManager.clientConnected(clientId, clientAttributes);
    }
}
```

最后的方法完成客户端的连接测试操作。我们直接进入，这里有区分是临时实例还是永久实例的处理

![image-20240806212439982](img/image-20240806212439982.png)

我们以临时实例为例进入

```Java
@Override
public boolean clientConnected(final Client client) {
    clients.computeIfAbsent(client.getClientId(), s -> {
        Loggers.SRV_LOG.info("Client connection {} connect", client.getClientId());
        IpPortBasedClient ipPortBasedClient = (IpPortBasedClient) client;
        // 客户端信息的初始化操作
        ipPortBasedClient.init();
        return ipPortBasedClient;
    });
    return true;
}
```

​	直接进入init方法中

```Java
public void init() {
    // 判断当前服务是临时还是持久的 通过责任链完成的健康检测
    if (ephemeral) {
        beatCheckTask = new ClientBeatCheckTaskV2(this); // 开启一个定时任务 延迟5秒执行 后面间隔5秒 重复执行
        HealthCheckReactor.scheduleCheck(beatCheckTask);
    } else {
        // 持久实例。永久节点的健康检查是由服务端定时与客户端建立tcp连接做健康检查，是服务端主动发起的探测，服定时请求客户端判断是否健康。
        healthCheckTaskV2 = new HealthCheckTaskV2(this);
        HealthCheckReactor.scheduleCheck(healthCheckTaskV2);
    }
}
```

​	在这里会判断实例的类型。以临时实例为例。通过设置了定时器来调度。执行`ClientBeatCheckTaskV2`中的定时任务。

```Java
public static void scheduleCheck(BeatCheckTask task) {
    Runnable wrapperTask =
            task instanceof NacosHealthCheckTask ? new HealthCheckTaskInterceptWrapper((NacosHealthCheckTask) task)
                    : task;
    futureMap.computeIfAbsent(task.taskKey(),
            k -> GlobalExecutor.scheduleNamingHealth(wrapperTask, 5000, 5000, TimeUnit.MILLISECONDS));
}
```

上面开启了一个延迟5秒执行。间隔5秒的定时任务。来执行检查逻辑

```java
    @Override
    public void doHealthCheck() {
        try {
            // 获取到所有的注册的服务信息
            Collection<Service> services = client.getAllPublishedService();
            for (Service each : services) {
                // 遍历每个服务
                HealthCheckInstancePublishInfo instance = (HealthCheckInstancePublishInfo) client
                        .getInstancePublishInfo(each);
                // 在拦截器链路中添加 需要 执行的心跳检测的任务  责任链调用
                interceptorChain.doInterceptor(new InstanceBeatCheckTask(client, each, instance));
            }
        } catch (Exception e) {
            Loggers.SRV_LOG.warn("Exception while processing client beat time out.", e);
        }
    }
```

拦截器链执行的是`InstanceBeatCheckTask`中的方法

```java
static {
        CHECKERS.add(new UnhealthyInstanceChecker()); // 健康检测
        CHECKERS.add(new ExpiredInstanceChecker()); // 过期检测
        CHECKERS.addAll(NacosServiceLoader.load(InstanceBeatChecker.class)); // 自定义的检测器
    }
    
    public InstanceBeatCheckTask(IpPortBasedClient client, Service service, HealthCheckInstancePublishInfo instancePublishInfo) {
        this.client = client;
        this.service = service;
        this.instancePublishInfo = instancePublishInfo;
    }
    
    @Override
    public void passIntercept() {
        for (InstanceBeatChecker each : CHECKERS) {
            // 过期检查 非健康检查 用户自定义实现了InstanceBeatChecker接口的检测。
            each.doCheck(client, service, instancePublishInfo);
        }
    }
```

过期检查、非健康检查，自定义实现都需要处理。非健康检查的逻辑为

```Java
@Override
public void doCheck(Client client, Service service, HealthCheckInstancePublishInfo instance) {
    // 调整实例健康状态
    if (instance.isHealthy() && isUnhealthy(service, instance)) {
        // 调整实例健康状态
        changeHealthyStatus(client, service, instance);
    }
}
```

在`isUnhealthy`方法中实现了处理逻辑

```java
    private boolean isUnhealthy(Service service, HealthCheckInstancePublishInfo instance) {
        // 获取超时时间 默认 15 秒；可通过 preserved.heart.beat.timeout 配置更改
        long beatTimeout = getTimeout(service, instance);
        // 当前时间距离上一次发送心跳包时间  超过了 规定的超时时间  则返回 true,代表节点不健康了
        return System.currentTimeMillis() - instance.getLastHeartBeatTime() > beatTimeout;
    }
```

​	到此临时实例的注册服务的健康检查完成。其实就是比较了上一次发送的心跳时间是否超过了间隔时间15秒。同样对应的持久化的实例的处理我们也可以分析下：

```Java
public void init() {
    // 判断当前服务是临时还是持久的 通过责任链完成的健康检测
    if (ephemeral) {
        beatCheckTask = new ClientBeatCheckTaskV2(this); // 开启一个定时任务 延迟5秒执行 后面间隔5秒 重复执行
        HealthCheckReactor.scheduleCheck(beatCheckTask);
    } else {
        // 持久实例。永久节点的健康检查是由服务端定时与客户端建立tcp连接做健康检查，是服务端主动发起的探测，服定时请求客户端判断是否健康。
        healthCheckTaskV2 = new HealthCheckTaskV2(this);
        HealthCheckReactor.scheduleCheck(healthCheckTaskV2);
    }
}
```

对应的就是通过`HealthCheckTaskV2`这个对象的方法来处理的。

```java
@Override
    public void doHealthCheck() {
        try {
            initIfNecessary();
            // 遍历所有注册的服务
            for (Service each : client.getAllPublishedService()) {
                if (switchDomain.isHealthCheckEnabled(each.getGroupedServiceName())) {
                    InstancePublishInfo instancePublishInfo = client.getInstancePublishInfo(each);
                    ClusterMetadata metadata = getClusterMetadata(each, instancePublishInfo);
                    // 健康检查处理代理类
                    ApplicationUtils.getBean(HealthCheckProcessorV2Delegate.class).process(this, each, metadata);
                    if (Loggers.EVT_LOG.isDebugEnabled()) {
                        Loggers.EVT_LOG.debug("[HEALTH-CHECK] schedule health check task: {}", client.getClientId());
                    }
                }
            }
        } catch (Throwable e) {
            Loggers.SRV_LOG.error("[HEALTH-CHECK] error while process health check for {}", client.getClientId(), e);
        } finally {
            if (!cancelled) {
                initCheckRT();
                HealthCheckReactor.scheduleCheck(this);
                // worst == 0 means never checked
                if (this.getCheckRtWorst() > 0) {
                    // TLog doesn't support float so we must convert it into long
                    long checkRtLastLast = getCheckRtLastLast();
                    this.setCheckRtLastLast(this.getCheckRtLast());
                    if (checkRtLastLast > 0) {
                        long diff = ((this.getCheckRtLast() - this.getCheckRtLastLast()) * 10000) / checkRtLastLast;
                        if (Loggers.CHECK_RT.isDebugEnabled()) {
                            Loggers.CHECK_RT.debug("{}->normalized: {}, worst: {}, best: {}, last: {}, diff: {}",
                                    client.getClientId(), this.getCheckRtNormalized(), this.getCheckRtWorst(),
                                    this.getCheckRtBest(), this.getCheckRtLast(), diff);
                        }
                    }
                }
            }
        }
    }
```

进入到process方法中

```java
    // HttpHealthCheckProcessor的process是通过 RestTemplate 完成健康检测请求
    @Override
    public void process(HealthCheckTaskV2 task, Service service, ClusterMetadata metadata) {
        HealthCheckInstancePublishInfo instance = (HealthCheckInstancePublishInfo) task.getClient()
                .getInstancePublishInfo(service);
        if (null == instance) {
            return;
        }
        try {
            // TODO handle marked(white list) logic like v1.x.
            if (!instance.tryStartCheck()) {
                SRV_LOG.warn("http check started before last one finished, service: {} : {} : {}:{}",
                        service.getGroupedServiceName(), instance.getCluster(), instance.getIp(), instance.getPort());
                healthCheckCommon
                        .reEvaluateCheckRT(task.getCheckRtNormalized() * 2, task, switchDomain.getHttpHealthParams());
                return;
            }
            
            Http healthChecker = (Http) metadata.getHealthChecker();
            int ckPort = metadata.isUseInstancePortForCheck() ? instance.getPort() : metadata.getHealthyCheckPort();
            // 拼接客户端的 访问地址
            URL host = new URL(HTTP_PREFIX + instance.getIp() + ":" + ckPort);
            URL target = new URL(host, healthChecker.getPath());
            Map<String, String> customHeaders = healthChecker.getCustomHeaders();
            Header header = Header.newInstance();
            header.addAll(customHeaders);
            // 发送http请求 调用客户端服务  HttpHealthCheckCallback 是服务访问结束后的回调处理
            ASYNC_REST_TEMPLATE.get(target.toString(), header, Query.EMPTY, String.class,
                    new HttpHealthCheckCallback(instance, task, service));
            MetricsMonitor.getHttpHealthCheckMonitor().incrementAndGet();
        } catch (Throwable e) {
            instance.setCheckRt(switchDomain.getHttpHealthParams().getMax());
            healthCheckCommon.checkFail(task, service, "http:error:" + e.getMessage());
            healthCheckCommon.reEvaluateCheckRT(switchDomain.getHttpHealthParams().getMax(), task,
                    switchDomain.getHttpHealthParams());
        }
    }
```

​	我们可以发现作为持久化实例的处理。是通过http请求来访问服务提供方来检测是否可用访问来实现的。



### 2.4 消费者的服务订阅

​	现在我们来看下Nacos的客户端中服务消费者是如何来加载获取Nacos中的服务列表信息的。同样我们先进入到spring.factories 中找到`NacosDiscoveryClientConfiguration`这个配置类。

![image-20240808104123542](img/image-20240808104123542.png)

进入后找到这个方法

```java
    @Bean
    public DiscoveryClient nacosDiscoveryClient(NacosServiceDiscovery nacosServiceDiscovery) {
        return new NacosDiscoveryClient(nacosServiceDiscovery);
    }
```

可以看到会把`NacosDiscoveryClient`对象注入到Spring容器中。找到该对象的`getInstances`方法

```java
    public List<ServiceInstance> getInstances(String serviceId) {
        try {
            return this.serviceDiscovery.getInstances(serviceId);
        } catch (Exception var3) {
            throw new RuntimeException("Can not get hosts from nacos server. serviceId: " + serviceId, var3);
        }
    }
```

继续进入

```java
    public List<ServiceInstance> getInstances(String serviceId) throws NacosException {
        String group = this.discoveryProperties.getGroup();
        // 获取获取列表信息
        List<Instance> instances = this.namingService().selectInstances(serviceId, group, true);
        return hostToServiceInstanceList(instances, serviceId);
    }
```

继续进入

```java
    public List<Instance> selectInstances(String serviceName, String groupName, List<String> clusters, boolean healthy,
            boolean subscribe) throws NacosException {
        
        ServiceInfo serviceInfo;
        if (subscribe) {
            // 获取服务列表
            serviceInfo = hostReactor.getServiceInfo(NamingUtils.getGroupedName(serviceName, groupName),
                    StringUtils.join(clusters, ","));
        } else {
            serviceInfo = hostReactor
                    .getServiceInfoDirectlyFromServer(NamingUtils.getGroupedName(serviceName, groupName),
                            StringUtils.join(clusters, ","));
        }
        return selectInstances(serviceInfo, healthy);
    }
```

进入到getServiceInfo方法中

```java
public ServiceInfo getServiceInfo(final String serviceName, final String clusters) {
        
        NAMING_LOGGER.debug("failover-mode: " + failoverReactor.isFailoverSwitch());
        String key = ServiceInfo.getKey(serviceName, clusters);
        if (failoverReactor.isFailoverSwitch()) {
            return failoverReactor.getService(key);
        }
        // 获取是否已经有改服务信息。缓存的处理
        ServiceInfo serviceObj = getServiceInfo0(serviceName, clusters);
        
        if (null == serviceObj) {
            serviceObj = new ServiceInfo(serviceName, clusters);
            
            serviceInfoMap.put(serviceObj.getKey(), serviceObj);
            
            updatingMap.put(serviceName, new Object());
            // 如果之前没有获取过。那么这块就会更新服务信息
            updateServiceNow(serviceName, clusters);
            updatingMap.remove(serviceName);
            
        } else if (updatingMap.containsKey(serviceName)) {
            
            if (UPDATE_HOLD_INTERVAL > 0) {
                // hold a moment waiting for update finish
                synchronized (serviceObj) {
                    try {
                        serviceObj.wait(UPDATE_HOLD_INTERVAL);
                    } catch (InterruptedException e) {
                        NAMING_LOGGER
                                .error("[getServiceInfo] serviceName:" + serviceName + ", clusters:" + clusters, e);
                    }
                }
            }
        }
        
        scheduleUpdateIfAbsent(serviceName, clusters);
        
        return serviceInfoMap.get(serviceObj.getKey());
    }
```



我们进入到`updateServiceNow`方法中

```java
    private void updateServiceNow(String serviceName, String clusters) {
        try {
            updateService(serviceName, clusters);
        } catch (NacosException e) {
            NAMING_LOGGER.error("[NA] failed to update serviceName: " + serviceName, e);
        }
    }
```

继续进入

```Java
public void updateService(String serviceName, String clusters) throws NacosException {
    ServiceInfo oldService = getServiceInfo0(serviceName, clusters);
    try {
        
        String result = serverProxy.queryList(serviceName, clusters, pushReceiver.getUdpPort(), false);
        
        if (StringUtils.isNotEmpty(result)) {
            processServiceJson(result);
        }
    } finally {
        if (oldService != null) {
            synchronized (oldService) {
                oldService.notifyAll();
            }
        }
    }
}
```

进入到`serverProxy.queryList(..)`方法中

```java
    public String queryList(String serviceName, String clusters, int udpPort, boolean healthyOnly)
            throws NacosException {
        
        final Map<String, String> params = new HashMap<String, String>(8);
        params.put(CommonParams.NAMESPACE_ID, namespaceId);
        params.put(CommonParams.SERVICE_NAME, serviceName);
        params.put("clusters", clusters);
        params.put("udpPort", String.valueOf(udpPort));
        params.put("clientIP", NetUtils.localIP());
        params.put("healthyOnly", String.valueOf(healthyOnly));
        // 远程调用Nacos服务端的list接口来获取服务列表信息
        return reqApi(UtilAndComs.nacosUrlBase + "/instance/list", params, HttpMethod.GET);
    }
```

​	到这儿就看到了调用Nacos服务端获取服务列表信息的代码了。那与之对应的我们就可以看下nacos服务端的这个接口怎么处理的了。

```java
    @GetMapping("/list")
    @Secured(action = ActionTypes.READ)
    public Object list(HttpServletRequest request) throws Exception {
        // 获取相关的基础信息
        String namespaceId = WebUtils.optional(request, CommonParams.NAMESPACE_ID, Constants.DEFAULT_NAMESPACE_ID);
        String serviceName = WebUtils.required(request, CommonParams.SERVICE_NAME);
        NamingUtils.checkServiceNameFormat(serviceName);
        
        String agent = WebUtils.getUserAgent(request);
        String clusters = WebUtils.optional(request, "clusters", StringUtils.EMPTY);
        String clientIP = WebUtils.optional(request, "clientIP", StringUtils.EMPTY);
        // UDP 端口
        int udpPort = Integer.parseInt(WebUtils.optional(request, "udpPort", "0"));
        boolean healthyOnly = Boolean.parseBoolean(WebUtils.optional(request, "healthyOnly", "false"));
        String app = WebUtils.optional(request, "app", StringUtils.EMPTY);
        // 创建了一个订阅对象
        Subscriber subscriber = new Subscriber(clientIP + ":" + udpPort, agent, app, clientIP, namespaceId, serviceName,
                udpPort, clusters);
        // 获取对应的服务列表信息
        return getInstanceOperator().listInstance(namespaceId, serviceName, subscriber, clusters, healthyOnly);
    }
```

进入到`listInstance`方法中查看

```Java
@Override
public ServiceInfo listInstance(String namespaceId, String serviceName, Subscriber subscriber, String cluster,
        boolean healthOnly) {
    Service service = getService(namespaceId, serviceName, true);
    // For adapt 1.X subscribe logic 判断是否需要订阅
    if (subscriber.getPort() > 0 && pushService.canEnablePush(subscriber.getAgent())) {
        String clientId = IpPortBasedClient.getClientId(subscriber.getAddrStr(), true);
        createIpPortClientIfAbsent(clientId);
        clientOperationService.subscribeService(service, subscriber, clientId);
    } 
    ServiceInfo serviceInfo = serviceStorage.getData(service); // 获取服务信息
    ServiceMetadata serviceMetadata = metadataManager.getServiceMetadata(service).orElse(null);
    ServiceInfo result = ServiceUtil
            .selectInstancesWithHealthyProtection(serviceInfo, serviceMetadata, cluster, healthOnly, true, subscriber.getIp());
    // adapt for v1.x sdk
    result.setName(NamingUtils.getGroupedName(result.getName(), result.getGroupName()));
    return result;
}
```

获取服务信息的方法是`serviceStorage.getData(service)`.进入查看

```Java
public ServiceInfo getData(Service service) {
    // 如果缓存的就直接返回了。
    return serviceDataIndexes.containsKey(service) ? serviceDataIndexes.get(service) : getPushData(service);
}
```

进入到`getPushData`方法中查看

```Java
public ServiceInfo getPushData(Service service) {
    ServiceInfo result = emptyServiceInfo(service);
    if (!ServiceManager.getInstance().containSingleton(service)) {
        return result;
    }
    Service singleton = ServiceManager.getInstance().getSingleton(service);
    result.setHosts(getAllInstancesFromIndex(singleton)); // 获取对应的实例信息
    serviceDataIndexes.put(singleton, result);
    return result;
}
```

这个方法是核心`getAllInstancesFromIndex(singleton)`进入查看

```Java
private List<Instance> getAllInstancesFromIndex(Service service) {
    Set<Instance> result = new HashSet<>();
    Set<String> clusters = new HashSet<>();
    for (String each : serviceIndexesManager.getAllClientsRegisteredService(service)) {
        // 这块的 getInstanceInfo 方法是核心
        Optional<InstancePublishInfo> instancePublishInfo = getInstanceInfo(each, service);
        if (instancePublishInfo.isPresent()) {
            InstancePublishInfo publishInfo = instancePublishInfo.get();
            //If it is a BatchInstancePublishInfo type, it will be processed manually and added to the instance list
            if (publishInfo instanceof BatchInstancePublishInfo) {
                BatchInstancePublishInfo batchInstancePublishInfo = (BatchInstancePublishInfo) publishInfo;
                List<Instance> batchInstance = parseBatchInstance(service, batchInstancePublishInfo, clusters);
                result.addAll(batchInstance);
            } else {
                Instance instance = parseInstance(service, instancePublishInfo.get());
                result.add(instance);
                clusters.add(instance.getClusterName());
            }
        }
    }
    // cache clusters of this service
    serviceClusterIndex.put(service, clusters);
    return new LinkedList<>(result);
}
```

进入到`getInstanceInfo(each, service)`中查看

```Java
private Optional<InstancePublishInfo> getInstanceInfo(String clientId, Service service) {
    Client client = clientManager.getClient(clientId); // 注册的时候 相关的服务都存储在了 clientManager中
    if (null == client) {
        return Optional.empty();
    }
    return Optional.ofNullable(client.getInstancePublishInfo(service));
}
```

​	可以看到获取的注册的服务是从`clientManager`中获取到的。这个和我们前面注册的时候保存的是同一个位置。



### 2.5 发布订阅原理

​	在Nacos注册中心中会通过事件的发布订阅机制把相关的逻辑串联起来。比如服务注册会发布相关事件通知对应的服务订阅者更新相关服务等。所以我们来看看这块是怎么设计实现的。

#### 2.5.1 NotifyCenter

​	在服务注册的时候。注册完成会通过`NotifyCenter`来发布相关的事件。

```Java
@Override
public void registerInstance(Service service, Instance instance, String clientId) throws NacosException {
    NamingUtils.checkInstanceIsLegal(instance);
    // 获取Service 对象
    Service singleton = ServiceManager.getInstance().getSingleton(service);
    if (!singleton.isEphemeral()) {
        throw new NacosRuntimeException(NacosException.INVALID_PARAM,
                String.format("Current service %s is persistent service, can't register ephemeral instance.",
                        singleton.getGroupedServiceName()));
    }
    Client client = clientManager.getClient(clientId); // 获取客户端
    if (!clientIsLegal(client, clientId)) {
        return;
    }
    InstancePublishInfo instanceInfo = getPublishInfo(instance);
    client.addServiceInstance(singleton, instanceInfo);
    client.setLastUpdatedTime();
    client.recalculateRevision();
    // 发布一个 客户端的注册事件
    NotifyCenter.publishEvent(new ClientOperationEvent.ClientRegisterServiceEvent(singleton, clientId));
    NotifyCenter
            .publishEvent(new MetadataEvent.InstanceMetadataEvent(singleton, instanceInfo.getMetadataId(), false));
}
```

​	上面我们可以看到发不了两个事件`ClientRegisterServiceEvent`和`InstanceMetadataEvent`事件。对应的相关订阅者就会接收到事件的处理。我们先来分析下`NotifyCenter`的设计。

​	NotifyCenter是统一事件通知中心。

```java
    /**
     * Publisher management container.
     * 存储所有的事件发布者
     * key ：是事件类的class全限定类名
     * values 是一个 EventPublisher接口（两个实现DefaultPublisher/NamingEventPublisher）的实现 简单来说就是一个发布者
     */
    private final Map<String, EventPublisher> publisherMap = new ConcurrentHashMap<>(16);

    /**
     * Publisher management container.
     * 存储所有的事件发布者
     * key ：是事件类的class全限定类名
     * values 是一个 EventPublisher接口（两个实现DefaultPublisher/NamingEventPublisher）的实现 简单来说就是一个发布者
     */
    private final Map<String, EventPublisher> publisherMap = new ConcurrentHashMap<>(16);
    
    static {
        // Internal ArrayBlockingQueue buffer size. For applications with high write throughput,
        // this value needs to be increased appropriately. default value is 16384
        String ringBufferSizeProperty = "nacos.core.notify.ring-buffer-size";
        ringBufferSize = Integer.getInteger(ringBufferSizeProperty, 16384);
        
        // The size of the public publisher's message staging queue buffer
        String shareBufferSizeProperty = "nacos.core.notify.share-buffer-size";
        shareBufferSize = Integer.getInteger(shareBufferSizeProperty, 1024);
        // 加载所有的 EventPublisher 接口的实现类
        final Collection<EventPublisher> publishers = NacosServiceLoader.load(EventPublisher.class);
        Iterator<EventPublisher> iterator = publishers.iterator(); // 获取 事件发布器 的迭代器
        
        if (iterator.hasNext()) {
            clazz = iterator.next().getClass(); // 如果有自定义的 发布器
        } else {
            clazz = DefaultPublisher.class; // 如果没有自定义的迭代器 就用默认的
        }
        // 定义了 EventPublishFactory 中定义的 apply() 方法的逻辑 在addXXX 方法中会触发
        DEFAULT_PUBLISHER_FACTORY = (cls, buffer) -> {
            try {
                EventPublisher publisher = clazz.newInstance();
                publisher.init(cls, buffer);
                return publisher;
            } catch (Throwable ex) {
                LOGGER.error("Service class newInstance has error : ", ex);
                throw new NacosRuntimeException(SERVER_ERROR, ex);
            }
        };
        
        try {
            
            // Create and init DefaultSharePublisher instance. 设置默认的事件发布者
            INSTANCE.sharePublisher = new DefaultSharePublisher();
            INSTANCE.sharePublisher.init(SlowEvent.class, shareBufferSize); // 初始化事件发布器 执行的是上面的代码
            
        } catch (Throwable ex) {
            LOGGER.error("Service class newInstance has error : ", ex);
        }
        
        ThreadUtils.addShutdownHook(NotifyCenter::shutdown);
    }
```

在static中主要的逻辑是判断是否有创建自定义的事件发布器。如果没有创建就使用默认的事件发布器`DefaultSharePublisher`.同时执行对应的`init`方法完成初始化的处理。

![image-20240811140913502](img/image-20240811140913502.png)

创建对应的事件发布器然后执行init方法初始化。

```java
    @Override
    public void init(Class<? extends Event> type, int bufferSize) {
        setDaemon(true);// 设置守护线程
        setName("nacos.publisher-" + type.getName()); // 设置 发布器 的名称
        this.eventType = type; // 记录 发布器
        this.queueMaxSize = bufferSize; // 设置阻塞队列的长度
        // 集合是一个 阻塞队列
        this.queue = new ArrayBlockingQueue<>(bufferSize);
        start(); // 调用start方法 开启线程 等待系统调度执行 run 方法
    }

    @Override
    public synchronized void start() {
        if (!initialized) {
            // start just called once
            super.start();
            if (queueMaxSize == -1) {
                queueMaxSize = ringBufferSize;
            }
            initialized = true;
        }
    }
```

`init`方法的逻辑比较简单。调用start方法等待调度执行run方法

```java
    @Override
    public void run() {
        openEventHandler();
    }
```

进入到`openEventHandler`方法中。

```java
 void openEventHandler() {
        try {
            
            // This variable is defined to resolve the problem which message overstock in the queue.
            int waitTimes = 60; // 默认的等待次数。主要是解决队列中消息积压的问题
            // To ensure that messages are not lost, enable EventHandler when
            // waiting for the first Subscriber to register 为了确保消息不丢失。需要等待第一个订阅者注册
            while (!shutdown && !hasSubscriber() && waitTimes > 0) {
                ThreadUtils.sleep(1000L);
                waitTimes--;
            }

            while (!shutdown) {
                // 从阻塞队列中获取对应的事件
                final Event event = queue.take();
                // 处理事件
                receiveEvent(event);
                UPDATER.compareAndSet(this, lastEventSequence, Math.max(lastEventSequence, event.sequence()));
            }
        } catch (Throwable ex) {
            LOGGER.error("Event listener exception : ", ex);
        }
    }
```

​	这里的关键就是`queue.take();`方法。这个方法会阻塞。当有事件添加到这个阻塞队列中的时候。这个方法就会获取到添加进来的事件。`receiveEvent(event)`就是对应的处理相关的事件的逻辑了。我们进入这个方法看看。

```Java
void receiveEvent(Event event) {
    final long currentEventSequence = event.sequence();
    
    if (!hasSubscriber()) { // 如果没有 订阅者 那么就没有处理的意义了
        LOGGER.warn("[NotifyCenter] the {} is lost, because there is no subscriber.", event);
        return;
    }
    
    // Notification single event listener
    for (Subscriber subscriber : subscribers) {
        if (!subscriber.scopeMatches(event)) {
            continue; // 如果订阅者订阅的事件和触发的事件不匹配就结束这次循环
        }
        
        // Whether to ignore expiration events
        if (subscriber.ignoreExpireEvent() && lastEventSequence > currentEventSequence) {
            LOGGER.debug("[NotifyCenter] the {} is unacceptable to this subscriber, because had expire",
                    event.getClass());
            continue;
        }
        
        // Because unifying smartSubscriber and subscriber, so here need to think of compatibility.
        // Remove original judge part of codes. 通知所有的订阅者处理事件
        notifySubscriber(subscriber, event);
    }
}
```

​	上面的方法的逻辑是根据当前的事件找到对应的订阅者。然后通过`notifySubscriber(xx)`方法来通知订阅者。

```Java
@Override
public void notifySubscriber(final Subscriber subscriber, final Event event) {
    
    LOGGER.debug("[NotifyCenter] the {} will received by {}", event, subscriber);
    // 声明了一个 Runnable 接口。指定了run方法的逻辑
    final Runnable job = () -> subscriber.onEvent(event);
    final Executor executor = subscriber.executor();
    
    if (executor != null) {
        executor.execute(job); // 异步执行
    } else {
        try {
            job.run(); // 同步执行
        } catch (Throwable e) {
            LOGGER.error("Event callback exception: ", e);
        }
    }
}
```

​	这里就可以看到找到对应的订阅者。调用订阅者的run方法。有两种方式。一个是同步一个是异步。后面就是对应的订阅者来处理逻辑了。

#### 2.5.2 订阅逻辑处理

​	上面看了Nacos服务器中的事件发布器的处理逻辑。然后我们来看看具体的订阅者是如何订阅并和事件发布器关联起来的。我们以服务消费者服务订阅的逻辑为例。在`list`接口为例，也就是前面介绍的消费者获取服务列表为例。

```java
@GetMapping("/list")
    @Secured(action = ActionTypes.READ)
    public Object list(HttpServletRequest request) throws Exception {
        // 获取相关的基础信息
        String namespaceId = WebUtils.optional(request, CommonParams.NAMESPACE_ID, Constants.DEFAULT_NAMESPACE_ID);
        String serviceName = WebUtils.required(request, CommonParams.SERVICE_NAME);
        NamingUtils.checkServiceNameFormat(serviceName);
        
        String agent = WebUtils.getUserAgent(request);
        String clusters = WebUtils.optional(request, "clusters", StringUtils.EMPTY);
        String clientIP = WebUtils.optional(request, "clientIP", StringUtils.EMPTY);
        // UDP 端口
        int udpPort = Integer.parseInt(WebUtils.optional(request, "udpPort", "0"));
        boolean healthyOnly = Boolean.parseBoolean(WebUtils.optional(request, "healthyOnly", "false"));
        String app = WebUtils.optional(request, "app", StringUtils.EMPTY);
        // 上面获取到了服务消费者的相关信息。然后创建了一个订阅者对象。包括了消费者的相关信息
        Subscriber subscriber = new Subscriber(clientIP + ":" + udpPort, agent, app, clientIP, namespaceId, serviceName,
                udpPort, clusters);
        return getInstanceOperator().listInstance(namespaceId, serviceName, subscriber, clusters, healthyOnly);
    }
```

也就是`Subscriber`对象。然后进入到`listInstance`方法中查看。

```Java
@Override
public ServiceInfo listInstance(String namespaceId, String serviceName, Subscriber subscriber, String cluster,
        boolean healthOnly) {
    Service service = getService(namespaceId, serviceName, true);
    // For adapt 1.X subscribe logic 判断是否需要订阅
    if (subscriber.getPort() > 0 && pushService.canEnablePush(subscriber.getAgent())) {
        String clientId = IpPortBasedClient.getClientId(subscriber.getAddrStr(), true);
        createIpPortClientIfAbsent(clientId);
        // 这块就是来做服务订阅的处理了。
        clientOperationService.subscribeService(service, subscriber, clientId);
    }
    ServiceInfo serviceInfo = serviceStorage.getData(service); // 获取服务信息
    ServiceMetadata serviceMetadata = metadataManager.getServiceMetadata(service).orElse(null);
    ServiceInfo result = ServiceUtil
            .selectInstancesWithHealthyProtection(serviceInfo, serviceMetadata, cluster, healthOnly, true, subscriber.getIp());
    // adapt for v1.x sdk
    result.setName(NamingUtils.getGroupedName(result.getName(), result.getGroupName()));
    return result;
}
```

​	上面的逻辑中在`clientOperationService.subscribeService(service, subscriber, clientId)`完成订阅服务的处理了。

```Java
@Override
public void subscribeService(Service service, Subscriber subscriber, String clientId) {
    // 获取 当前订阅的服务信息
    Service singleton = ServiceManager.getInstance().getSingletonIfExist(service).orElse(service);
    // 获取之前已经注册的 需要订阅的服务信息
    Client client = clientManager.getClient(clientId);
    if (!clientIsLegal(client, clientId)) { // 简单判断是否合法
        return;
    }
    // 这块实现服务订阅。也就是 singleton 服务的订阅者 是  subscriber
    client.addServiceSubscriber(singleton, subscriber);
    client.setLastUpdatedTime();
    // 发不了一个对应的订阅事件
    NotifyCenter.publishEvent(new ClientOperationEvent.ClientSubscribeServiceEvent(singleton, clientId));
}
```

​	上面方法的逻辑是找到需要订阅的服务。然后把这个服务和当前订阅者关联起来。`client.addServiceSubscriber(singleton, subscriber)`在这个方法中可以看到和前面关联的内容了。

```java
    @Override
    public boolean addServiceSubscriber(Service service, Subscriber subscriber) {
        if (null == subscribers.put(service, subscriber)) { // subscribers 就是所有的订阅者
            MetricsMonitor.incrementSubscribeCount();
        }
        return true;
    }
```

​	可以看到`服务提供者`和`订阅者 `和我们前面介绍的事件发布器中用到的`subscribers`关联起来了。



#### 2.5.3 事件发布机制

​	我们可以看到在Nacos中处理了相关逻辑后都会通过`NotifyCenter.publishEvent`来发布相关的事件。并且在参数中传递对应的事件类型。这块的逻辑我们可以跟踪来看看。

```java
    @Override
    public void subscribeService(Service service, Subscriber subscriber, String clientId) {
        // 获取 当前订阅的服务信息
        Service singleton = ServiceManager.getInstance().getSingletonIfExist(service).orElse(service);
        // 获取之前已经注册的 需要订阅的服务信息
        Client client = clientManager.getClient(clientId);
        if (!clientIsLegal(client, clientId)) { // 简单判断是否合法
            return;
        }
        // 这块实现服务订阅。也就是 singleton 服务的订阅者 是  subscriber
        client.addServiceSubscriber(singleton, subscriber);
        client.setLastUpdatedTime();
        NotifyCenter.publishEvent(new ClientOperationEvent.ClientSubscribeServiceEvent(singleton, clientId));
    }
```

```java 
public static boolean publishEvent(final Event event) {
    try {
        return publishEvent(event.getClass(), event); //发布事件
    } catch (Throwable ex) {
        LOGGER.error("There was an exception to the message publishing : ", ex);
        return false;
    }
}
    private static boolean publishEvent(final Class<? extends Event> eventType, final Event event) {
        if (ClassUtils.isAssignableFrom(SlowEvent.class, eventType)) {
            return INSTANCE.sharePublisher.publish(event);
        }
        
        final String topic = ClassUtils.getCanonicalName(eventType);
        // 找到对应的事件发布器
        EventPublisher publisher = INSTANCE.publisherMap.get(topic);
        if (publisher != null) {
            return publisher.publish(event); // 发布事件
        }
        if (event.isPluginEvent()) {
            return true;
        }
        LOGGER.warn("There are no [{}] publishers for this event, please register", topic);
        return false;
    }
```

进入到`publish`方法中

```java
    @Override
    public boolean publish(Event event) {
        checkIsStart();
        boolean success = this.queue.offer(event); // 把事件添加到队列的尾部
        if (!success) {
            LOGGER.warn("Unable to plug in due to interruption, synchronize sending time, event : {}", event);
            receiveEvent(event);
            return true;
        }
        return true;
    }
```

​	可以看到这里通过`this.queue.offer(event);`方法把对应的事件添加到了阻塞队列的尾部。这样就和前面的事件发布器初始化的时候调用`init`方法调用的`queue.task(event)`方法关联起来了。

![image-20240811144648191](img/image-20240811144648191.png)

​	触发`take`方法后就会执行`receiveEvent(event)`方法来处理这个事件了。

#### 2.5.3 事件类型

​	然后我们来看看Nacos服务端的事件类型有哪些。每个事件类型处理了什么逻辑。

| 事件名称                      | 介绍                   | 相关处理器                  |
| ----------------------------- | ---------------------- | --------------------------- |
| ClientRegisterServiceEvent    | 客户端注册服务事件     | ClientServiceIndexesManager |
| ClientDeregisterServiceEvent  | 客户端取消注册服务事件 | ClientServiceIndexesManager |
| ClientSubscribeServiceEvent   | 客户端订阅服务事件     | ClientServiceIndexesManager |
| ClientUnsubscribeServiceEvent | 客户端取消订阅服务事件 | ClientServiceIndexesManager |
| InstanceMetadataEvent         | 元数据事件             | NamingMetadataManager       |
| ServiceMetadataEvent          | 服务元数据事件         | NamingMetadataManager       |
| ClientDisconnectEvent         | 客户端断开连接事件     | NamingMetadataManager       |
| ClientChangedEvent            | 客户端变化的事件       | DistroClientDataProcessor   |
| .....                         |                        |                             |



![e1d0a33920ec43deb3f569c403d0341e](img/e1d0a33920ec43deb3f569c403d0341e.jpeg)

## 3.配置中心

### 3.1 案例准备

​	在介绍Nacos配置中心实现原理之前我们先准备一个基础的案例。来演示下怎么获取配置中心的配置信息和及时获取Nacos配置中心的更新。案例代码如下：

```java
@Test
    void contextLoads() throws Exception{
        Properties properties = new Properties();
        properties.put("serverAddr", "localhost:8848");
        properties.put("namespace", "public");
        // 通过指定参数，创建一个 configService
        ConfigService configService = NacosFactory.createConfigService(properties);

        String dataId = "nacos-client-dev.yaml";
        String group = "DEFAULT_GROUP";

        // 通过dataId、group获取配置
        String config = configService.getConfig(dataId, group, 3000);
        System.out.println(config);

        // 监听服务端配置变更
        configService.addListener(dataId, group, new Listener() {
            @Override
            public Executor getExecutor() {
                System.out.println("==---服务器变化了----");
                return null;
            }

            @Override
            public void receiveConfigInfo(String configInfo) {
                System.out.println("-------配置发生变更，变更后的配置：" + configInfo);
            }
        });

        CountDownLatch countDownLatch = new CountDownLatch(1);
        countDownLatch.await();

    }
```

启动服务后可以获取到配置中心中的信息。当配置中心的信息发送变化后同步的也可以获取到对应的信息

![image-20240811205551901](img/image-20240811205551901.png)



### 3.2 客户端获取配置信息

​	通过上面的案例我们可以来分析下Nacos的客户端是如何来获取到Nacos服务端的配置信息的。

![image-20240812134035688](img/image-20240812134035688.png)

分析的入口方法是`getConfig`方法。直接进入后

```java
private String getConfigInner(String tenant, String dataId, String group, long timeoutMs) throws NacosException {
    // 判断下group的情况
        group = null2defaultGroup(group);
    // 校验下 DataID和Group是否合法
        ParamUtils.checkKeyParam(dataId, group);
        ConfigResponse cr = new ConfigResponse();
        
        cr.setDataId(dataId);
        cr.setTenant(tenant);
        cr.setGroup(group);
        
        // 优先使用本地配置  
        String content = LocalConfigInfoProcessor.getFailover(agent.getName(), dataId, group, tenant);
        if (content != null) {
            // 本地缓存中有数据直接获取并通过过滤器来处理
            LOGGER.warn("[{}] [get-config] get failover ok, dataId={}, group={}, tenant={}, config={}", agent.getName(),
                    dataId, group, tenant, ContentUtils.truncateContent(content));
            cr.setContent(content);
            configFilterChainManager.doFilter(null, cr);
            content = cr.getContent();
            return content;
        }
        
        try {
            // 通过 网络请求来获取服务端的配置信息
            String[] ct = worker.getServerConfig(dataId, group, tenant, timeoutMs);
            cr.setContent(ct[0]);
            // 处理获取的数据
            configFilterChainManager.doFilter(null, cr);
            content = cr.getContent();
            
            return content;
        } catch (NacosException ioe) {
            if (NacosException.NO_RIGHT == ioe.getErrCode()) {
                throw ioe;
            }
            LOGGER.warn("[{}] [get-config] get from server error, dataId={}, group={}, tenant={}, msg={}",
                    agent.getName(), dataId, group, tenant, ioe.toString());
        }
        
        LOGGER.warn("[{}] [get-config] get snapshot ok, dataId={}, group={}, tenant={}, config={}", agent.getName(),
                dataId, group, tenant, ContentUtils.truncateContent(content));
        content = LocalConfigInfoProcessor.getSnapshot(agent.getName(), dataId, group, tenant);
        cr.setContent(content);
        configFilterChainManager.doFilter(null, cr);
        content = cr.getContent();
        return content;
    }
```

然后进入到`getServerConfig`方法中查看具体的逻辑。

```Java
public String[] getServerConfig(String dataId, String group, String tenant, long readTimeout)
        throws NacosException {
    String[] ct = new String[2];
    if (StringUtils.isBlank(group)) {
        group = Constants.DEFAULT_GROUP;
    }
    
    HttpRestResult<String> result = null;
    try {
        // 封装请求需要的参数
        Map<String, String> params = new HashMap<String, String>(3);
        if (StringUtils.isBlank(tenant)) {
            params.put("dataId", dataId);
            params.put("group", group);
        } else {
            params.put("dataId", dataId);
            params.put("group", group);
            params.put("tenant", tenant);
        }
        // 发起网络请求 并获取返回结果  /v1/cs/configs
        result = agent.httpGet(Constants.CONFIG_CONTROLLER_PATH, null, params, agent.getEncode(), readTimeout);
    } catch (Exception ex) {
        // 如果网络请求出现异常 抛出异常信息
        String message = String
                .format("[%s] [sub-server] get server config exception, dataId=%s, group=%s, tenant=%s",
                        agent.getName(), dataId, group, tenant);
        LOGGER.error(message, ex);
        throw new NacosException(NacosException.SERVER_ERROR, ex);
    }
    
    switch (result.getCode()) {
            // 根据请求返回的状态码来处理
        case HttpURLConnection.HTTP_OK: // 请求成功 把查询的信息缓存到本地
            LocalConfigInfoProcessor.saveSnapshot(agent.getName(), dataId, group, tenant, result.getData());
            ct[0] = result.getData();
            if (result.getHeader().getValue(CONFIG_TYPE) != null) {
                ct[1] = result.getHeader().getValue(CONFIG_TYPE);
            } else {
                ct[1] = ConfigType.TEXT.getType();
            }
            return ct;
        case HttpURLConnection.HTTP_NOT_FOUND: // 如果没有找到相关的信息。清空本地缓存的信息
            LocalConfigInfoProcessor.saveSnapshot(agent.getName(), dataId, group, tenant, null);
            return ct;
        case HttpURLConnection.HTTP_CONFLICT: { // 冲突的话 抛异常
            LOGGER.error(
                    "[{}] [sub-server-error] get server config being modified concurrently, dataId={}, group={}, "
                            + "tenant={}", agent.getName(), dataId, group, tenant);
            throw new NacosException(NacosException.CONFLICT,
                    "data being modified, dataId=" + dataId + ",group=" + group + ",tenant=" + tenant);
        }
        case HttpURLConnection.HTTP_FORBIDDEN: { // 跨域问题抛异常
            LOGGER.error("[{}] [sub-server-error] no right, dataId={}, group={}, tenant={}", agent.getName(),
                    dataId, group, tenant);
            throw new NacosException(result.getCode(), result.getMessage());
        }
        default: { // 其他问题也抛异常
            LOGGER.error("[{}] [sub-server-error]  dataId={}, group={}, tenant={}, code={}", agent.getName(),
                    dataId, group, tenant, result.getCode());
            throw new NacosException(result.getCode(),
                    "http error, code=" + result.getCode() + ",dataId=" + dataId + ",group=" + group + ",tenant="
                            + tenant);
        }
    }
}
```

上面的代码逻辑也很清楚

1. 组装服务端的相关信息
2. 通过Get方法访问`/v1/cs/configs`接口
3. 访问成功把数据同步缓存到本地
4. 如果从配置中心没有加载到信息同时清空本地缓存数据
5. 其他情况抛出相关异常信息



### 3.3 服务端响应

​	接下来我们看看服务端的`/v1/cs/configs`的接口是如何处理获取配置信息的这个请求的。

```Java
@GetMapping
@TpsControl(pointName = "ConfigQuery")
@Secured(action = ActionTypes.READ, signType = SignType.CONFIG)
public void getConfig(HttpServletRequest request, HttpServletResponse response,
        @RequestParam("dataId") String dataId, @RequestParam("group") String group,
        @RequestParam(value = "tenant", required = false, defaultValue = StringUtils.EMPTY) String tenant,
        @RequestParam(value = "tag", required = false) String tag)
        throws IOException, ServletException, NacosException {
    // check tenant
    ParamUtils.checkTenant(tenant);
    tenant = NamespaceUtil.processNamespaceParameter(tenant);
    // check params
    ParamUtils.checkParam(dataId, group, "datumId", "content");
    ParamUtils.checkParam(tag);
    
    final String clientIp = RequestUtil.getRemoteIp(request);
    String isNotify = request.getHeader("notify");
    // 获取配置信息的方法
    inner.doGetConfig(request, response, dataId, group, tenant, tag, isNotify, clientIp);
}
```

进入到`doGetConfig`方法中。核心的是通过`findConfigInfo4Beta`方法来获取对应的配置信息

![image-20240812135711650](img/image-20240812135711650.png)



对于`findConfigInfo4Tag(...)`方法的实现有两个

![image-20240812135745382](img/image-20240812135745382.png)

一个是内嵌的存储一个是扩展的存储方式。我们这里通过内嵌的方式来查看

```Java
@Override
public ConfigInfoBetaWrapper findConfigInfo4Beta(final String dataId, final String group, final String tenant) {
    String tenantTmp = StringUtils.isBlank(tenant) ? StringUtils.EMPTY : tenant;
    // 找到对应的Mapper对象
    ConfigInfoBetaMapper configInfoBetaMapper = mapperManager.findMapper(dataSourceService.getDataSourceType(),
            TableConstant.CONFIG_INFO_BETA);
    // 构建相关的SQL语句
    final String sql = configInfoBetaMapper.select(
            Arrays.asList("id", "data_id", "group_id", "tenant_id", "app_name", "content", "beta_ips",
                    "encrypted_data_key"), Arrays.asList("data_id", "group_id", "tenant_id"));
    // 根据我们传递的相关参数来获取 配置信息
    return databaseOperate.queryOne(sql, new Object[] {dataId, group, tenantTmp},
            CONFIG_INFO_BETA_WRAPPER_ROW_MAPPER);
    
}
```

可以看到查询配置信息的操作逻辑。



### 3.4 客户端定时拉取

​	如果服务端的配置信息做了更新后对应的客户端是如何及时获取到相关的配置信息的呢？接下来我们就分析下这块的原理。对应的入口代码还是从我们前面介绍的案例入手：

![image-20240812160944695](img/image-20240812160944695.png)

从创建的ConfigService对象开始。

```Java
public static ConfigService createConfigService(Properties properties) throws NacosException {
    try {
        Class<?> driverImplClass = Class.forName("com.alibaba.nacos.client.config.NacosConfigService");
        Constructor constructor = driverImplClass.getConstructor(Properties.class);
        // 通过构造方法创建对应的对象
        ConfigService vendorImpl = (ConfigService) constructor.newInstance(properties);
        return vendorImpl;
    } catch (Throwable e) {
        throw new NacosException(NacosException.CLIENT_INVALID_PARAM, e);
    }
}
```

ConfigService接口的实现是`NacosConfigService`对象。

```Java
public NacosConfigService(Properties properties) throws NacosException {
    // 校验对应的属性信息
    ValidatorUtils.checkInitParam(properties);
    String encodeTmp = properties.getProperty(PropertyKeyConst.ENCODE);
    if (StringUtils.isBlank(encodeTmp)) {
        this.encode = Constants.ENCODE;
    } else {
        this.encode = encodeTmp.trim();
    }
    // 初始化名称空间
    initNamespace(properties);
    // 创建对应的Agent对象
    this.agent = new MetricsHttpAgent(new ServerHttpAgent(properties));
    this.agent.start();
    this.worker = new ClientWorker(this.agent, this.configFilterChainManager, properties);
}
```

核心的是进入到ClientWorker的构造方法查看，这里需要先补充下长轮询机制的基础内容。

- 短轮询：指客户端每隔一段时间向服务器发起一次Http请求，服务端收到请求后，进行处理，然后返回给客户端。
- 长轮询：指客户端向服务端发起一个带**超时时间(timeout)**的Http请求，并在Http连接超时前，不主动断开连接，需要服务端主动回写数据，否则将一直重复以上过程。

Nacos就是利用了长轮询机制，客户端会开启一个线程，不断向服务端发起一个配置是否存在变更的请求(30s超时)，服务端收到请求后，如果配置不存在变更，并不会立即返回，而是当配置发生变更后，主动是否将消息回写给客户端。

客户端会存在两种情况：

1. 请求超时：无配置变更，开启下一次轮询请求
2. 服务器返回数据：解析服务器返回数据，通过Nameserver、dataId、group重新回去服务器配置，更新本地缓存，触发事件监听，开启下一次轮询请求。

```Java
public ClientWorker(final HttpAgent agent, final ConfigFilterChainManager configFilterChainManager,
        final Properties properties) {
    this.agent = agent;
    this.configFilterChainManager = configFilterChainManager;
    
    // Initialize the timeout parameter
    
    init(properties);
    
    this.executor = Executors.newScheduledThreadPool(1, new ThreadFactory() {
        @Override
        public Thread newThread(Runnable r) {
            Thread t = new Thread(r);
            t.setName("com.alibaba.nacos.client.Worker." + agent.getName());
            t.setDaemon(true);
            return t;
        }
    });
    
    this.executorService = Executors
            .newScheduledThreadPool(Runtime.getRuntime().availableProcessors(), new ThreadFactory() {
                @Override
                public Thread newThread(Runnable r) {
                    Thread t = new Thread(r);
                    t.setName("com.alibaba.nacos.client.Worker.longPolling." + agent.getName());
                    t.setDaemon(true);
                    return t;
                }
            });
    
    this.executor.scheduleWithFixedDelay(new Runnable() {
        @Override
        public void run() {
            try {
               // 这个是开启长轮询的核心方法
                checkConfigInfo();
            } catch (Throwable e) {
                LOGGER.error("[" + agent.getName() + "] [sub-check] rotate check error", e);
            }
        }
    }, 1L, 10L, TimeUnit.MILLISECONDS); // 第一次延迟10毫秒执行  之后是上一个任务执行完成后延迟10毫秒执行
}
```

进入到`checkConfigInfo()`方法

```java
public class ClientWorker {
	
	// 检查配置信息：分批处理，一次最多3000
	public void checkConfigInfo() {
        // 分任务
        int listenerSize = cacheMap.get().size();
        // 向上取整为批数
        int longingTaskCount = (int) Math.ceil(listenerSize / ParamUtil.getPerTaskConfigSize());
        if (longingTaskCount > currentLongingTaskCount) {
            for (int i = (int) currentLongingTaskCount; i < longingTaskCount; i++) {
                // 要判断任务是否在执行 这块需要好好想想。 任务列表现在是无序的。变化过程可能有问题
                // i 为当前批次，用于筛选过滤出属于当前批次的cacheData
                executorService.execute(new LongPollingRunnable(i));
            }
            currentLongingTaskCount = longingTaskCount;
        }
    }
    
	class LongPollingRunnable implements Runnable {
		// 当前批次id，用于筛选过滤出属于当前批次的cacheData
        private int taskId;

        public LongPollingRunnable(int taskId) {
            this.taskId = taskId;
        }

        @Override
        public void run() {

            List<CacheData> cacheDatas = new ArrayList<CacheData>();
            List<String> inInitializingCacheList = new ArrayList<String>();
            try {
                // check failover config
               	// 获取属于当前批次的cacheData
                for (CacheData cacheData : cacheMap.get().values()) {
                    if (cacheData.getTaskId() == taskId) {
                        cacheDatas.add(cacheData);
                        try {
                        	// 检查本地配置
                            checkLocalConfig(cacheData);
                            if (cacheData.isUseLocalConfigInfo()) { // 使用本地配置信息
                            	// 检查cacheData和内存缓存文件是否不一致，如果不一致，通知所有Listener
                                cacheData.checkListenerMd5();
                            }
                        } catch (Exception e) {
                            LOGGER.error("get local config info error", e);
                        }
                    }
                }

                // check server config
                // 长轮询：将当前批次的所有cacheData通过Http请求发送给服务端，并附带30s超时时间
                // 1.服务端数据无变化，请求超时，changedGroupKeys = Collections.emptyList()
                // 2.服务端数据存在变更，循环遍历，通过getServerConfig获取并更新本地缓存，触发事件监听
                List<String> changedGroupKeys = checkUpdateDataIds(cacheDatas, inInitializingCacheList);

				// 遍历发送变更的groupKey 
                for (String groupKey : changedGroupKeys) {
                    String[] key = GroupKey.parseKey(groupKey);
                    String dataId = key[0];
                    String group = key[1];
                    String tenant = null;
                    if (key.length == 3) {
                        tenant = key[2];
                    }
                    try {
                    	// 重新获取服务端配置，本更新本地配置文件缓存内容
                        String content = getServerConfig(dataId, group, tenant, 3000L);
						// 更新本地内存配置
                        CacheData cache = cacheMap.get().get(GroupKey.getKeyTenant(dataId, group, tenant));
                        cache.setContent(content);
                        LOGGER.info("[{}] [data-received] dataId={}, group={}, tenant={}, md5={}, content={}",
                            agent.getName(), dataId, group, tenant, cache.getMd5(),
                            ContentUtils.truncateContent(content));
                    } catch (NacosException ioe) {
                        String message = String.format(
                            "[%s] [get-update] get changed config exception. dataId=%s, group=%s, tenant=%s",
                            agent.getName(), dataId, group, tenant);
                        LOGGER.error(message, ioe);
                    }
                }
                
                // 遍历cacheDatas，判断是否需要重新初始化本地文件缓存
                for (CacheData cacheData : cacheDatas) {
                    if (!cacheData.isInitializing() || inInitializingCacheList
                        .contains(GroupKey.getKeyTenant(cacheData.dataId, cacheData.group, cacheData.tenant))) {
                        // 检查cacheData和内存缓存文件是否不一致，如果不一致，通知所有Listener
                        cacheData.checkListenerMd5();
                        cacheData.setInitializing(false);
                    }
                }
                inInitializingCacheList.clear();

                executorService.execute(this);

            } catch (Throwable e) {

                // If the rotation training task is abnormal, the next execution time of the task will be punished
                LOGGER.error("longPolling error : ", e);
                // 如果发生异常，延迟taskPenaltyTime后执行当前任务
                executorService.schedule(this, taskPenaltyTime, TimeUnit.MILLISECONDS);
            }
        }
    }

	/**
     * 从Server获取值变化了的DataID列表。返回的对象里只有dataId和group是有效的。 保证不返回NULL。
     */
    List<String> checkUpdateDataIds(List<CacheData> cacheDatas, List<String> inInitializingCacheList) throws IOException {
        StringBuilder sb = new StringBuilder();
        for (CacheData cacheData : cacheDatas) {
            if (!cacheData.isUseLocalConfigInfo()) {
                sb.append(cacheData.dataId).append(WORD_SEPARATOR);
                sb.append(cacheData.group).append(WORD_SEPARATOR);
                if (StringUtils.isBlank(cacheData.tenant)) {
                    sb.append(cacheData.getMd5()).append(LINE_SEPARATOR);
                } else {
                    sb.append(cacheData.getMd5()).append(WORD_SEPARATOR);
                    sb.append(cacheData.getTenant()).append(LINE_SEPARATOR);
                }
                if (cacheData.isInitializing()) {
                    // cacheData 首次出现在cacheMap中&首次check更新
                    inInitializingCacheList
                        .add(GroupKey.getKeyTenant(cacheData.dataId, cacheData.group, cacheData.tenant));
                }
            }
        }
        boolean isInitializingCacheList = !inInitializingCacheList.isEmpty();
        // 检查更新配置字符串
        return checkUpdateConfigStr(sb.toString(), isInitializingCacheList);
    }
    
	/**
     * 从Server获取值变化了的DataID列表。返回的对象里只有dataId和group是有效的。 保证不返回NULL。
     */
    List<String> checkUpdateConfigStr(String probeUpdateString, boolean isInitializingCacheList) throws IOException {

        List<String> params = Arrays.asList(Constants.PROBE_MODIFY_REQUEST, probeUpdateString);

        List<String> headers = new ArrayList<String>(2);
        headers.add("Long-Pulling-Timeout");
        // 设置超时时间，默认30s
        headers.add("" + timeout);

        // told server do not hang me up if new initializing cacheData added in
        // 是否初始化缓存列表
        if (isInitializingCacheList) {
            headers.add("Long-Pulling-Timeout-No-Hangup");
            headers.add("true");
        }

		// 为空，直接return
        if (StringUtils.isBlank(probeUpdateString)) {
            return Collections.emptyList();
        }

        try {
        	// 发送带超时时间的Http请求，请求路径：/v1/cs/configs/listener
            HttpResult result = agent.httpPost(Constants.CONFIG_CONTROLLER_PATH + "/listener", headers, params,
                agent.getEncode(), timeout);

            if (HttpURLConnection.HTTP_OK == result.code) {
                setHealthServer(true);
                // 解析更新数据 ID 响应
                return parseUpdateDataIdResponse(result.content);
            } else {
                setHealthServer(false);
                LOGGER.error("[{}] [check-update] get changed dataId error, code: {}", agent.getName(), result.code);
            }
        } catch (IOException e) {
            setHealthServer(false);
            LOGGER.error("[" + agent.getName() + "] [check-update] get changed dataId exception", e);
            throw e;
        }
        // 超时返回 Collections.emptyList()
        return Collections.emptyList();
    }
    
    // 检查本地配置
	private void checkLocalConfig(CacheData cacheData) {
        final String dataId = cacheData.dataId;
        final String group = cacheData.group;
        final String tenant = cacheData.tenant;
        File path = LocalConfigInfoProcessor.getFailoverFile(agent.getName(), dataId, group, tenant);

        // 没有 -> 有
        if (!cacheData.isUseLocalConfigInfo() && path.exists()) {
            String content = LocalConfigInfoProcessor.getFailover(agent.getName(), dataId, group, tenant);
            String md5 = MD5.getInstance().getMD5String(content);
            cacheData.setUseLocalConfigInfo(true);
            cacheData.setLocalConfigInfoVersion(path.lastModified());
            cacheData.setContent(content);

            LOGGER.warn("[{}] [failover-change] failover file created. dataId={}, group={}, tenant={}, md5={}, content={}",
                agent.getName(), dataId, group, tenant, md5, ContentUtils.truncateContent(content));
            return;
        }

        // 有 -> 没有。不通知业务监听器，从server拿到配置后通知。
        if (cacheData.isUseLocalConfigInfo() && !path.exists()) {
            cacheData.setUseLocalConfigInfo(false);
            LOGGER.warn("[{}] [failover-change] failover file deleted. dataId={}, group={}, tenant={}", agent.getName(),
                dataId, group, tenant);
            return;
        }

        // 有变更
        if (cacheData.isUseLocalConfigInfo() && path.exists()
            && cacheData.getLocalConfigInfoVersion() != path.lastModified()) {
            String content = LocalConfigInfoProcessor.getFailover(agent.getName(), dataId, group, tenant);
            String md5 = MD5.getInstance().getMD5String(content);
            cacheData.setUseLocalConfigInfo(true);
            cacheData.setLocalConfigInfoVersion(path.lastModified());
            cacheData.setContent(content);
            LOGGER.warn("[{}] [failover-change] failover file changed. dataId={}, group={}, tenant={}, md5={}, content={}",
                agent.getName(), dataId, group, tenant, md5, ContentUtils.truncateContent(content));
        }
    }
}

```

然后我们可以看下服务端是怎么来处理这个请求的，客户端会发送一个 `/v1/cs/configs/listener` 的请求。

```java
// 发送带超时时间的Http请求，请求路径：/v1/cs/configs/listener
HttpResult result = agent.httpPost(Constants.CONFIG_CONTROLLER_PATH + "/listener", headers, params,
    agent.getEncode(), timeout);

```

服务端收到请求后，处理如下：

```java
@Controller
// Constants.CONFIG_CONTROLLER_PATH = /v1/cs/configs
@RequestMapping(Constants.CONFIG_CONTROLLER_PATH)
public class ConfigController {

    /**
     * 比较MD5
     */
	@RequestMapping(value = "/listener", method = RequestMethod.POST)
    public void listener(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
        request.setAttribute("org.apache.catalina.ASYNC_SUPPORTED", true);
        // 获取需要比较的字符串
        String probeModify = request.getParameter("Listening-Configs");
        if (StringUtils.isBlank(probeModify)) {
            throw new IllegalArgumentException("invalid probeModify");
        }

		// 解码
        probeModify = URLDecoder.decode(probeModify, Constants.ENCODE);

        // key -> groupKey  value -> md5
        Map<String, String> clientMd5Map;
        try {
        	// 获取客户端传输过来的md5值
            clientMd5Map = MD5Util.getClientMd5Map(probeModify);
        } catch (Throwable e) {
            throw new IllegalArgumentException("invalid probeModify");
        }

        // do long-polling
        // 长轮询
        inner.doPollingConfig(request, response, clientMd5Map, probeModify.length());
    }
}

```

进入到轮询的方法中

```java
public String doPollingConfig(HttpServletRequest request, HttpServletResponse response,
            Map<String, String> clientMd5Map, int probeRequestSize) throws IOException {
        
        // Long polling.
        if (LongPollingService.isSupportLongPolling(request)) {
            // 添加长轮询的客户端
            longPollingService.addLongPollingClient(request, response, clientMd5Map, probeRequestSize);
            return HttpServletResponse.SC_OK + "";
        }
        
        // Compatible with short polling logic. 兼容短轮询逻辑
        List<String> changedGroups = MD5Util.compareMd5(request, response, clientMd5Map);
        
        // Compatible with short polling result. 兼容短轮询的result
        String oldResult = MD5Util.compareMd5OldResult(changedGroups);
        String newResult = MD5Util.compareMd5ResultString(changedGroups);
        
        String version = request.getHeader(Constants.CLIENT_VERSION_HEADER);
        if (version == null) {
            version = "2.0.0";
        }
        int versionNum = Protocol.getVersionNumber(version);
        
        // Before 2.0.4 version, return value is put into header.
        // 2.0.4 之前的版本 返回值放入header中
        if (versionNum < START_LONG_POLLING_VERSION_NUM) {
            response.addHeader(Constants.PROBE_MODIFY_RESPONSE, oldResult);
            response.addHeader(Constants.PROBE_MODIFY_RESPONSE_NEW, newResult);
        } else {
            request.setAttribute("content", newResult);
        }
        
        // Disable cache. 禁用缓存
        response.setHeader("Pragma", "no-cache");
        response.setDateHeader("Expires", 0);
        response.setHeader("Cache-Control", "no-cache,no-store");
        response.setStatus(HttpServletResponse.SC_OK);
        return HttpServletResponse.SC_OK + "";
    }
```

然后来看看是如何添加长轮询的客户端的`longPollingService.addLongPollingClient`

```java 
public void addLongPollingClient(HttpServletRequest req, HttpServletResponse rsp, Map<String, String> clientMd5Map,
        int probeRequestSize) {
    // 获取客户端超时时间
    String str = req.getHeader(LongPollingService.LONG_POLLING_HEADER);
    // 挂断标志
    String noHangUpFlag = req.getHeader(LongPollingService.LONG_POLLING_NO_HANG_UP_HEADER);
    // 延迟时间，服务端处理时间。0.5s
    int delayTime = SwitchService.getSwitchInteger(SwitchService.FIXED_DELAY_TIME, 500);

    /**
     * 提前500ms返回响应，为避免客户端超时
     */
    // Add delay time for LoadBalance, and one response is returned 500 ms in advance to avoid client timeout.
    long timeout = -1L;
    if (isFixedPolling()) {
        timeout = Math.max(10000, getFixedPollingInterval());
        // Do nothing but set fix polling timeout.
    } else {
        timeout = Math.max(10000, Long.parseLong(str) - delayTime);
        long start = System.currentTimeMillis();
        // 先检查是否存在变更，如果存在，直接返回
        List<String> changedGroups = MD5Util.compareMd5(req, rsp, clientMd5Map);
        if (changedGroups.size() > 0) {
            generateResponse(req, rsp, changedGroups);
            LogUtil.CLIENT_LOG.info("{}|{}|{}|{}|{}|{}|{}", System.currentTimeMillis() - start, "instant",
                    RequestUtil.getRemoteIp(req), "polling", clientMd5Map.size(), probeRequestSize,
                    changedGroups.size());
            return;
        } else if (noHangUpFlag != null && noHangUpFlag.equalsIgnoreCase(TRUE_STR)) {
            LogUtil.CLIENT_LOG.info("{}|{}|{}|{}|{}|{}|{}", System.currentTimeMillis() - start, "nohangup",
                    RequestUtil.getRemoteIp(req), "polling", clientMd5Map.size(), probeRequestSize,
                    changedGroups.size());
            return;
        }
    }
    /*************************不存在变更，采用servlet 3.0 异步处理***************************/
    // 客户端ip
    String ip = RequestUtil.getRemoteIp(req);

    ConnectionCheckResponse connectionCheckResponse = checkLimit(req);
    if (!connectionCheckResponse.isSuccess()) {
        generate503Response(req, rsp, connectionCheckResponse.getMessage());
        return;
    }
    // 一定要由HTTP线程调用，否则离开后容器会立即发送响应
    // Must be called by http thread, or send response.
    final AsyncContext asyncContext = req.startAsync();

    // AsyncContext.setTimeout()的超时时间不准，所以只能自己控制
    // AsyncContext.setTimeout() is incorrect, Control by oneself
    asyncContext.setTimeout(0L);
    
    String appName = req.getHeader(RequestUtil.CLIENT_APPNAME_HEADER);
    String tag = req.getHeader("Vipserver-Tag");
    // 开启定时任务
    // 其中，timeout = Math.max(10000, Long.parseLong(str) - delayTime) = Math.max(10000, 30000 - 500) = 29.5s
    ConfigExecutor.executeLongPolling(
            new ClientLongPolling(asyncContext, clientMd5Map, ip, probeRequestSize, timeout, appName, tag));
}
```

ClientLongPolling 为 LongPollingService 的内部类,我们进入

```Java
@Override
public void run() {
    // 开启一个延时线程，timeoutTime=29.5s
    asyncTimeoutFuture = ConfigExecutor.scheduleLongPolling(() -> {
        try {
            // 获取并设置客户端IP
            getRetainIps().put(ClientLongPolling.this.ip, System.currentTimeMillis());
            
            // Delete subscriber's relations. 删除订阅关系
            boolean removeFlag = allSubs.remove(ClientLongPolling.this);
            // 是否固定轮询
            if (removeFlag) {
                if (isFixedPolling()) {
                    LogUtil.CLIENT_LOG
                            .info("{}|{}|{}|{}|{}|{}", (System.currentTimeMillis() - createTime), "fix",
                                    RequestUtil.getRemoteIp((HttpServletRequest) asyncContext.getRequest()),
                                    "polling", clientMd5Map.size(), probeRequestSize);
                    // 通过md5值，获取当前所有变更的groups
                    List<String> changedGroups = MD5Util
                            .compareMd5((HttpServletRequest) asyncContext.getRequest(),
                                    (HttpServletResponse) asyncContext.getResponse(), clientMd5Map);
                    if (changedGroups.size() > 0) {
                        // 发送数据
                        sendResponse(changedGroups);
                    } else {
                        // 发送数据
                        sendResponse(null);
                    }
                } else {
                    LogUtil.CLIENT_LOG
                            .info("{}|{}|{}|{}|{}|{}", (System.currentTimeMillis() - createTime), "timeout",
                                    RequestUtil.getRemoteIp((HttpServletRequest) asyncContext.getRequest()),
                                    "polling", clientMd5Map.size(), probeRequestSize);
                    // 发送数据
                    sendResponse(null);
                }
            } else {
                LogUtil.DEFAULT_LOG.warn("client subsciber's relations delete fail.");
            }
        } catch (Throwable t) {
            LogUtil.DEFAULT_LOG.error("long polling error:" + t.getMessage(), t.getCause());
        }
        
    }, timeoutTime, TimeUnit.MILLISECONDS);
    // 添加订阅关系
    allSubs.add(this);
}

void sendResponse(List<String> changedGroups) {
    
    // Cancel time out task. 取消超时任务
    if (null != asyncTimeoutFuture) {
        asyncTimeoutFuture.cancel(false);
    }
    generateResponse(changedGroups);
}

void generateResponse(List<String> changedGroups) {

    if (null == changedGroups) {
        // Tell web container to send http response. 告诉容器发送HTTP响应
        asyncContext.complete();
        return;
    }

    HttpServletResponse response = (HttpServletResponse) asyncContext.getResponse();
    
    try {
        // 获取resp
        final String respString = MD5Util.compareMd5ResultString(changedGroups);
        
        // Disable cache. // 禁用缓存
        response.setHeader("Pragma", "no-cache");
        response.setDateHeader("Expires", 0);
        response.setHeader("Cache-Control", "no-cache,no-store");
        response.setStatus(HttpServletResponse.SC_OK);
        response.getWriter().println(respString); // 回写数据
        asyncContext.complete();
    } catch (Exception ex) {
        PULL_LOG.error(ex.toString(), ex);
        asyncContext.complete();
    }
}
```

到这儿服务端的处理我们就明白了。也就是服务端会延迟29.5秒来执行配置检查的任务。如果有变化话就返回变化的数据否则返回null给客户端。客户端获取到的如果是null就用本地的数据了。

**注意：如果在29.5s内发生变化，那么nacos是怎么处理的呢？**

假如，在这29.5s内，你进入了nacos控制台，修改配置内容后，保存发布，那这个时候，nacos服务端会做哪些内容呢？？

通过浏览器控制台可发现，当你点击保存后，会调用nacos服务端的 `/v1/cs/configs/` 请求，最后发送一个 LocalDataChangeEvent 事件。

```java
@Service
public class LongPollingService extends AbstractEventListener {
    /**
     * 长轮询订阅关系
     */
    final Queue<ClientLongPolling> allSubs;
    
	@Override
    public void onEvent(Event event) {
        if (isFixedPolling()) {
            // ignore
        } else {
            if (event instanceof LocalDataChangeEvent) {
            	// 接收 LocalDataChangeEvent  
                LocalDataChangeEvent evt = (LocalDataChangeEvent)event;
                // 执行 DataChangeTask
                scheduler.execute(new DataChangeTask(evt.groupKey, evt.isBeta, evt.betaIps));
            }
        }
    }

	class DataChangeTask implements Runnable {
        @Override
        public void run() {
            try {
                ConfigService.getContentBetaMd5(groupKey);
                // 循环遍历 allSubs Queue<ClientLongPolling> allSubs;
                for (Iterator<ClientLongPolling> iter = allSubs.iterator(); iter.hasNext(); ) {
                    ClientLongPolling clientSub = iter.next();
					
					// 如果当前 ClientLongPolling  中的 clientMd5Map key中存在当前 groupKey，则进行通知
                    if (clientSub.clientMd5Map.containsKey(groupKey)) {
                        // 如果beta发布且不在beta列表直接跳过
                        if (isBeta && !betaIps.contains(clientSub.ip)) {
                            continue;
                        }

                        // 如果tag发布且不在tag列表直接跳过
                        if (StringUtils.isNotBlank(tag) && !tag.equals(clientSub.tag)) {
                            continue;
                        }

                        getRetainIps().put(clientSub.ip, System.currentTimeMillis());
                        
                        // 删除订阅关系
                        iter.remove();
                        
                        LogUtil.clientLog.info("{}|{}|{}|{}|{}|{}|{}",
                            (System.currentTimeMillis() - changeTime),
                            "in-advance",
                            RequestUtil.getRemoteIp((HttpServletRequest)clientSub.asyncContext.getRequest()),
                            "polling",
                            clientSub.clientMd5Map.size(), clientSub.probeRequestSize, groupKey);
                            
                        // 发送服务配置变更groupKey，完成实时通知
                        clientSub.sendResponse(Arrays.asList(groupKey));
                    }
                }
            } catch (Throwable t) {
                LogUtil.defaultLog.error("data change error:" + t.getMessage(), t.getCause());
            }
        }

        DataChangeTask(String groupKey) {
            this(groupKey, false, null);
        }

        DataChangeTask(String groupKey, boolean isBeta, List<String> betaIps) {
            this(groupKey, isBeta, betaIps, null);
        }

        DataChangeTask(String groupKey, boolean isBeta, List<String> betaIps, String tag) {
            this.groupKey = groupKey;
            this.isBeta = isBeta;
            this.betaIps = betaIps;
            this.tag = tag;
        }

        final String groupKey;
        final long changeTime = System.currentTimeMillis();
        final boolean isBeta;
        final List<String> betaIps;
        final String tag;
    }
}

```

至此，Nacos 配置中心原理分析完成，下面我们回顾一下整体流程。

![image-20240815162733344](img/image-20240815162733344.png)



### 3.5 SpringBoot整合的逻辑

​	前面我们分析配置中的处理是通过单独的案例来讲解的。接下来我们可以看看在完整的SpringBoot项目中是怎么和配置中心结合起来的。首先找配置中心客户端的核心依赖。

![image-20240815165139101](img/image-20240815165139101.png)

找到对应的配置类`NacosConfiguration`类

```Java
@Bean
public NacosConfigManager nacosConfigManager(NacosConfigProperties nacosConfigProperties) {
    return new NacosConfigManager(nacosConfigProperties);
}
```

进入后找到这个核心方法,会把`NacosCofingManager`注入到SpringBoot容器中。而进入到这个类中查看。

在对应的构造方法中完成了`ConfigService`对象的创建

```java
    public NacosConfigManager(NacosConfigProperties nacosConfigProperties) {
        this.nacosConfigProperties = nacosConfigProperties;
        createConfigService(nacosConfigProperties);
    }
static ConfigService createConfigService(NacosConfigProperties nacosConfigProperties) {
        if (Objects.isNull(service)) {
            Class var1 = NacosConfigManager.class;
            synchronized(NacosConfigManager.class) {
                try {
                    if (Objects.isNull(service)) {
                        // 完成对应的 ConfigService 的创建
                        service = NacosFactory.createConfigService(nacosConfigProperties.assembleConfigServiceProperties());
                    }
                } catch (NacosException var4) {
                    log.error(var4.getMessage());
                    throw new NacosConnectionFailureException(nacosConfigProperties.getServerAddr(), var4.getMessage(), var4);
                }
            }
        }

        return service;
    }
```

逻辑和我们前面介绍讲解的是一致的。然后我们来看看获取配置信息的方法什么时候执行的。

![image-20240815165902174](img/image-20240815165902174.png)

这时我们可以在`ConfigService`的`getConfig(...)`方法处打个断点来跟踪下执行的路径就清楚了。

![image-20240815170028380](img/image-20240815170028380.png)



### 3.6 @RefreshScope注解的原理

​	@Value注解可以在项目启动时获取到配置中心的值，但是如果在Nacos配置中心后台修改了值，此时项目是无法动态感知修改后的值，需要利用@RefreshScope注解来实现动态感知。

首先来看看@RefreshScope这个注解：

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Scope("refresh")
@Documented
public @interface RefreshScope {
    ScopedProxyMode proxyMode() default ScopedProxyMode.TARGET_CLASS;
}
```

其内部就一个属性默认ScopedProxyMode.TARGET_CLASS。

然后我们需要跟踪来看看这个注解修饰的java类会怎么样。

被@RefreshScope注解的类，最终会调用RefreshScope的get()方法实例化Bean。

org.springframework.beans.factory.support.AbstractBeanFactory#doGetBean

```java
... ...
if (mbd.isSingleton()) {
	// 单例Bean的实例化
}
else if (mbd.isPrototype()) {
	// 多例Bean的实例化
}
else {
	// 自定义作用域的Bean的实例化
	// scopeName为refresh
	String scopeName = mbd.getScope();
	if (!StringUtils.hasLength(scopeName)) {
		throw new IllegalStateException("No scope name defined for bean ´" + beanName + "'");
	}
	// scope为RefreshScope
	Scope scope = this.scopes.get(scopeName);
	if (scope == null) {
		throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
	}
	try {
		Object scopedInstance = scope.get(beanName, () -> {
			beforePrototypeCreation(beanName);
			try {
				return createBean(beanName, mbd, args);
			}
			finally {
				afterPrototypeCreation(beanName);
			}
		});
		bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
	}
	catch (IllegalStateException ex) {
		throw new BeanCreationException(beanName,
										"Scope '" + scopeName + "' is not active for the current thread; consider " +
										"defining a scoped proxy for this bean if you intend to refer to it from a singleton",
										ex);
	}
}

```

RefreshScope.get()

@RefreshScope作用域的Bean会在第一次创建时进行缓存，包装了一个内部类 BeanLifecycleWrapperCache来对加了@RefreshScope从而创建的对象进行缓存，使其在不刷新时获取的都是同一个对象。

org.springframework.cloud.context.scope.GenericScope#get


```java
public Object get(String name, ObjectFactory<?> objectFactory) {
	BeanLifecycleWrapper value = this.cache.put(name,
												new BeanLifecycleWrapper(name, objectFactory));
	this.locks.putIfAbsent(name, new ReentrantReadWriteLock());
	try {
		return value.getBean();
	}
	catch (RuntimeException e) {
		this.errors.put(name, e);
		throw e;
	}
}

```

知道了对象是缓存的，所以在配置修改后只需要清除缓存，重新创建就好了。

RefreshEventListener处理容器的刷新事件

当配置中心的内容变更后，Nacos客户端收到变更会触发RefreshEvent事件。

org.springframework.cloud.endpoint.event.RefreshEventListener#handle(org.springframework.cloud.endpoint.event.RefreshEvent)

```java
public void handle(RefreshEvent event) {
	if (this.ready.get()) { // don't handle events before app is ready
		log.debug("Event received " + event.getEventDesc());
		Set<String> keys = this.refresh.refresh();
		log.info("Refresh keys changed: " + keys);
	}
}

```

org.springframework.cloud.context.refresh.ContextRefresher#refresh

```java
public synchronized Set<String> refresh() {
	Set<String> keys = refreshEnvironment();
	this.scope.refreshAll();
	return keys;
}

```

org.springframework.cloud.context.scope.refresh.RefreshScope#refreshAll



```java
public void refreshAll() {
	super.destroy();
	this.context.publishEvent(new RefreshScopeRefreshedEvent());
}

```

org.springframework.cloud.context.scope.GenericScope#destroy()

```java
public void destroy() {
	List<Throwable> errors = new ArrayList<Throwable>();
	// 清理缓存
	Collection<BeanLifecycleWrapper> wrappers = this.cache.clear();
	for (BeanLifecycleWrapper wrapper : wrappers) {
		try {
			Lock lock = this.locks.get(wrapper.getName()).writeLock();
			lock.lock();
			try {
				// 销毁实例
				wrapper.destroy();
			}
			finally {
				lock.unlock();
			}
		}
		catch (RuntimeException e) {
			errors.add(e);
		}
	}
	if (!errors.isEmpty()) {
		throw wrapIfNecessary(errors.get(0));
	}
	this.errors.clear();
}

```

在下一次使用对象的时候，代理对象中获取目标对象的时候会调用GenericScope.get()方法创建一个新的对象，并存入缓存中，此时新对象因为Spring的装配机制就是新的属性了。



# 四、Nacos常见面试题

## 1.Nacos为什么会选择GRPC?

​	这里需要首先Nacos有两个比较重要的版本

- 1.x：是基于http协议
- 2.x：是基于GRPC协议，性能相比1.x有了显著的提示(官方说明10倍)

​	

这里我们需要明白这三个问题

1. http1.x 协议的短板
2. 这个短板在nacos中的影响
3. GRPC的优势【http2.x 多路复用 Header压缩 Protobuf 长连接】



## 2.Nacos中的保护阈值的作用是什么？

​	假如现在有一个服务，本来有10个实例，但是现在挂掉了8个，剩下2个正常实例，此时本来由10个实例处理的流量，就全部交给这个两个正常实例来处理了，此时这两个实例很有可能是处理不过来的，最终导致被压垮，为了应对这种情况，Nacos提供了保护阈值这个功能，我们可以给某个服务设置一个0-1的阈值，比如0.5，那就表示，一旦实例中只剩下一半的健康实例了，比如10个实例，只剩下5个健康实例了，那么消费者在进行服务发现时，则会把该服务的所有实例，也包括不健康的实例都拉取到本地，然后再从所有实例中进行负载均衡，选出一个实例进行调用，在这种情况下，选出来的即可能是一个健康的实例，也可能是挂掉的实例，但是通过这种方式，很好的保护的剩下的健康实例，至少保证了一部分请求能正常的访问，而不至于所有请求都不能正常访问，这就是Nacos中的保护阈值。



## 3. NACOS中的负载均衡是怎么样的？

​	Nacos的负载均衡指的是，在进行服务发现时进行负载均衡，正常情况下，在进行服务发现时，会根据服务名从Nacos中拉取所有的实例信息，但是Nacos中提供了一个功能，就是可以在拉取实例时，可以根据随机策略只拉取到所有实例中的某一个，这就是Nacos中的负载均衡，它跟Ribbon的负载均衡并不冲突，可以理解为Ribbon的负载均衡是发生在Nacos的负载均衡之后的。

https://nacos.io/docs/ebook/ktwggk.mdx/

## 4. Nacos的就近访问是什么意思？

![image-20240817115713312](img/image-20240817115713312.png)

​	首先，在Nacos中，一个服务可以有多个实例，并且可以给实例设置cluster-name，就是可以再进一步的给所有实例划分集群，那如果现在某个服务A想要调用服务B，那么Naocs会看调用服务A的实例是属于哪个集群的，并且调用服务B时，那就会调用同样集群下的服务B实例，根据cluster-name来判断两个实例是不是同一个集群，这就是Nacos的就近访问。



## 5.Nacos中保证的是CP还是AP？

​	通常我们说，Nacos既能保证CP，也能保证AP，具体看如何配置，但其实只不过是Nacos中的注册中心能保证CP或AP，Nacos中的配置中心其实没什么CP或AP，因为配置中心的数据是存在一个Mysql中的，只有注册中心的数据需要进行集群节点之间的同步，从而涉及到是CP还是AP，如果注册的节点是临时节点，那么就是AP，如果是非临时节点，那么就是CP，默认是临时节点。



## 6. Nacos服务是如何判断服务实例的状态的?

- 心跳检测：Nacos会定期向每个注册的服务实例发送心跳请求，以检测其是否存活。如果Nacos在一段时间内没有收到实例的心跳回复，就会将该实例标记为不可用。
- 健康检查：Nacos可以配置一些健康检查的规则，例如HTTP接口的返回状态码、响应时间等。Nacos会定期调用这些接口，根据返回结果来判断服务实例的健康状况。如果实例返回的结果不符合预期的健康规则，Nacos会将其标记为不可用。
- 负载均衡：Nacos还会根据实例的负载情况来判定其状态。如果某个实例的负载过高，超过了一定的阈值，Nacos可能会将其标记为不可用，以避免过多的请求落在该实例上，导致服务质量下降。
  



## 7. Nacos1.x 注册中心架构流程

![image-20240817133401962](img/image-20240817133401962.png)

1. 服务启动的时候，通过api发起服务注册，来告诉nacos，我可以提供服务了

2. 服务消费者在启动的时候会拉取 自已要用的服务的列表。（这只是启动的时候拉取，那平常有上下线服务怎么办呢？定时拉取）

3. 消费者会每10秒进行拉取一下数据。拉取我可以调用的服务。（如果我们只用定时任务拉取配置，那注册服务会有一定的滞后性，那怎么让它具有实时性呢？下面udp协议）

4. Nacos服务检测到有异常（服务上下线）就会发送UDP协议给客户端进行更新（为什么是udp写协议？因为udp协议相对于tcp协议来说，他并不是可靠的协议，但他的优点就是他非常快，他耗时比较短，不需要跟自己目标服务器建立长连接，在服务注册这种场景，我们服务消费者要比服务提供者要多很多，所以每一次 服务的更新，nacos需要和成千上万的消费者，去建立tcp的话，他的性能一定是受不了的，所以他会选择一个udp协议进行通知，那有的同学会问了，udp他不可靠，他通知失败了怎么办？ 大家可以看一下第三部，我们消费者每10秒还会拉取一下服务数据信息上面说是检测到异常，那Nacos怎样判断异常的呢？ 心跳，）

5. 客户端会每5秒定时发送心跳到服务端，来维持他的一个健康的检查，

6. Nacos定时任务检测：通过每5秒检查一下心跳信息来判断是否超时,就是用当前时间减去上次一心跳的时间，如果超过15秒则将节点设置为非健康状态并进行广播，如果超过30秒则将节点进行移除，说明节点不可用。

7. 集群数据同步任务使用协议：Distro(AP) , Raft(CP)



## 8.Nacos2.x作为注册中心架构流程

![image-20240817134623714](img/image-20240817134623714.png)





## 9. Nacos中的Distro协议

​	Distro 协议是 Nacos 社区自研的一种 AP 分布式协议，是面向临时实例设计的一种分布式协议，其保证了在某些 Nacos  节点宕机后，整个临时实例处理系统依旧可以正常工作。作为一种有状态的中间件应用的内嵌协议，Distro 保证了各个 Nacos  节点对于海量注册请求的统一协调和存储。

Distro 协议的主要设计思想如下：

- Nacos 每个节点是平等的都可以处理写请求，同时把新数据同步到其他节点。
- 每个节点只负责部分数据，定时发送自己负责数据的校验值到其他节点来保持数据一致性。
- 每个节点独立处理读请求，及时从本地发出响应。

https://nacos.io/docs/ebook/ktwggk.mdx/







