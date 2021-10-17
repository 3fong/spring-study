### spring context

```

```

![ApplicationContext构成](https://img-blog.csdn.net/20160301002829498)

ApplicationContext的实质功能都是继承于以下6个实体:

MessageSource: 用于国际化的接口，可以将其理解为公司的翻译。用户可以通过bean配置自定义MessageSource–要求name为“messageSource”，spring会在容器refresh时自动探测并且初始化它。

ApplicationEventPublisher: 用于发布应用事件，例如ContextRefreshed，stopped, started等。它就像是企业邮箱，通过它可以接收到公司的事件通知。用户通过配置ApplicationListener类型的bean即可订阅这类事件，spring通过getBeansOfType取得所有的listener，并依次通知。

ResourcePatternResolver: 对ResourceLoader的扩展，后者只支持对具体路径资源的加载，而前者则支持对某pattern路径资源的加载，默认是ant风格的模式。Resource是特指配置文件，或者class路径(里的扫描路径)。我们可以将它理解为打单的机器，将各个地方发过来的单子打出来。

ListableBeanFactory和HierachicalBeanFactory: 自然的context也是个工厂，context里持有的依然是DefaultListableBeanFactory，通过它完成工厂的相应行为。介绍下这两个工厂，前者主要用于取得批量bean，比如getBeansOfType；后一个工厂则主要体现层级概念，但是context的parentFactory也是一个context，这是因为context具有beanFactory的所有特征。

EnviromentCapable: 则类似是公司的行政部门，负责办公场所等设施维护。它关联着Enviroment。Enviroment也类似context是个包装类，虽然继承了PropertyResolver，但在实现类里是委托给ConfigurablePropertyResolver处理的。Enviroment代表应用环境，比如测试环境还是生产环境又或者开发环境。
用户可以通过或者@Profile指定某个配置的profile。然后通过activeProfile指定应用环境，从而会enable相应profile的beans
activeProfile可以通过5种方式指定：
1）. servlet config init param，这种方式只适用于spring mvc的dispatcherServlet配置上
2）. servlet context init param
3）. system property
4）. system env。以上四种方式设置的key均为spring.profiles.active
5.） @ActiveProfile，这种方式只适用于junit单元测试

Context通过继承获得了工厂，事件发布，环境定义，资源加载以及国际化的能力。


#### web application context

![web application context构成](https://img-blog.csdn.net/20160301223833002)

常见的WebApplicationContext实现主要有两个，分别是XmlWebApplicationContext和AnnotationConfigWebApplicationContext。他们共同的父类为AbstractRefreshableWebApplicationContext，我们从它出发，看一下context的类结构。

LifeCycle和Closeable代表着容器的整个生命周期，被ConfigurableApplicationContext继承，使继承该接口的context具有生命周期的特征。


用户可以配置Lifecycle类型bean，在context start以及stop时也会相应的启动LifeCycle bean的start或者stop。对于允许autoStartup的SmartLifecycle，context refresh的过程中会自动启动。

ConfigurableApplicationContext继承了生命周期，代表一个可以修改相关属性行为的context，上一节提到的Enviroment和ApplicationListener等等都可以通过它设置。同时整个context的核心refresh方法也是定义在他里面，所以所有的context都继承了它。


实现这个接口的AbstractApplicationContext很自然的也承担了context的绝大部分职能，上一节提过的context的六个方面的功能就几乎都是由它实现，除BeanFactory以外，其它的4个实体都是聚合在这个类里。而DefaultListableBeanFactory–那个最著名的BeanFactory则是由它的子类AbstractRefreshableApplicationContext生成，它自己通过模板方法使用。

AbstractRefreshableApplicationContext定义了loadBeanDefinitions模板方法，由具体的实现提供。它创建bean factory并load配置文件。beans配置文件load过程参考这里

AbstractRefreshableConfigApplicationContext主要用于设置资源的路径。几乎所有的ApplicationContext都会继承这个类，除非不需要读取配置资源。

AbstractRefreshableWebApplicationContext主要针对web的一些特性提供一些context属性行为设置能力，例如覆盖默认的enviroment（默认的由AbstractApplicationContext提供），生成StandardServletEnviroment，又例如覆盖ResourcePatternResolver以及对BeanFactory的后处理等。

ConfigurableWebApplicationContext则是web application context的通用接口，为了支持spring-mvc和spring-web定义了一些set方法，例如设置configLocations（ConfigurableApplicationContext接口并不支持setConfigLocation，因为这个接口是跟着ClassPathXmlApplicationContext一起发布的，而web和mvc是后来加上的功能）和nameSpace等。可以把它看成是专门支持web和mvc的一个接口（web和mvc中生成context是通过class.newInstance的无参构造，所以无法将这些信息作为参数传入，必须显示提供set方法以设置属性）



















参考资料:

[源码理解Spring中的各种context](https://www.jianshu.com/p/2537e2fec546)
[理解Spring容器、BeanFactory和ApplicationContext](https://www.jianshu.com/p/2854d8984dfc)
[spring beans架构](https://blog.csdn.net/szwandcj/article/details/50688616)
[spring context简述](https://www.cnblogs.com/deveypf/p/11406940.html)

























































