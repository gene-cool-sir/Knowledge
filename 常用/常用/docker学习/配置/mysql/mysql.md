# mysql

\[mysqld]

\# 设置3306端口

port=3306

\# 设置mysql的安装目录

&#x20;basedir = /var/lib/mysql

&#x20;datadir = /var/lib/mysql/data

\#lc\_messages\_dir=D:\SoftWareProgram\MySQL\mysql-5.7.26-winx64\share

\# 允许最大连接数

max\_connections=200

\# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统

max\_connect\_errors=10

\# 服务端使用的字符集默认为UTF8

character-set-server=utf8

\# 创建新表时将使用的默认存储引擎

default-storage-engine=INNODB

\# 默认使用“mysql\_native\_password”插件认证

default\_authentication\_plugin=mysql\_native\_password

\#开启查询缓存

\#explicit\_defaults\_for\_timestamp=true

\[mysql]

\# 设置mysql客户端默认字符集

default-character-set=utf8

\[client]

\# 设置mysql客户端连接服务端时默认使用的端口

port=3306

default-character-set=utf8

\# 不区分大小写

lower\_case\_table\_names=1
