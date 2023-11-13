# cloud

cloud-service-partner

合作伙伴服务梳理

### 服务列表

|                       | 服务名称                        | 相关服务                                                          | 存储                 | 描述                                                                              |
| --------------------- | --------------------------- | ------------------------------------------------------------- | ------------------ | ------------------------------------------------------------------------------- |
| cloud-service-partner | oauth-service               | 独立/Azure对API中转映射                                              | mongodb            | 1.提供第三方服务注册(前端) 2. 提供Oauth2.0协议相关认证处理                                           |
|                       | asko-ingestion-function-app |                                                               | Azure blob         | 触发函数对新上传到Blob Storage作出反应。, c #                                                 |
|                       | asko-ingestion-api          | oauth-service/                                                | Azure blob         | Asko Fit文件摄取的一部分。负责生成上传链接，检查上传状态。                                               |
|                       | asko-ingestion-processor    | asko-ingestion-api&#xA;oauth-service&#xA;asko-backend-systems | mongodb/Azure blob | Asko Fit文件摄取的处理部分。负责接收队列消息，加载FIT文件并将其转换为ASKO锻炼和遗留二进制文件等。                        |
|                       | external-service-connector  |                                                               |                    | 该应用程序运行从Azure服务总线队列读取消息、处理消息并发送到外部服务的服务。                                        |
|                       | partner-integration         |                                                               |                    | 处理来自Azure服务总线队列的消息并相应地处理它们                                                      |
|                       | partner-pull-request-poller |                                                               |                    | 1. 调用ASKO API端点，将更改从合作伙伴拉到ASKO。2. 需使用到Azure Functions扩展以及相关配置，定时从合作伙伴处拉路线，训练等数据 |
|                       | asko-apiserver-grpc         |                                                               |                    | 定义了grpc 相关protobuf文件                                                            |

### oauth-servie

#### 1. 数据库

| 数据库名       | Document        | 描述          |
| ---------- | --------------- | ----------- |
| STTtracker | user            |             |
|            | apimuser        | 无           |
|            | ExternalToken   | 关联合作伙伴token |
| oauth      | clientdetails   | 第三方合作伙伴     |
|            | clienttag       |             |
|            | clientinvite    | 无           |
|            | authcode        |             |
|            | usernotfoundkey |             |
|            | userkeychange   |             |

#### 2. API接口

