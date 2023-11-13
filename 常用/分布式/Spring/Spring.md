# Spring

&#x20;Bean的创建生命周期

UserService.class  —> 无参构造函数（推断构造方法） —》普通对象 —〉依赖注入（属性赋值）—》初始化前（@PostConstruct） —〉初始化(InitializingBean) —》初始化后(AOP—>代理对象)    —》 Bean

UserServiceProxy —> 代理对象 —> 代理对象的target=普通对象（代理对象有一个target属性，赋值为之前生成的普通对象）

```bash
class UserServiceProxy extends UserService{
    UserService target
    public void test(){
      //执行切面@Before方法
      target.test(); // 执行原来的方法， 不是调用super.test()方法，因为代理对戏那个UserServiceProxy代理对象只是集成啦UserService, 集成的这个对象如果有@Autowried注解的属性是null值
                    // （代理对象并没有经过完整的Spring对象生命周期）
    }
}
```

***

《Spring源码编译教程》

[https://www.yuque.com/docs/share/17a9d955-2153-4113-8bbb-79c35e274ed0?#](https://www.yuque.com/docs/share/17a9d955-2153-4113-8bbb-79c35e274ed0?# "https://www.yuque.com/docs/share/17a9d955-2153-4113-8bbb-79c35e274ed0?#")&#x20;

1\. Spring底层核心原理理解

***

<https://note.youdao.com/ynoteshare/index.html?id=9edfa5ccafca457ee2c1aecf17bccee5&type=note&_time=1696946620203>

02-手写模拟Spring底层原理.note

<https://note.youdao.com/ynoteshare/index.html?id=b632a56d32fe506d944541d650d339d5&type=note&_time=1696946930231>

03-Spring之底层架构核心概念解析

<https://note.youdao.com/ynoteshare/index.html?id=663444a188ed9057dffd07cfbeed43cc&type=note&_time=1696947000602>

04-Spring之Bean生命周期源码解析上

<https://note.youdao.com/ynoteshare/index.html?id=e8e3fcbeb1428c2da9e0764c796d2660&type=note&_time=1696947105811>

05-Spring之Bean生命周期源码解析（下）

<https://note.youdao.com/ynoteshare/index.html?id=1f3f25f6dc56c043ce8cc9dca586f2db&type=note&_time=1696947187122>

[RocketMQ](RocketMQ/RocketMQ.md "RocketMQ")
