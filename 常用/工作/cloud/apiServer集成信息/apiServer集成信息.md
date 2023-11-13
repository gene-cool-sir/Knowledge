# apiServer集成信息

配置信息:

1.  激活OpenApiPartnersMongoConfig相应的Oath数据库配置
2.  存储相关配置BlobStorageConfig
3.  队列相关配置SocialNetworkConfig

integration包:&#x20;

集成消息总线:

AExternalServiceApplicationConnector

sendMessageWithConnectionBuilder: 将消息对象转换为字符串JSON并将其发送到Azure服务总线(异步)

1.  AS360ServiceApplicationConnector: 发送到消费者和生产者数据到Azure Service Bus
    1.  sendConsumerDataToAS360: 通过Azure服务总线向AS360发送带有订阅的用户消费者数据
    2.  sendProductDataToAS360: 通过Azure服务总线向AS360发送齿轮信息(生产着数据)
2.  DABIServiceConnector
    1.  sendUserDataToDABI: 通过向Azure服务总线发送JSON消息，将用户数据发送到DABI。
    2.  sendWorkoutDataToDABI: 通过向Azure服务总线发送JSON消息，将一个workout发送到DABI
3.  EventReporterBean: 触发事件回调
    1.  sendPartnerEvent:  将合作伙伴连接相关的事件异步发送到Azure事件中心
    2.  sendCrmEvent:  异步发送AmerSports AS360相关事件到Azure事件中心
        &#x20;疑问: 可能是采用的Azure Databricks结构化流式处理是一个近实时处理引擎, 代码中只是找到事件发送到Azure ?&#x20;
4.  PushNotificationApplicationConnector
    1.  sendToastsToQueue ： 将通知消息推送给Azure 服务总线

PartnerConnectionListCreator ：连接合作伙伴相关配置信息

createPartnerConnectionList : 该方法创建一个PartnerConnectionList对象，以提供给客户端应用程序。该列表包含有关当前可能的第三方合作伙伴连接的数据

1.  通过IOS或者Android的不同版本,获取包含有关当前可能的第三方合作伙伴连接的数据.
2.  获取openApi 链接, 是第三方合作伙伴授权登录Suunto APP
3.  createOAuth2Partners:  接入第三方合作伙伴,Suunto APP可以授权登录第三方合作伙伴
4.  createOAuth1Partners:  只有接入了ENDOMONDO

partners包:

&#x20;  以下是SuuntoAPP 接入的合作伙伴

1.  strava : StravaPartnerCommunicator/StravaTestPartnerCommunicator
2.  endomondo: EndomondoPartnerCommunicator
3.  trainingPeaks: TrainingPeaksPartnerCommunicator
4.  mapMyRun: MapMyRunPartnerCommunicator
5.  runtastic: RuntasticPartnerCommunicator
6.  komoot : KomootPartnerCommunicator
7.  OAuth2PartnerCommunicator

&#x20;PartnersRoutesIntegrationService:  合作伙伴集成线路

sendRouteToPartners 向服务总线发送合作伙伴集成路线消息

发送消息:

1.  sendConsumerDataToCRMs 触发sendConsumerDataToAS360

#### workout

