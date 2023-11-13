# PlSQL

## PromQL

PromQL（Prometheus Query Language）是 Prometheus 内置的数据查询语言，它能实现对事件序列数据的查询、聚合、逻辑运算等。它并且被广泛应用在 Prometheus 的日常应用当中，包括对数据查询、可视化、告警处理当中. 目前主要支持两种匹配模式：完全匹配和正则匹配

> 完全匹配

PromQL 支持使用 = 和 != 两种完全匹配模式。

-   等于。通过使用 `label=value` 可以选择那些标签满足表达式定义的时间序列。
-   不等于。通过使用 `label!=value` 则可以根据标签匹配排除时间序列。
    eg. prometheus\_http\_requests\_total{code!="200"}

> 正则匹配

PromQL 还可以使用正则表达式作为匹配条件，并且可以使用多个匹配条件。

-   正向匹配。使用 `label=~regx` 表示选择那些标签符合正则表达式定义的时间序列。
-   反向匹配。使用 `label!~regx` 进行排除。

eg. 查询指标 prometheus\_http\_requests\_total 中，所有 handler 标签以 `/api/v1` 开头的记录，那么我的表达式为：`prometheus_http_requests_total{handler=~"/api/v1/.*"}`。

> 范围查询

通过类似 prometheus\_http\_requests\_total 表达式查询时间序列时，同一个指标同一标签只会返回一条数据。这样的表达式我们称之为**瞬间向量表达式**，而返回的结果称之为**瞬间向量**

查询一段时间范围内的样本数据，那么我们就需要用到**区间向量表达式**，其查询出来的结果称之为**区间向量**

时间范围通过时间范围选择器 `[]` 进行定义 : eg. prometheus\_http\_requests\_total{}\[5m]

-   s - 秒    m - 分钟   h - 小时	d - 天	w - 周 	y - 年

> 时间位移操作

在瞬时向量表达式或者区间向量表达式中，都是以当前时间为基准：

```纯文本
# 瞬时向量表达式，选择当前最新的数据
prometheus_http_requests_total{} 
# 区间向量表达式，选择以当前时间为基准，5分钟内的数据
prometheus_http_requests_total{}[5m]
```

想查询 5 分钟前的瞬时样本数据，或昨天一天的区间内的样本数据呢? 这个时候我们就可以使用位移操作，位移操作的关键字为 offset

```纯文本
# 查询 5 分钟前的最新数据
http_request_total{} offset 5m
# 往前移动 1 天，查询 1 天前的数据
# 例如现在是 2020-10-07 00:00:00
# 那么这个表达式查询的数据是：2020-10-05 至 2020-10-06 的数据
http_request_total{}[1d] offset 1d
```

> 聚合操作

```纯文本
- 计算一共有几条数据：`count(prometheus_http_requests_total)` 
- 计算所有数据的 value 总和：`sum(prometheus_http_requests_total)` 
```

> 标量

标量是一个浮点型的数字值，没有时序。例如：`10`

当使用表达式`count(http_requests_total)`，返回的数据类型，依然是瞬时向量。用户可以通过内置函数scalar()将单个瞬时向量转换为标量

eg. `scalar(count(http_requests_total))`

> 字符串

字符串是一个简单的字符串值。直接使用字符串作为 PromQL 表达式，则会直接返回字符串

#### 操作符

用户可以使用这些操作符对进一步的对事件序列进行二次加工。这些操作符包括：数学运算符，逻辑运算符，布尔运算符等等

> 数学运算符

数学运算符比较简单，就是简单的加减乘除等。

通过 `prometheus_http_response_size_bytes_sum` 可以查询到 Prometheus 这个应用的 HTTP 响应字节总和。但是这个单位是字节，我们希望用 MB 显示。那么我们可以这么设置：`prometheus_http_response_size_bytes_sum/8/1024`

-   `+ (加法)` 	`- (减法)`	`* (乘法)`	`/ (除法)` 	`% (求余)`	`^ (幂运算)`

> 布尔运算符

支持用户根据时间序列中样本的值，对时间序列进行过滤。例如我们可以通过 prometheus\_http\_requests\_total 查询出每个接口的请求次数，但是如果我们想筛选出请求次数超过 20 次的接口呢？

```纯文本
prometheus_http_requests_total > 20
```

但如果我们希望对符合条件的数据，value 变为 1。不符合条件的数据，value 变为 0。那么我们可以使用**bool修饰符**

```纯文本
prometheus_http_requests_total > bool 20
```

-   `* == (相等)`	`!= (不相等)`	`> (大于)`	`< (小于)`	`>= (大于等于)`	`<= (小于等于)`

> 集合运算符

通过集合运算，可以在两个瞬时向量与瞬时向量之间进行相应的集合操作

