# docker-compose-env

version: '3.8'

services:

&#x20; mysql:

&#x20;   image: mysql

&#x20;   container\_name: my-mysql

&#x20;   command: --default-authentication-plugin=mysql\_native\_password #这行代码解决无法访问的问题

&#x20;   restart: always  #关机或者重启机器时，docker同时重启容器，一般mysql服务可以这么设置，保持服务一直都在

&#x20;   environment:

&#x20;     MYSQL\_ROOT\_PASSWORD: 123456 #设置root帐号密码

&#x20;   ports:

&#x20;     \- 3307:3306

&#x20;   volumes:

&#x20;     \- "D:/ProgramSoftware/Docker/container/mysql/data/db:/var/lib/mysql" #数据文件挂载

&#x20;     \- "D:/ProgramSoftware/Docker/container/mysql/file/my.cnf:/etc/mysql/my.conf" #配置文件挂载

&#x20;     \- "D:/ProgramSoftware/Docker/container/mysql/log:/var/log/mysql" #日志文件挂载

&#x20; redis:  &#x20;

&#x20;   image: redis

&#x20;   container\_name: my-redis

&#x20;   command: redis-server --appendonly yes --requirepass 123456

&#x20;   privileged: true # 使用该参数，container内的root拥有真正的root权限。否则，container内的root只是外部的一个普通用户权限

&#x20;   volumes:

&#x20;     \- "D:/ProgramSoftware/Docker/container/redis/data:/data"

&#x20;     \- "D:/ProgramSoftware/Docker/container/redis/file/redis.conf:/etc/redis/redis.conf"

&#x20;   ports:

&#x20;     \- 6379:6379

&#x20; mongo:

&#x20;   image: mongo

&#x20;   container\_name: mongo

&#x20;   command: --storageEngine wiredTiger --wiredTigerEngineConfigString="cache\_size=300M"

&#x20;   environment:

&#x20;     MONGO\_INITDB\_ROOT\_USERNAME: root

&#x20;     MONGO\_INITDB\_ROOT\_PASSWORD: 123456

&#x20;     TZ: "Asia/Shanghai"

&#x20;   volumes:

&#x20;     \- "D:/ProgramSoftware/Docker/container/mongo/data/db:/data/db" #数据文件挂载

&#x20;   ports:

&#x20;     \- 27017:27017

&#x20; nacos:

&#x20;   image: nacos/nacos-server

&#x20;   container\_name: nacos

&#x20;   environment:

&#x20;     \- MODE=standalone

&#x20;   volumes:

&#x20;     \- "D:/ProgramSoftware/Docker/container/nacos/log/:/home/nacos/logs"

&#x20;   ports:

&#x20;     \- "8848:8848"
