# minio服务器

# docker 单机安装

> 服务端

```纯文本
docker run -p 9000:9000 -p 50000:50000 --name minio \
-d --restart=always \
-e "MINIO_ROOT_USER=admin" \
-e "MINIO_ROOT_PASSWORD=xie@1234" \
-v  /opt/software/minio/data:/data \
-v  /opt/software/config:/root/.minio \
minio/minio server --console-address ":50000" /data
```

> 客户端

```纯文本
wget http://dl.minio.org.cn/client/mc/release/linux-amd64/mc
# 执行权限
chmod +x mc
./mc --help
mv mc /usr/local/sbin/
```

> 参考 : [https://juejin.cn/post/6988340287559073799](https://juejin.cn/post/6988340287559073799 "https://juejin.cn/post/6988340287559073799")

> 分布式文件系统应用场景

-   互联网海量非结构化数据的存储需求
-   电商网站：海量商品图片
-   视频网站：海量视频文件
-   网盘 : 海量文件
-   社交网站：海量图片

> 介绍

> MinIO 是一个基于Apache License v2.0开源协议的对象存储服务。它兼容亚马逊S3云存储服务接口，非 常适合于存储大容量非结构化的数据，例如图片、视频、日志文件、备份数据和容器/虚拟机镜像等，而 一个对象文件可以是任意大小，从几kb到最大5T不等。 MinIO是一个非常轻量的服务,可以很简单的和其他应用的结合，类似 NodeJS, Redis 或者 MySQL。

> Minio优点

-   部署简单: 一个single二进制文件即是一切，还可支持各种平台。
-   minio支持海量存储，可按zone扩展(原zone不受任何影响)，支持单个对象最大5TB;
-   兼容Amazon S3接口，充分考虑开发人员的需求和体验;
-   低冗余且磁盘损坏高容忍，标准且最高的数据冗余系数为2(即存储一个1M的数据对象，实际占用
    磁盘空间为2M)。但在任意n/2块disk损坏的情况下依然可以读出数据(n为一个纠删码集合(Erasure
    Coding Set)中的disk数量)。并且这种损坏恢复是基于单个对象的，而不是基于整个存储卷的。
    读写性能优异

> MinIO的基础概念

-   Object：存储到 Minio 的基本对象，如文件、字节流，Anything...
-   Bucket：用来存储 Object 的逻辑空间。每个 Bucket 之间的数据是相互隔离的。对于客户端而言，就相当于一个存放文件的顶层文件夹。
-   Drive：即存储数据的磁盘，在 MinIO 启动时，以参数的方式传入。Minio 中所有的对象数据都会存储在 Drive 里。
-   Set ：即一组 Drive 的集合，分布式部署根据集群规模自动划分一个或多个 Set ，每个 Set 中的Drive 分布在不同位置。一个对象存储在一个 Set 上（For example: {1...64} is divided into 4sets each of size 16.）
-   一个对象存储在一个Set上
-   一个集群划分为多个Set
-   一个Set包含的Drive数量是固定的，默认由系统根据集群规模自动计算得出
-   一个SET中的Drive尽可能分布在不同的节点上

> 命令

-   ls 列出文件和文件夹。
-   mb 创建一个存储桶或一个文件夹。
-   cat 显示文件和对象内容。
-   pipe 将一个STDIN重定向到一个对象或者文件或者STDOUT。
-   share 生成用于共享的URL。
-   cp 拷贝文件和对象。
-   mirror 给存储桶和文件夹做镜像。
-   find 基于参数查找文件。
-   diff 对两个文件夹或者存储桶比较差异。
-   rm 删除文件和对象。
-   events 管理对象通知。
-   watch 监视文件和对象的事件。
-   policy 管理访问策略。
-   config 管理mc配置文件。
-   update 检查软件更新。
-   version 输出版本信息。

> 配置mc操作minio服务

```纯文本
# 查询mc host配置
mc config host ls
# 添加minio服务
mc config host add minio-server http://192.168.62.130:9000 admin xie@1234
# 删除host
mc config host remove minio-server
```

> 上传下载

```纯文本
# 查询minio服务上的所有buckets(文件和文件夹)
mc ls minio-server
# 下载文件
mc cp minio-server/temp/xzb/1.jpg /tmp/
#删除文件
mc rm minio-server/temp/xzb/1.jpg
#上传文件
mc cp abc.text  minio-server/xzb/
```

> Bucket管理

```纯文本
# 创建bucket
mc mb minio-server/bucket01
# 删除bucket
mc rb minio-server/bucket02
# bucket不为空，可以强制删除 慎用
mc rb --force minio-server/bucket01
#查询bucket03磁盘使用情况
mc du minio-server/bucket03
```

> mc admin使用

MinIO Client（mc）提供了“ admin”子命令来对您的MinIO部署执行管理任务。

```纯文本
service 服务重启并停止所有MinIO服务器
update 更新更新所有MinIO服务器
info 信息显示MinIO服务器信息
user 用户管理用户
group 小组管理小组
policy MinIO服务器中定义的策略管理策略
config 配置管理MinIO服务器配置
heal 修复MinIO服务器上的磁盘，存储桶和对象
profile 概要文件生成概要文件数据以进行调试
top 顶部提供MinIO的顶部统计信息
trace 跟踪显示MinIO服务器的http跟踪
console 控制台显示MinIO服务器的控制台日志
prometheus Prometheus管理Prometheus配置
kms kms执行KMS管理操作
```

> 用户管理

```纯文本
mc admin user --help
#新建用户
mc admin user add minio-server xzb
mc admin user add minio-server xzb 12345678
#查看用户
mc admin user list minio-server
#禁用用户
mc admin user disable minio-server xzb
#启用用户
mc admin user disable minio-server xzb
#查看用户信息
mc admin user info minio-server xzb
#删除用户
mc admin user remove minio-server xzb
```

> 策略管理

policy命令，用于添加，删除，列出策略，获取有关策略的信息并为MinIO服务器上的用户设置策略。

```纯文本
mc admin policy --help
#列出MinIO上的所有固定策略
mc admin policy list minio-server
# 查看plicy信息
mc admin policy info minio-server readwrite
```

> 添加新的策略

编写策略文件 /opt/software/minio/xzbTemp.json

```纯文本
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "s3:GetBucketLocation",
      "s3:GetObject"
    ],
    "Resource": [
      "arn:aws:s3:::xzbTemp"
    ]
  }, {
    "Effect": "Allow",
    "Action": [
      "s3:*"
    ],
    "Resource": [
      "arn:aws:s3:::xzbTemp/*"
    ]
  }]
}

```

xzbTemp.json添加到策略数据库

> \#添加新的策略, bucket01 这个桶资源xzb可以授权使用
> mc admin policy add minio-server bucket01  /opt/software/minio/xzbTemp.json
> mc admin policy list minio-server
> mc admin user add minio-server xzb 12345678
> \#设置用户的访问策略
> mc admin policy set minio-server bucket01   user=xzb

> Minio Java Client使用

> MinIO Java Client SDK提供简单的API来访问任何与Amazon S3兼容的对象存储服务。
> 官方demo: [https://github.com/minio/minio-java](https://github.com/minio/minio-java "https://github.com/minio/minio-java")
> 官方文档：[https://docs.min.io/docs/java-client-api-reference.html](https://docs.min.io/docs/java-client-api-reference.html "https://docs.min.io/docs/java-client-api-reference.html")

```java
依赖: 
<dependency>
  <groupId>io.minio</groupId>
  <artifactId>minio</artifactId>
  <version>8.3.0</version>
</dependency>
<dependency>
  <groupId>me.tongfei</groupId>
  <artifactId>progressbar</artifactId>
  <version>0.5.3</version>
</dependency>
<dependency>
  <groupId>com.squareup.okhttp3</groupId>
  <artifactId>okhttp</artifactId>
  <version>4.8.1</version>
</dependency>
```

> 文件上传

```java
public class FileUploader {
  public static void main(String[] args)
  throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    try {
      // Create a minioClient with the MinIO server playground, its access key
      and secret key.
      MinioClient minioClient =
      MinioClient.builder()
      .endpoint("http://192.168.62.130:9000")
      .credentials("admin", "xie@1234")
      .build();
      // 创建bucket
      String bucketName = "bucket01";
      Boolean exists =
      minioClient.bucketExists(BucketExistsArgs.builder().bucket(bucketName).build());
      if (!exists) {
        // 不存在，创建bucket
        minioClient.makeBucket(MakeBucketArgs.builder().bucket(bucketName).build());
      }
      // 上传文件
      minioClient.uploadObject(
      UploadObjectArgs.builder()
      .bucket(bucketName)
      .object("1.txt") // 上传对象名称
      .filename("D:\1.txt") // 上传的本地文件
      .build());
      System.out.println("上传文件成功");
    }
    catch (MinioException e) {
      System.out.println("Error occurred: " + e);
      System.out.println("HTTP trace: " + e.httpTrace());
    }
  }
}
```

> 文件下载

```java
public class DownLoadDemo {
  public static void main(String[] args) {
    // Create a minioClient with the MinIO server playground, its access key
    and secret key.
    MinioClient minioClient =
    MinioClient.builder()
    .endpoint("http://192.168.62.130:9000")
    .credentials("admin", "xie@1234")
    .build();
    // Download object given the bucket, object name and output file name
    try {
      minioClient.downloadObject(
      DownloadObjectArgs.builder()
      .bucket("bucket01")
      .object("temp/1.jpg")
      .filename("1.jpg")
      .build());
    }
    catch (Exception e) {
      e.printStackTrace();
    }
  }
}
```

> 3.3 Spring boot整合minio

构建MinioClient对象，并交给spring管理

> //yml
> minio:
> endpoint: [http://192.168.62.130:9000](http://192.168.62.130:9000 "http://192.168.62.130:9000")
> accesskey: admin
> secretKey: xie\@1234

```java
@Data
@Component
@ConfigurationProperties(prefix = "minio")
public class MinioProperties {
  private String endpoint;
  private String accessKey;
  private String secretKey;
}

    
@Configuration
public class MinioConfig {
  @Autowired
  private MinioProperties minioProperties;
    @Bean
    public MinioClient minioClient(){
        MinioClient minioClient =
        MinioClient.builder()
        .endpoint(minioProperties.getEndpoint())
        .credentials(minioProperties.getAccessKey(),
        minioProperties.getSecretKey())
        .build();
        return minioClient;
    }
}
```

> 实现文件上传，下载，删除操作

```java
@RestController
@Slf4j
public class MinioController {
  @Autowired
  private MinioClient minioClient;
  @Value("${minio.bucketName}")
  private String bucketName;
  @GetMapping("/list")
  public List<Object> list() throws Exception {
    //获取bucket列表
    Iterable<Result<Item>> myObjects = minioClient.listObjects(
    ListObjectsArgs.builder().bucket(bucketName).build());
    Iterator<Result<Item>> iterator = myObjects.iterator();
    List<Object> items = new ArrayList<>();
    String format = "{'fileName':'%s','fileSize':'%s'}";
    while (iterator.hasNext()) {
      Item item = iterator.next().get();
      items.add(JSON.parse(String.format(format, item.objectName(),
      formatFileSize(item.size()))));
    }
    return items;
  }
    
  @PostMapping("/upload")
  public Res upload(@RequestParam(name = "file", required = false)
  MultipartFile[] file) {
    if (file == null || file.length == 0) {
      return Res.error("上传文件不能为空");
    }
    List<String> orgfileNameList = new ArrayList<>(file.length);
    for (MultipartFile multipartFile : file) {
      String orgfileName = multipartFile.getOriginalFilename();
      orgfileNameList.add(orgfileName);
      try {
        //文件上传
        InputStream in = multipartFile.getInputStream();
        minioClient.putObject(
        PutObjectArgs.builder().bucket(bucketName).object(orgfileName).stream(
        in, multipartFile.getSize(), -1)
        .contentType(multipartFile.getContentType())
        .build());
        in.close();
      }
      catch (Exception e) {
        log.error(e.getMessage());
        return Res.error("上传失败");
      }
    }
    Map<String, Object> data = new HashMap<String, Object>();
    data.put("bucketName", bucketName);
    data.put("fileName", orgfileNameList);
    return Res.ok("上传成功",data);
  }
  @RequestMapping("/download/{fileName}")
  public void download(HttpServletResponse response, @PathVariable("fileName")
  String fileName) {
    InputStream in = null;
    try {
      // 获取对象信息
      StatObjectResponse stat = minioClient.statObject(
      StatObjectArgs.builder().bucket(bucketName).object(fileName).build());
      response.setContentType(stat.contentType());
      response.setHeader("Content-Disposition", "attachment;filename=" +
      URLEncoder.encode(fileName, "UTF-8"));
      //文件下载
      in = minioClient.getObject(GetObjectArgs.builder()
      .bucket(bucketName)
      .object(fileName)
      .build());
      IOUtils.copy(in, response.getOutputStream());
    }
    catch (Exception e) {
      log.error(e.getMessage());
    }
    finally {
      if (in != null) {
        try {
          in.close();
        }
        catch (IOException e) {
          log.error(e.getMessage());
        }
      }
    }
  }
  @DeleteMapping("/delete/{fileName}")
  public Res delete(@PathVariable("fileName") String fileName) {
    try {
      minioClient.removeObject(
      RemoveObjectArgs.builder().bucket(bucketName).object(fileName).build());
    }
    catch (Exception e) {
      log.error(e.getMessage());
      return Res.error("删除失败");
    }
    return Res.ok("删除成功",null);
  }
  private static String formatFileSize(long fileS) {
    DecimalFormat df = new DecimalFormat("#.00");
    String fileSizeString = "";
    String wrongSize = "0B";
    if (fileS == 0) {
      return wrongSize;
    }
    if (fileS < 1024) {
      fileSizeString = df.format((double) fileS) + " B";
    } else if (fileS < 1048576) {
      fileSizeString = df.format((double) fileS / 1024) + " KB";
    } else if (fileS < 1073741824) {
      fileSizeString = df.format((double) fileS / 1048576) + " MB";
    } else {
      fileSizeString = df.format((double) fileS / 1073741824) + " GB";
    }
    return fileSizeString;
  }
}
```