-   `and 与操作`	`or 或操作`	`unless 排除操作`
    -   and与操作
        > vector1 and vector2 进行一个与操作，会产生一个新的集合。该集合中的元素同时在 vector1 和 vector2 中都存在。
        > 例如我们有 vector1 为 A B C，vector2 为 B C D，那么 vector1 and vector2 的结果为：B C
-   or操作
    > vector1 and vector2 进行一个或操作，会产生一个新的集合。该集合中包含 vector1 和 vector2 中的所有元素
-   unless
    > 该集合首先取 vector1 集合的所有元素，然后排除掉所有在 vector2 中存在的元素。
    > 例如我们有 vector1 为 A B C，vector2 为 B C D，那么 vector1 unless vector2 的结果为：A

> 操作符优先级

PromQL操作符中优先级由高到低依次为：

-   `^`
-   `*, /, %`
-   `+, -`
-   `==, !=, <=, <, >=, >`
-   `and, unless`
-   `or`

#### PromQL 聚合操作

Prometheus 还提供了聚合操作符，这些操作符作用于瞬时向量。可以将瞬时表达式返回的样本数据进行聚合，形成一个新的时间序列。目前支持的聚合函数有

-   `sum (求和) `   : 对记录的 value 值进行求和
-   `	min (最小值) `	 对记录的 value 最小值
-   `	max (最大值)`	对记录的 value 最大值
-   `	avg (平均值)	`	函数返回所有记录的平均值
-   `stddev (标准差) ` 用来描述数据的波动大小,为了反映一组数据，偏离平均值的程度
    -   `stdvar (标准方差)  `
    -   ` count (计数)`  返回所有记录的计数
    -   `	count_values`
    -   ` (对value进行计数)`
        -   `bottomk (后n条时序) `  对样本值进行排序，返回当前样本值后 N 位的时间序列
            > 例如获取 HTTP 请求量后 5 位的请求，可以使用表达式：
            > bottomk(5, prometheus\_http\_requests\_total)
        -   `	topk (前n条时序)`
            例如获取 HTTP 请求量前 5 位的请求，可以使用表达式：
            ```纯文本
            topk(5, prometheus_http_requests_total)
            ```
        -   `	quantile (分位数)`

#### PromQL内置函数

> rate 增长率

这里通过node\_cpu\[2m]获取时间序列最近两分钟的所有样本，increase计算出最近两分钟的增长量，最后除以时间120秒得到node\_cpu样本在最近两分钟的平均增长率。并且这个值也近似于主机节点最近两分钟内的平均CPU使用率。`increase(v range-vector) `

```纯文本
increase(node_cpu[2m]) / 120
```

`rate(v range-vector)函数`，rate函数可以直接计算区间向量v在时间窗口内平均增长速率

通过以下表达式可以得到与increase函数相同的结果

```纯文本
rate(node_cpu[2m])
```

另外一个灵敏度更高的函数 `irate(v range-vector)`  ,irate 同样用于计算区间向量的计算率，但是其反应出的是瞬时增长率

```纯文本
irate(node_cpu[2m])
```

> predict\_linear 增长预测

`predict_linear(v range-vector, t scalar)` 函数可以帮助系统管理员更好的处理此类情况

阈值通常来说不是固定的，需要定期进行调整才能保证该告警阈值能够发挥去作用

例如，基于2小时的样本数据，来预测主机可用磁盘空间的是否在4个小时候被占满，可以使用如下表达式：

```纯文本
predict_linear(node_filesystem_free{job="node"}[2h], 4 * 3600) < 0
```

#### Metric 指标

在 Prometheus 中，我们所有的信息都以 Metrics（指标） 的形式存在。Metrics 由 metric name 和 label name 组成。

```纯文本
<metric name>{<label name>=<label value>, ...}
```

例如下面的 `api_http_requests_total` 就是 metrics name（指标名称），而 `method` 就是 label name（标签）。而 metric name 加上 label name 就是一个完整的 Metric。

```纯文本
api_http_requests_total{method="POST", handler="/messages"}
```

#### Metric Type 指标类型

Prometheus 主要有4种不同的指标类型

-   counter 计数器
    > 数据从 0 开始累计，理想状态下应该是永远增长或者是不变。
    > 适用于例如机器开机时间、HTTP 访问量等数值
-   gauges 计量器
    > 获取一个返回值，采集回来是多少就是多少。数值可能升高，也可能降低。
    > 适用于例如硬盘容量、CPU 内存使用率等数值
-   histogram 柱状图
    > 获取一个返回值，采集回来是多少就是多少。数值可能升高，也可能降低。
    > 适用于例如硬盘容量、CPU 内存使用率等数值
    > eg. 我们 1 分钟内有 1000 个 http 请求，我们想要知道大多数的请求耗时是多少。这时我们使用评价耗时可能不太准，但是我们使用 histogram 柱状图就可以看出这些请求大多数都是分布在哪个耗时区间
