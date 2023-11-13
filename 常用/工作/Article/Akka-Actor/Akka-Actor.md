# Akka Actor

[基于Actor的系统](https://www.ibm.com/developerworks/java/library/j-javaactors/index.html#artrelatedtopics "基于Actor的系统")通过实现消息传递方案，使并行处理更易于编码。 在这种方案中，系统中的每个参与者都可以接收消息。 执行消息要求的操作； 并向其他参与者（包括他们自己）发送消息，以执行复杂的操作序列。 actor之间的所有消息都是异步的 ，这意味着发送方在收到任何答复之前会继续进行处理。 因此，演员的一生可能会花费在接收和处理消息的无限循环中。

[Akka](https://so.csdn.net/so/search?q=Akka\&spm=1001.2101.3001.7020 "Akka")框架的核心就是Actor模型，因此理解Actor是理解Akka框架的关键。Actor是一种并发编程模型，它将计算任务分解为独立的Actor，每个Actor都有自己的状态和行为，并通过异步消息传递来实现彼此之间的通信。

[https://ifeve.com/introducing-actors-akka-notes-part-1/](https://ifeve.com/introducing-actors-akka-notes-part-1/ "https://ifeve.com/introducing-actors-akka-notes-part-1/")

[https://developer.lightbend.com/guides/akka-quickstart-scala/](https://developer.lightbend.com/guides/akka-quickstart-scala/ "https://developer.lightbend.com/guides/akka-quickstart-scala/")

[https://betheme.net/news/txtlist\_i43838v.html?action=onClick](https://betheme.net/news/txtlist_i43838v.html?action=onClick "https://betheme.net/news/txtlist_i43838v.html?action=onClick")

[https://blog.csdn.net/qq\_36017232/article/details/130101126](https://blog.csdn.net/qq_36017232/article/details/130101126 "https://blog.csdn.net/qq_36017232/article/details/130101126")

[https://blog.csdn.net/weixin\_44451022/category\_12134010.html](https://blog.csdn.net/weixin_44451022/category_12134010.html "https://blog.csdn.net/weixin_44451022/category_12134010.html")

<https://juejin.cn/post/6844904192797065224#heading-5>

Actor 是 Akka 中的执行单元。Actor 模型是一种抽象，可以更轻松地编写正确的并发、并行和分布式系统

当我们通过ActorSystem对象的apply方法创建ActorSystem实例而不指定任何配置的时候，它会在classpath的根路径下依次去查找application.conf, application.json以及application.peroperties文件并自动进行加载。

```java
一、Actor模型
Actor模型是一种并发编程模型，它通过封装状态和行为，将并发问题转化为消息传递问题，从而简化了并发编程。在Actor模型中，每个Actor都是一个独立的执行单元，它可以接收消息、发送消息、改变状态和执行行为。Actor之间的消息传递是异步的，不需要共享状态，从而避免了并发竞争和死锁等问题。

二、Akka的特点
高性能
Akka使用基于事件驱动的异步消息传递机制，避免了锁竞争和线程切换等开销，从而提高了系统的吞吐量和响应速度。

高可靠性
Akka提供了监督机制和容错策略，能够在出现异常或故障时自动恢复或重启Actor，从而保证了系统的可靠性和稳定性。

分布式支持
Akka提供了分布式Actor模型，能够将Actor分布在多个节点上，实现分布式系统开发。同时，Akka还提供了远程调用、集群管理和路由等支持，简化了分布式系统的开发和维护。

扩展性
Akka采用模块化的设计，提供了丰富的扩展点和插件机制，能够灵活扩展和定制框架功能。
三、应用场景
Akka框架的应用场景比较广泛，以下列举几个典型的应用场景：

高并发、分布式系统：Akka框架基于Actor模型，提供了异步、非阻塞的消息传递机制，能够轻松构建高并发、分布式系统，提升系统的可伸缩性和性能。

大数据处理：Akka框架可以与Apache Spark、Apache Flink等大数据处理框架集成，帮助开发者快速构建分布式、并行的大数据处理系统。

微服务：Akka框架提供了轻量级的Actor和路由机制，能够轻松构建高可用、可扩展的微服务架构。

实时数据处理：Akka框架能够实现实时数据流处理，适用于实时监控、实时日志处理等场景。

IoT：Akka框架能够处理海量设备的事件流，实现设备间的消息传递和协同处理。

总的来说，Akka框架适用于需要处理高并发、分布式、异步、非阻塞等问题的场景，能够提升系统的性能和可伸缩性。
————————————————

1. ActorSystem是一个全局的、线程安全的Actor容器，每个Actor都是ActorSystem中的子Actor。
2.这个指向 Actor 实例的引用叫做 ActorRef。ActorRef 是一个无类型的引用，将其指向的 Actor 封装起来，提供了更高层的抽象，并且给用户提供了一种与 Actor 进行通信的机制。
  为了保证能够将 Actor 的实例封装起来，不让其被外部直接访问，我们将所有构造函数的参数传给一个 Props 的实例

3.// 上述代码将一个类型为String、内容为"Ping"的消息发送给myActor，
  // 同时指定了消息的发送者为ActorRef.noSender()，表示该消息是无发送者的。当myActor收到消息时，它会调用其createReceive方法中匹配类型为String的消息的行为，即打印收到的消息内容。
   myActor.tell("Ping",ActorRef.noSender());
   

4. // Akka提供的Patterns.ask方法向myActor发送了一个类型为"在吗"的消息，并设置了一个超时时间为5秒。当myActor收到该消息时，它会调用其createReceive方法中匹配类型为的消息的行为，执行该行为并返回一个结果。
        Future<Object> future = Patterns.ask(myActor,"Ping", Timeout.apply(5, TimeUnit.SECONDS));
        // 上述代码会阻塞当前线程，直到Actor返回结果，然后返回Actor返回的结果
        Object result = Await.result(future, Duration.Inf());
        System.out.println(result);
   

5.  // ActorRef还可以用于进行远程调用，这需要使用Akka的远程调用功能。
        // 如果ActorRef指向的Actor实例在远程节点上，则可以通过ActorRef进行远程调用。远程调用的方式与本地调用相同，
        // 只是在ActorRef的构造函数中需要指定远程Actor的地址。
        ActorRef remoteActorRef = actorSystem.actorOf(Props.create(JavaPongActor.class), "myRemoteActor");
        // "akka.tcp://mySystem@remotehost:1234/user/myRemoteActor"
        ActorSelection remoteActorSelection = actorSystem.actorSelection("akka://gdpr-system/user/myRemoteActor");
        remoteActorSelection.tell("hello", ActorRef.noSender());
        
6.   // 关闭ActorSystem
     actorSystem.terminate();
     

7. tell()方法：
tell()方法是Actor之间通信的基本方法。它是异步的，不会等待接收方Actor处理完消息。在tell()方法中，第一个参数是要发送的消息内容，第二个参数是接收方Actor的ActorRef，如果不需要接收方Actor回复消息，则可以使用ActorRef.noSender()作为第二个参数。

8. ask()方法：
ask()方法也是一种向Actor发送消息的方式，不同的是，它会等待接收方Actor处理完消息并返回一个Future对象。在ask()方法中，第一个参数是接收方Actor的ActorRef，第二个参数是要发送的消息内容。除此之外，还需要使用Akka提供的Timeout类指定等待超时时间，以及使用Akka提供的ExecutionContext来执行回调函数。
Timeout timeout = Timeout.create(Duration.ofSeconds(5));
Future<Object> future = Patterns.ask(actorRef, "hello", timeout);
future.onComplete(new OnComplete<Object>() {
  @Override
  public void onComplete(Throwable throwable, Object o) throws Throwable {
    if (throwable != null) {
      System.out.println("ask failed: " + throwable.getMessage());
    } else {
      System.out.println("ask result: " + o);
    }
  }
}, system.dispatcher());


总之，tell()方法是异步的，不等待接收方Actor处理完消息，而ask()方法是同步的，会等待接收方Actor处理完消息并返回一个Future对象。

https://cloudgdpr.blob.core.windows.net/gdprexports/gene820.zip?sig=K915BNlSv8U4NOGt%2FZUhckwXYQPFGd3J7RoSZKlqumk%3D&st=2023-09-09T06%3A57%3A54Z&se=2023-09-16T07%3A02%3A54Z&sv=2019-02-02&sp=r&sr=b

```

```java
package com.suunto.gdpr.service;

import akka.actor.*;
import akka.pattern.Patterns;
import akka.util.Timeout;
import scala.concurrent.Await;
import scala.concurrent.Future;
import scala.concurrent.duration.Duration;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

/**
 * AbstractActor 类有一个 createReceive 方法，其子类必须实现这个方法或是通过构造函数调用该方法。
 * 连续调用 receiveBuilder 的方法，为所有需要匹配处理的输入消息类型提供响应方法的描述，然后调用 build() 方法生成需要返回的Receive
 * 这个指向 Actor 实例的引用叫做 ActorRef。ActorRef 是一个无类型的引用，将其指向的 Actor 封装起来，提供了更高层的抽象，并且给用户提供了一种与 Actor 进行通信的机制。
 */
public class JavaPongActor extends AbstractActor {
    /**
     * 这个 Actor 接收一个字符串“Ping”，返回字符串“Pong”作为响应
     * @return
     */
    @Override
    public Receive createReceive() {
        return receiveBuilder()
                .matchEquals("Ping", s -> {
                    System.out.println("接收消息:" + s);
                    // 把 pong 消息发送给自己
                    sender().tell("pong", ActorRef.noSender());
                })
                .matchEquals("hello", s -> {
                    System.out.println("接收消息:" + s);
                })
                .match(String.class, s -> {
                    ActorRef sender = getSender();
                    ActorRef receive = getSelf();
                    System.out.println("receive: " + receive.path() + ", 接收消息:" + s + " , sent by: " + sender.path());
                })
                .matchAny(x ->sender().tell(
                        new Status.Failure(new Exception("unknown message")
                        ), self())
                ).build();
    }

    public static Props props(String response) {
        return Props.create(JavaPongActor.class, response);
    }

    /**
     *
     * @param args
     * @throws InterruptedException
     * @throws TimeoutException
     */
    public static void main(String[] args) throws InterruptedException, TimeoutException {
        //ActorSystem是一个全局的、线程安全的Actor容器，每个Actor都是ActorSystem中的子Actor。
        ActorSystem actorSystem = ActorSystem.create("gdpr-system"); // load Config 配置


        // 为了保证能够将 Actor 的实例封装起来，不让其被外部直接访问，我们将所有构造函数的参数传给一个 Props 的实例
        ActorRef myActor = actorSystem.actorOf(Props.create(JavaPongActor.class),"my-actor");
        System.out.println(myActor.path());
        System.out.println(myActor.path().toStringWithoutAddress());
        // 上述代码将一个类型为String、内容为"Ping"的消息发送给myActor，
        // 同时指定了消息的发送者为ActorRef.noSender()，表示该消息是无发送者的。当myActor收到消息时，它会调用其createReceive方法中匹配类型为String的消息的行为，即打印收到的消息内容。
        myActor.tell("Ping",ActorRef.noSender());

        // Akka提供的Patterns.ask方法向myActor发送了一个类型为"在吗"的消息，并设置了一个超时时间为5秒。当myActor收到该消息时，它会调用其createReceive方法中匹配类型为的消息的行为，执行该行为并返回一个结果。
        Future<Object> future = Patterns.ask(myActor,"Ping", Timeout.apply(5, TimeUnit.SECONDS));
        // 上述代码会阻塞当前线程，直到Actor返回结果，然后返回Actor返回的结果
        Object result = Await.result(future, Duration.Inf());
        System.out.println(result);

        // ActorRef还可以用于进行远程调用，这需要使用Akka的远程调用功能。
        // 如果ActorRef指向的Actor实例在远程节点上，则可以通过ActorRef进行远程调用。远程调用的方式与本地调用相同，
        // 只是在ActorRef的构造函数中需要指定远程Actor的地址。
        ActorRef remoteActorRef = actorSystem.actorOf(Props.create(JavaPongActor.class), "myRemoteActor");
        // "akka.tcp://mySystem@remotehost:1234/user/myRemoteActor"
        ActorSelection remoteActorSelection = actorSystem.actorSelection("akka://gdpr-system/user/myRemoteActor");
        remoteActorSelection.tell("hello", ActorRef.noSender());

        // ActorRef actor1 = actorSystem.actorOf(JavaPongActor.props("PongFoo"));
        // 关闭ActorSystem
        actorSystem.terminate();

    }
}
```

## 自定义mailbox

```java
import akka.actor.ActorRef;
import akka.actor.ActorSystem;
import akka.actor.Props;
import akka.actor.UntypedAbstractActor;

/**
 * @description:
 * @author: shu
 * @createDate: 2022/12/23 14:08
 * @version: 1.0
 */
class MsgPriorityActor extends UntypedAbstractActor {
    @Override
    public void onReceive(Object msg) throws Exception {
        System.out.println(getSelf()+"--->"+msg+""
                +Thread.currentThread().getName());
    }


    public static void main(String[] args) throws InterruptedException {
        ActorSystem sys=ActorSystem.create("system");
        //ActorRef ref= sys. actorOf(Props.create(MsgPriorityActor.class).withMailbox("msgprio-mailbox"), "priorityActor");
        ActorRef ref= sys. actorOf(Props.create(MsgPriorityActor.class).withDispatcher("my-msgprio-dispatcher"), "priorityActor");
        Object[] messages= {"王五", "李四", "张三", "小二"};
        ref.tell("小二", ActorRef.noSender());
        for(Object msg: messages) {
            ref.tell(msg, ActorRef.noSender());
        }
        Thread.sleep(1000);
        ref.tell("张三", ActorRef.noSender());
    }
}

import akka.actor.ActorSystem;
import akka.dispatch.PriorityGenerator;
import akka.dispatch.UnboundedStablePriorityMailbox;
import com.typesafe.config.Config;

/**
 * @description: 自定义优先级
 * @author: shu
 * @createDate: 2022/12/23 19:23
 * @version: 1.0
 */
class MsgPriorityMailBox extends UnboundedStablePriorityMailbox {


    /**
     * 返回值越小，优先级越高
     * @param settings
     * @param config
     */
    public MsgPriorityMailBox(ActorSystem.Settings settings, Config config) {
        super(new PriorityGenerator() {
            @Override
            public int gen(Object message) {
                if (message.equals("张三")) {
                    return 0;
                }else if(message.equals("李四")) {
                    return 1;
                }else if(message.equals("王五")) {
                    return 2;
                }else {
                    return 3;
                }
            }
        });
    }}

## application.conf

my-msgprio-dispatcher {
            type = Dispatcher
        mailbox-type = "com.suunto.gdpr.service.MsgPriorityMailBox"
            # 其他dispatcher配置在此省略
}

akka.actor.deployment {

  /priorityActor {

    mailbox = msgprio-mailbox

  }

}

msgprio-mailbox {
      mailbox-type = "com.suunto.gdpr.service.MsgPriorityMailBox"
}

```

gdpr-export

![](image/image_Tf1iuDz49o.png)

断路器:

<https://blog.csdn.net/sunquan291/article/details/78250636?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-4-78250636-blog-102717020.235^v38^pc_relevant_sort_base2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-4-78250636-blog-102717020.235^v38^pc_relevant_sort_base2&utm_relevant_index=9>

#### 协程

```java
协程，英文名 Coroutine，源自 Simula 和 Modula-2 语言，它是一种协作式多任务实现，是一种编程思想，并不局限于特定的语言。协程设计的初衷是为了解决并发问题，让协作式多任务实现起来更加方便

Kotlin 协程简单来说是一套线程操作框架，详细点说它就是一套基于线程而实现的一套更上层的工具 API，类似于 Java 的线程池，你可以理解 Kotlin 新造了一些概念用来帮助你更好地使用这些 API，仅此而已

Kotlin 协程可以用看起来同步的方式写出异步的代码，帮你优雅的处理回调地狱。

**特点**：

协程是我们在 Android 上进行异步编程的推荐解决方案。值得关注的特点包括：

- 轻量：您可以在单个线程上运行多个协程，因为协程支持挂起，不会使正在运行协程的线程阻塞。**挂起比阻塞节省内存，且支持多个并行操作。
- 内存泄漏更少：使用结构化并发机制在一个作用域内执行多项操作。**
- 内置取消支持：取消操作会自动在运行中的整个协程层次结构内传播。
- Jetpack 集成：许多 Jetpack 库都包含提供全面协程支持的扩展。某些库还提供自己的协程作用域，可供您用于结构化并发。
```