域名: [cloudapi-oauth.suunto.cn](http://cloudapi-oauth.suunto.cn "cloudapi-oauth.suunto.cn")

| 接口名                                          | 页面位置            | 服务位置                 | 描述 |
| -------------------------------------------- | --------------- | -------------------- | -- |
| /user                                        | 无               |                      |    |
| /partnerconfig/info/{id}                     | APP             | asko-backend-systems |    |
| /partnerconfig/list                          | APP             |                      |    |
| /partnerconfig/list/extended                 |                 |                      |    |
| /partnerconfig/list/unfiltered               |                 |                      |    |
| /partnerconfig/tags                          |                 |                      |    |
| /apim/clientdetails/{userName}               | SUUNTO API ZONE |                      |    |
| /apim/clientdetails/{userName}/client-secret |                 |                      |    |
| /partnerconfig/partner/{clientId}            |                 |                      |    |
| /oauth/apple-token                           |                 | asko-backend-systems |    |
| /oauth/confirm\_access                       | Oauth登录/注册页面    |                      |    |
| /oauth/deauthorize                           |                 |                      |    |
| /oauth/facebook-auth                         |                 |                      |    |
| /oauth/facebook-token                        |                 |                      |    |
| /uaa/login                                   |                 |                      |    |
| /uaa/start-sms-login                         |                 |                      |    |
| /apim/signup                                 |                 |                      |    |

#### 3. 部分接口详情

1.  查找用户已经激活的第三方token

-   查询 `ExternalToken` 表,找到指定用户的 `ExternalToken`(该表存储了用户已经`绑定的第三方合作伙伴数据`) 信息.    \* 主要是记录的`clientId`(这个可以通过`oauth.clientdetails`表获取对应第三方的详细信息),用户绑定了哪些第三方服务.        \* 通过状态(`status`字段)为`ON`,并且`direction`字段为`TO`或者`BIDIRECTIONAL`.

1.  获取远程配置([remoteconfig.json](./remoteconfig.json "remoteconfig.json")).

-   接口地址 `https://asko-assets.suunto-operations.cn/remoteconfig/remoteconfig.json`    \* `Android` 获取 `partnerConnectionsAndroid` 集合, `IOS` 获取 `partnerConnectionsiOS` 集合.        \* 并且限制当前的客户端 `version` 必须大于配置的 `minVersion`.

1.  获取 `partnerconfig` 配置([partnerconfiglist.json](./partenerconfiglist.json "partnerconfiglist.json"))

-   接口地址 `https://cloudapi-oauth.suunto.cn/partnerconfig/list`.该接口用于获取所有的`OPENAPI`类型的合作伙伴.        \* 接口是 `oauth` 服务代码,为 `PartnerInformationController#getPartnerList` 方法.        \* 查询的数据库为 `Oauth` 中的 `clientdetails`

    合作伙伴条件:

    1.authorities角色类型包含: PARTNER

    2.有i18n国际化的配置信息

    3.publish发布为true
    1.  需要有readMoreUrl(更多链接)/合作伙伴url(partnerUrl)/合作伙伴图标(iconImageUrl)
    -   通过用户已经绑定的第三方服务的`status`状态,获取所有已经激活的第三方服务的`clientId`.获取到以及激活的第三方服务的详细信息.

4.除去 `OPENAPI` 类型的合作伙伴,还有`Oauth1`类型以及`Oauth2`的合作伙伴列表.

-   `Oauth1`,仅有 `endomondo` 信息,通过配置看,目前已经没有使用该配置信息.    \* `Oatuh2`,包含下列合作伙伴条目.并且取决于用户是否连接,若未连接则不会返回.        \* `strava`(`所有用户默认有`)        \* `stravatest`(`internaltest`用户权限才有)        \* `trainingpeaks`        \* `mapmyrun`        \* `runtastic`        \* `komoot`

### sko-ingestion-function-app

触发函数对新上传到Blob Storage作出反应。并存储在消息队列, 等待 asko-ingestion-processor 服务消费消息(加载fit文件,转换workout运动数据相关处理) &#x20;

### asko-ingestion-api

Asko Fit文件摄取的一部分。负责生成上传链接，检查上传状态。

启用GRPC作为RPC通信方式, 开启客户端,连接服务端(apiServer)

1.  前端发起上传文件之前需要处理的请求initUpdate: 生产准备上传文件信息to Upload, 并获取(Azure 需要存储文件服务读写权限相关的 认证token信息)
2.  可通过上传文件id, 查看文件上传的状态以及处理信息
3.  用户认证相关信息通过WebFilter, 在请求头中传递STTAuthorization, 然后通过Grpc对服务端发起用户请求,验证用户信息

### asko-ingestion-processor

Asko Fit文件摄取的处理部分。负责接收队列消息，加载FIT文件并将其转换为ASKO锻炼和遗留二进制文件等。

启用Grpc作为RPC通信方式, 作为客户端,连接服务端(apiServer)

配置信息:

1.  Azure Queue / Azure Blob
2.  Grpc 引用以及配置
3.  通过oauth-service服务配置,获取合作伙伴是否开启通知, 通知URL, 通知Token与Secret信息,发起通知
4.  开启事件监听,订阅Azure Queue信息
5.  获取fit 文件,调用ApiServerShared服务fitToWorkout
6.  SummaryExtension/FitnessExtension 扩展信息做特殊
7.  通过GRPC保存workout信息(apiServer: GrpcWorkoutServiceImpl.save())
8.  更新Upload上传文件处理状态

[Oauth-service](Oauth-service/Oauth-service.md "Oauth-service")

合作伙伴列表:     GET /apiserver/v1/partnerconnection HTTP/1.1 &#x20;

关联服务Token:   GET /apiserver/v1/tokens HTTP/1.1

合作伙伴图片信息:&#x20;

GET /open-api/partner-images/0de83d86-0031-454c-b980-47270c360f0b-image.png HTTP/1.1

GET /open-api/partners/codoon/codoon-app.jpg HTTP/1.1

律跑:

重定向: [https://cloudapi-oauth.suunto.cn/oauth/authorize?response\_type=code&\&client\_id=46218296-1bd4-44f3-930b-ab60f90cd99a\&state=1b18c02738f611eeab53b8599fe79a70\&redirect\_uri=https%3A%2F%2Flvpao.jianrenmatou.com%2Fapp%2Fsuunto%2FoauthCallback](https://cloudapi-oauth.suunto.cn/oauth/authorize?response_type=code&\&client_id=46218296-1bd4-44f3-930b-ab60f90cd99a\&state=1b18c02738f611eeab53b8599fe79a70\&redirect_uri=https://lvpao.jianrenmatou.com/app/suunto/oauthCallback "https://cloudapi-oauth.suunto.cn/oauth/authorize?response_type=code&\&client_id=46218296-1bd4-44f3-930b-ab60f90cd99a\&state=1b18c02738f611eeab53b8599fe79a70\&redirect_uri=https%3A%2F%2Flvpao.jianrenmatou.com%2Fapp%2Fsuunto%2FoauthCallback")

登录: /uaa/login

跳转输入手机验证码: /uaa/start-sms-login?subscription-key=

授权页: /uaa/login?subscription-key=

点击授权成功: /oauth/authorize?subscription-key=

[FQA](FQA/FQA.md "FQA")

[apiServer集成信息](apiServer集成信息/apiServer集成信息.md "apiServer集成信息")

[如何集成到cloud-service-partner服务](如何集成到cloud-service-partner服务/如何集成到cloud-service-partner服务.md "如何集成到cloud-service-partner服务")

[服务](服务/服务.md "服务")
