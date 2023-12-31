# Spring

### ApplicationContextInitializer

```java
/**
 * 这是整个spring容器在刷新之前初始化ConfigurableApplicationContext的回调接口，
 * 简单来说，就是在容器刷新之前调用此类的initialize方法。这个点允许被用户自己扩展。
 * 用户可以在整个spring容器还没被初始化之前做一些事情。
 * 它会在ConfigurableApplicationContext的refresh()方法调用之前被调用,做一些容器的初始化工作。
 *
 * 可以想到的场景可能为，在最开始激活一些配置，或者利用这时候class还没被类加载器加载的时机，进行动态字节码注入等操作。
 */

public class Test01ApplicationContextInitializer implements ApplicationContextInitializer {
    @Override
    public void initialize(ConfigurableApplicationContext configurableApplicationContext) {
    System.out.println("MyApplicationContextInitializer.......");

    // 打印容器里面有多少个bean
    System.out.println("bean count:" + configurableApplicationContext.getBeanDefinitionCount());

    System.out.println("bean count: "+ configurableApplicationContext.getBeanDefinitionCount());

    String[] beanDefinitionNames = configurableApplicationContext.getBeanDefinitionNames();

    System.out.println("bean names:"+ Arrays.toString(beanDefinitionNames));
    
    }
}
```

### BeanDefinitionRegistryPostProcessor

```java
/**
 * 这个接口在读取项目中的beanDefinition之后执行，提供一个补充的扩展点
 *
 * 使用场景：你可以在这里动态注册自己的beanDefinition，可以加载classpath之外的bean
 */
//@Component
public class Test02BeanDefinitionRegistryPostProcessor implements BeanDefinitionRegistryPostProcessor {
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
        System.out.println("[BeanDefinitionRegistryPostProcessor] postProcessBeanDefinitionRegistry");
    }

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        System.out.println("[BeanDefinitionRegistryPostProcessor] postProcessBeanFactory");
    }
}
```

### BeanFactoryPostProcessor

```java
/**
 * 这个接口是beanFactory的扩展接口，调用时机在spring在读取beanDefinition信息之后，实例化bean之前。
 *
 * 在这个时机，用户可以通过实现这个扩展接口来自行处理一些东西，比如修改已经注册的beanDefinition的元信息。
 */
// @Component
public class Test03BeanFactoryPostProcessor implements BeanFactoryPostProcessor {

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        System.out.println("[BeanFactoryPostProcessor]");
    }
}
```

### InstantiationAwareBeanPostProcessor

```java
/**
 * 该类主要的扩展点有以下5个方法，主要在bean生命周期的两大阶段：实例化阶段 和初始化阶段 ，下面一起进行说明，按调用顺序为：
 *
 * postProcessBeforeInstantiation：实例化bean之前，相当于new这个bean之前
 * postProcessAfterInstantiation：实例化bean之后，相当于new这个bean之后
 * postProcessPropertyValues：bean已经实例化完成，在属性注入时阶段触发，@Autowired,@Resource等注解原理基于此方法实现
 * postProcessBeforeInitialization：初始化bean之前，相当于把bean注入spring上下文之前
 * postProcessAfterInitialization：初始化bean之后，相当于把bean注入spring上下文之后
 * 使用场景：这个扩展点非常有用 ，无论是写中间件和业务中，都能利用这个特性。比如对实现了某一类接口的bean在各个生命期间进行收集，或者对某个类型的bean进行统一的设值等等。
 */
// @Component
public class Test04InstantiationAwareBeanPostProcessor implements InstantiationAwareBeanPostProcessor {

  @Override
  public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
    System.out.println("[TestInstantiationAwareBeanPostProcessor] before initialization " + beanName);
    return bean;
  }

  @Override
  public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
    System.out.println("[TestInstantiationAwareBeanPostProcessor] after initialization " + beanName);
    return bean;
  }

  @Override
  public Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException {
    System.out.println("[TestInstantiationAwareBeanPostProcessor] before instantiation " + beanName);
    return null;
  }

  @Override
  public boolean postProcessAfterInstantiation(Object bean, String beanName) throws BeansException {
    System.out.println("[TestInstantiationAwareBeanPostProcessor] after instantiation " + beanName);
    return true;
  }

  @Override
  public PropertyValues postProcessPropertyValues(PropertyValues pvs, PropertyDescriptor[] pds, Object bean, String beanName) throws BeansException {
    System.out.println("[TestInstantiationAwareBeanPostProcessor] postProcessPropertyValues " + beanName);
    return pvs;
  }
}
```