```java
1. 手动添加活动数据:
接口：POST /apiserver/v1/workout Content-Type: multipart/form-data  (IOS调用此接口)
参数: 
     1.通过User-Agent请求头信息: 判断userBrand: (SUUNTOAPP、SPORTSTRACKER、SUUNTOLINK、PARTNERAPP)
        通过user的用户uuid, 去匹配userbrands文档中的用户使用的移动品牌记录,没有则新增,有则更新
     2.判断是否传递了workoutExtensions数据(默认false)
         如果有: 转换为Map<WorkoutExtensionType, AWorkoutExtension> : 类型对应的类型扩展数据
     3. SML文件或者扩展数据信息分为3类
        1) 老的IOS客户端通过发送SMLExtension扩展来发送sml数据  (通过GZIPOutputStream 生产压缩数据二进制)
        2)Android 通过发送.zip的压缩包包含2个JSON文件(summary.json and samples.json) 发送包含SML数据格式数据  (将2个文件内容组合为单个json文件信息,然后通过GZIPOutputStream 生产压缩数据二进制)
        3) 新的IOS客户端通过发送单个的.gz的压缩包文件,发送包含SML数据格式数据 (直接获取单个.gz的文件流二进制数据)
     4.获取传入的workout二进制文件数据
     依据是否传入SML格式数据,进行不同逻辑的锻炼数据保存处理
     SML不存在: workoutService.saveNewWorkout(user, buffer, extensions, WorkoutDeserializer.ReadWhs.YES, brand)
        否则: workoutService.saveNewWorkout(user, buffer, extensions, compressedSMLExtensionData, brand)
存储逻辑:
  1.只存储Workout二进制文件数据逻辑( workoutService.saveNewWorkout(user, buffer, extensions, WorkoutDeserializer.ReadWhs.YES, brand)) 
          1.过滤参数中传递的掉不符合的AWorkoutExtension extension 数据
          2.反序列化workout二进制文件信息为 WorkoutImporter.ImportedWorkoutData数据信息(数据流DataInputSream)
                1. 序列化读取WorkoutHeaderAttributes数据: mongodb:workoutheaderattributes (锻炼标题属性)
                    (按照字节数读取, 比如int applicationId = readInt() 读取4个字节,然后将4个字节转为十进制的int类型值),依次读取文件内容信息
                   读取文件的厂商,锻炼名称,开始时间,运动时间,总时间,运动距离,能量消耗,单位,开始坐标,结束坐标
                   统计锻炼数据信息(按照维度/经度/高度/深度/心率)
                   & 2 (与010 与操作;)是否有活动, & 4是否活动地点; & 8 活动圈数; & 16 心率数据(心率统计,心率区间值,时间区域指); & 32 活动图片; & 64 活动视频; &128 活动音乐; & 256 天气值; &512 步骤统计 &2048 是否是手动添加;
                   & 4096 是否导入; & 8192 是否编辑
                   中心坐标信息
                   锻炼标题的服务属性数据: 版本号,评论数,点赞数,投票数,媒体文件数,Facebook,Twitter喜欢数
                   Workout:
                   读取Workout: 读取事件统计和事件数据;事件位置;事件心率数据; 事件圈数;事件媒体;事件天气
                   
      
          3. workout二进制文件中解析出来的AWorkoutExtension 扩展数据, 生成扩展数据IntensityExtension(校正时间区域数据和心率数据)
          5. 整合参数传递的AWorkoutExtension 与 二进制文件中解析出来的AWorkoutExtension 信息到同一个Map<WorkoutExtensionType, AWorkoutExtension>中,生成的扩展信息与参数传递的扩展信息进行putAll
          6. 持久化Workout锻炼数据(persistWorkout): 将新锻炼保留到 Mongo，存储二进制和其他操作，这些操作应在保存新锻炼时始终执行 由所有类似 saveNewWorkout 的端点调用，但有一个例外 - Movescount 导入
                1.更新User的强度区域设置(update intensity zones settings) 
                2. 初始化HeaderAttributes数据(Initialize fields in header)
                    设置key,id,username,extensions,计算数据,将地理详细信息添加到锻炼的标题属性中(将折线添加到标题属性/添加中心位置到标题属性/添加上升和下降到标题属性),
                    计算锻炼排名(按照锻炼距离和其实位置,时间等找到相似的锻炼排名), 计算锻炼的恢复时间(距离上一次锻炼), 将 TSS 更新为标头属性
                    添加成就到锻炼(获得时间距离相关成就/)
                3. 设置Blob资源(AZ_WORKOUTS)
                4. 设置锻炼标签(Set possible Suunto tags)
                5. 检查并更新User 的Tag(Check user tags for workout and update usage)
                6. 检查重复项(通过workoutId 和 activityId来确认是否重复)
                持久化数据:
                7. 保存扩展数据(save extensions),(通过username/workoutId关联保存扩展信息到AWorkoutExtension相关的文档)
                8. 保存二进制文件数据到Blob,类型AZ_WORKOUT 
                9. 保存成就数据(save achievements) 
                10. 保存WorkoutHeaderAttributes数据(Save header), 文档workoutheaderattributes 
                11. 保存数据到geo服务(save to geo-service)
                12. 保存数据到统计服务(send to statistics-service), 信息到队列中
                13. 进行各种通知(Various notifications)
                    发送锻炼通知: 仅在三天前进行锻炼的情况下发送电子邮件和推送通知 (send external notifications)
                    给允许分享的朋友发送通知: (记录文档operation, 用户动态:userfeed)
                    更新用户gears为SUUNTO手表的设备信息,并发送设备信息到CRM的Azure服务队列中 (update gear and send to as360)
                    发送锻炼数据到DABI对应的服务总线,提供消费 (send to DABI)
                    发送锻炼数据到合作伙伴(send to partners)
                14. 记录Event
                
                
  2. 存储保护sml文件: workoutService.saveNewWorkout(user, buffer, extensions, compressedSMLExtensionData, brand)
        
                
                    
                    
                    
                
                
                
                    
         
          
          
          
          

          
  
接口：POST /apiserver/v1/workout Content-Type: multipart/form-data  (Android调用此接口)        
 
          
    
```
