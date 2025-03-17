---
title: "Java学习错误记录和总结笔记（2020年）"
subtitle: ""
date: 2022-07-20T21:51:19+08:00
lastmod: 2024-08-17T14:33:11+08:00
draft: false
author: ""
authorLink: ""
description: ""
license: ""
images: []

tags: [Java]
categories: [个人成长]

featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: true
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: false
rssFullText: false

toc:
  enable: true
  auto: false
  keepStatic: false
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
library:
  css:
    # someCSS = "some.css"
    # located in "assets/"
    # Or
    # someCSS = "https://cdn.example.com/some.css"
  js:
    # someJS = "some.js"
    # located in "assets/"
    # Or
    # someJS = "https://cdn.example.com/some.js"
seo:
  images: []
  # ...
---

<!--more-->
[//]: # (添加 <!--more--> 摘要分割符来拆分文章生成摘要. 摘要分隔符之前的内容将用作该文章的摘要.建议填写description属性，这里留空)



## 1-07：java防空指针神器Optional<T> 对象的使用

Optional<T> 对象相当于一个容器，利用泛型，可以将返回单体数据用此对象存储，调用方在调用时拿到这个对象，可以不用再写判空的代码，也可以利用lambda和方法引用等函数式编程完成相关拖沓的校验代码，简洁代码使代码不易出错；

**以后要记得利用这个对象，拒绝空指针；记住，记住，记住！**

可以参考：https://blog.csdn.net/agzhchren/article/details/90515728

## 1-07: java8的集合stream()流操作 API

- stream() |将集合list转化为流

- parallelStream() |   parallelStream() 是并行流方法，能够让数据集执行并行操作

- filter(T -> boolean)  |   过滤出 boolean 为 true 的元素

- collect(Collectors.toList()) |   把流转换为 List 类型集合； 同理，也有可以转换为 set 等类型的

- distinct()  |   去除重复元素，这个方法是通过类的 equals 方法来判断两个元素是否相等的，，因此，若元素为对象类型 则需要 重写 equals和hashcode 方法

- sorted() / sorted((T, T) -> int)  |   对元素进行排序   ，需要实现 Comparable 接口  ，也可以简化为： sorted(Comparator.comparing(Person::getAge))等；comparing（）方法后可接 reversed() 方法，将排序 倒转，reversed 就是倒转的意思

- limit(long n)  |  返回前 n 个元素

- skip(long n)  |  去除前 n 个元素，即跳过（不要）前面 n 个元素

- map(T -> R)  |  将流中的每一个元素 T 映射为 R（类似类型转换）

- flatMap(T -> Stream<R>)  |  将流中的每一个元素 T 映射为一个流，再把每一个流连接成为一个流。使用场景：一个集合里含有很多元素，然后每个元素需要切割成一个个的个体，然后拼成一个集合，如下：

```java
List<String> list = new ArrayList<>();
list.add("aaa bbb ccc");
list.add("ddd eee fff");
list.add("ggg hhh iii");

list = list.stream().map(s -> s.split(" ")).flatMap(Arrays::stream).collect(toList());
```

- anyMatch(T -> boolean)  |  流中是否至少有一个元素匹配给定的 T -> boolean 条件，满足则返回 true，否则 false

- allMatch(T -> boolean)  |  流中是否所有元素都匹配给定的 T -> boolean 条件，满足则返回 true，否则 false

- noneMatch(T -> boolean)  |  流中是否没有元素匹配给定的 T -> boolean 条件，满足则返回 true，否则 false

- findAny()：找到其中一个元素 （使用 stream() 时找到的是第一个元素；使用 parallelStream() 并行时找到的是其中一个元素）

- findFirst()：找到第一个元素。。。。。。。。。。。。。。这两个方法返回的是一个 Optional<T> 对象

- reduce((T, T) -> T) 和 reduce(T, (T, T) -> T)  |  用于组合流中的元素，如求和，求积，求最大值等

```java
//计算年龄总和：
int sum = list.stream().map(Person::getAge).reduce(0, (a, b) -> a + b);
//与之相同:
int sum = list.stream().map(Person::getAge).reduce(0, Integer::sum);
```

其中，reduce 第一个参数 0 代表起始值为 0，lambda (a, b) -> a + b 即将两值相加产生一个新值  
同样地，返回的是 Optional 类型

- count()  |  返回流中元素个数，结果为 long 类型

- collect() |  收集方法，我们很常用的是 collect(toList())，当然还有 collect(toSet()) 等，参数是一个收集器接口

- forEach()  |  用于遍历，，最常用

参考链接： https://www.jianshu.com/p/0bb4daf6c800     |     <https://www.jianshu.com/p/24af4f3ab046>    |    数值流操作 及  常用 集合操作  ： <https://www.jianshu.com/p/e429c517e9cb>

## 4-24：设计模式中观察者模式（可以理解为发布-订阅模式）的使用总结

​	此设计模式分为两个部分：观察者与被观察者；可以理解为 服务发布通知者与服务订阅者。属于典型的一对多类型；服务发布者方相当于一个容器，存放订阅者对象，拥有增加，删除，通知等基本方法；订阅者方需要定义一个方法，用于服务发布者的调用。**体系可以分为4部分：抽象主题方**：定义订阅者对象 增加，删除，通知等基本方法；**具体主题方**：用于实现抽象主题方，然后添加具体的业务逻辑，在需要通知订阅者时进行调用通知方法；**抽象订阅方**：定义被通知时方法；**具体订阅方**：实现抽象订阅方，实现被通知方法，写具体需要实现的业务逻辑；**客户端调用方**：创建主题方对象和订阅方对象，进行订阅操作等，这个过程可以以配置的方式完成

​	待优化的点：可以在通知的方法中定义参数，实现待参通知；基本的发布订阅模式不带路由，所以一旦订阅，便可以收到所有通知，可以在体系中设置路由参数，以达到选择性订阅的功能

​	**java中已经实现了发布订阅模式体系，我们可以直接应用而不需要自己再创造这个体系：java.util.Observable和观察者方 Observer接口**

参考链接： https://blog.csdn.net/weixin_43627118/article/details/105715870

## 4-30：善于使用Java中获取操作系统相关信息的API

​	今天在查看开源项目时学习到 人家在操作 系统文件或者 需要有些东西与操作系统挂钩时 调用Java的相关方法最好。比如你在拼接文件路径时，Windows是“\”，但是linux是“/”。比如获取当前操作系统用户的文件夹，或者当前操作系统的名称，或者创建文件夹时需要进行判断权限授权操作，执行shell脚本时，此时使用Java的api进行操作可以让我们从环境切换等问题脱身出来。因为java帮我们做了这些判断操作；

文件路径的分隔符：File.separator

获取当前操作系统用户的文件夹：System.getProperty("user.dir");

判断字符串是否包含文字符号，用于判断字符串是否为全空格字符串：StringUtils.hasText(String str);

## 5-21：关于http协议的请求与响应中，请求体，响应体只能被读写一次（坑。。。）

​	如题。http协议是安全的信息传输协议，服务端收到请求之后，经过tomcat的封装成HttpServletRequest对象。这个对象封装了http协议中必有的相关信息：包括请求头，请求体等。一般情况下，tomcat传递好request对象，经过spring的处理，我们就可以直接拿到请求体中的数据，这个没有问题。但是如果需要对请求进行预处理，比如说在过滤器中对请求进行过滤，除掉非法请求，此时我们需要获取请求体中的内容，需要用到request对象中的getInputStream()方法，一般流中有reSet()方法,可以重置流中读取的指针的位置，这样在流未关闭之前可以重复读，但是request对象中获取的流是一个子类，**不支持reSet()方法，也就是说只能读取一次**；此时如果我们在过滤器中读取之后，后续spring就无法在进行处理了。为了避免这个问题。可以采用两个方案来解决：1，用于安全校验的参数放在请求头里面；2，若已经放在请求体中了没办法则可以重写一个request类继承tomcat的request对象的类，重写getInputStream()等只能读取一次的方法。对requst对象进行包装。具体实现思路是：构造方法中传入request对象对请求体进行读取，转成字节数组存储起来。然后重写getInputStream()方法，对字节数组读取，转换成一个输入流返回。

## 5-21：在tomcat应用中获取请求方的真实IP

​	某些情况下，我们需要对请求方的ip进行分析。但是我们的应用收到的请求一般是走NGINX反向代理过来的，虽然request里面有获取客户端ip的方法，但是这样获取到的是nginx的ip，不是真实客户端请求的ip，此时需要配置nginx在请求转发时，将真实ip带过来。经过几层nginx代理，就要配置几层

nginx配置：

```java
    location ^~ /your-service/ {
            proxy_set_header        X-Real-IP       $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://localhost:60000/your-service/;
    }
    
//java代码：
    public String getIpAddress(HttpServletRequest request) {
            String Xip = request.getHeader("X-Real-IP");
            String XFor = request.getHeader("X-Forwarded-For");

            if (!Strings.isNullOrEmpty(XFor) && !"unKnown".equalsIgnoreCase(XFor)) {
                //多次反向代理后会有多个ip值，第一个ip才是真实ip
                int index = XFor.indexOf(",");
                if (index != -1) {
                    return XFor.substring(0, index);
                } else {
                    return XFor;
                }
            }
            XFor = Xip;
            if (!Strings.isNullOrEmpty(XFor) && !"unKnown".equalsIgnoreCase(XFor)) {
                return XFor;
            }
            if (Strings.nullToEmpty(XFor).trim().isEmpty() || "unknown".equalsIgnoreCase(XFor)) {
                XFor = request.getHeader("Proxy-Client-IP");
            }
            if (Strings.nullToEmpty(XFor).trim().isEmpty() || "unknown".equalsIgnoreCase(XFor)) {
                XFor = request.getHeader("WL-Proxy-Client-IP");
            }
            if (Strings.nullToEmpty(XFor).trim().isEmpty() || "unknown".equalsIgnoreCase(XFor)) {
                XFor = request.getHeader("HTTP_CLIENT_IP");
            }
            if (Strings.nullToEmpty(XFor).trim().isEmpty() || "unknown".equalsIgnoreCase(XFor)) {
                XFor = request.getHeader("HTTP_X_FORWARDED_FOR");
            }
            if (Strings.nullToEmpty(XFor).trim().isEmpty() || "unknown".equalsIgnoreCase(XFor)) {
                XFor = request.getRemoteAddr();
            }
            return XFor;
        }
```
参考链接：<https://juejin.im/post/5d8479b95188254d426969e7>



## 7-30：实现接口 ApplicationRunner 或 CommandLineRunner 可以在springboot初始化之后，启动成功之前执行代码

​	适用场景：某些业务需要将一些数据缓存进内存才能进行下一步操作。每次应用启动时就需要执行的代码

```java
import org.springframework.boot.*
import org.springframework.stereotype.*

@Component
public class MyBean implements CommandLineRunner {

    public void run(String... args) {
        // Do something...
    }

}
```

如果定义了必须在特定 order 中调用的多个`CommandLineRunner`或`ApplicationRunner` beans，您还可以实现`org.springframework.core.Ordered`接口或使用`org.springframework.core.annotation.Order` annotation。

可以使用@order注解 标注其执行顺序

## 9-27：maven的\<dependencyManagement\>标签“依赖管理”，用法原理

父项目用此标签包起来依赖，不会自动被子项目继承，需要子项目写出依赖来继承，此时可以不用写版本号。若父项目没用此标签包起来的依赖则可以被子项目自动继承，此时不需要显式的写出来。

## 9-28：spring-boot-starter-actuator WEB应用的运行健康状况监控框架。及一些访问接口

#### 1. 应用配置类：获取应用程序中的 应用配置、环境变量及自动化配置报告等一系列与springboot应用密切相关的配置报告

- /autoconfig    获取应用的自动化配置报告
- /beans      获取应用上下文创建的所有bean
- /configprops     获取应用中配置的属性信息报告
- /env    获取应用中所有可用的环境属性报告
- /mappings    获取应用中所有springMVC控制器映射mapping
- /info     获取应用中的自定义信息

#### 2. 度量指标类：获取应用程序在运行过程用于监控的度量指标，比如 内存信息、线程池信息、http请求统计数据等

- /metrics     获取应用各项重要度量指标，如内存信息、线程信息、垃圾回收信息等
- /health       获取应用各类健康指标信息
- /dump     用于暴露程序运行中的线程信息
- /trace      用于返回基本的http追踪信息

#### 3. 操作控制类：提供了对应用远程关闭等操作类的功能

- /shutdown   原生只提供这一个接口，用于远程关闭应用。默认此功能是关闭状态，配置 **endpoints.shutdown.enable = true** 来开启此功能（结合springcloud会用到更多的操作类接口）

注意：此框架默认是禁止访问这些接口的。需要配置   **management.security.enabled=false** 将安全开关关闭才能访问到

## 10-15：spring项目中 用自定义注解申明bean注入

在学习springcloud的Ribbon客户端负载均衡器时，从源码分析阶段发现： 在@Bean注解标注的配置方法下的restTemplate如果标注@LoadBalance注解会被ribbon注入负载均衡拦截器，而没有标注的则不会注入负载均衡。发现原因是，标注的在@AutoWired注解的字段下，标注@LoadBalance注解的会被注入然后注入拦截器，其他的则被过滤掉了。因此我怀疑**spring这里有一个隐藏操作：在@AutoWired注解的字段下标注自定义注解时，会只注入标注同样自定义注解的bean对象。**经过我的验证，结果得到了证实。

## 10-21：微服务架构spring-cloud体系各组件总结

springcloud是spring团队基于微服务架构思想，对一系列第三方构建微服务组件的封装整合，使其更加方便的与spring框架集成。它是基于springboot框架开发，一系列微服务组件的统称。

- #### <u>**springboot**</u>

  springboot框架是一个基于spring框架，对开源世界各种开源工具进行分类整合，实现预先的自动化配置，使开发者不需要进行重复的大量的xml配置就能快速搭建项目并启动。内部实现了自动配置 ，提供了嵌入式web容器和maven打jar包插件，可以使web项目以jar包的形势运行。

  **为什么用它**：基于springboot开发的项目可以减少大量配置，并且内部提供了对相关依赖版本的统一管理，使开发者可以减少在配置和依赖版本冲突上的时间

  **自动配置或者说不需要配置的原理是什么？** spring的后面版本提供了注解配置。springboot基于spring的注解配置进一步开发了应用启动环节加载预先注解配置的功能。当在启动类上标注@SpringbootApplication注解时，其内部封装了@EnableAutoConfiguration注解（用于打开springboot自动配置开关）、@ComponentScan注解（用于扫描配置的包下面的spring相关的注解）、@Configuration注解（用于标注当前类为配置类，可将其输出加载到spring的ioc容器中）。也就是说，默认打开了自动配置的开关，之后springboot启动上下文会在加载依赖包时读取 META-INF 目录下的factories配置文件，这里面标注了所有提供自动化配置的配置类，然后加载这些类根据@Conditional相关注解进行判断，初始化相应的默认配置，在加载完默认配置之后，springboot的pom文件中声明了名为：application的yml或properties配置文件，将其加载对默认配置进行覆盖。

- #### <u>**springcloud-eureka**</u>

  springcloud-eureka是spring 基于springboot，结合其自动化配置，对服务的注册与发现eureka框架的封装整合的框架，主要用于微服务架构体系中的服务的注册与发现。分为client客户端和server服务端。客户端的功能包括 对服务的发现与注册，心跳机制实现服务续约。服务端的功能主要包括对服务进行注册，和对服务信息列表进行维护和与其他注册中心服务同步

  **为什么需要注册中心？**因为在微服务架构中，大项目被划分为多个小项目，小项目之间难免会存在数据交互，并且由于各个小项目之间的业务复杂度不同，可能存在某些服务需要集群部署的情况，此时在进行数据交互，服务调用时由于需要指定ip和端口号，但是此时服务过多，配置混杂，并且可能存在某些服务不可用的情况，所以需要一个可以来管理这些服务配置的东西，即 注册中心

  **eureka有哪些功能，怎么使用？** euraka分为服务端和客户端；客户端有：服务发现，向注册中心注册服务，心跳机制续签，请求下线等功能。使用方法：引入spring-cloud-starter-netflix-eureka-client包，在启动类上标注@EnableEurekaClient注解，配置文件中注明端口号、服务名、注册中心地址等等配置，启动项目即可。服务端的功能有：服务注册、服务传播、服务下线、心跳监控、失效服务剔除等功能。使用方法：引入spring-cloud-starter-netflix-eureka-server包，在启动类上标注@EnableEurekaServer注解，配置文件中注明端口号、服务名、其他注册中心地址等等配置，启动项目即可。

- #### <u>**springcloud-ribbon**</u>

  springcloud-ribbon是一个客户端负载均衡工具类框架，他实现了与springcloud-eureka的客户端交互获取调用服务清单及清单维护等功能，和eureka无缝集成，使开发者不需要关心服务调用列表等信息即可实现服务调用的负载均衡。

  **为什么要用到ribbon？**微服务中各个服务会根据并发等情况做集群部署，以达到高可用的目的，但是为避免出现某些服务访问过大，而另一些服务却无人访问的情况，必须要做到负载均衡。ribbon就是一个客户端的负载均衡器。

  **ribbion有哪些功能？**主要实现各个服务相互交互时的负载均衡

  **ribbon的实现原理是什么？**ribbon是基于RestTemplate实现的负载均衡，在请求url中主机写要调用的服务名，就能实现负载均衡，其实现过程是这样的：首先需要注入ioc容器restTemplate对象，并在注入方法上标注@LoadBalanced注解，之后在项目启动之后会触发ribbon的自动配置，该自动配置的前提条件是，上下文中存在restTemplate对象并且环境中存在LoadBalancerClient类，此配置会注入所有标记了@LoadBalanced注解的restTemplate对象，并为他们注入一个拦截器，此拦截器会在发送请求之前执行。之后应用启动之后，eureka客户端在获取了注册中心中的可用服务清单之后，会被ribbon拿取一份，在发送请求之前，进入拦截器逻辑，此时会获取到服务清单，根据url中的服务名，筛选出对应的zone下的相关服务的ip清单，在经过过滤器过滤之后（如果配置了过滤名单的话），根据默认配置的负载均衡策略（ribbon中实现的策略主要有：线性轮训，随机，加权随机）选择出具体的协议、ip、端口号拼装成真正的url发起调用。

  **ribbon怎么使用** 使用很简单：引入spring-cloud-starter-netflix-ribbon包，在有eureka的前提下，配置restTemplate对象注入ioc容器，在配置方法上标注@LoadBalanced注解。在具体调用其他服务时，调用的url中的ip和端口号用目标服务名替换即可。

- #### <u>**springcloud-hystrix**</u>

  springcloud-hystrix是实现了微服务调用中应对服务调用异常情况时进行服务熔断，服务降级等策略的框架，其主要功能有服务熔断，服务降级，请求缓存，请求合并，服务监控等功能。

  **为什么要用springcloud-hystrix？**在微服务架构中，大项目拆分成一个个独立的小项目，各自运行在独立的进程内，但是各个微服务之间难免存在数据交互，在交互时由于采用网络调用，难免会遇到调用延时、网络错误、服务宕机或是其他等等异常情况，此时调用会产生长时间的等待导致自身服务的请求被积压，而自身服务也可能被其他服务所依赖，从而导致这一异常现象传递到其他服务上，导致整个微服务体系崩溃，所以需要hystrix提供服务熔断，服务降级等措施，在根源处解决问题。

  **hystrix各个功能组件的实现原理？** 1、服务熔断：在打开断路器开关时，根据默认的配置：执行命令超过2000毫秒时，就会触发熔断，直接返回错误信息。主要原理是被@HystrixCommand注解标注的方法会被解析为“命令”，hystrix会根据注解中的配置，为每个组创建一个线程池，并通过线程异步调用服务，因此可以设置任务执行超时时间并进行熔断。如果熔断策略采用信号量实现的话，将不能设置超时时间和异步执行，但会有其他熔断策略，由于不用线程池实现，因此资源占用很小。

- #### <u>**springcloud-feign**</u>

  springcloud-feign是springcloud微服务架构中用于声明式服务调用的组件，它是基于ribbon和hystrix封装的，内部依赖于这两个组件，因此自动继承了客户端负载均衡和服务熔断及服务降级等功能，由于在更高应用层次做了封装，所以可能没办法更加灵活的使用hystrix的其他特性如：请求缓存、请求合并以及熔断策略的选择配置。

  **为什么要使用springcloud-feign？** 因为对于内部服务之间的调用，基本上都会使用ribbon负载均衡功能和hystrix的熔断功能，使用feign可以简化对这两个组件的开发，减少重复性的工作，并且可以将服务调用相关的接口独立整合出来，方便管理。

  **springcloud-feign怎么用？**  由于feign内部依赖ribbon和hystrix的包，所以先引入open-feign的依赖包，不需要引入另外两个starter包了，以免起冲突，然后在启动类上添加@EnableFeignClients注解 开启自动配置，之后可以在配置文件中添加feign的个性化配置，比如ribbon的连接或请求的超时时间，hystrix的熔断超时时间（熔断时间一定要比请求超时时间要长，不然没有意义），以及重试机制的次数等等，配置好之后就可以添加相关具体代码。可以申明需要调用的服务的restful接口，配好请求相关的注解，参数注解的值一定不能漏写，写好之后在接口名上标注@FeignClient注解。若需要使用服务降级，则可以实现该接口，并在@FeignClient注解的fallback属性标注好实现类，则实现类的方法就是对应接口方法的服务降级的方法。

- #### **<u>springcloud-zuul</u>**

  在前后端接口交互的过程中必然会有正常或非法的请求，权限校验或者数据加密解密等等需求，这些功能一般与具体服务的业务关系不大，但是在每一个服务里面做代码又过于冗余，所以抽象到网关里来做会比较好，springcloud-zuul就是一个负责服务路由的网关，并且支持客户端负载均衡，若结合注册中心，则不需要手工维护路由服务的地址，结合config配置中心，还可以实现动态刷新路由。

  **springcloud-zuul 的实现原理？** springcloud-zuul网关的核心就在于ZuulFilter 接口，这个接口定义了4个方法，1：过滤器的类型，2：过滤器的执行排名（越小则越先执行），3：shouldFilter方法，返回boolean类型，意为是否应该执行；4：run方法，过滤器的主体逻辑方法。其中过滤器的类型有pre、routing、post、error4种类型。pre类型表示此过滤器是路由前的过滤器，会在请求服务之前执行；routing表示此过滤器会在请求服务时调用；post类型表示会在请求服务并返回之后调用，此时在过滤器中就不仅可以获取到请求信息，还可以拿到响应数据，若需要对响应数据做处理的话，就在此阶段做；error类型的过滤器会在前面阶段的过滤器抛出异常时执行。另外就是过滤器的执行排名，此方法会返回一个int类型的值，在相同类型的过滤器中，排名越小的越先执行。shouldFilter方法可以动态的根据请求内容做处理并判断此过滤器是否需要被执行。在zuul的包里，有预装已经实现的一系列过滤器，主要是对请求的一些预处理或者后处理，还有对过滤器抛出异常之后的拦截处理等。

- #### **<u>springcloud-config</u>**

## 11-06：Springboot-amqp对接rabbitMQ时配置Binding时注意一个坑

​	在配置binding时，需要绑定队列queue到到交换机exchange（顺便指定routingkey）；习惯性操作：在配置类中用@Bean注解标注方法，然后直接 new一个队列，再new一个交换机，然后就直接用BindingBuider开始绑定了。 之后启动测试就会发现 报404：rabbitmq服务器找不到对应的 交换机 和队列。

原因：在客户端 申明的 队列和交换机，都应该有一个 在服务器中创建的操作。但是在spring的封装下，将这一步省略了，所以在自己本地声明的队列和交换机。直接进行绑定肯定是会报错的。需要在申明之后，将对象通过@Bean 注入到IOC容器中。然后springboot-amqp的自动配置中会注入申明的队列，并将之提交到服务器创建，然后才进行绑定。

## 11-13：开发中常用linux的shell指令

- cd 用于目录间的跳转
- ls/ll  ls用于列出目录下元素的清单；ll用于以列表的形势列出目录下元素的详细信息的清单
- cp  用于复制  cp  ./source.txt[文件] ./target.txt[文件]    若要复制一整个目录  则加 -R/r 如   cp -r  ./source[目录]  /usr/target[目录]
- mv  用于移动文件或目录   若移动前后的父目录不变则相当于重命名
- rm  删除文件或目录    -r  递归删除   -f  取消确认询问
- mkdir 创建目录
- ps   用于查看进程的状态  -e/-a 显示所有程序   -f  显示程序的进程号uid
- grep 用于搜索文本 并把匹配的行打印出来
- find 用于查找指定目录下的文件   find /home  -name "*.txt" (查找/home目录下 以名称为匹配对象 匹配*.txt的所有文件)
- kill 杀死指定的进程
- cat 查看指定的文件
- tail  常用于浏览日志  tail -f a.log（实时监控文本数据记录）
- vim 编辑文本
- top 用于监控cpu使用情况
- free 用于监控内存使用情况
- df 用于查看磁盘使用情况单位kb
- du 也是查看使用空间的，但是与[df](http://man.linuxde.net/df)命令不同的是Linux du命令是对文件和目录磁盘使用的空间的查看，还是和df命令有一些区别的。
- chmod  用于权限分配

## 11-14：数据库优化总结

数据库优化在层次上可以分为硬件优化和软件优化；硬件上：将数据库单独部署在一台机器上增加数据库的资源调度能力；提高cpu和内存和硬盘的硬件水平也可以增加数据库的性能。软件上的优化又可以分为数据库表结构的优化和sql语句的优化：表结构优化层次上，根据具体的业务需求合理的设计表字段，在设计层次上减小sql查询的复杂度（比如一个要求存储树形结构数据的表，在需求上要求查询某节点数据的根节点数据详情，此时可以在设计表的时候将附带增加一个字段用于存储根ID，这样在查询时就可以不用每次都递归查找了，增加了查询的效率）；在查询作为条件需求多的字段上添加索引；如果表的数据量特别大时，根据数据特点建立分区表等等；在sql层次的优化上尽量简化sql语句，不要写太过复杂的sql语句；在查询上要避免慢查询，要提高查询条件命中索引率，避免全盘扫描，比如1、尽量不要用is null 、!=等条件。2、在预期查询结果返回1条时，尽量加上rownum=1或者limit 1。3、在模糊匹配时尽量不要使用全模糊匹配，这将不会使用索引。4、尽量不要在查询条件里面拼接or，应该换成union all。5、查询条件的左边字段上不要用表达式，不然就会导致放弃走索引。6、不要join on 太多表，会降低效率。7、不要使用select * ，需要什么字段就写什么字段。

## 11-16：普惠一站项目总结

普惠一站是一个用于帮助银行理财经理获取客户的app，功能模块上分为新闻资讯、产品管理、朋友管理、语音通话、视频直播、积分商城和营销统计等模块，后台服务上分为 app端、中台端、小程序端、定时任务端、南京银行定制版等5个java后台服务。整体是基于springboot开发，用redis和阿里oss对象存储对早高峰流量削峰，oracle作为数据存储。内部一些功能模块的实现上对接了一些第三方云平台，比如新闻资讯模块需要采集新闻标签，这个对接了百度云的自然语言处理api。语音通话对接了百度云的语音双呼和科大讯飞的语音转写；视频直播对接了腾讯云的云直播服务等等。本人的职责主要是负责这5个java后台的功能迭代还有日常维护，包括但不限于功能代码编写与迭代，第三方平台服务对接，项目bug修改，运营数据收集以及制作报表，生产上线运维。

普惠一站运营数据：

普惠一站刚上线一年多，目前处于用户积累期，现在用户量在2万左右，日均活跃用户在1万左右，新闻的日均访问量在5万左右，客户累计访问量在1千万以上，tps最高在50左右

项目中曾经遇到了哪些问题？

曾经遇到过一个线上java定时项目服务中线程池的所有进程阻塞问题。

第一个问题最终排查的原因是：linux操作系统中通过一个进程调用另一个子进程时，子进程的输出流会输出到父进程里面，而如果子进程存在错误流时，默认则不会输出到父进程里面，而是会写入到缓冲区里，需要手动将缓冲区的数据进行读取清空，如果缓冲区被占满，则子进程将会被阻塞，因此python进程被阻塞之后，导致调用python的java进程也被阻塞了，直到整个线程池的线程都被阻塞完。解决方法其实jdk也提供了：用java调用第三方进程时，可以将子进程的输出流和错误流合并，一同输出到父进程，这样就不会发生死锁问题。



第三方接口对接流程：

语音双呼：用户在界面上发起呼叫请求，后台收到请求之后将发起者的手机号码与被呼叫者的手机号码作为参数调用百度语音服务接口，调用成功之后，运营商会给发起呼叫方打电话，发起方接听电话之后运营方会呼叫被呼叫方的手机，被呼叫者接听之后双方就可以通话了。

## 11-21：面试总结

这次几家面试下来总结发现：数据框架不在乎学习多少，技术栈该有的都有就行，最重要的问的最多的是 多线程，数据库相关；

所以接下来我的研究方向：java并发编程、mysql从入门到精通，这两本书看完。必须深入学习下这两个方面。

## 11-22：mysql数据库设计及优化总结

mysql数据库的表设计需要遵循数据库设计的三大范式：第一大范式：表中的字段要保证最小粒度，不可拆分。第二大范式：在第一大范式的基础上，非主键字段要对主键字段完整依赖，而不能部分依赖（就是说不要建联合主键）。第三大范式：在第二大范式的基础上，表中的非主键字段只能依赖于主键字段而不能依赖其他非主键字段。在遵循这三大范式的基础之上可以降低数据库的字段的冗余性，但是在某些具体的业务需求之下为了提升性能也是可以允许冗余的。

在创建字段的类型时，要选择合适的格式，打个比方：建立主键能用数字类型就不要用字符串类型，因为数字类型在查找匹配时只需要计算一次，而字符串类型则在匹配时会根据字符串的长度有多长，就计算多少次，在大数据量的时候，这个性能差距就比较大了。另外，在建立字符串类型时，尽量选择可变长度varchar类型，可以减少空间的浪费。

在数据量达到一定规模时，表结构上的优化可以对mysql数据库进行分库分表优化，分库分表可以分为垂直切分和水平切分；一般看情况处理，如果是数据库中表非常多，并且各个业务模块的表都比较独立，那最好采用垂直切分，将数据量大的模块的表分离出来，建到不同的数据库中，达到分库的目的。如果是某些表里面的数据量特别大的话，可以采用水平切分，将一些特征相同的数据分离到多个结构一样的表中去，使一个大表变成多个小表，达到分表的目的。

在sql上的优化，原则就是要避免全盘扫描，要避免表级别的锁，要触发索引，常见的一些优化有：1、当预期查询的结果只有一条时，可以加上limit 1.这样可以让数据库引擎在找到这条数据之后立马返回而不会全表扫描。2、在选择数据库引擎时根据读写的的需求，可以选择合适的数据库引擎，InnoDB支持行锁，支持事务，适合有读写的需求。MyISAM只支持表锁，并且不支持事务，所以在写的需求大的时候效率会比较低，但是它的查询性能比较高，适合只有读的需求（myisam之所以查询速度比innodb快，是因为myisam在缓存上只缓存索引块，并且数据记录的是具体文件的磁盘位置，而innodb则即缓存了数据块，又缓存了索引快，在查找时先找到数据块，再找到对应的行。）3、尽量不要使用not in 而用not Exists ，后者用到了连接，可以出发索引，效率较高。4、不要使用 or、is null、is not null、<> 等条件，这些条件不会触发索引，可以使用union等方式替换，空值不会触发索引，可以设置默认值代替。5、不要使用全模糊匹配，条件字段不要使用函数表达式。

## 11-22：mysql中常用的函数和语法



## 12-22：spring的aop代理类手动触发代理调用

我们知道，spring的aop功能将需要被代理的类创建了代理类，当调用方法时走的是代理类的增强方法，一个典型的场景就是事务控制。一般当A类调用B类的方法时才会触发代理类的方法，因为这样调用时走的就是代理对象的方法。当A类的方法调用A类自己的另一个方法时，由于是内部调用，不会走代理方法。此时需要用AopContext.currentProxy();获取当当前的代理类调用此方法才能走代理方法。这样调用的方式实现了在类的内部调用方法也能触发代理的功能。这样就不必新写一个类来触发代理了。这个还是挺常用的



#### 1，消息发送接收端设置语言偏好不生效的问题

问题复现：送货单提交之后发送站内消息。消息参数都有设置多语言，消息模板都有配置多语言版本。但是我在接收人账号将语言偏好设置为英语之后，接到的消息仍然是中文的。

问题排查：检查了消息发送的代码，没有发现哪里与规范约定的不一样，只能将矛头指向消息服务。在申请到消息服务源代码之后，发现消息发送时获取接收人语言偏好的逻辑是：查询redis的用户缓存，并没有直接查询数据库的用户表。因此在前台修改语言偏好到刷新缓存会存在一个时间差。另外我使用的账号是一个多人共享的账号，同一时间我切换语言时，可能别人也会切换语言，就会造成影响。

问题推断：由于消息服务查询接收人语言偏好是通过缓存的方式，生效存在延时问题，在还未生效这期间，由于其他人也在使用这个测试账号，导致其他人登陆后修改了语言偏好，但是我前端页面没有刷新，所以还是英文界面，就造成了后端数据库里仍然是中文，但是我页面是英文的现象，然后等他缓存刷新时，刷进去的当然就还是中文，所以发送过来的消息仍然是中文格式。

问题验证：使用了一个独享账号，切成英文，等待缓存刷新后，创建送货单并提交，发送消息，收到的消息是英文格式。验证推断正确。

#### 2，开发的需求中某些点有疑问一定要找业务确认，不能偷懒，不能跟着自己的感觉走，不然肯定就是一个生产问题过来

#### 3，适配器引用了未上线的组件，结果需求提前发版，导致运行报错的问题

适配器切环境同步过去之后先试运行，没问题之后再启用。可以先在开发环境造一份测试数据保存。

#### 4，适配器 注意要点

​	1，要操作的表（不论是查询还是修改），都要求必须是适配器定义所在的服务里面有对应表的mapper。
​	2，适配器的入参和出参，都必须是“对象”，不能是集合类型的参数。
​	3，适配器里面做 updateOptional操作时，传入的对象必须有“租户id”，“主键id”，“版本号”3个参数。
​	4，后面的修改参数对应为java 的可变数组（此功能11月27上线）
​

#### 5，工作流经验总结：

​	1，spuc服务启动工作流会调用工作流服务，工作流服务启动一个流程事例，并在同一线程里执行审批规则获取审批人的任务，当获取审批人任务执行完成，工作流服务才会返回spuc服务响应信息，之后spuc服务执行剩下的逻辑，并提交事务。
​	2，给某个节点设置一个监听器，若类型为“创建”，则当前节点设置审批人时就会执行，若有多个审批人，则执行多次；若类型为：“完成”，则当前节点审批人执行审批之后就会执行，若有多个审批人，则执行多次；若类型为“离开”，则离开当前节点时会执行，只会执行一次，不管审批通过还是拒绝都会执行。