### SmartInstantiationAwareBeanPostProcessor

```java
/**
 * 该扩展接口有3个触发点方法：
 *
 * predictBeanType：该触发点发生在postProcessBeforeInstantiation之前(在图上并没有标明，因为一般不太需要扩展这个点)，这个方法用于预测Bean的类型，返回第一个预测成功的Class类型，如果不能预测返回null；当你调用BeanFactory.getType(name)时当通过bean的名字无法得到bean类型信息时就调用该回调方法来决定类型信息。
 * determineCandidateConstructors：该触发点发生在postProcessBeforeInstantiation之后，用于确定该bean的构造函数之用，返回的是该bean的所有构造函数列表。用户可以扩展这个点，来自定义选择相应的构造器来实例化这个bean。
 * getEarlyBeanReference：该触发点发生在postProcessAfterInstantiation之后，当有循环依赖的场景，当bean实例化好之后，为了防止有循环依赖，会提前暴露回调方法，用于bean实例化的后置处理。这个方法就是在提前暴露的回调方法中触发。
 */
// @Component
public class Test05SmartInstantiationAwareBeanPostProcessor implements SmartInstantiationAwareBeanPostProcessor {

    @Override
    public Class<?> predictBeanType(Class<?> beanClass, String beanName) throws BeansException {
        System.out.println("[TestSmartInstantiationAwareBeanPostProcessor] predictBeanType " + beanName);
        return beanClass;
    }

    @Override
    public Constructor<?>[] determineCandidateConstructors(Class<?> beanClass, String beanName) throws BeansException {
        System.out.println("[TestSmartInstantiationAwareBeanPostProcessor] determineCandidateConstructors " + beanName);
        return null;
    }

    @Override
    public Object getEarlyBeanReference(Object bean, String beanName) throws BeansException {
        System.out.println("[TestSmartInstantiationAwareBeanPostProcessor] getEarlyBeanReference " + beanName);
        return bean;
    }
}
```

### BeanFactoryAware

```java
/**
 * 这个类只有一个触发点，发生在bean的实例化之后，注入属性之前，也就是Setter之前。这个类的扩展点方法为setBeanFactory，可
 * 以拿到BeanFactory这个属性。
 *
 * 使用场景为，你可以在bean实例化之后，但还未初始化之前，拿到 BeanFactory，在这个时候，可以对每个bean作特殊化的定制。也或者可以把BeanFactory拿到进行缓存，日后使用。
 */
//@Component
public class Test06BeanFactoryAware implements BeanFactoryAware {
    @Override
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        System.out.println("[TestBeanFactoryAware] " + beanFactory.getBean(Test06BeanFactoryAware.class).getClass().getSimpleName());
    }
}
```

### ApplicationContextAwareProcessor

```java
/**
 * Test09ApplicationContextAwareProcessor
 *该类本身并没有扩展点，但是该类内部却有6个扩展点可供实现 ，这些类触发的时机在bean实例化之后，初始化之前
 *
 * 可以看到，该类用于执行各种驱动接口，在bean实例化之后，属性填充之后，通过执行以上红框标出的扩展接口，来获取对应容器的变量。所以这里应该来说是有6个扩展点 ，这里就放一起来说了
 *
 * EnvironmentAware：用于获取EnviromentAware的一个扩展类，这个变量非常有用， 可以获得系统内的所有参数。当然个人认为这个Aware没必要去扩展，因为spring内部都可以通过注入的方式来直接获得。
 * EmbeddedValueResolverAware：用于获取StringValueResolver的一个扩展类， StringValueResolver用于获取基于String类型的properties的变量，一般我们都用@Value的方式去获取，如果实现了这个Aware接口，把StringValueResolver缓存起来，通过这个类去获取String类型的变量，效果是一样的。
 * ResourceLoaderAware：用于获取ResourceLoader的一个扩展类，ResourceLoader可以用于获取classpath内所有的资源对象，可以扩展此类来拿到ResourceLoader对象。
 * ApplicationEventPublisherAware：用于获取ApplicationEventPublisher的一个扩展类，ApplicationEventPublisher可以用来发布事件，结合ApplicationListener来共同使用，下文在介绍ApplicationListener时会详细提到。这个对象也可以通过spring注入的方式来获得。
 * MessageSourceAware：用于获取MessageSource的一个扩展类，MessageSource主要用来做国际化。
 * ApplicationContextAware：用来获取ApplicationContext的一个扩展类，
 * ApplicationContext应该是很多人非常熟悉的一个类了，就是spring上下文管理器，可以手动的获取任何在spring上下文注册的bean，我们经常扩展这个接口来缓存spring上下文，包装成静态方法。同时ApplicationContext也实现了BeanFactory，MessageSource，ApplicationEventPublisher等接口，也可以用来做相关接口的事情。
 * @author zhangbing.xie
 * @version 1.0
 * @description aaa
 * @date 2022/8/17 11:12
 */
public class Test07ApplicationContextAwareProcessor {
}
```

