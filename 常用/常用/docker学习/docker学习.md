# docker学习

> docker images
> docker search
> docker ps

-   docker pull  镜像:版本
-   docker rmi 镜像ID  / docker rm -f  \$(docker ps -a -q)
-   docker run -d -p 外部端口:内部端口 容器ID
-   docker stop/start/kill 容器ID
-   docker  inspect  容器ID
-   docker container  logs  容器ID
-   docker  top  容器ID
-   docker  cp  容器ID:容器内部的文件路径: 宿主机路径
-   docker exec -it 容器ID /bin/bash

> DockerFile

-   docker build -t 镜像名称:版本 -f  dockerFile文件
    ```纯文本
    # 基于哪个镜像
    From java:8
    # 复制文件到容器
    ADD microservice-eureka-server-0.0.1-SNAPSHOT.jar /app.jar
    # 声明需要暴露的端口
    EXPOSE 8761
    # 配置容器启动后执行的命令
    ENTRYPOINT ["java","-jar","/app.jar"]   
    ```
    ​
-   docker run -m 500M redis   #限制容器的最大使用内存为500M
-   docker stats  # 返回容器资源的实时使用情况，1秒刷新一次
-   docker stats --no-stream  # 返回容器当时的资源使用情况
-   ​      使用 -v 可以挂载一个主机上的目录到容器的目录
-   docker run -p 外部端口:内部端口 -v  宿主机路径:容器路径  容器ID
-   docker tag  镜像名  分组/镜像名
-   docker push  分组/镜像名
-   docker container rm 容器ID
-   [https://glory.blog.csdn.net/article/details/101845129](https://glory.blog.csdn.net/article/details/101845129 "https://glory.blog.csdn.net/article/details/101845129")

**Dockerfile常用指令**

|           |                                                                                                                 |
| --------- | --------------------------------------------------------------------------------------------------------------- |
| 命令        | 用途                                                                                                              |
| FROM      | 基础镜像文件                                                                                                          |
| RUN       | 构建镜像阶段执行命令                                                                                                      |
| ADD       | 添加文件，从src目录复制文件到容器的dest，其中 src可以是 Dockerfile所在目录的相对路径，也可以是一个 URL,还可以是一个压缩包                                      |
| COPY      | 拷贝文件，和ADD命令类似，但不支持URL和压缩包                                                                                       |
| CMD       | 容器启动后执行命令                                                                                                       |
| EXPOSE    | 声明容器在运行时对外提供的服务端口                                                                                               |
| WORKDIR   | 指定容器工作路径                                                                                                        |
| ENV       | 指定环境变量                                                                                                          |
| ENTRYPINT | 容器入口， ENTRYPOINT和 CMD指令的目的一样，都是指定 Docker容器启动时执行的命令，可多次设置，但只有最后一个有效。                                             |
| USER      | 该指令用于设置启动镜像时的用户或者 UID,写在该指令后的 RUN、 CMD以及 ENTRYPOINT指令都将使用该用户执行命令。                                               |
| VOLUME    | 指定挂载点，该指令使容器中的一个目录具有持久化存储的功能，该目录可被容器本身使用，也可共享给其他容器。当容器中的应用有持久化数据的需求时可以在 Dockerfile中使用该指令。格式为： VOLUME\["/data"]。 |

注意：RUN命令在 image 文件的构建阶段执行，执行结果都会打包进入 image 文件；CMD命令则是在容器启动后执行。另外，一个 Dockerfile 可以包含多个RUN命令，但是只能有一个CMD命令。

注意，指定了CMD命令以后，docker container run命令就不能附加命令了（比如前面的/bin/bash），否则它会覆盖CMD命令。

<https://note.youdao.com/ynoteshare/index.html?id=42384826563c36cddfa032983505bd0d&type=note&_time=1690940028984>

> Mysql8 服务安装

> docker search mysql
> docker pull mysql:8.0
> docker images
> 在宿主机conf目录下创建如下my.cnf

```纯文本
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
secure-file-priv= NULL
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Custom config should go here
!includedir /etc/mysql/conf.d/
```

> 启动命令： （参考： [https://www.shuzhiduo.com/A/1O5E3Zw3z7/）](https://www.shuzhiduo.com/A/1O5E3Zw3z7/） "https://www.shuzhiduo.com/A/1O5E3Zw3z7/）")

```docker
docker run -p 3306:3306 --restart=always  --name mysql -v /Users/bingxie/Documents/ProgramSoftWare/Mysql/conf/my.cnf:/etc/mysql/my.cnf -v /Users/bingxie/Documents/ProgramSoftWare/Mysql/logs:/var/log/mysql -v /Users/bingxie/Documents/ProgramSoftWare/Mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root --privileged=true -d mysql:8.0

```

> 进入容器： docker exec -it mysql bash

> 登陆mysql： mysql -uroot -proot

> 修改用户密码/并远程访问： (host为 % 表示不限制ip localhost表示本机使用 plugin非mysql\_native\_password 则需要修改密码)

1.  ALTER USER 'root'@'%' IDENTIFIED WITH mysql\_native\_password BY '123456';
2.  alter user ‘root’@‘localhost’ identified by ‘123456’;
3.  flush privileges;

> **mysqldump** -u remote-admin -P 3306 -h 172.16.5.55 -p   suunto-crm suunto-system > d:\suunto.sql

### redis

> docker run -p 6379:6379 --name my-redis -v D:/ProgramSoftware/Docker/container/redis/data:/data  -v D:/ProgramSoftware/Docker/container/redis/file/redis.conf:/etc/redis/redis.conf --requirepass 123456 --appendonly yes

### mongodb:

-   启动方式： 指定数据存储的位置和日志存储的位置

```纯文本
mongod --dbpath  D:\ProgramSoftware\MongoDB\mongodb4.4.15\data\db  --logpath   D:\ProgramSoftware\MongoDB\mongodb4.4.15\data\log\mongo.log --storageEngine wiredTiger --wiredTigerEngineConfigString="cache_size=300M"  
```

-   use admin;
-   创建角色： db.createUser({user:"root",pwd:"123456",roles:\[{role:"userAdminAnyDatabase",db:"admin"}]})
-   认证用户： db.auth("root","123456")      执行后认证创建的角色才会生效
-   show users;

### 常用的一些docker-compose命令：

-   查看compose内的容器
    docker-compose -f docker-compose-app.yml ps
-   关闭或启动或重启compose内的某个容器
    docker-compose -f docker-compose-app.yml stop/start/restart <服务名>
-   关闭或重启compose所有容器
    docker-compose -f docker-compose-app.yml stop/restart
-   查看compose所有容器的运行日志
    docker-compose -f docker-compose-app.yml logs -f
-   查看compose下某个容器的运行日志
    docker-compose -f docker-compose-app.yml logs -f <服务名>
-   也可以把compose的容器日志输出到日志文件里去，然后用tail -f 随时查看
    docker-compose -f docker-compose-app.yml logs -f >> myDockerCompose.log &
-   重新构建有变化的镜像并更新到容器再启动
    docker-compose -f docker-compose-app.yml up --build -d
-   重新创建docker-compose.yml配置有变化的容器并启动
    docker-compose -f docker-compose-app.yml up --force-recreate -d
-   停掉容器再删除容器
    docker-compose -f docker-compose-app.yml down

<https://note.youdao.com/ynoteshare/index.html?id=b808b0736cf90cde53a6be1c6813f047&type=note&_time=1690939787963>

## 查看linux信息

```bash
[root@jq ~]# cd /
[root@jq /]# pwd
# 查看在指定端口上进行端口扫描测试：端口是否放开 nc -vz 127.0.0.1 8848
nc -vz <host> <start-port>-<end-port>

[root@jq /]# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#内核是3.10以上的
[root@jq /]# uname -r
3.10.0-123.el7.x86_64

#系统版本
[root@jq /]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

```

## 安装docker

> 帮助文档：[https://docs.docker.com/](https://docs.docker.com/ "https://docs.docker.com/")
> 参考：
> [Centos7下两种方式安装Docker-CE\_LarsCheng's Blog-CSDN博客](https://blog.csdn.net/qq_33619378/article/details/89460088 "Centos7下两种方式安装Docker-CE_LarsCheng's Blog-CSDN博客")[Linux下Docker的安装及使用 - 羊37 - 博客园 (cnblogs.com)](https://www.cnblogs.com/yang37/p/14464674.html "Linux下Docker的安装及使用 - 羊37 - 博客园 (cnblogs.com)")

```bash
#确保yum包更新到最新
sudo yum update
                  
#需要必要的系统工具
#yum-utils 提供了 yum-config-manager 实用程序，用于设置yum源
#devicemapper 存储驱动需要 device-mapper-persistent-data 和 lvm2
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

#设置镜像的仓库
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo  #默认是国外的
    
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
#更新yum软件包索引
sudo yum makecache fast

#安装docker ce社区版 ee企业版
sudo yum install docker-ce docker-ce-cli containerd.io

#启动docker
sudo systemctl start docker
sudo systemctl enable docker #加入开机启动

#docker版本
sudo docker version
```

```纯文本
问题1：Another app is currently holding the yum lock
解决:参考 https://www.cnblogs.com/jianshneg/p/11258161.html
可以使用 rm -rf /var/run/yum.pid 强制杀死进程来解决

问题2：桥接模式，NAT模式

问题3:yum
https://www.cnblogs.com/ccdr/p/9160281.html
```

## 配置镜像加速器

> 针对Docker客户端版本大于 1.10.0 的用户
> 可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```纯文本
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://pt4rrit7.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## docker容器部署Prometheus服务

> docker容器部署Prometheus服务——云平台监控利器
> 参考：
> [docker容器部署Prometheus服务——云平台监控利器 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1678621 "docker容器部署Prometheus服务——云平台监控利器 - 云+社区 - 腾讯云 (tencent.com)")[Docker：使用Docker容器部署prometheus监控系统\_莫忘初心yhj-CSDN博客](https://blog.csdn.net/weixin_43305348/article/details/115322098 "Docker：使用Docker容器部署prometheus监控系统_莫忘初心yhj-CSDN博客")

```bash
Prometheus Server：Prometheus服务的主服务器 ；
Node Exporter：收集Host硬件和操作系统的信息；
cAdvrisor：负责收集Host上运行的容器信息；
Grafana：用来展示Prometheus监控操作界面（给我们提供一个友好的web界面）
```

### 拉取镜像

```bash
docker pull prom/node-exporter
docker pull google/cadvisor
docker pull prom/prometheus
docker pull grafana/grafana
```

### 运行node server容器

```bash
docker run -d -p 9100:9100 -v /proc:/host/proc -v /sys:/host/sys -v /:/rootfs --net=host --restart=always prom/node-exporter --path.procfs /host/proc --path.sysfs /host/sys --collector.filesystem.ignored-mount-points "^/(sys|proc|dev|host|etc)($|/)"

#注意：按上述方法启动容器，会提示一个警告：Published ports are discarded when using host network mode！原因是因为将网络模式指定为host，这时候映射的端口将不起任何作用，使用浏览器也无法打开页面！所以，在此处我将–net=host修改为–net=bridge模式

docker run -d -p 9100:9100 -v /proc:/host/proc -v /sys:/host/sys -v /:/rootfs -e TZ=Asia/Shanghai --net=bridge --restart=always --name=prom-node prom/node-exporter --path.procfs /host/proc --path.sysfs /host/sys --collector.filesystem.ignored-mount-points "^/(sys|proc|dev|host|etc)($|/)"

#此时使用浏览器地址访问http://192.168.74.128:9100便可以访问页面！
```

### 运行cAdvrisor容器

```bash
docker run --restart=always --privileged=true -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker:/var/lib/docker:ro -p 8080:8080 -e TZ=Asia/Shanghai --detach=true --net=bridge --name=prom-cadvisor google/cadvisor

#此时使用浏览器地址访问http://192.168.74.128:8080便可以访问页面！
```

### 运行prometheus server容器

```bash
#先基于prom/prometheus镜像运行一个容器
docker run -d -p 9090:9090 -e TZ=Asia/Shanghai --restart=always --name=prometheus --net=bridge prom/prometheus

#复制prometheus容器中的主配置文件到宿主机本地
docker cp prometheus:/etc/prometheus/prometheus.yml /root/

#打开配置文件
vim /root/prometheus.yml
#找到如下行并修改
- targets: ['localhost:9090','192.168.74.128:9100','192.168.74.128:8080']
  labels:
    env: dev-test-v4
    projectCode: platform-v4

#停止并删除已经启动的prometheus容器
docker stop prometheus
docker rm -f prometheus

#重新启动新的prometheus容器，并将刚刚修改的主配置文件挂载到容器中的指定位置
#以后若要修改主配置文件，则直接修改本地的即可。
#挂载主配置文件后，本地的和容器内的相当于同一份，在本地修改内容的话，会同步到容器中
docker run -d -p 9090:9090 -v /root/prometheus.yml:/etc/prometheus/prometheus.yml -e TZ=Asia/Shanghai --restart=always --name=prometheus --net=bridge prom/prometheus

#启动之后，访问http://192.168.97.128:9090，即可看到监控页面；然后单击上方的“status”，然后点击“Targets”，会查看到监控项
```

```bash
问题1：node_exporter 提示错误 Get “http://localhost:9100/metrics”: dial tcp 127.0.0.1:9100: connect: connection refused

解决：不要使用localhost，因为使用localhost或者127.0.0.1都是去访问普罗米修斯容器的本身；你要使用宿主机ip+端口


问题2：Warning: Error fetching server time: Detected 73503.54800009727 seconds time difference between your browser and the server. Prometheus relies on accurate time and time drift might cause unexpected query results.
解决：ntpdate ntp.aliyun.com

#问题3：虚拟机关机打开后连不上网
#临时关闭
systemctl stop NetworkManager
#永久关闭
systemctl disable NetworkManager
#重启
systemctl restart network

#问题4：docker启动的时候提示WARNING: IPv4 forwarding is disabled. Networking will not work.
vim /usr/lib/sysctl.d/00-system.conf
net.ipv4.ip_forward=1
systemctl restart network

docker exec -it containerID /bin/sh  #进入容器交互  containerID:镜像ID
docker exec -it containerID /bin/bash

#添加标签
 - job_name: redis_exporter
    static_configs:
      - targets: ['10.100.11.53:9121','10.100.11.54:9121','10.100.11.55:9121']
        labels:
          env: platform-v4
          projectCode: dev-test-v4

```

### 运行grafana容器

```bash
cd 

mkdir grafana-storage

chmod -R 777 grafana-storage/

docker run -d -p 3000:3000 -e TZ=Asia/Shanghai --restart=always --name prom-grafana -v /root/grafana-storage:/var/lib/grafana -e "GF_SECURITY_ADMIN_PASSWORD=123456" grafana/grafana

#上述命令中的“-e”选项是为了设置默认的登录用户admin，密码为“123456”。
#如果启动容器的过程中，提示iptables等相关的错误信息，
#则需要执行命令systemctl restart docker，重启docker服务，然后重新运行容器
#但是需要注意，若运行容器时没有增加“--restart=always”选项的话，
#那么在重启docker服务后，还需将所有容器手动重启。
#重启所有容器命令“docker ps -a -q | xargs docker start”

#运行完成之后，即可打开页面访问IP:3000进行访问！

#获取模板ID：https://grafana.com/grafana/dashboards?dataSource=prometheus&search=docker&orderBy=downloads&direction=desc
8321
```

### prometheus告警

> 参考：
> [prometheus+alertmanager+webhook实现自定义监控报警系统 - yang-leo - 博客园 (cnblogs.com)](https://www.cnblogs.com/leoyang63/articles/13973749.html "prometheus+alertmanager+webhook实现自定义监控报警系统 - yang-leo - 博客园 (cnblogs.com)")

```bash
# 报警规则文件可以指定多个，并且可以使用通配符*
vim prometheus.yml
rule_files:
  - "/usr/local/prometheus/rules/*_rules.yml"
  # - "first_rules.yml"
  # - "second_rules.yml"
  
# 新增规则
mkdir -p prometheus/rules
cd prometheus/rules/
# *_rules.yml具体内容见下文
vim node_up_rules.yml
vim host_stats_rules.yml
 
# prometheus需要增加挂载文件rules,重新运行
docker stop prometheus
docker rm -f prometheus
docker run -d -p 9090:9090 -v /root/prometheus.yml:/etc/prometheus/prometheus.yml -v /root/prometheus/rules:/usr/local/prometheus/rules -e TZ=Asia/Shanghai --restart=always --name=prometheus --net=bridge prom/prometheus 

# 运行完成之后，即可打开页面访问IP:9090/alerts，可以看到配置的规则
```

设置报警规则，node\_up\_rules.yml

```bash
[root@aubin rules]# cat node_up_rules.yml
groups:
- name: nodeup     #设置报警的名称
  rules:
  - alert: nodeup
    expr: up{job="prometheus"} == 0      #该job必须和Prometheus的配置文件中job_name完全一致
    for: 15s
    labels:
      severity: P1           #一级警告
      team: node
    annotations:
      summary: "{{ $labels.instance }} 已停止运行超过 15s!"

```

设置报警规则，host\_stats\_rules.yml

```bash
groups:
# 报警组组名称
- name: hostStatsAlert
  #报警组规则
  rules:
   #告警名称，需唯一
  - alert: hostCpuUsageAlert
    #promQL表达式
    expr: sum(avg without (cpu)(irate(node_cpu_seconds_total{mode!='idle'}[5m]))) by (instance) > 0.85
    #满足此表达式持续时间超过for规定的时间才会触发此报警
    for: 1m
    labels:
      #严重级别
      severity: P1
    annotations:
     #发出的告警标题
      summary: "实例 {{ $labels.instance }} CPU 使用率过高"
      #发出的告警内容
      description: "实例{{ $labels.instance }} CPU 使用率超过 85% (当前值为: {{ $value }})"
  - alert: hostMemUsageAlert
    expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes)/node_memory_MemTotal_bytes > 0.85
    for: 1m
    labels:
      severity: P1
    annotations:
      summary: "实例 {{ $labels.instance }} 内存使用率过高"
      description: "实例 {{ $labels.instance }} 内存使用率 85% (当前值为: {{ $value }})"
```

#### 运行alertmanager

```bash
#1.先简单运行一个容器
docker run --name alertmanager -d -p 9093:9093 -e TZ=Asia/Shanghai prom/alertmanager

# 将容器中的配置文件复制到本地
docker cp alertmanager:/etc/alertmanager/alertmanager.yml /root

 # 然后把他给删了
docker rm -f alertmanager 

# 具体修改见下文:可以选择webhook,邮件，钉钉等方式
vim alertmanager.yml
#配置文件中可以分为以下几组：
#global:全局配置。设置报警策略，报警渠道等；
#route：分发策略；
#receivers:接收者，指定谁来接收你发送的这些信息；
#inhibit_rules:抑制策略。当存在于另一组匹配的警报，抑制规则将禁用于一组匹配的警报

#启动
docker run -d --name alertmanger -p 9093:9093 -v /root/alertmanager.yml:/etc/alertmanager/alertmanager.yml -e TZ=Asia/Shanghai --restart=always prom/alertmanager

# 运行完成之后，即可打开页面访问IP:9093，点击status查看

# prometheus关联alertmanager，并重启
vim prometheus.yml
alerting:
  alertmanagers:
  - static_configs:
    - targets: ['192.168.74.128:9093']
   
docker restart prometheus    
```

alertmanager.yml配置 - webhook-demo

```bash
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 1m
  repeat_interval: 4h
  receiver: 'webhook'
receivers:
- name: 'webhook'
  webhook_configs:
    - url: 'http://10.66.63.157:7777/demo/recv'
```

alertmanager.yml配置 - 邮件-demo

```bash
global:
  resolve_timeout: 5m
  smtp_from: '1772514679@qq.com'
  smtp_smarthost: 'smtp.qq.com:25'
  smtp_auth_username: '1772514679@qq.com'
  smtp_auth_password: 'mgqcrvbznmiybgfe' #注意：是邮件认证不是登录密码
  smtp_require_tls: false
  smtp_hello: 'qq.com'
route:
  group_by: ['alertname']
  group_wait: 5s
  group_interval: 5s
  repeat_interval: 5m
  receiver: 'email'
receivers:
- name: 'email'
  email_configs:
  - to: '1772514679@qq.com'
```

> 使用webhook结果

```java
package com.example.demo.controller;

import com.alibaba.fastjson.JSONObject;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/demo")
public class DemoController {

//    @PostMapping(value = "/recv", produces = "application/json;charset=UTF-8")
//    public void recvMsg(@RequestBody String msg){
//        System.out.println(System.currentTimeMillis()+"-收到告警消息：");
//        System.out.println(msg);
//    }

    @PostMapping(value = "/recv")
    public void recvMsg(@RequestBody JSONObject data){
        System.out.println(System.currentTimeMillis()+"-收到告警消息：");
        System.out.println(data.toJSONString());
    }
}


//关闭一个容器后（如：docker stop prom-cadvisor），控制台打印如下信息
/**
1627980336403-收到告警消息：
{"receiver":"webhook","status":"firing","alerts":[{"status":"firing","labels":{"alertname":"node-up","env":"dev-test-v4","instance":"192.168.74.128:8080","job":"prometheus","projectCode":"platform-v4","severity":"1","team":"node"},"annotations":{"summary":"192.168.74.128:8080 已停止运行超过 15s!"},"startsAt":"2021-08-03T08:39:19.903Z","endsAt":"0001-01-01T00:00:00Z","generatorURL":"http://e0bb74b316d3:9090/graph?g0.expr=up%7Bjob%3D%22prometheus%22%7D+%3D%3D+0\u0026g0.tab=1","fingerprint":"c164a079cd80ef20"}],"groupLabels":{"alertname":"node-up"},"commonLabels":{"alertname":"node-up","env":"dev-test-v4","instance":"192.168.74.128:8080","job":"prometheus","projectCode":"platform-v4","severity":"1","team":"node"},"commonAnnotations":{"summary":"192.168.74.128:8080 已停止运行超过 15s!"},"externalURL":"http://4e73b2ee5913:9093","version":"4","groupKey":"{}:{alertname=\"node-up\"}","truncatedAlerts":0}
**/

```

## docker常用命令

```bash
docker ps
docker ps -a
docker restart ***
docker stop ***
docker images
docker  cp  容器ID:容器内部的文件路径: 宿主机路径
sudo systemctl start docker

#删除所有容器
docker rm `docker ps -aq`

docker stop `docker ps -aq`

docker exec -ti a428842ddb7f /bin/sh
date
```

-e TZ=Asia/Shanghai

[配置](配置/配置.md "配置")
