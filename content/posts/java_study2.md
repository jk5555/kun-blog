---
title: "Java学习错误记录和总结笔记（2019年）"
subtitle: ""
date: 2022-07-17T14:53:14+08:00
lastmod: 2023-05-12T12:41:14+08:00
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


## 3-4: oracle数据库sql递归语法

```sql
SELECT * FROM SYS_AREABASE
START WITH areacode='433127'
CONNECT BY PRIOR areacode=PARENTcode
```

### 树结构的描述:

​        树结构的数据存放在表中，数据之间的层次关系即父子关系，通过表中的列与列间的关系来描述，如EMP表中的EMPNO和MGR。EMPNO表示该雇员的编号，MGR表示领导该雇员的人的编号，即子节点的MGR值等于父节点的EMPNO值。在表的每一行中都有一个表示父节点的MGR（除根节点外），通过每个节点的父节点，就可以确定整个树结构。

其中：CONNECT BY子句说明每行数据将是按层次顺序检索，并规定将表中的数据连入树型结构的关系中。PRIOR运算符必须放置在连接关系的两列中某一个的前面。对于节点间的父子关系，**PRIOR运算符在一侧表示父节点，在另一侧表示子节点，从而确定查找树结构是的顺序是自顶向下还是自底向上**。

​         在连接关系中，除了可以使用列名外，还允许使用列表达式。START WITH 子句为可选项，用来标识哪个节点作为查找树型结构的根节点。若该子句被省略，则表示所有满足查询条件的行作为根节点。
​         START WITH：不但可以指定一个根节点，还可以指定多个根节点。

参考链接:https://www.cnblogs.com/zhjx0521/p/7338876.html

## 4-1: 今日学习mybatis总结

1. 公司的分页插件触发条件是:dao层方法传入inDTO对象,若不想分页的话则不要传入inDTO对象,直接传入Map对象即可!
2. mybatis能够根据 sql标签中的databaseId属性区分不同数据库厂商的sql语句, 若相同的sql语句有多条, 则只会加载带有databaseId属性的sql,,另外需要在著配置文件里面声明DatabaseIdProvider标签
3. mybatis给参数赋值有两个方式: #{} 是预编译方式填值,参数需要经过转义; ${}是 字符串拼接方式,参数不会经过转义!  有这个区别之后,正常安全 赋值可以继续使用 #{} ;   在有一些特别的需求时  要灵活利用${} 拼接字符串的方式会节省时间和避免冗余的代码!
4. *where* 元素标签只会在至少有一个子元素标签的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，*where* 元素标签也会将它们去除。

## 4-11: maven工程引入本地jar包,并需要将之打进war包中的做法及 注意的问题;

1. 如果使用<scope>system</scope><systemPath>****//***//**</systemPath>这个作用域可以实现在 编译期从本地jar包引入,但是在打成war包时不会 将之打入进去!

2. 如果引用了本地jar包并且需要打进war包时,需要在第 1 条的基础上 再在 项目的依赖方的pom文件中配置插件:

   设置maven-war-plugin打包插件，让maven在导出war包时把设置好的路径下的文件打进war包

   ```xml
   			<plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-war-plugin</artifactId>
                   <configuration>
                       <webResources>
                           <resource>
                               <directory>${project.basedir}/libs</directory>
                               <targetPath>WEB-INF/libs</targetPath>
                               <filtering>true</filtering>
                               <includes>
                                   <include>**/*.jar</include>
                               </includes>
                           </resource>
                       </webResources>
                   </configuration>
               </plugin>
   ```

   设置好后再执行mvn clean package命令就会将指定路径下的文件打进war包



## 5-13: 好用的集合工具类: org.apache.commons.collections.CollectionUtils

取两个集合的并集(相当于去重的功能)

```java
List list = CollectionUtils.union(listA, listB);//取两个集合的并集(去重)
    
List list = CollectionUtils.intersection(listA, listB);//取两个集合的交集(得到重复的数据)
    
List list = CollectionUtils.disjunction(listA, listB);//取交集的补集(得到不重复的那部分数据)
    
boolean flag = CollectionUtils.isEqualCollection(boy1,boy2);//判断两集合是否相等
```

除了以上的这些好用的方法之外,还有很多其他好用的方法, 有时间探索一下!!!



## 9-20：oracle数据库随机排序函数：ORDER BY **<u>DBMS_RANDOM</u>**.VALUE

可以将结果集排序打乱，，，当需求为随机获取几条数据时  就可以派上用场；

原理：Order By dbms_random.value ，为结果集的每一行计算一个随机数，dbms_random.value 是结果集的一个列（虽然这个列并不在select list 中），然后根据该列排序，得到的顺序自然就是随机的啦。