### BeanNameAware

```java
/**
 * 可以看到，这个类也是Aware扩展的一种，触发点在bean的初始化之前，也就是postProcessBeforeInitialization之前，这个类的触发点方法只有一个：setBeanName
 *
 * 使用场景为：用户可以扩展这个点，在初始化bean之前拿到spring容器中注册的的beanName，来自行修改这个beanName的值。
 */
//@Component
public class Test08NormalBeanA implements BeanNameAware {
    public Test08NormalBeanA() {
        System.out.println("NormalBean constructor");
    }

    @Override
    public void setBeanName(String name) {
        System.out.println("[BeanNameAware] " + name);
    }

  /**
   * 这个并不算一个扩展点，其实就是一个标注。其作用是在bean的初始化阶段，
   * 如果对一个方法标注了@PostConstruct，会先调用这个方法。这里重点是要关注下这个标准的触发点，这个触发点是在postProcessBeforeInitialization之后，InitializingBean.afterPropertiesSet之前。
   *
   * 使用场景：用户可以对某一方法进行标注，来进行初始化某一个属性
   */
  @PostConstruct
  public void init(){
    System.out.println("[PostConstruct] NormalBeanA");
  }
}
```

### PostConstruct

> 这个并不算一个扩展点，其实就是一个标注。其作用是在bean的初始化阶段，如果对一个方法标注了`@PostConstruct`，会先调用这个方法。这里重点是要关注下这个标准的触发点，这个触发点是在`postProcessBeforeInitialization`之后，`InitializingBean.afterPropertiesSet`之前

### InitializingBean

```java
/**
 * TestInitializingBean
 *
 * @author aaa
 * @version 1.0
 * @description InitializingBean接口为bean提供了初始化方法的方式，它只包括afterPropertiesSet方法，凡是继承该接口的类，
 * 在初始化bean的时候都会执行该方法。这个扩展点的触发时机在postProcessAfterInitialization之前。
 * 使用场景：用户实现此接口，来进行系统启动的时候一些业务指标的初始化工作
 * @date 2022/10/13 11:35
 */
public class TestInitializingBean implements InitializingBean {
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("[InitializingBean] TestInitializingBean");
    }
}
```

### FactoryBean

```java
/**
 * 一般情况下，Spring通过反射机制利用bean的class属性指定支线类去实例化bean，
 * 在某些情况下，实例化Bean过程比较复杂，如果按照传统的方式，则需要在bean中提供大量的配置信息。配置方式的灵活性是受限的，
 * 这时采用编码的方式可能会得到一个简单的方案。Spring为此提供了一个org.springframework.bean.factory.FactoryBean的工厂类接口，
 * 用户可以通过实现该接口定制实例化Bean的逻辑。FactoryBean接口对于Spring框架来说占用重要的地位，Spring自身就提供了70多个FactoryBean的实现。它们隐藏了实例化一些复杂bean的细节，给上层应用带来了便利。从Spring3.0开始，FactoryBean开始支持泛型，即接口声明改为FactoryBean<T>的形式
 *
 * 使用场景：用户可以扩展这个类，来为要实例化的bean作一个代理，比如为该对象的所有的方法作一个拦截，在调用前后输出一行log，
 * 模仿ProxyFactoryBean的功能。
 */
//@Component
public class Test10FactoryBean implements FactoryBean<Test10FactoryBean.TestFactoryInnerBean> {

    @Override
    public TestFactoryInnerBean getObject() throws Exception {
        System.out.println("[FactoryBean] getObject");
        return new TestFactoryInnerBean();
    }

    @Override
    public Class<?> getObjectType() {
        return TestFactoryInnerBean.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }

    public static class TestFactoryInnerBean{

    }
}
```