-   summary 汇总
    > 柱状图同样表示样本的分布情况，与 Histogram 类似，其会有总数、数量表示。**但其多了一个是中位数的表示。** 常用来表示类似于：请求持续时间、响应大小的信息
    > **Histogram 指标直接反应了在不同区间内样本的个数，区间通过标签len进行定义。而 summary 则是使用中位数反映样本的情况**

#### 任务(JOB) 和 实例 (Instance)

Prometheus 中抓取数据的应用叫做实例（Instance），而几个为了同个目的的实例组合起来称之为任务（Job）。例如下面是一个有4个实例的服务工作

```纯文本
job: api-server  // 名为api-server的job
    instance 1: 1.2.3.4:5670  
    instance 2: 1.2.3.4:5671  
    instance 3: 5.6.7.8:5670
    instance 4: 5.6.7.8:5671
```

`一个任务（Job）可以有多个实例（Instance），一个实例上可以有多个指标（Metric），一个指标只会有一个指标类型（Metric Type）`

-   告警配置文件

```纯文本
https://blog.csdn.net/u014686399/article/details/88902340

global:
resolve_timeout: 5m # (当告警的状态有firing变为resolve的以后还要呆多长时间，才宣布告警解除) (解析超时时间解析超时时间)
http_config: {}
smtp_hello: localhost
smtp_require_tls: true
pagerduty_url: https://events.pagerduty.com/v2/enqueue
hipchat_api_url: https://api.hipchat.com/
opsgenie_api_url: https://api.opsgenie.com/
wechat_api_url: https://qyapi.weixin.qq.com/cgi-bin/
victorops_api_url: https://alert.victorops.com/integrations/generic/20131114/alert/
route: # 告警内容从这里进入，寻找自己应该用那种策略发送出去
receiver: webhook
group_by: # 告警应该根据那些标签进行分组
  - alertname
   # group_wait：分组等待的时间
  group_wait: 10s #  同一组的告警发出前要等待多少秒，这个是为了把更多的告警一个批次发出去
  # group_interval：上下两组发送告警的间隔时间
  group_interval: 10s # 同一组的多批次告警间隔多少秒后，才能发出
   # repeat_interval：重复发送告警时间。
  repeat_interval: 10m # 重复的告警要等待多久后才能再次发出去
receivers: # 定义谁接收告警
- name: webhook
  webhook_configs: #PrometheUS把告警发送给webhook，也就是一个http的url
  - send_resolved: true # 当问题解决了是否也要通知一下
    http_config: {}
    url: http://devops-web.dc-pre.rootcloudapp.com/devops/mds/alarm/history/send
templates: []
```

-   告警规则

```纯文本
alert: NodemenUsage
expr: 100
  - avg by(instance, projectCode, env, category, type, group, instance, port) (irate(node_cpu_seconds_total{env="prod-exclusive",mode="idle",projectCode="platform-v4"}[5m]))
  * 100 > 95
for: 15m  # 当一个监控项超过了阀值时，这个告警处于pengding状态，而pending状态维持for秒以后，就会切换为fire状态，将告警信息发送给了alertmanager
labels:
  severity: P2
annotations:
  description: '{{ $labels.instance }} of type  {{$labels.type}} cpu使用率超过90%[发生告警值是{{$value
    }}]'
  summary: Instance {{ $labels.type}} cpu使用率过高
```

-   告警信息格式

> [https://www.soulchild.cn/2169.html](https://www.soulchild.cn/2169.html "https://www.soulchild.cn/2169.html")

1.  解析告警信息
2.  遍历告警信息,
    1.  告警信息已恢复 : 发送告警恢复通知
    2.  新增的告警信息:  都会发送告警通知
        ​	 1.已经存在还未处理的告警信息,更新告警时间,否则新增告警记录
        ​	 2. 是否调用自动恢复方法, 自动恢复告警信息
        ​    3. 未设置告警次数或者小于一次的, 或者当前告警规则小于配置的告警规则次数的才会发送告警通知

官网:[https://prometheus.io/docs/prometheus/latest/configuration/recording\_rules/](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/ "https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/")

告警流程: [https://www.cnblogs.com/xiangsikai/p/11289966.html](https://www.cnblogs.com/xiangsikai/p/11289966.html "https://www.cnblogs.com/xiangsikai/p/11289966.html")

<http://idcsec.com/2019/06/27/prometheus-containerg%E5%91%8A%E8%AD%A6%E8%A7%84%E5%88%99/>

prometheus入门: [https://www.cnblogs.com/chanshuyi/category/1862951.html](https://www.cnblogs.com/chanshuyi/category/1862951.html "https://www.cnblogs.com/chanshuyi/category/1862951.html")

<https://www.cnblogs.com/musen/p/15211028.html>