**dbms_random是一个可以生成随机数值或者字符串的程序包。这个包有initialize()、seed()、terminate()、value()、normal()、random()、string()等几个函数，但value()是最常用的。**
**value()的用法一般有两种，**
**第一种 function value return number;**
**这种用法没有参数，会返回一个具有38位精度的数值，范围从0.0到1.0，但不包括1.0，如下:**
**select dbms_random.value() from dual  结果为：0.265729284748573**

**第二种value带有两个参数，第一个指下限，第二个指上限，将会生成下限到上限之间的数字，但不包含上限，如下:**
**select dbms_random.value(1,7) from dual  结果为：3.38380283953849**

**两者合起来用 trunc(dbms_random.value(1,7))   也就是随机取得整数X , 1<=X<7,   如下：**
**select trunc(dbms_random.value(1, 7)) from dual  结果为：3**

可以参考：<https://www.cnblogs.com/ivictor/p/4476031.html>

## 9-23：oracle的集合运算

- **INTERSECT** (交集)，返回两个查询共有的记录
- **UNION ALL**（并集），返回各个查询的所有记录，包括重复记录
- **UNION**（并集去重），返回各个查询的所有记录，自动去重，并按照自己的规则排序（也就是说，取并集之前作排序的话会在此之后顺序被打乱）
- **MINUS**（补集），返回第一个查询检索出的记录减去第二个查询检索出的记录之后剩余的记录。



## 9-26：java语法：在重写父类方法时，可以在 修饰词 前面加上 synchronized关键字使之变为同步方法；



## 9-30：oracle问题：分页查询会在不同页面查到重复的数据

​	今天遇到一个奇怪的问题：对一个列表做分页查询，对几个字段加了排序，，然后发现有几页数据有重复现象；但是不分页全部查出来却没有重复数据；我估计问题出在了分页上面

然后百度了一下，学到了：原来oracle在数据的顺序上，如果不排序，则默认按照数据库中数据的存储顺序进行排序，也就是默认顺序；当对某个字段进行排序的时候，若此字段有很多重复数据时，这段重复数据的部分的记录的顺序就无法保证了，所以就有可能发生在前一页查到了几条，到了下一页又会查到。 出现这种情况的原因是因为排序列值的不唯一性。 Oracle这里使用的排序算法不具有稳定性，
也就是说，对于键值相等的数据，这种算法完成排序后，不保证这些键值相等的数据保持排序前的顺序。

解决的方法是在后边增加一个唯一性列，比如主键。
所以解决方法如下（两个条件必须同时满足）：
1.sql语句中需要有排序条件。
2.排序条件如果没有唯一性，那么必须在后边跟上一个唯一性的条件，比如主键。

可以参考：<https://blog.csdn.net/u010395242/article/details/52047341>

## 10-8：web项目，当前台发起一个耗时的请求之后，后台线程在处理中，如何在前台在再次发送一个请求安全的中断这次后台正在处理的任务？

实现方式涉及到线程的中断操作；不能使用Thread.stop()这个方法，因为它存在安全问题，并且已经被java平台废弃，此次实现方式 涉及到的方法为： getId（）；interrupt（）；isInterrupted（）；

其中： getId（）；通过对象调用，获取此线程对象的唯一标识id

interrupt（）；通过对象调用，将此线程对象所对应的线程 标记为中断状态

isInterrupted（）；通过对象调用，返回此线程对象所对应的线程的中断状态，并且不会重置这个状态值；这个与interrupted（0 方法相反， 他是会将中断状态重置为false

**实现思路：**前台发起第一次请求之后，后台处理数据，并开启一个新的线程来处理此次耗时的操作；并通过此线程对象调用  getId（）方法获取此线程的id 返回给前台，同时将id作为key，对象作为value存入全局缓存；前台标注正在处理中，并且显示一个取消的按钮，当点击这个按钮时，就将id传递回给后台，后台通过id获取到 处理任务的线程对象，调用interrupt（）方法将状态 标记为中断状态，然后返回； 再来说说 线程任务里的逻辑处理：在循环里面增加判断isInterrupted（）当返回true就 跳出循环，结束方法，或者提交，或者回滚； 或者将代码try。。。catch ，，当返回true 就抛出异常，在 catch 这个异常，然后做相应处理； 或者其他处理方式；总之利用isInterrupted（）方法进行判断

## 10-10：ajax请求二进制文件流时发生跨域问题的解决

