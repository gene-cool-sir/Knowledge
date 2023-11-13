# RocketMQ快速开始

RocketMQ的官网地址： [http://rocketmq.apache.org](http://rocketmq.apache.org/ "http://rocketmq.apache.org")&#x20;

github地址是 [https://github.com/apache/rocketmq](https://github.com/apache/rocketmq "https://github.com/apache/rocketmq")

### 一. 简介

#### 1.发展史

早期阿里使用ActiveMQ，但是，当消息开始逐渐增多后，ActiveMQ的IO性能很快达到了瓶颈。于是，阿里开始关注Kafka。但是Kafka是针对日志收集场景设计的，他的并发性能并不是很理想。尤其当他的Topic过多时，由于Partition文件也会过多，会严重影响IO性能。于是阿里才决定自研中间件，最早叫做MetaQ，后来改名成为RocketMQ。最早他所希望解决的最大问题就是多Topic下的IO性能压力。但是产品在阿里内部的不断改进，RocketMQ开始体现出一些不一样的优势。

#### 2.特点

[**Apache RocketMQ**](https://rocketmq.apache.org/ "Apache RocketMQ")**是一个分布式消息和流媒体平台，具有低延迟、高性能和可靠性、万亿级容量和灵活的可扩展性。**

它提供多种功能：

-   消息传递模式包括发布/订阅、请求/回复和流式传输
-   金融级交易消息
-   [基于DLedger Controller 的](https://github.com/apache/rocketmq/blob/develop/docs/en/controller/quick_start.md "基于DLedger Controller 的")内置容错和高可用性配置选项
-   内置消息追踪能力，也支持opentracing
-   多功能大数据和流媒体生态系统集成
-   按时间或偏移量追溯消息
-   同一队列中可靠的 FIFO 和严格有序的消息传递
-   高效的拉推消费模式
-   单队列百万级消息累积能力
-   多种消息传递协议，例如 gRPC、MQTT、JMS 和 OpenMessaging
-   灵活的分布式横向扩展部署架构
-   闪电般快速的批量消息交换系统
-   SQL、Tag等多种消息过滤机制
-   用于隔离测试和云隔离集群的 Docker 镜像
-   用于配置、指标和监控的功能丰富的管理仪表板
-   认证与授权
-   免费的开源连接器，适用于源和接收器
-   轻量级实时计算

### 二.快速开始

### 1.单机安装Linux安装

#### 1.环境要求与准备

-   RocketMQ可以在所有主要操作系统上运行，并且只需要安装Java JDK版本8或更高版本。要检查，请运行`java -version`
-   &#x20; 1\. 准备一台CentOS7的Linux机器

    uname -a
    1.  创建一个普通用户和工作目录
        sudo  mkdir /app &#x20;
    2.  java 环境安装
        vi \~/.bash\_profile
    source \~/.bash\_profile

    &#x20; 4\. Linux下载

    \# Download release from the Apache mirror

    \$ wget [https://dist.apache.org/repos/dist/release/rocketmq/5.1.4/rocketmq-all-5.1.4-bin-release.zip](https://dist.apache.org/repos/dist/release/rocketmq/5.1.4/rocketmq-all-5.1.4-bin-release.zip "https://dist.apache.org/repos/dist/release/rocketmq/5.1.4/rocketmq-all-5.1.4-bin-release.zip")

    \# Unpack the release

    \$ unzip rocketmq-all-5.1.4-bin-release.zip

    cp  ./    /app/
    1.  把rocketmq的bin目录也配置到环境变量当中。
        &#x20;vi \~/.bash\_profile，加入以下内容，并执行source \~/.bash\_profile让环境变量生效

#### 2.RocketMQ组成

RocketMQ由以下这几个组件组成

-   NameServer : 提供轻量级的Broker路由服务。
-   Broker：实际处理消息存储、转发等服务的核心组件。
-   Producer：消息生产者集群。通常是业务系统中的一个功能模块。
-   Consumer：消息消费者集群。通常也是业务系统中的一个功能模块。
-   Topic：区分消息的种类；一个发送者可以发送消息给一个或者多个Topic；一个消息的接收者可以订阅一个或者多个Topic消息

#### 2.服务启动

每启动一个服务,可通过JPS 查看JAVA服务启动进程

-   &#x20; 1\. **启动名称服务器**NameServer&#x20;
    -   RocketMQ默认预设的JVM内存是4G，这是RocketMQ给我们的最佳配置。但是通常我们用虚拟机的话都是不够4G内存的，所以需要调整下JVM内存大小。[修改的方式是直接修改runserver.sh](http://xn--runserver-z89na9857bcqmtlfda85rmzcf95l5zb.sh/ "修改的方式是直接修改runserver.sh")。 用vi runserver.sh编辑这个脚本(按照机器内存大小调整)
        JAVA\_OPT="\${JAVA\_OPT} -server -Xms512m -Xmx512m -Xmn256m - XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
    -   NameServer 将监听`0.0.0.0:9876`，确保本机上该端口没有被其他人使用，然后执行以下操作
    -   启动NameServer
        &#x20;nohup sh mqnamesrv &
    &#x20;tail -f \~/logs/rocketmqlogs/namesrv.log
-   2 **. 启动Broker服务** NameServer
    -   [启动Broker的脚本是runbroker.sh](http://xn--brokerrunbroker-wy8y53qb44gl6dtn1h9p4b.sh/ "启动Broker的脚本是runbroker.sh")。Broker的默认预设内存是8G，启动前，如果内存不够，同样需要调整下JVM内存。vi [runbroker.sh](http://runbroker.sh/ "runbroker.sh")，找到这一行，进行内存调整
        &#x20;JAVA\_OPT="\${JAVA\_OPT} -server -Xms512m -Xmx512m"
    -   需要找到\$ROCKETMQ\_HOME/conf/broker.conf， vi指令进行编辑(开启自动创建topic主题(测试开启,线上关闭))
        autoCreateTopicEnable=true
    -   启动 broker
        nohup sh bin/mqbroker -n localhost:9876 &
-   留意

    在观察runserver.sh和runbroker.sh时，我们还可以查看到其他的JVM执行参数，这些参数都可以进行定制。例如我们观察到一个比较有意思的地方

    nameServer使用的是CMS垃圾回收器

    而Broker使用的是G1垃圾回收器。
-   命令行客户端

    在RocketMQ的安装包中，提供了一个tools.sh工具可以用来在命令行快速验证RocketMQ服务。
    -   需要配置一个环境变量NAMESRV\_ADDR指向我们启动的NameServer服务
        export NAMESRV\_ADDR='localhost:9876'&#x20;
    -   启动消息生产者发送消息：默认会发1000条消息
        bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
    -   启动消息消费者接收消息
        bin/tools.sh  org.apache.rocketmq.example.quickstart.Consumer
    &#x20; 而这个Consume指令并不会结束，他会继续挂起，等待消费其他的消息。我们可以使用CTRL+C停止该进程。
-   RocketMQ服务关闭

    1.关闭NameServer &#x20;

    sh bin/mqshutdown namesrv &#x20;

    2.关闭Broker &#x20;

    sh bin/mqshutdown broker

### 2.集群搭建

## 三.控制台管理(RocketMQ Dashboard)

`RocketMQ Dashboard` 是 RocketMQ 的管控利器，为用户提供客户端和应用程序的各种事件、性能的统计信息，支持以可视化工具代替 Topic 配置、Broker 管理等命令行操作。

git地址:  [https://github.com/apache/rocketmq-dashboard](https://github.com/apache/rocketmq-dashboard "https://github.com/apache/rocketmq-dashboard")

安装npm:  npm install -g yarn

mvn install -Dmaven.test.skip=true

注意: 请记得切换1.0的分支,否则可能编译问题

## 四. [Apache RocketMQ 社区](https://github.com/apache/rocketmq#apache-rocketmq-community "Apache RocketMQ 社区")

-   [社区](https://github.com/apache/rocketmq#apache-rocketmq-community "社区")
    -   [RocketMQ Streams](https://github.com/apache/rocketmq-streams "RocketMQ Streams")：基于Apache RocketMQ的轻量级流计算引擎。
    -   [RocketMQ Flink](https://github.com/apache/rocketmq-flink "RocketMQ Flink")：Apache Flink 的 Apache RocketMQ 连接器，支持数据流和表中的源连接器和接收器连接器。
    -   [RocketMQ API](https://github.com/apache/rocketmq-apis "RocketMQ API")：RocketMQ protobuf 协议。
    -   [RocketMQ客户端](https://github.com/apache/rocketmq-clients "RocketMQ客户端")：基于gRPC/protobuf的RocketMQ客户端。
    -   基于RocketMQ远程处理的客户端
        -   [RocketMQ客户端CPP](https://github.com/apache/rocketmq-client-cpp "RocketMQ客户端CPP")
        -   [RocketMQ客户端Go](https://github.com/apache/rocketmq-client-go "RocketMQ客户端Go")
        -   [RocketMQ 客户端 Python](https://github.com/apache/rocketmq-client-python "RocketMQ 客户端 Python")
        -   [RocketMQ 客户端 Nodejs](https://github.com/apache/rocketmq-client-nodejs "RocketMQ 客户端 Nodejs")
    -   [RocketMQ Spring](https://github.com/apache/rocketmq-spring "RocketMQ Spring")：帮助开发人员快速将 Apache RocketMQ 与 Spring Boot 集成的项目。
    -   [RocketMQ Exporter](https://github.com/apache/rocketmq-exporter "RocketMQ Exporter")：Prometheus 的 Apache RocketMQ 导出器。
    -   [RocketMQ Operator](https://github.com/apache/rocketmq-operator "RocketMQ Operator")：提供一种在 Kubernetes 上运行 Apache RocketMQ 集群的方法。
    -   [RocketMQ Docker](https://github.com/apache/rocketmq-docker "RocketMQ Docker")：Apache RocketMQ 的 Docker 镜像的 Git 存储库。
    -   [RocketMQ Dashboard](https://github.com/apache/rocketmq-dashboard "RocketMQ Dashboard")：Apache RocketMQ的运维控制台。
    -   [RocketMQ Connect](https://github.com/apache/rocketmq-connect "RocketMQ Connect")：一种在 Apache RocketMQ 和其他系统之间可扩展且可靠的地面传输数据的工具。
    -   [RocketMQ MQTT](https://github.com/apache/rocketmq-mqtt "RocketMQ MQTT")：一种全新的MQTT协议架构模型，基于该模型Apache RocketMQ可以更好地支持来自物联网设备、手机APP等终端的消息。
    -   [RocketMQ EventBridge](https://github.com/apache/rocketmq-eventbridge "RocketMQ EventBridge")：EventBridge使构建事件驱动的应用程序变得更加容易。
    -   [RocketMQ 孵化器社区项目](https://github.com/apache/rocketmq-externals "RocketMQ 孵化器社区项目")：Apache RocketMQ 孵化器社区项目，包括[logappender](https://github.com/apache/rocketmq-externals/tree/master/logappender "logappender")、[RocketMQ-ansible](https://github.com/apache/rocketmq-externals/tree/master/rocketmq-ansible "RocketMQ-ansible")、[RocketMQ-beats-integration](https://github.com/apache/rocketmq-externals/tree/master/rocketmq-beats-integration "RocketMQ-beats-integration")、[RocketMQ-cloudevents-binding](https://github.com/apache/rocketmq-externals/tree/master/rocketmq-cloudevents-binding "RocketMQ-cloudevents-binding")等。
    -   [RocketMQ Site](https://github.com/apache/rocketmq-site "RocketMQ Site")：Apache RocketMQ 网站的存储库。
    -   [RocketMQ E2E](https://github.com/apache/rocketmq-e2e "RocketMQ E2E")：用于测试 Apache RocketMQ 的项目，包括品质、性能、兼容性测试。
