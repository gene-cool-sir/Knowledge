# Oauth-service

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

网关: [stpartnerintegrationapi-cn.portal.azure-api.cn](https://stpartnerintegrationapi-cn.portal.azure-api.cn "stpartnerintegrationapi-cn.portal.azure-api.cn")

服务: [cloudapi-oauth.suunto.cn](http://cloudapi-oauth.suunto.cn "cloudapi-oauth.suunto.cn")

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

#### 3.网关映射接口

| API                                                                                                                                                                                                                                                                    | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | source                                                                                                             | 目标                                                                                                                                                                                                                                                                                                                        | 原域名                                                                                                                                                                 |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AUTHORISATION API                                                                                                                                                                                                                                                      | GET [Authorize](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Authorize")  POST [Process authorize](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Process authorize")                                                                                                                                                                                                                                                                       | (GET/POST)/oauth/authorize?response\_type={response\_type}\&client\_id={client\_id}\&redirect\_uri={redirect\_uri} | [https://apimanagement.hosting.portal.azure.net/apimanagement/Content/1.0.412.2/apimap/apimap-apis/null](https://apimanagement.hosting.portal.azure.net/apimanagement/Content/1.0.412.2/apimap/apimap-apis/null "https://apimanagement.hosting.portal.azure.net/apimanagement/Content/1.0.412.2/apimap/apimap-apis/null") | [https://cloudapi-oauth.suunto.cn](https://cloudapi-oauth.suunto.cn "https://cloudapi-oauth.suunto.cn")&#xA;&#xA;                                                   |
|                                                                                                                                                                                                                                                                        | POST  [Confirm access](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Confirm access")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | /oauth/confirm\_access                                                                                             |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | POST [Login](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Login")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | /uaa/login(POST)                                                                                                   |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | GET  [Login screen](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Login screen")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | /uaa/login (GET)                                                                                                   |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | POST  [Token](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Token")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | /oauth/token?grant\_type={grant\_type}                                                                             |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
| [GUIDES](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "GUIDES") API     | POST createGuide                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | POST /files                                                                                                        | [https://stpartnerintegrationapi-cn.azure-api.cn//v2/guides](https://stpartnerintegrationapi-cn.azure-api.cn//v2/guides "https://stpartnerintegrationapi-cn.azure-api.cn//v2/guides")                                                                                                                                     | [https://api.suunto.cn/apiserver/v1/suuntoplus/guides](https://api.suunto.cn/apiserver/v1/suuntoplus/guides "https://api.suunto.cn/apiserver/v1/suuntoplus/guides") |
|                                                                                                                                                                                                                                                                        | DEL  [deleteGuide](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "deleteGuide")  GET [getFile](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "getFile")  PUT [updateGuideFile](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "updateGuideFile") | DEL/GET/PUT  /files/{id}                                                                                           |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | GET [getItems](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "getItems")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | GET /items                                                                                                         |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
| INTEGRATION API(第三方请求Suunto)                                                                                                                                                                                                                                           | [Export user workout in FIT](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Export user workout in FIT")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | GET /workout/exportFit/{workoutIdOrKey}  (已验证)                                                                     | [https://api.sports-tracker.com/](https://api.sports-tracker.com/ "https://api.sports-tracker.com/")                                                                                                                                                                                                                      | [https://api.suunto.cn/apiserver/v1](https://api.suunto.cn/apiserver/v1 "https://api.suunto.cn/apiserver/v1")                                                       |
|                                                                                                                                                                                                                                                                        | [Get workout image](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Get workout image")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | GET /image/{key}.jpg    (有待验证)                                                                                     |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | [Get workout summary as JSON](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Get workout summary as JSON")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | GET /workouts/{workoutKey} (已验证)                                                                                   | [https://api.suunto.cn/apiserver/v1](https://api.suunto.cn/apiserver/v1 "https://api.suunto.cn/apiserver/v1")                                                                                                                                                                                                             |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | [Get workouts list](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Get workouts list")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | GET /workouts (已验证)                                                                                                |                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                     |
| [ROUTE API](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "ROUTE API")   | POST  [Import GPX](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Import GPX")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | POST /import (已验证)                                                                                                 | [https://api.suunto.cn/apiserver/v2/routes](https://api.suunto.cn/apiserver/v2/routes "https://api.suunto.cn/apiserver/v2/routes")                                                                                                                                                                                        | [https://api.suunto.cn](https://api.suunto.cn "https://api.suunto.cn")                                                                                              |
| [UPLOAD API](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "UPLOAD API") | POST  [Init Upload](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Init Upload")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | POST /                                                                                                             | [http://52.131.86.6:8080/upload](http://52.131.86.6:8080/upload "http://52.131.86.6:8080/upload")                                                                                                                                                                                                                         | [http://139.217.92.39:8080](http://139.217.92.39:8080 "http://139.217.92.39:8080")                                                                                  |
|                                                                                                                                                                                                                                                                        | GET  [Status](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Status")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | GET /{id}                                                                                                          | [http://52.131.86.6:8080/upload](http://52.131.86.6:8080/upload "http://52.131.86.6:8080/upload")                                                                                                                                                                                                                         |                                                                                                                                                                     |
| [Link](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Link")             | POST  [Add POI](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Add POI")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | POST /poi                                                                                                          | [https://stpartnerintegrationapi-cn.azure-api.cn/slink](https://stpartnerintegrationapi-cn.azure-api.cn/slink "https://stpartnerintegrationapi-cn.azure-api.cn/slink")                                                                                                                                                    | [https://api.suunto.cn/apiserver/v1](https://api.suunto.cn/apiserver/v1 "https://api.suunto.cn/apiserver/v1")                                                       |
|                                                                                                                                                                                                                                                                        | DEL [Delete POI](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Delete POI")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | DEL /poi/{poiKey}                                                                                                  | [https://stpartnerintegrationapi-cn.azure-api.cn/slink](https://stpartnerintegrationapi-cn.azure-api.cn/slink "https://stpartnerintegrationapi-cn.azure-api.cn/slink")                                                                                                                                                    |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | GET  [Get routes list](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Get routes list")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | GET /routes                                                                                                        | [https://api.suunto.cn/apiserver/v2](https://api.suunto.cn/apiserver/v2 "https://api.suunto.cn/apiserver/v2")                                                                                                                                                                                                             |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | GET  [Get user settings](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Get user settings")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | GET /user/settings                                                                                                 | [https://stpartnerintegrationapi-cn.azure-api.cn/slink](https://stpartnerintegrationapi-cn.azure-api.cn/slink "https://stpartnerintegrationapi-cn.azure-api.cn/slink")                                                                                                                                                    |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | GET  [List POI for user](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "List POI for user")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | GET /poi                                                                                                           | [https://stpartnerintegrationapi-cn.azure-api.cn/slink](https://stpartnerintegrationapi-cn.azure-api.cn/slink "https://stpartnerintegrationapi-cn.azure-api.cn/slink")                                                                                                                                                    |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | POST  [Save SML](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Save SML")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | POST /workout/sml                                                                                                  | [https://stpartnerintegrationapi-cn.azure-api.cn/slink](https://stpartnerintegrationapi-cn.azure-api.cn/slink "https://stpartnerintegrationapi-cn.azure-api.cn/slink")                                                                                                                                                    |                                                                                                                                                                     |
|                                                                                                                                                                                                                                                                        | PUT  [Update POI](https://apimanagement.hosting.azureportal.chinacloudapi.cn/apimanagement/Content/1.728.2.0/apimap//apimap-apis/index.html?clientOptimizations=undefined\&l=zh-hans.zh-cn\&trustedAuthority=https://portal.azure.cn\&shellVersion=undefined# "Update POI")                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | PUT /poi/{poiKey}                                                                                                  | [https://stpartnerintegrationapi-cn.azure-api.cn/slink](https://stpartnerintegrationapi-cn.azure-api.cn/slink "https://stpartnerintegrationapi-cn.azure-api.cn/slink")                                                                                                                                                    |                                                                                                                                                                     |

-   AUTHORISATION API : 此 API 用于根据 Suunto 系统对用户进行身份验证。为了在 Suunto 系统之间传输任何数据，用户需要通过授权流程提供访问权限
-   INTEGRATION API: 此 API，您可以访问 Suunto 消费者与手表一起使用的不同资产

    API 的访问权限的订阅密钥: Ocp-Apim-Subscription-Key 这个参数来自于`SUUNTO API Zone`

    &#x20;[https://stpartnerintegrationapi-cn.azure-api.cn/v2/workout/exportFit/{workoutIdOrKey}](https://stpartnerintegrationapi-cn.azure-api.cn/v2/workout/exportFit/{workoutIdOrKey} "https://stpartnerintegrationapi-cn.azure-api.cn/v2/workout/exportFit/{workoutIdOrKey}")
    ```yaml
    curl -v -X GET "https://stpartnerintegrationapi-cn.azure-api.cn/v2/workout/exportFit/{workoutIdOrKey}"
    -H "Authorization: "
    -H "Ocp-Apim-Subscription-Key: {subscription key}"
    ```

#### 4. 部分接口详情

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

#### 5. SUUNTO API Zone 前端

> 登录:  [https://stpartnerintegrationapi-cn.portal.azure-api.cn/signin](https://stpartnerintegrationapi-cn.portal.azure-api.cn/signin "https://stpartnerintegrationapi-cn.portal.azure-api.cn/signin")

&#x20;获取用户Oauth信息:&#x20;

#### 附件

```yaml
openapi: "3.0.3"
info:
  title: "oauth_service API"
  description: "oauth_service API"
  version: "1.0.0"
servers:
  - url: "https://oauth_service"
paths:
  /user: { }
  /partnerconfig/info/{id}:
    get:
      summary: "GET partnerconfig/info/{id}"
      operationId: "getPartner"
      parameters:
        - name: "id"
          in: "path"
          required: true
          schema:
            type: "string"
        - name: "Accept-Language"
          in: "header"
          required: true
          schema:
            type: "string"
        - name: "Authorization"
          in: "header"
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /partnerconfig/list:
    get:
      summary: "GET partnerconfig/list"
      operationId: "getPartnerList"
      parameters:
        - name: "Accept-Language"
          in: "header"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /partnerconfig/list/extended:
    get:
      summary: "GET partnerconfig/list/extended"
      operationId: "getPartnerStatList"
      parameters:
        - name: "Accept-Language"
          in: "header"
          required: true
          schema:
            type: "string"
        - name: "Authorization"
          in: "header"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /partnerconfig/list/unfiltered:
    get:
      summary: "GET partnerconfig/list/unfiltered"
      operationId: "getUnfilteredPartnerList"
      parameters:
        - name: "Accept-Language"
          in: "header"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /partnerconfig/tags:
    get:
      summary: "GET partnerconfig/tags"
      operationId: "getPartnerTags"
      parameters:
        - name: "Accept-Language"
          in: "header"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /apim/clientdetails/{userName}:
    get:
      summary: "GET apim/clientdetails/{userName}"
      operationId: "getClientDetail"
      parameters:
        - name: "userName"
          in: "path"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
    post:
      summary: "POST apim/clientdetails/{userName}"
      operationId: "createOrUpdateClientDetail"
      parameters:
        - name: "userName"
          in: "path"
          required: true
          schema:
            type: "string"
        - name: "client_data"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /apim/clientdetails/{userName}/client-secret:
    post:
      summary: "POST apim/clientdetails/{userName}/client-secret"
      operationId: "updateClientSecret"
      parameters:
        - name: "userName"
          in: "path"
          required: true
          schema:
            type: "string"
        - name: "clientsecret"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /partnerconfig/partner/{clientId}:
    get:
      summary: "GET partnerconfig/partner/{clientId}"
      operationId: "getPartnerConnection"
      parameters:
        - name: "clientId"
          in: "path"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /oauth/apple-token:
    post:
      summary: "POST oauth/apple-token"
      operationId: "processAppleAuthCode"
      parameters:
        - name: "code"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /oauth/confirm_access:
    get:
      summary: "GET oauth/confirm_access"
      operationId: "getConfirmAccessView"
      parameters:
        - name: "client_id"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /oauth/deauthorize:
    get:
      summary: "GET oauth/deauthorize"
      operationId: "unlinkUserAccount"
      parameters:
        - name: "client_id"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /oauth/facebook-auth:
    get:
      summary: "GET oauth/facebook-auth"
      operationId: "startFBAuth"
      parameters:
        - name: "code"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /oauth/facebook-token:
    post:
      summary: "POST oauth/facebook-token"
      operationId: "processFBAuth"
      parameters:
        - name: "code"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /uaa/login:
    get:
      summary: "GET uaa/login"
      operationId: "handleLogin"
      responses:
        "200":
          description: "OK"
  /uaa/start-sms-login:
    get:
      summary: "GET uaa/start-sms-login"
      operationId: "handleSmsLoginError"
      parameters:
        - name: "phone"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "country-code"
          in: "query"
          required: true
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
    post:
      summary: "POST uaa/start-sms-login"
      operationId: "startSmsLogin"
      parameters:
        - name: "phone"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "country-code"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "wechat-client-id"
          in: "query"
          schema:
            type: "string"
        - name: "user-agent"
          in: "header"
          schema:
            type: "string"
        - name: "accept-language"
          in: "header"
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
  /apim/signup:
    get:
      summary: "GET apim/signup"
      operationId: "getSignUpView"
      parameters:
        - name: "returnUrl"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "salt"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "sig"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "username"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "mode"
          in: "query"
          required: true
          schema:
            type: "string"
        - name: "clientId"
          in: "query"
          schema:
            type: "string"
      responses:
        "200":
          description: "OK"
```

案例

```java
package com.suunto.usersync.api;

import cn.hutool.core.date.DateUtil;
import cn.hutool.http.HttpRequest;
import cn.hutool.http.HttpUtil;
import com.suunto.platform.controller.handler.HttpMessageConverterFactory;
import com.suunto.platform.controller.handler.RestTemplateRequestInterceptor;
import com.suunto.platform.controller.handler.ResultHttpMessageReader;
import com.suunto.platform.exception.BusinessException;
import org.apache.commons.lang3.StringUtils;
import org.springframework.http.*;
import org.springframework.http.client.SimpleClientHttpRequestFactory;
import org.springframework.http.converter.StringHttpMessageConverter;
import org.springframework.http.converter.json.MappingJackson2HttpMessageConverter;
import org.springframework.web.client.RestTemplate;

import java.net.URI;
import java.net.URLEncoder;
import java.util.Base64;
import java.util.Date;

/**
 * 1. Suunto用户的username是不让更改的,且是唯一的; 除非用户删除了账户,那么用户的相关数据将不存在;
 * 2. SUUNTO API 提供了授权用户refresh_token的接口https://cloudapi-oauth.suunto.com/oauth/token?grant_type={grant_type}[&code][&refresh_token][&redirect_uri]
 *    可以通过刷新token给用户toke续约(生成新的有效token) (https://cloudapi-oauth.suunto.com/oauth/token?grant_type=refresh_token&redirect_uri=xxx)
 *    access_token: (3600*24)  refresh_token: (3600*24*180)seconds
 *    这个需要你们在程序中做一些处理
 * 3. 用户修改Suunto密码, access_token会立即失效, 在refresh_token没有过期的前提下, 此时可以通过refresh_token重新获取access_token
 * 4. 如果refresh_token 也处于过期状态，则需要重新登录授权
 */
public class TokenTest {


    public static void main(String[] args) throws Exception{
        //getAccessToken("DFmcIa");
        // dtwkyj
        //TokenTest tokenTest = new TokenTest();
        //tokenTest.getAccessToken2("dtwkyj");
        //1707300992   1691835392
        long l = System.currentTimeMillis();

        System.out.println(DateUtil.formatDate(new Date(1691835392000L)));
        System.out.println(DateUtil.formatDate(new Date(1707300992000L)));

        refreshAccessToken("eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1a3YiOiIxIiwidXNlcl9uYW1lIjoiZ2VuZTgyMCIsInVrIjoiOUVjdUIzbC9LV2t5UTNOQy9TZkMyTlF4WWt4TVdSQUIybEFMY1RndUdXUERsK09KamIxTm56WWx5ZzNpcVFLZXlIT2IrdHpBaWVTMlIyUHJHMnplcWc9PSIsInNjb3BlIjpbIndvcmtvdXQiXSwiYXRpIjoiWnRhQUpqTWJYSlRqNWZXMkdjbk9ndzE5Z3BFIiwiZXhwIjoxNzA3NjM1ODQ0LCJ1c2VyIjoiZ2VuZTgyMCIsImF1dGhvcml0aWVzIjpbIlBBUlRORVIiXSwianRpIjoic2c2anlwNTZBWHlmQXhMZk5jOWNrOHp1Qk5rIiwiY2xpZW50X2lkIjoiMzJkM2NlYjYtYWZmOS00MWVmLWFkYWItNjY1MDlhYWJkMTkwIn0.F8KopWTJe4QDSwoH4ZvliYij_yRnop-_7s78xo1Zq5I");
    }

    /**
     response: {"access_token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1a3YiOiIxIiwidXNlcl9uYW1lIjoiZ2VuZTgyMCIsInVrIjoiYmhubkVjd2YrMDdDaEhDVnMrUmJpNUpBeXlBK1ZKR2JFS3RDUjRsMVcycVZid1JsTU16MFRWREZVU0ozN0dqOSs3RlIwdVRtMzNvZXFZQlJ1clR2Umc9PSIsInNjb3BlIjpbIndvcmtvdXQiXSwiZXhwIjoxNjkyMTcyNzI0LCJ1c2VyIjoiZ2VuZTgyMCIsImF1dGhvcml0aWVzIjpbIlBBUlRORVIiXSwianRpIjoiUUxHT0NzemE2SW50RzlmWmZLWktVZ2FwSkV3IiwiY2xpZW50X2lkIjoiMzJkM2NlYjYtYWZmOS00MWVmLWFkYWItNjY1MDlhYWJkMTkwIn0.hBpDKlGwieXZ29QbkFK0XU1ahePGcqc950cuJeU8B5c","token_type":"bearer","refresh_token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1a3YiOiIxIiwidXNlcl9uYW1lIjoiZ2VuZTgyMCIsInVrIjoiYmhubkVjd2YrMDdDaEhDVnMrUmJpNUpBeXlBK1ZKR2JFS3RDUjRsMVcycVZid1JsTU16MFRWREZVU0ozN0dqOSs3RlIwdVRtMzNvZXFZQlJ1clR2Umc9PSIsInNjb3BlIjpbIndvcmtvdXQiXSwiYXRpIjoiUUxHT0NzemE2SW50RzlmWmZLWktVZ2FwSkV3IiwiZXhwIjoxNzA3NjM4MzI0LCJ1c2VyIjoiZ2VuZTgyMCIsImF1dGhvcml0aWVzIjpbIlBBUlRORVIiXSwianRpIjoiNTBPdjlUSXFFSEhZQkV3STZkZ1dJNTJ3U0NvIiwiY2xpZW50X2lkIjoiMzJkM2NlYjYtYWZmOS00MWVmLWFkYWItNjY1MDlhYWJkMTkwIn0.bsJJToXxUayW5gQZqnSUn8RVemNpWysesbTyTHA3eK4","expires_in":86400,"scope":"workout","ukv":"1","uk":"bhnnEcwf+07ChHCVs+Rbi5JAyyA+VJGbEKtCR4l1W2qVbwRlMMz0TVDFUSJ37Gj9+7FR0uTm33oeqYBRurTvRg==","user":"gene820","jti":"QLGOCsza6IntG9fZfKZKUgapJEw"}
     * @param authCode
     * @return
     */
    // eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1a3YiOiIxIiwidXNlcl9uYW1lIjoiZ2VuZTgyMCIsInVrIjoiSGxRekhyY21WcGpMaEhUMWlsR0hncXJkWm5EOXhYelpsa1dzdmp6a2xWMkRrMlVkSGxYZ2o0NzZiS2diZ2t5ZmdtM2hYNjVpVjByT2toaWNVQUhsY0E9PSIsInNjb3BlIjpbIndvcmtvdXQiXSwiZXhwIjoxNjkxODM1MzkyLCJ1c2VyIjoiZ2VuZTgyMCIsImF1dGhvcml0aWVzIjpbIlBBUlRORVIiXSwianRpIjoiY2pHNGdTc25ZeXE2ZDI1am1BR0JVSmVrelJvIiwiY2xpZW50X2lkIjoiMzJkM2NlYjYtYWZmOS00MWVmLWFkYWItNjY1MDlhYWJkMTkwIn0.JTFhl9--HwZ49T8P2UtPDrXf4x5UUq5CJM8g92rpaJs
    public static String getAccessToken(String authCode) {
        try {
            StringBuffer requestUrl = new StringBuffer("https://cloudapi-oauth.suunto.cn/oauth/token");
            requestUrl.append("?grant_type=authorization_code").append("&code=").append(authCode);
            requestUrl.append("&redirect_uri=").append(URLEncoder.encode("https://static.suunto.cn/suunto/member-center/oauth.html", "UTF-8"));
            String authValue = "32d3ceb6-aff9-41ef-adab-66509aabd190" + ":" + "9Kk1AE1U4UJdr1XDgLRI";
            HttpRequest httpRequest = HttpUtil.createPost(requestUrl.toString());
            httpRequest.auth("Basic " + Base64.getEncoder().encodeToString(authValue.getBytes("UTF-8")));
            httpRequest.contentType("application/json;charset=UTF-8");
            String response = httpRequest.execute().body();
            if (StringUtils.isNotBlank(response)) {
                System.out.println("response: " + response);
                return response;
            }
            return "";
        } catch (Exception e) {

            throw new BusinessException("SUUNTO CLOUD APP授权失败");
        }
    }

    public static String refreshAccessToken(String refreshToken) throws Exception {
        StringBuffer requestUrl = new StringBuffer("https://cloudapi-oauth.suunto.cn/oauth/token");
        requestUrl.append("?grant_type=refresh_token").append("&refresh_token=").append(refreshToken);
        requestUrl.append("&redirect_uri=").append(URLEncoder.encode("https://static.suunto.cn/suunto/member-center/oauth.html", "UTF-8"));
        String authValue = "32d3ceb6-aff9-41ef-adab-66509aabd190" + ":" + "9Kk1AE1U4UJdr1XDgLRI";
        System.out.println("开始向SUUNTO APP刷新token信息：" + requestUrl.toString());
        HttpRequest httpRequest = HttpUtil.createPost(requestUrl.toString());
        httpRequest.auth("Basic " + Base64.getEncoder().encodeToString(authValue.getBytes("UTF-8")));
        httpRequest.contentType("application/json;charset=UTF-8");
        String response = httpRequest.execute().body();
        if (StringUtils.isNotBlank(response)) {
            System.out.println("response: " + response);
            return response;
        }
        return "";
    }

    public RestTemplate restTemplate(){
        SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
        // 设置超时
        requestFactory.setConnectTimeout(5000);
        requestFactory.setReadTimeout(60000);
        // 利用复杂构造器可以实现超时设置，内部实际实现为 HttpClient
        RestTemplate restTemplate = new RestTemplate(requestFactory);
        restTemplate.getInterceptors().add(0, new RestTemplateRequestInterceptor());
        restTemplate.getMessageConverters().removeIf(mc -> mc instanceof StringHttpMessageConverter || mc instanceof MappingJackson2HttpMessageConverter);
        restTemplate.getMessageConverters().add(0, new ResultHttpMessageReader());
        restTemplate.getMessageConverters().add(1, HttpMessageConverterFactory.stringHttpMessageConverter());
        restTemplate.getMessageConverters().add(HttpMessageConverterFactory.fastJsonHttpMessageConverter());
        return restTemplate;
    }

    public String getAccessToken2(String code) {
        try {
            String code2accessTokenUrl = "https://cloudapi-oauth.suunto.cn/oauth/token";
            String  clientId = "32d3ceb6-aff9-41ef-adab-66509aabd190";
            String clientSecret = "9Kk1AE1U4UJdr1XDgLRI";
            String redirectUri = "https://static.suunto.cn/suunto/member-center/oauth.html";
            String redirectUriEncode = URLEncoder.encode(redirectUri, "UTF-8");
            //请求参数
            String params =
                    //"client_id="+clientId
            "grant_type=authorization_code"
            + "&code=" + code
            + "&redirect_uri=" + redirectUriEncode;

            HttpHeaders headers = new org.springframework.http.HttpHeaders();
            //传递请求体时必须设置传递参数的格式
             headers.setContentType(MediaType.APPLICATION_JSON);

            headers.setBasicAuth(clientId,clientSecret);
            HttpEntity<String> request =  new HttpEntity<>(headers);

            RestTemplate restTemplate = restTemplate();
            String url = code2accessTokenUrl+ "?" + params;

            URI uri = new URI(url);
            ResponseEntity<String> response = restTemplate.exchange(
                    uri,
                    HttpMethod.POST,
                    request,
                    String.class);
            System.out.println("颂拓"+response);
            return response.getBody();
        } catch (Exception e) {
            throw new BusinessException("SUUNTO CLOUD APP授权失败",e);
        }
    }
}

```