这种情况下，客户端与服务器会先建立一次预连接，这次预连接会经过服务器端的过滤器，一般会在过滤器里面设置好响应头处理跨域；所以预连接会成功返回，然后进行正式连接，正式连接应该是没有经过过滤器（具体没有深究），所以在response对象写返回的二进制流时，前台会报跨域错误，原因就是 正式连接的响应头里面没有加上 跨域头子，，，所以解决办法就是手动在方法返回里加上 响应跨域头子

## 11-19：springTask任务默认单线程执行，百度sdk工具包调用接口发生错误可能会中止线程

- springTask的定时任务组件使用非常简便，只需要在启动类上添加@EnableScheduling注解，然后再在需要定时调用的方法上加@Scheduled(cron = "* * * * * ?")注解，即可；spring底层使用的是线程池，但是默认只加入了一个线程，可以保证线程安全，但是可以配置线程池中线程的数量，让任务多线程执行；
- 百度开放平台会提供一些功能接口，可以使用百度的sdk工具包调用，但是若使用不当可能会导致sdk工具包杀死当前线程；（这可能也是一些开放平台的自我保护机制）

## 12-17：Java程序调用第三方程序所需要注意的坑：

java调用第三方工具可以使用ProcessBuilder类构建命令，然后调用start()方法，开启进程，并返回一个进程对象Process；通过这个进程对象，可以控制子进程的生命周期（这一点和Thread线程类似，以对象的方式来表示一个抽象的东西）；

process对象可以使子进程立即终止，也可以使父进程阻塞等待子进程执行完成；**但是这里边有一个特别大的坑**：

​	子进程在执行时，根据你执行的程序的不同，可能会有信息流的输入或输出，若通过操作系统直接调用的话，进程可以从操作系统那里获取到控制台对象，则信息流可以直接定向到控制台；**若通过父进程调用时，是不能够获取到控制台的，信息流会重定向到父进程；并且java会提供一个有限的缓冲区，供信息流输入或输出，但是，缓冲区太小，一旦填满则子进程就会阻塞住，等待缓冲区被释放；因此父进程必须获取到信息流及时读取清空缓冲区，否则的话会导致子进程阻塞甚至死锁**。

​	因此，父进程必须读取子进程的输出流，保证子进程不被阻塞；但是子进程的输出流有2种：errorstream和outputstream；对应的Process的getErrorStream()和getInputStream()；这两个流在子进程中输出顺序具有随机性，但是一旦一个阻塞，进程就会阻塞住；传统的解决方法就是开启新的线程，使父进程异步读取子进程的两个输出流；推荐使用ProcessBuilder的redirectErrorStream(true)方法将错误流与输出流合并，这样父进程只需要调用getInputStream()方法获取到合并流并读取完即可；

**正确的用法：**

**1，ProcessBuilder类构建命令；**

**2，调用redirectErrorStream(true)方法将错误流与输出流合并；**

**3，调用start()方法启动子进程返回Process进程对象；**

**4，Process调用getInputStream()方法获取合并流；**

**5，循环读取信息，直到读取完毕；**

**6，Process调用waitFor()方法等待子进程执行完成（或者设置一个等待时间，超时父进程将跳过等待，由于子进程输出完成，则不会发生阻塞情况，待验证）；**

**7，调用过程完毕**

可以参考：<https://blog.csdn.net/zzldn/article/details/83907025>

## 12-18：spring切入点表达式 还可以支持 “与，或，非”  等 基本运算符

比如我想拦截某个包路径下面的，以及路径子包下面的所有类和所有方法，，但是需要排除某些 类和方法，这种 表达式就可以写成如下：

```java
!execution(* com.yihuisoft.productbiz.service.puhui.FormCreateHandler.*(..)) && execution(* com.yihuisoft..service..*.*(..))
```

## 12-26：记一次生产上事务失效问题排查

spring的声明式事务或者注解式事务一般情况下不生效的原因是：1，方法内部调用同一个类中的方法；2，方法内进行了异常捕获未抛出；3，启动类上没有加上开启事务的注解，等等

但是这次出现失效的情况不在上面任何一个情况之中，找问题找了好几天才找到。是**多数据源导致的**

一般配置多数据源需要扫描不同的路径下面的xml文件（sql语句）才能是事务生效，这样每一个sql语句都会被一个数据源的连接执行，此时spring能够获取到同一个事务对象开启事务，回滚事务等，此时事务是安全的，但当多个数据源扫描到同一个sql语句时，那么spring在获取数据源执行sql时会随机获取一个，所以存在 “**在甲数据源开启事务，但是是乙数据源执行的sql，导致事务没有被控制住**” 的情况，因此在多数据源时要控制sql隔离