### SmartInitializingSingleton

```java
/**
 * 这个接口中只有一个方法afterSingletonsInstantiated，
 * 其作用是是 在spring容器管理的所有单例对象（非懒加载对象）初始化完成之后调用的回调接口。
 * 其触发时机为postProcessAfterInitialization之后。
 *
 * 使用场景：用户可以扩展此接口在对所有单例对象初始化完毕后，做一些后置的业务处理。
 */
//@Component
public class Test11SmartInitializingSingleton implements SmartInitializingSingleton {
    @Override
    public void afterSingletonsInstantiated() {
        System.out.println("[TestSmartInitializingSingleton]");
    }
}
```

### CommandLineRunner

```java
/**
 * 这个接口也只有一个方法：run(String... args)，触发时机为整个项目启动完毕后，自动执行。
 * 如果有多个CommandLineRunner，可以利用@Order来进行排序。
 *
 * 使用场景：用户扩展此接口，进行启动项目之后一些业务的预处理。
 */
public class Test12CommandLineRunner /*implements CommandLineRunner*/ {

   // @Override
    public void run(String... args) throws Exception {
        System.out.println("[TestCommandLineRunner]");
    }
}
```

### DisposableBean

```java
/**
 * 这个扩展点也只有一个方法：destroy()，其触发时机为当此对象销毁时，
 * 会自动执行这个方法。比如说运行applicationContext.registerShutdownHook时，就会触发这个方法。
 */
@Component
public class Test13NormalBeanA implements DisposableBean {
    @Override
    public void destroy() throws Exception {
        System.out.println("[DisposableBean] NormalBeanA");
    }
}
```

### ApplicationListener

```java
这个应该不算spring&springboot当中的一个扩展点，ApplicationListener可以监听某个事件的event，触发时机可以穿插在业务方法执行过程中，用户可以自定义某个业务事件。但是spring内部也有一些内置事件，这种事件，可以穿插在启动调用中。我们也可以利用这个特性，来自己做一些内置事件的监听器来达到和前面一些触发点大致相同的事情。

spring主要的内置事件：

ContextRefreshedEvent

ApplicationContext 被初始化或刷新时，该事件被发布。这也可以在ConfigurableApplicationContext接口中使用 refresh()方法来发生。此处的初始化是指：所有的Bean被成功装载，后处理Bean被检测并激活，所有Singleton Bean 被预实例化，ApplicationContext容器已就绪可用。

ContextStartedEvent

当使用 ConfigurableApplicationContext （ApplicationContext子接口）接口中的 start() 方法启动 ApplicationContext时，该事件被发布。你可以调查你的数据库，或者你可以在接受到这个事件后重启任何停止的应用程序。

ContextStoppedEvent

当使用 ConfigurableApplicationContext接口中的 stop()停止ApplicationContext 时，发布这个事件。你可以在接受到这个事件后做必要的清理的工作

ContextClosedEvent

当使用 ConfigurableApplicationContext接口中的 close()方法关闭 ApplicationContext 时，该事件被发布。一个已关闭的上下文到达生命周期末端；它不能被刷新或重启

RequestHandledEvent

这是一个 web-specific 事件，告诉所有 bean HTTP 请求已经被服务。只能应用于使用DispatcherServlet的Web应用。在使用Spring作为前端的MVC控制器时，当Spring处理用户请求结束后，系统会自动触发该事件
```

```纯文本
项目课程代码外网部署访问地址：https://gitee.com/tearwind/tlmall-v5-single.git``电商项目管理后台前端地址：https://gitee.com/tearwind/mall-admin-web``电商项目商城前端地址：https://gitee.com/tearwind/tlmall-front
文档: https://www.yuque.com/books/share/9f4576fb-9aa9-4965-abf3-b3a36433faa6#
ggmc
https://pan.baidu.com/s/1JbzeeLY1imD0zmbMRf253A#list/path=%2F
```
