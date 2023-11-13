# pdf生成

### 生成pdf文件

#### 文件要素

-   文件中包含图片
-   文件中包含列表
-   文件中包含表格
-   文件中包含循环嵌套写入的内容
-   文件需要后端生成echarts图表数据

基于以上几点考虑

#### 技术选取

> 方式一

itext7是一款用于生成PDF文档的一个java类库,通过iText不仅可以生成PDF或rtf的文档，而且可以将XML、Html文件转化为PDF文件。

网上资料比较少, 可以通过写段落/图片/表格,也可以定制pdf模板然后通过填充域属性值,达到生成PDF文件的结果;

难点; 通过代码写PDF,某些排版不好配置, 可以通过PDF模板生成,据说不能太复杂

官网: [https://kb.itextpdf.com/home/it7kb](https://kb.itextpdf.com/home/it7kb "https://kb.itextpdf.com/home/it7kb")

> 方式二  常用的JAVA POI 写WORD 文件 转PDF

通过WORD 作为文件模板,填充数据->WORD转PDF

使用原生POI API 方式难度比较大

后端生成Echarts图片是一个难点(不通过浏览器响应)

WORD模板填充数据后,转PDF, 样式问题,排版问题,技术选择问题

#### POI-TL 转为模板

官网: [http://deepoove.com/poi-tl/1.9.x/](http://deepoove.com/poi-tl/1.9.x/ "http://deepoove.com/poi-tl/1.9.x/")

**poi-tl**是一个免费开源的Java类库，你可以非常方便的加入到你的Java项目中, 替换目模板,很方便;案例都可以去官网看看,文档中大部分功能已经满足使用, 一目了然.

| 方案           | 移植性                | 功能性                | 易用性                                                                                                                                                            |
| ------------ | ------------------ | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ​**Poi-tl**  | Java跨平台            | Word模板引擎           | 基于Apache POI                                                                                                                                                   |
| Apache POI   | Java跨平台            | Apache项目，功能丰富      | 文档不全，这里有一个教程：[](http://deepoove.com/poi-tl/apache-poi-guide.html)[Apache POI Word快速入门](http://deepoove.com/poi-tl/apache-poi-guide.html "Apache POI Word快速入门") |
| Freemarker   | XML跨平台             | 仅支持文本，很大的局限性       | 不推荐，需要维护XML结构，代码后期不可维护                                                                                                                                         |
| OpenOffice   | 部署OpenOffice，移植性较差 | -                  | 需要了解OpenOffice的API                                                                                                                                             |
| HTML浏览器导出    | 依赖浏览器的实现，移植性较差     | HTML不能很好的兼容Word的格式 | -                                                                                                                                                              |
| Jacob、winlib | Windows平台          | -                  | 复杂，完全不推荐使用                                                                                                                                                     |

| 引擎功能           | 描述                                                            |
| -------------- | ------------------------------------------------------------- |
| 文本             | 将标签渲染为文本                                                      |
| 图片             | 将标签渲染为图片                                                      |
| 表格             | 将标签渲染为表格                                                      |
| 列表             | 将标签渲染为列表                                                      |
| 图表             | 条形图（3D条形图）、柱形图（3D柱形图）、面积图（3D面积图）、折线图（3D折线图）、雷达图、饼图（3D饼图）等图表渲染 |
| If Condition判断 | 隐藏或者显示某些文档内容（包括文本、段落、图片、表格、列表、图表等）                            |
| Foreach Loop循环 | 循环某些文档内容（包括文本、段落、图片、表格、列表、图表等）                                |
| Loop表格行        | 循环渲染表格的某一行                                                    |
| Loop表格列        | 循环渲染表格的某一列                                                    |
| Loop有序列表       | 支持有序列表的循环，同时支持多级列表                                            |
| 图片替换           | 将原有图片替换成另一张图片                                                 |
| 书签、锚点、超链接      | 支持设置书签，文档内锚点和超链接功能                                            |
| 强大的表达式         | 完全支持SpringEL表达式，可以扩展更多的表达式：OGNL, MVEL…                        |
| 标签定制           | 支持自定义标签前后缀                                                    |
| 文本框            | 文本框内标签支持                                                      |
| 样式             | 模板即样式，同时代码也可以设置样式                                             |
| 模板嵌套           | 模板包含子模板，子模板再包含子模板                                             |
| 合并             | Word合并Merge，也可以在指定位置进行合并                                      |
| 用户自定义函数(插件)    | 在文档任何位置执行函数                                                   |

贴一段生成动态表格代码 : [https://blog.csdn.net/liushimiao0104/article/details/78603443](https://blog.csdn.net/liushimiao0104/article/details/78603443 "https://blog.csdn.net/liushimiao0104/article/details/78603443")

```java
public class MyDynamicTableCountPolicy extends DynamicTableRenderPolicy {

    // 填充数据所在行数
    private int startDataRow;

    // 实体类型:属性自己添加的,想依据不同属性做不同的事情
    private String optType;
  // 需要填充的表格的列数
    private int cellNum;

    public InstanceResourceTableCountPolicy(int startDataRow, int cellNum) {
        this.startDataRow = startDataRow;
        this.cellNum = cellNum;
    }

    public InstanceResourceTableCountPolicy(String optType, int startDataRow, int cellNum) {
        this.startDataRow = startDataRow;
        this.cellNum = cellNum;
        this.optType = optType;
    }

    @Override
    public void render(XWPFTable table, Object data) throws Exception {
        table.removeRow(startDataRow);
        if (ObjectUtil.isEmpty(data)) return;
        List<RowRenderData> rowDataList = (List<RowRenderData>)data;
        // 循环插入行
        for (RowRenderData rowData : rowDataList ){
            XWPFTableRow insertNewTableRow = table.insertNewTableRow(startDataRow);
            for (int j = 0; j < cellNum; j++) {
                XWPFTableCell cell = insertNewTableRow.createCell();
                cell.setWidthType(TableWidthType.NIL); //不设置列宽
            }
            TableRenderPolicy.Helper.renderRow(table.getRow(startDataRow), rowData);
            startDataRow++; // 这是在每一行后添加, 不加的话每次添加行就是在标题行后添加行
        }
    }

}
```

#### 后端生成Echarts图片

关于后端生成Echarts图片,通过网上查资料有对Echarts生成Options的javaAPI操作

```纯文本
<dependency>
  <groupId>com.github.abel533</groupId>
  <artifactId>ECharts</artifactId>
  <version>3.0.0.6</version>
</dependency>
```

具体用法网上查找一下, 通过相应API,最终生成Options ,通过JSONObject 或GESON 转换字符串后

```纯文本
{    color : ['#FB0000', '#FFC009', '#FFE082'],      legend: {        data: ['P0', 'P1', 'P2'],        left: '3%',        bottom: '3%',        itemWidth: 15,        itemHeight: 5,        itemGap: 40      },      grid: {        left: '3%',        right: '4%',        bottom: '10%',        top: '3%',        containLabel: true      },       xAxis: {        type: 'category',        axisTick: {          show: false        },        axisLine: {          lineStyle: {            color: '#C6CCD7'          }        },        axisLabel: {          color: '#505363',          height: '14px',          width: '70px',          textAlign: 'center',          rotate:30        },          data: xShowData,      },   yAxis: [        {          type: 'value',          splitLine: {            show: true,            lineStyle: {              color: '#ccc'            }          },          axisTick: {            show: false,          },          axisLine: {            show: false          },          axisLabel: {            color: '#333'          }        }      ],    series: [        {          name: 'P0',          type: 'bar',          stack: 'number',          barWidth: 70,          data: seriesData1        },        {          name: 'P1',          type: 'bar',          stack: 'number',          data: seriesData2        },        {          name: 'P2',          type: 'bar',          stack: 'number',          data: seriesData3        },      ]};
```

其中xShowData 是自己需要替换的echarts的很坐标, seriesData1,seriesData2,seriesData3 是图形y坐标的值, 这里提供的是一个模板

后端生成了相应echarts的options , 我们可以通过html模板生成echarts

#### 通过Phantomjs(无界面的浏览器) 做很多事情

PhantomJS是一个基于webkit的JavaScript API。它使用QtWebKit作为它核心浏览器的功能，使用webkit来编译解释执行JavaScript代码。任何你可以在基于webkit浏览器做的事情，它都能做到。它不仅是个隐形的浏览器，提供了诸如CSS选择器、支持Web标准、DOM操作、JSON、HTML5、Canvas、SVG等，同时也提供了处理文件I/O的操作，从而使你可以向操作系统读写文件等。PhantomJS的用处可谓非常广泛，诸如网络监测、网页截屏、无需浏览器的 Web 测试、页面访问自动化等。

> PhantomJS官方地址：[http://phantomjs.org/](https://links.jianshu.com/go?to=http%3A%2F%2Fphantomjs.org%2F "http://phantomjs.org/")。
> 　PhantomJS官方API：[http://phantomjs.org/api/](https://links.jianshu.com/go?to=http%3A%2F%2Fphantomjs.org%2Fapi%2F "http://phantomjs.org/api/")。
> 　PhantomJS官方示例：[http://phantomjs.org/examples/](https://links.jianshu.com/go?to=http%3A%2F%2Fphantomjs.org%2Fexamples%2F "http://phantomjs.org/examples/")。
> 　PhantomJS GitHub：[https://github.com/ariya/phantomjs/](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fariya%2Fphantomjs%2F "https://github.com/ariya/phantomjs/")

> 后端需要生成Echarts图片, 采用Phantomjs可以进行对Echarts截图

-   \*\*步骤一: 下载地址: \*\*[**https://npm.taobao.org/mirrors/phantomjs**](https://npm.taobao.org/mirrors/phantomjs "https://npm.taobao.org/mirrors/phantomjs")

![](C:\Users\zhangbing.xie\AppData\Roaming\Typora\typora-user-images\image-20210901154212617.png)

需要下载对应的版本, 在开发阶段我们使用windows版本即可, 但是我们服务器一般都是部署在linux服务器上所以需要下载linux版本,

后续也会分享一下linux版本在打包成镜像后, phantomjs在镜像中的使用

之前也想过很多办法如何在后端生成echarts图形, 最终还是考虑使用phantomjs插件来实现

-   **步骤二: 准备生成Echarts图片的脚本**
    ​	1. jquery-3.2.1.min.js
    ​    2. echarts.min.js
    ```纯文本
    3. echarts-convert.js 此脚本为最主要脚本，生成echart图片的主要逻辑代码都在此脚本内
    ```
    后续代码贴一下
-   步骤3

​       自己编写常用的测试options，主要为折线图、柱状图、饼图

提供操作phantomjs工具类

```纯文本
package com.irootech.mds.controller.report.util;

import cn.hutool.core.util.ObjectUtil;
import com.alibaba.fastjson.JSONObject;

import java.io.*;
import java.util.List;
import java.util.Map;
import java.util.UUID;

/**
 * @ClassName PhantomJsUtils
 * @Description TODO
 * @Author xzb
 * @Version 1.0
 */
public class PhantomJsUtils {

    /**
     * 生成EChart图 : 需要 phantomjs 可执行文件/  echarts-convert.js 转换文件(依赖echarts.js/jquery.js)  
     * options : 就是echarts前端代码options的json字符串,这个也可以通过上面的echartsApi生成,也可以直接写成字符串,需要把他生成在xxx.json文件中
     * tempDir : 生成存放一个临时目录
     * 生成图片的宽和高度  850  * 500 
     * @return
     */
    public static String generateEChart(String options,String tempDir, int width, int height) throws Exception{
        //echarts-convert.js 脚本所在 path
        String scriptPath = ReportUtils.BASE_LINUX_PATH;
        String defaultPhantomjsPath = "";
        if (isWindows()) {
            scriptPath = ReportUtils.readWinFilePath("script");
            defaultPhantomjsPath = scriptPath + "/win/";
        }
        String defaultEchartjsPath = scriptPath + "/echarts-convert.js";
        String defaultTempImg = scriptPath + "/tempImg/" + tempDir +"/";
        // 生成Echart的初始化json文件
        String dataPath = writeFile(options, defaultTempImg);
        // 生成随机文件名
        String fileName = UUID.randomUUID().toString().substring(0, 8) + ".png";
        String imgPath =  defaultTempImg + fileName;

        // 文件路径（路径+文件名）
         createDirFile(imgPath);
        // 这里只能写绝对路径，因为要执行系统命令行
        String cmd = defaultPhantomjsPath + "phantomjs  " +
                defaultEchartjsPath +
                " -infile " + dataPath +
                " -outfile " + imgPath + " -width "+ width +" -height "+ height;
        Process process = null;
        try{
            process =Runtime.getRuntime().exec(cmd);
            readProcess(process);
            // 关闭进程资源对象
            process.waitFor();
        } finally {
            if (ObjectUtil.isNotEmpty(process)) process.destroy();
        }
        // 删除生成的临时json文件
        File jsonFile = new File(dataPath);
        jsonFile.delete();
        return imgPath;

    }

    public static void readProcess(Process process) throws Exception{
        //取得命令结果的输出流
        InputStream fis=process.getInputStream();
        //用一个读输出流类去读
        InputStreamReader isr=new InputStreamReader(fis);
        //用缓冲器读行
        BufferedReader br=new BufferedReader(isr);
        String line=null;
        //直到读完为止
        while((line=br.readLine())!=null) {
            System.out.println(line);
        }
        fis.close();
        isr.close();
        br.close();
    }

    public static File createDirFile(String path) throws IOException{
        // 文件路径（路径+文件名）
        File file = new File(path);
        // 文件不存在则创建文件，先创建目录
        if (!file.exists()) {
            File dir = new File(file.getParent());
            dir.mkdirs();
            file.createNewFile();
        }
        return file;
    }

    public static boolean isWindows() {
        return System.getProperty("os.name").toUpperCase().indexOf("WINDOWS") >= 0 ? true : false;
    }

    /**
     * 保存EChart临时json
     * @param options echart初始化js
     * @param tmpPath 临时文件保存路径
     * @return 文件完整路径
     */
    public static String writeFile(String options, String tmpPath) throws IOException{
        String dataPath = tmpPath + UUID.randomUUID().toString().substring(0, 8) + ".json";
        /* 写入Txt文件 */
        File writeName = createDirFile(dataPath);
        BufferedWriter out = new BufferedWriter(new FileWriter(writeName));
        out.write(options);
        // 把缓存区内容压入文件
        out.flush();
        // 最后记得关闭文件
        out.close();
        return dataPath;
    }

}
```

生成的图片,我们替换模板{{@xxxChart}}   3/4 大小就差不多了

```纯文本
/**
 * 功能描述: 图片渲染<br>
 *
 * @return: <br>
 * @date: 2021/8/20 16:21
 */
public static void renderImageData(String imagePath, String renderKey, Map renderMap) throws IOException {
    BufferedImage picture = ImageIO.read(new FileInputStream(imagePath)); //获取图片对象
    //设置图片的大小为原图的3/4
    renderMap.put(renderKey, new PictureRenderData(picture.getWidth() * 3 / 4, picture.getHeight() * 3 / 4, imagePath));
}
```

#### POI-TL 替换world模板生成World

> 主要代码

```纯文本
template = XWPFTemplate.compile(templatePath, builder.build()).render(renderMap);
template.writeAndClose(new FileOutputStream(desPath));
```

#### Word 模板转 PDF

> 网上找好几种方式

方式一: 只支持windows,可惜\~\~\~\~\~\~\~\~\~\~\~\~ 放弃,本人尝试linux上,不支持转换

```纯文本
<!--pdf-->
<!--word转pdf,感觉很牛逼-->
<dependency>
    <groupId>com.documents4j</groupId>
    <artifactId>documents4j-local</artifactId>
    <version>1.0.3</version>
</dependency>
<dependency>
    <groupId>com.documents4j</groupId>
    <artifactId>documents4j-transformer-msoffice-word</artifactId>
    <version>1.0.3</version>
</dependency>
<!--end-->
```

```纯文本
   File inputWord = new File("xxx.docx");
    File outputFile = new File("xxx.pdf");
    try  {
        InputStream docxInputStream = new FileInputStream(inputWord);
        OutputStream outputStream = new FileOutputStream(outputFile);
        IConverter converter = LocalConverter.builder().build();         
        converter.convert(docxInputStream).as(DocumentType.DOCX).to(outputStream).as(DocumentType.PDF).execute();
        outputStream.close();
        System.out.println("success");
    } catch (Exception e) {
        e.printStackTrace();
    }
```

方式二. 使用破解版, 网上找的

```纯文本
<dependency>
    <groupId>com.aspose</groupId>
    <artifactId>aspose-words</artifactId>
    <version>18.6</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/src/main/resources/lib/aspose-words-18.6-jdk16-crack.jar</systemPath>
</dependency>

此处需要自己下载aspose-words-18.6-jdk16-crack.jar 这个包, 存放在resources下新建一个lib目录, 这样在springboot打包的时候,才可以打包到项目中
```

> 在resources下需要新建一个License.xml配置文件,就是需要读取的一个随机码

```纯文本
<License>
    <Data>
        <Products>
            <Product>Aspose.Total for Java</Product>
        </Products>
        <EditionType>Enterprise</EditionType>
        <SubscriptionExpiry>20991231</SubscriptionExpiry>
        <LicenseExpiry>20991231</LicenseExpiry>
        <SerialNumber>8bfe198c-7f0c-4ef8-8ff0-acc3237bf0d7</SerialNumber>
    </Data>
    <Signature>sNLLKGMUdF0r8O1kKilWAGdgfs2BvJb/2Xp8p5iuDVfZXmhppo+d0Ran1P9TKdjV4ABwAgKXxJ3jcQTqE/2IRfqwnPf8itN8aFZlV3TJPYeD3yWE7IT55Gz6EijUpC7aKeoohTb4w2fpox58wWoF3SNp6sK6jDfiAUGEHYJ9pjU=</Signature>
</License>
```

> java应用,很简单

```纯文本
/**
 * 加载license 用于破解 不生成水印
 *
 * @author LCheng
 * @date 2020/12/25 13:51
 */
@SneakyThrows
private static void getLicense() {
    try (InputStream is = TestPath.class.getClassLoader().getResourceAsStream("License.xml")) {
        License license = new License();
        license.setLicense(is);
    }
}

/**
 * word转pdf
 *
 * @param wordPath word文件保存的路径
 * @param pdfPath  转换后pdf文件保存的路径
 * @author LCheng
 * @date 2020/12/25 13:51
 */
@SneakyThrows
public static void wordToPdf(String wordPath, String pdfPath) {
    getLicense();
    File file = new File(pdfPath);
    try (FileOutputStream os = new FileOutputStream(file)) {
        Document doc = new Document(wordPath);
        doc.save(os, SaveFormat.PDF);
    }
}
```

基于以上部分,需要提供一个读取resources下文件路径的一个坑:

```纯文本
/**
 * 读取windows路径下。 filePath为resources目录下的相对路径，如：tmp/category.xlsx
 */
public static String readWinFilePath(String filePath) throws IOException {
    //获得文件流。 ClassPathResource自动定位到resources目录
    return new ClassPathResource(filePath).getFile().getAbsolutePath();
}
这个方法在windows下获取某个文件或者文件夹的路径是没毛病的,但是当我们在springboot打jar包后是获取不到的. 这个坑搞了我很久

```

> 读取资源文件,可以这样win/linux 是没问题的

```纯文本
static{
    try {
        ClassPathResource resource = new ClassPathResource("script/options.properties");
        InputStream in=resource.getInputStream();
        //解决中文乱码
        BufferedReader bf = new BufferedReader(new InputStreamReader(in, "UTF-8"));
        prop.load(bf);
        in.close();
        bf.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
并且在pom.xml中要加上可读配置, 可以让刚才的jar包打进springboot的jar, 这2段配置要加上, 也是采坑的

    <build>
        <!-- 定义包含这些资源文件，能在jar包中获取这些文件 -->
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.*</include>
                </includes>
                <!--是否替换资源中的属性-->
                <filtering>false</filtering>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <!-- 打包时会将本地jar一起打包 -->
                    <includeSystemScope>true</includeSystemScope>
                    <fork>true</fork>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

> 在linux目录下我们读取的都是 /  开始的根目录, 因为我们项目使用的docker就需要把某些访问路径的文件通过COPY/ADD命令拷贝到 容器的 / 更目录下, 去操作某些文件资源

#### 踩坑

1.  需要把额外的jar包打入 springboot中的jar包中,此时需要pom.xml中天健build配置
2.  springboot在jar包部署项目后, 读取resources资源文件路径采坑,不能像读取War包一样读取springboot打成jar包后的方式读取
3.  phantomjs 读取可执行文件, 如果没有配置环境变量,需要写全路径, 我们下载的包, 只需要要解压后bin/目录下的这一个文件即可
4.  phantomjs 通过DockerFile 构建在项目中时, 需要注意基础的镜像版本, 镜像版本不同,phantomjs 在部署的docker容器中执行就是not found, 这是一个很大的坑,摸索了好久, 最后发现DockerFile拉取的基础镜像问题
5.  Process开启子线程可能出现阻塞:[https://www.bilibili.com/read/cv8706996/](https://www.bilibili.com/read/cv8706996/ "https://www.bilibili.com/read/cv8706996/")

提供一个简单的DockerFile 吧, 此处基础镜像采用的 java:8 没问题

> From java:8
> ENV TZ Asia/Shanghai
> ADD target/demo \*.jar /pdfdemo.jarADD target/demo \*.jar /pdfdemo.jar
> \#把idea中target下需要访问资源的文件夹进行add到容器中
> ADD target/classes/script/  /script/
> \#COPY拷贝到容器内也要把名字写过去,否则用ADD压缩包可能会自动解压
> COPY target/classes/script/linux/phantomjs-2.1.1-linux-x86\_64.tar.bz2    /opt/phantomjs-2.1.1-linux-x86\_64.tar.bz2
> \#在容器内对phantomjs进行解压,并且放到bin目录下,就可以直接使用了
> RUN mkdir -p /usr/share &&&#x20;
> cd /usr/share&#x20;
> && tar -xvf /opt/phantomjs-2.1.1-linux-x86\_64.tar.bz2 -C /usr/share&#x20;
> && mv /usr/share/phantomjs-2.1.1-linux-x86\_64/bin/phantomjs /usr/share/&#x20;
> && ln -s /usr/share/phantomjs /usr/bin/phantomjs&#x20;
> && phantomjs --version
> \#需要给当前目录赋予权限,有phantomjs可执行文件
> RUN chmod -R 777 /script/
> EXPOSE 8082
> \#配置容器启动后执行的命令
> ENTRYPOINT \["java","-jar","/app.jar"]

#### 补上之前的echarts-convert.js

```纯文本
(function () {
    var system = require('system');
    var fs = require('fs');
    var config = {
        // define the location of js files
        JQUERY: 'jquery-3.2.1.min.js',
        //ESL: 'esl.js',
        ECHARTS: 'echarts.min.js',
        // default container width and height
        DEFAULT_WIDTH: '600',
        DEFAULT_HEIGHT: '700'
    }, parseParams, render, pick, usage;

    usage = function () {
        console.log("\nUsage: phantomjs echarts-convert.js -options options -outfile filename -width width -height height"
            + "OR"
            + "Usage: phantomjs echarts-convert.js -infile URL -outfile filename -width width -height height\n");
    };

    pick = function () {
        var args = arguments, i, arg, length = args.length;
        for (i = 0; i < length; i += 1) {
            arg = args[i];
            if (arg !== undefined && arg !== null && arg !== 'null' && arg != '0') {
                return arg;
            }
        }
    };

    parseParams = function () {
        var map = {}, i, key;
        if (system.args.length < 2) {
            usage();
            phantom.exit();
        }
        for (i = 0; i < system.args.length; i += 1) {
            if (system.args[i].charAt(0) === '-') {
                key = system.args[i].substr(1, i.length);
                if (key === 'infile') {
                    // get string from file
                    // force translate the key from infile to options.
                    key = 'options';
                    try {
                        map[key] = fs.read(system.args[i + 1]).replace(/^\s+/, '');
                    } catch (e) {
                        console.log('Error: cannot find file, ' + system.args[i + 1]);
                        phantom.exit();
                    }
                } else {
                    map[key] = system.args[i + 1].replace(/^\s+/, '');
                }
            }
        }
        return map;
    };

    render = function (params) {
        var page = require('webpage').create(), createChart;

        var bodyMale = config.SVG_MALE;
        page.onConsoleMessage = function (msg) {
            console.log(msg);
        };

        page.onAlert = function (msg) {
            console.log(msg);
        };

        createChart = function (inputOption, width, height,config) {
            var counter = 0;
            function decrementImgCounter() {
                counter -= 1;
                if (counter < 1) {
                    console.log(messages.imagesLoaded);
                }
            }

            function loadScript(varStr, codeStr) {
                var script = $('<script>').attr('type', 'text/javascript');
                script.html('var ' + varStr + ' = ' + codeStr);
                document.getElementsByTagName("head")[0].appendChild(script[0]);
                if (window[varStr] !== undefined) {
                    console.log('Echarts.' + varStr + ' has been parsed');
                }
            }

            function loadImages() {
                var images = $('image'), i, img;
                if (images.length > 0) {
                    counter = images.length;
                    for (i = 0; i < images.length; i += 1) {
                        img = new Image();
                        img.onload = img.onerror = decrementImgCounter;
                        img.src = images[i].getAttribute('href');
                    }
                } else {
                    console.log('The images have been loaded');
                }
            }
            // load opitons
            if (inputOption != 'undefined') {
                // parse the options
                loadScript('options', inputOption);
                // disable the animation
                options.animation = false;
            }

            // we render the image, so we need set background to white.
            $(document.body).css('backgroundColor', 'white');
            var container = $("<div>").appendTo(document.body);
            container.attr('id', 'container');
            container.css({
                width: width,
                height: height
            });
            // render the chart
            var myChart = echarts.init(container[0]);
            myChart.setOption(options);
            // load images
            loadImages();
            return myChart.getDataURL();
        };

        // parse the params
        page.open("about:blank", function (status) {
            // inject the dependency js
            page.injectJs(config.ESL);
            page.injectJs(config.JQUERY);
            page.injectJs(config.ECHARTS);


            var width = pick(params.width, config.DEFAULT_WIDTH);
            var height = pick(params.height, config.DEFAULT_HEIGHT);

            // create the chart
            var base64 = page.evaluate(createChart, params.options, width, height,config);
            fs.write("base64.txt",base64);
            // define the clip-rectangle
            page.clipRect = {
                top: 0,
                left: 0,
                width: width,

                height: height
            };
            // render the image
            page.render(params.outfile);
            console.log('render complete:' + params.outfile);
            // exit
            phantom.exit();
        });
    };
// get the args
    var params = parseParams();

// validate the params
    if (params.options === undefined || params.options.length === 0) {
        console.log("ERROR: No options or infile found.");
        usage();
        phantom.exit();
    }
// set the default out file
    if (params.outfile === undefined) {
        var tmpDir = fs.workingDirectory + '/tmp';
        // exists tmpDir and is it writable?
        if (!fs.exists(tmpDir)) {
            try {
                fs.makeDirectory(tmpDir);
            } catch (e) {
                console.log('ERROR: Cannot make tmp directory');
            }
        }
        params.outfile = tmpDir + "/" + new Date().getTime() + ".png";
    }

// render the image
    render(params);
}());

```

![](C:\Users\zhangbing.xie\AppData\Roaming\Typora\typora-user-images\image-20210901173510844.png)

这几个文件要在相同目录下

1.  记录一个mybatis采坑:
    使用 别名的时候  as '别名' ,  在mybatis 中别名包含了 点, 这返回map的时候会有问题
    > as '192.168.62.130'
    > 返回的map会变为
    > {
    > "192": {
    > ​		 "168": {
    > ​				"62" : {
    > ​						"130" : 值
    > ​				}
    > ​			}
    > ​		}
    > }

这个bug我暂时没解决, 我换了一种方式,解决,不要用这样的别名.
