---
title: "Java学习错误记录和总结笔记"
subtitle: ""
date: 2022-07-15T15:35:00+08:00
lastmod: 2024-03-15T15:35:00+08:00
draft: true
author: ""
authorLink: ""
description: ""
license: ""
images: []

tags: [Java]
categories: [个人成长]

featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
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
  auto: true
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


## 2018年:

### 10-21: spring-security框架配置跳转路径错误

- spring-security框架配置中:配置默认跳转的路径 必须和 前台页面中写的路劲保持完全一致,(一个点儿都不能少)

```xml
<!-- 定义跳转的具体的页面 -->
<security:form-login
        login-page="/pages/login.jsp"
        login-processing-url="/login.do"
        default-target-url="/index.jsp"
        authentication-failure-url="/pages/failer.jsp"
        authentication-success-forward-url="/index.jsp"
/>
```

这前台和后台的配置指向的路径要完全一致,不然就会出现**无法跳转**的异常.

```xml
<form action="${pageContext.request.contextPath}/login.do" method="post">
```

### 10-23: mybatis在dao层方法的多参数注入错误

- mybatis框架注解开发时,或者xml映射文件配置时,若dao层的方法参数列表只有一个参数(无论是基本参数类型还是引用数据类型),可以不加@Param注解,,但是当出现多个参数时就要加@Param注解了

  ​	不然会 报 类似: **org.mybatis.spring.MyBatisSystemException:Parameter 'userId' not found.** 的异常(--详情请百度"@Param注解的用法")

  ```java
  @Insert("insert into users_role(userId,roleId) values(#{userId},#{roleId})")
  void addRoleToUser(@Param("userId")String userId, @Param("roleId")String roleId);
  ```

### 10-24: 关于a++ 自增的java基础语法错误

- java基础:a++自增问题(a--自减问题同上),  我们知道a++自增是 先运算,后自增,,所谓的先运算只管它之后的一次运算,一次运算之后就会执行自增了,若后面又多次运算,他只管第一次;自身忽略括号,(a++)和a++是一样的,不会先执行自增的;

  如下题:

  ```java
  int a=8; int b=10;
  System.out.print(a*b+a+++b);//98
  System.out.print(a*b+(a++)+b);//98
  System.out.print(a*b+b+(a++));//98
  System.out.print(a+++b+a*b);//108
  System.out.print((a++)+b+a*b);//108
  ```

### 10-27: lucene全文检索技术的原理分析

- 对于结构化数据的查询一般用于数据库中的查询,格式固定,查询时用于比对的元素是不重复的,因此计算机扫描时采用"全等"的策略,一旦找到要查的结果,就直接返回了,而对于非结构化数据来说,格式不固定,内容也可能有很多重复的地方,计算机查询的时候需要采用 "顺序扫描"的方式,对数据与查询条件做"包含"的运算,这种运算和"全等"相比,本身就会加大计算成本,而且顺序扫描的话,在数据量庞大的情况下,花费的时间会非常多,而且计算机为了确保能找到所有"包含" 查询条件的数据,必须把所有数据遍历一遍,这样会非常耗时间!,,,,,,,,,,,,,,,,,,,,,而采用lucene全文检索技术则是:**==先建立索引库,然后对索引库进行查询==**,建立索引库的过程就是将 每一条数据的通用信息提取出来,保存到通用的字段空间下,如每一个文件都会具有:文件名,文件内容,文件大小,文件路径(或url网络路径)等通用信息,先将每个文件这些信息提取出来放入一个文档对象中(也就是说一条文档对象就包含一个文件的所有提取出来的信息),并且在创建文档对象的过程中还进行了 分词处理,一个文件或多个文件之间可能有大量重复元素,若将这个大整体分解成一个个独立的元素那么肯定能减少数据的复杂度(参考数以万计的单词,拆分开来也就仅仅是26个英文字母组成的而已),分词就是这么回事,,分开的词与所属的文档对象保持着引用的关系,**而这种引用关系和分出的词的整体就称为索引**,而对于"关键词"来说,去掉重复的,所剩余的那部分的总体就组成了**索引库**,这索引库和原始数据相比数据量就非常小了,之后的查询 **只需要使用关键词查询,然后拿着查询条件里的 关键词和 索引库里保存的词单元做遍历 "全等"比较,找到就立马跳出循环(这一过程由于数据量不大,并且计算机的计算能力非常强,一般所花费的时间非常少),然后再根据此关键词与索引之间的引用关系 快速确定要搜索的那些文档对象,,再将这些文档对象里保存的字段信息,返回给用户,由于这信息结构固定,所以展示也很容易,之后用户只需要查看展示的信息,选择那一条是自己想查的数据,再根据此信息中的路径快速找到想要找的文件!**

### 10-28:ElasticSearch有关操作总结

- ElasticSearch简称es,它的核心在于他的查询方面,官方人员也说了,虽然es结构与数据库类似,并且查询性能比数据库高,**但是es在保存数据方面可能存在着数据丢失的问题,**并且在索引库和类型,字段等的增删改方面表现的并不好,关键是还不支持事务,它专注的是查询方法,所以并不能代替数据库..

- ##### es的索引库的增删以及类型和字段的确定:

  - [put] http://[ip地址]:9200/[索引库名].....请求体里面写json语法: {json语句}

  - [delete]  http://[ip地址]:9200/[索引库名]

- ##### 文档的增删改查

  - 增    [put/post] http://[ip地址]:9200/[索引库名]/[type名]/[id]-->id可写可不写,写的话即手动指定Id,不写则自定生成id..

  - 删    [delete] http://[ip地址]:9200/[索引库名]/[type名]/[id]-->删除指定id的文档对象

  - 改    和增一样,,id不重复则为增,id重复则为覆盖

  - 查  (一般会安装IK-analyzer中文分词器)  

    根据id查询:  [get] http://[IP地址]:9200/[索引库名]/[type名]/id

    根据关键词查询: [post] http://[IP地址]:9200/[索引库名]/[type名]/_seqrch  (其中type名可以不指定,这样范围大些)

    ```json
    {
        "query":{
            "term":{
                "字段名":"关键词"
            }
        }
    }
    ```

    根据长句(字符串)查询:[post] http://[IP地址]:9200/[索引库名]/[type名]/_seqrch  (其中type名可以不指定,这样范围大些)

    ```json
    {
        "query":{
            "query_string":{
                "default_field":"字段名",
                "query":"xxxxxxx...."
            }
        }
    }
    ```

### 10-29: Java代码操作elasticsearch的错误

- 使用Java代码写json数据时,适用对象调方法的流式编程方式,,只需要写 type名后跟properties属性再接字段属性,不需要像直接写json数据一样  还需要拼上  mapping这个属性.



### 10-31: 线程安全的单例模式实现

```java
/**
 * 线程安全的单例模式实现,
 * 实现了不同线程之间对象不同,同一线程之中对象一致!
 */
class Kun {
    int i;
    private static ThreadLocal<Kun> kunThreadLocal=null;
    private Kun(){}
    public static Kun getKunInstance(){
        if (kunThreadLocal==null) {
            //说明是第一次获取对象
            kunThreadLocal = new ThreadLocal<>();
        }
        if (kunThreadLocal.get()==null) {
            kunThreadLocal.set(new Kun());
        }
        return kunThreadLocal.get();
    }
}
/**
 * 实现了所有情况下都只能获取到单一对象的情况
 */
public class Kun{
    //私有构造
    private Kun(){}
    //创建私有静态内部类创建本类对象
    private static class KunInstance{
        private static final Kun instance = new Kun();
    }
    public Kun getInstance(){
        return KunInstance.instance;
    }
}

/**
 * 双重校验锁机制的单例模式
 */
public class Kun{
    //私有构造
    private Kun(){}
    //定义好单例对象
    private static final volatile Kun kunInstance=null;
    
    private static synchronized createInstance(){
        if(kunInstance==null){
            kunInstance = new Kun();
        }
    } 
    
    public static Kun getInstance(){
        if(kunInstance==null){
            createInstance();
        }
        return kunInstance;
    }
}
```

- ##### 查看HashSet源码发现,hashSet是基于HashMap实现的,基本上每个方法都需要调用HashMap中的方法才能实现!------>(猜想)说明HashMap集合实现的时间比HashSet早!



### 11-3: maven在执行install命令时报: zip Header not found  错误

- 经过不懈努力的查找,终于发现,当**maven仓库里所依赖的jar包出现异常(或者说"损坏")时,会报这个错误**!

  解决方法就是,找到仓库中这个jar包的位置,把他干掉,让maven重新下载即可!

  - 另外还有一个小错误就是:**如果执行install命令是通过plugin插件执行的话,可能会抛出jar包找不到的异常,尽量使用生命周期里面的执行命令**!



### 11-4: 前端JS框架的常用指令

- 表达式写法: {{变量}},,配合指令:ng-app  一起使用

- $sce  : 过滤器服务 $sce.trustAsHtml(data); 传入的数据当作html解析

- 动态绑定: ng-model="变量"

- 初始化指令: ng-init="变量='value'"

- 定义模型: angular.module("模块名",[所依赖的模块])

- 定义控制器: 模块.controller("控制器名",function($scope){  .......  })

- 点击事件指令:  ng-click

- 循环指令: ng-repeat="entity in list "

- 判断指令: ng-if : 

- 增强下拉列表指令:  select2 , <input select2 select2-model="" config="" mudlble="" />

  其中: select2指的是: 指令,声明这是个下拉框;

  ​	  select2-model指的是:选择的数据存贮 数据格式:{"id":1,"text":"xxx"}

  ​	  config指的是:数据来源,读取的数据格式为: {data:[{"id":1,"text":"xxx"},{"id":1,"text":"xxx"}]}

  ​	  mudible指的是 是否为多选,true为多选,false为单选

- 下拉列表指令: ng-options=" entity.id as entity.name for entity in list ";用于select下拉框中

- 变量监控: $watch("被监控的变量", function( ){  })    被检控的变量值发生变化时,会触发  函数的执行

- 地址栏参数监控: $location    在当前页面的controller控制器中注入此变量,可以获取跳转到此页面时地址中带有的参数列表!   $location.search()获取包含所有参数的json**对象**, 前提是地址栏参数列表是 ***#?*** 开始的才可以获取!



### 11-4: 使用springMVC框架时,设置拦截路径"/"和"*.do"的区别

"/"是静态资源页面,和class访问路径全都拦截," *.do"是只拦截以此结尾的路径,而这个也只能用于class路径上,所以为了实际好应用,建议所有class路径都以" *.do"结尾,



### 11-9: SpringMVC的执行流程

- 首先客户端浏览器发送请求给服务器,前端控制器获取到请求,调用处理器映射器,处理器映射器根据url生成相应的处理器对象,然后前端控制器调取处理器适配器,将具体的处理器对象传递给处理器适配器,然后处理器适配器通过处理器对象对请求信息处理之后,返回ModelAndView对象,前端控制器获取到ModelAndView对象之后,将其传递给视图解析器,返回得到相应的view,之后前端控制器将数据填充到view中得到最终的静态资源,然后响应给客户端浏览器!



### 11-9: ==**有关复制pdf文档上的内容的问题,,,,千万别直接往代码里面贴,里面有看不见的字符导致各种问题!   切记切记!**==



### 11-10: Spring-Security框架常见错误

- 若与spring整合时版本不符可能会报错,导依赖时,security的依赖之间版本要统一
- spring-secuity版本在5.0版本之后必须声明加入passwordEncoding加密类,不然就会报错,想不加的话,可以在密码前面加上  {noop}声明!



### 11-10: angularJS前端框架前台 ng-click 指令使用错误

ng-click指令只能用来调方法,不能直接在里面写执行的代码,  写了也不会执行!   可以一次性调取多个方法,中间用分号隔开.



### 11-13: angularJS前端框架 在引入goodsController.js控制器时报错:无法注入参数值

当几个页面共用一个controller.js时,某些页面注了很多$xxx 等自带的值, 但是在另外一个页面不需要这些值时,直接引用此controller.js文件就会报此错,你需要把能注入此值的其他js文件也引入进来,即使你不用! 或者不共用此controller.js,自己在写一个也行!



### 11-18: JS原生关键词: delete  作用: 删除一个js对象中的一个属性

- 用法:  person: {name: "江昆",age: 20},   删除name 属性,  delete  person.name;  就可以删除掉name属性!


### 11-22:freemarker使用输出流乱码问题

- 一般使用的FileWirter()输出流是默认采用系统字符集进行编码输出,可能会造成乱码问题, 此时应该采用可以手动设置字符集的输出流 FileOutputStream 代替:

  ```java
  OutputStreamWriter writer = new OutputStreamWriter(new FileOutputStream(pageDir+id+".html"), "UTF-8");
  
  ```

## 2019年:

### 3-4: oracle数据库sql递归语法

```sql
SELECT * FROM SYS_AREABASE
START WITH areacode='433127'
CONNECT BY PRIOR areacode=PARENTcode
```

#### 树结构的描述:

​        树结构的数据存放在表中，数据之间的层次关系即父子关系，通过表中的列与列间的关系来描述，如EMP表中的EMPNO和MGR。EMPNO表示该雇员的编号，MGR表示领导该雇员的人的编号，即子节点的MGR值等于父节点的EMPNO值。在表的每一行中都有一个表示父节点的MGR（除根节点外），通过每个节点的父节点，就可以确定整个树结构。

其中：CONNECT BY子句说明每行数据将是按层次顺序检索，并规定将表中的数据连入树型结构的关系中。PRIOR运算符必须放置在连接关系的两列中某一个的前面。对于节点间的父子关系，**PRIOR运算符在一侧表示父节点，在另一侧表示子节点，从而确定查找树结构是的顺序是自顶向下还是自底向上**。

​         在连接关系中，除了可以使用列名外，还允许使用列表达式。START WITH 子句为可选项，用来标识哪个节点作为查找树型结构的根节点。若该子句被省略，则表示所有满足查询条件的行作为根节点。
​         START WITH：不但可以指定一个根节点，还可以指定多个根节点。

参考链接:https://www.cnblogs.com/zhjx0521/p/7338876.html

### 4-1: 今日学习mybatis总结

1. 公司的分页插件触发条件是:dao层方法传入inDTO对象,若不想分页的话则不要传入inDTO对象,直接传入Map对象即可!
2. mybatis能够根据 sql标签中的databaseId属性区分不同数据库厂商的sql语句, 若相同的sql语句有多条, 则只会加载带有databaseId属性的sql,,另外需要在著配置文件里面声明DatabaseIdProvider标签
3. mybatis给参数赋值有两个方式: #{} 是预编译方式填值,参数需要经过转义; ${}是 字符串拼接方式,参数不会经过转义!  有这个区别之后,正常安全 赋值可以继续使用 #{} ;   在有一些特别的需求时  要灵活利用${} 拼接字符串的方式会节省时间和避免冗余的代码! 
4. *where* 元素标签只会在至少有一个子元素标签的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，*where* 元素标签也会将它们去除。

### 4-11: maven工程引入本地jar包,并需要将之打进war包中的做法及 注意的问题;

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



### 5-13: 好用的集合工具类: org.apache.commons.collections.CollectionUtils

取两个集合的并集(相当于去重的功能)

```java
List list = CollectionUtils.union(listA, listB);//取两个集合的并集(去重)
    
List list = CollectionUtils.intersection(listA, listB);//取两个集合的交集(得到重复的数据)
    
List list = CollectionUtils.disjunction(listA, listB);//取交集的补集(得到不重复的那部分数据)
    
boolean flag = CollectionUtils.isEqualCollection(boy1,boy2);//判断两集合是否相等
```

除了以上的这些好用的方法之外,还有很多其他好用的方法, 有时间探索一下!!!



### 9-20：oracle数据库随机排序函数：ORDER BY **<u>DBMS_RANDOM</u>**.VALUE

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

### 9-23：oracle的集合运算

- **INTERSECT** (交集)，返回两个查询共有的记录
- **UNION ALL**（并集），返回各个查询的所有记录，包括重复记录
- **UNION**（并集去重），返回各个查询的所有记录，自动去重，并按照自己的规则排序（也就是说，取并集之前作排序的话会在此之后顺序被打乱）
- **MINUS**（补集），返回第一个查询检索出的记录减去第二个查询检索出的记录之后剩余的记录。



### 9-26：java语法：在重写父类方法时，可以在 修饰词 前面加上 synchronized关键字使之变为同步方法；



### 9-30：oracle问题：分页查询会在不同页面查到重复的数据

​	今天遇到一个奇怪的问题：对一个列表做分页查询，对几个字段加了排序，，然后发现有几页数据有重复现象；但是不分页全部查出来却没有重复数据；我估计问题出在了分页上面

然后百度了一下，学到了：原来oracle在数据的顺序上，如果不排序，则默认按照数据库中数据的存储顺序进行排序，也就是默认顺序；当对某个字段进行排序的时候，若此字段有很多重复数据时，这段重复数据的部分的记录的顺序就无法保证了，所以就有可能发生在前一页查到了几条，到了下一页又会查到。 出现这种情况的原因是因为排序列值的不唯一性。 Oracle这里使用的排序算法不具有稳定性，
也就是说，对于键值相等的数据，这种算法完成排序后，不保证这些键值相等的数据保持排序前的顺序。

解决的方法是在后边增加一个唯一性列，比如主键。
所以解决方法如下（两个条件必须同时满足）： 
 1.sql语句中需要有排序条件。 
 2.排序条件如果没有唯一性，那么必须在后边跟上一个唯一性的条件，比如主键。

可以参考：<https://blog.csdn.net/u010395242/article/details/52047341>

### 10-8：web项目，当前台发起一个耗时的请求之后，后台线程在处理中，如何在前台在再次发送一个请求安全的中断这次后台正在处理的任务？

实现方式涉及到线程的中断操作；不能使用Thread.stop()这个方法，因为它存在安全问题，并且已经被java平台废弃，此次实现方式 涉及到的方法为： getId（）；interrupt（）；isInterrupted（）；

其中： getId（）；通过对象调用，获取此线程对象的唯一标识id

interrupt（）；通过对象调用，将此线程对象所对应的线程 标记为中断状态

isInterrupted（）；通过对象调用，返回此线程对象所对应的线程的中断状态，并且不会重置这个状态值；这个与interrupted（0 方法相反， 他是会将中断状态重置为false

**实现思路：**前台发起第一次请求之后，后台处理数据，并开启一个新的线程来处理此次耗时的操作；并通过此线程对象调用  getId（）方法获取此线程的id 返回给前台，同时将id作为key，对象作为value存入全局缓存；前台标注正在处理中，并且显示一个取消的按钮，当点击这个按钮时，就将id传递回给后台，后台通过id获取到 处理任务的线程对象，调用interrupt（）方法将状态 标记为中断状态，然后返回； 再来说说 线程任务里的逻辑处理：在循环里面增加判断isInterrupted（）当返回true就 跳出循环，结束方法，或者提交，或者回滚； 或者将代码try。。。catch ，，当返回true 就抛出异常，在 catch 这个异常，然后做相应处理； 或者其他处理方式；总之利用isInterrupted（）方法进行判断

### 10-10：ajax请求二进制文件流时发生跨域问题的解决

这种情况下，客户端与服务器会先建立一次预连接，这次预连接会经过服务器端的过滤器，一般会在过滤器里面设置好响应头处理跨域；所以预连接会成功返回，然后进行正式连接，正式连接应该是没有经过过滤器（具体没有深究），所以在response对象写返回的二进制流时，前台会报跨域错误，原因就是 正式连接的响应头里面没有加上 跨域头子，，，所以解决办法就是手动在方法返回里加上 响应跨域头子

### 11-19：springTask任务默认单线程执行，百度sdk工具包调用接口发生错误可能会中止线程

- springTask的定时任务组件使用非常简便，只需要在启动类上添加@EnableScheduling注解，然后再在需要定时调用的方法上加@Scheduled(cron = "* * * * * ?")注解，即可；spring底层使用的是线程池，但是默认只加入了一个线程，可以保证线程安全，但是可以配置线程池中线程的数量，让任务多线程执行；
- 百度开放平台会提供一些功能接口，可以使用百度的sdk工具包调用，但是若使用不当可能会导致sdk工具包杀死当前线程；（这可能也是一些开放平台的自我保护机制）

### 12-17：Java程序调用第三方程序所需要注意的坑：

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

### 12-18：spring切入点表达式 还可以支持 “与，或，非”  等 基本运算符

比如我想拦截某个包路径下面的，以及路径子包下面的所有类和所有方法，，但是需要排除某些 类和方法，这种 表达式就可以写成如下：

```java
!execution(* com.yihuisoft.productbiz.service.puhui.FormCreateHandler.*(..)) && execution(* com.yihuisoft..service..*.*(..))
```

### 12-26：记一次生产上事务失效问题排查

spring的声明式事务或者注解式事务一般情况下不生效的原因是：1，方法内部调用同一个类中的方法；2，方法内进行了异常捕获未抛出；3，启动类上没有加上开启事务的注解，等等

但是这次出现失效的情况不在上面任何一个情况之中，找问题找了好几天才找到。是**多数据源导致的**

一般配置多数据源需要扫描不同的路径下面的xml文件（sql语句）才能是事务生效，这样每一个sql语句都会被一个数据源的连接执行，此时spring能够获取到同一个事务对象开启事务，回滚事务等，此时事务是安全的，但当多个数据源扫描到同一个sql语句时，那么spring在获取数据源执行sql时会随机获取一个，所以存在 “**在甲数据源开启事务，但是是乙数据源执行的sql，导致事务没有被控制住**” 的情况，因此在多数据源时要控制sql隔离



## 2020年:

### 1-07：java防空指针神器Optional<T> 对象的使用

Optional<T> 对象相当于一个容器，利用泛型，可以将返回单体数据用此对象存储，调用方在调用时拿到这个对象，可以不用再写判空的代码，也可以利用lambda和方法引用等函数式编程完成相关拖沓的校验代码，简洁代码使代码不易出错；

**以后要记得利用这个对象，拒绝空指针；记住，记住，记住！**

可以参考：https://blog.csdn.net/agzhchren/article/details/90515728

### 1-07: java8的集合stream()流操作 API

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

### 4-24：设计模式中观察者模式（可以理解为发布-订阅模式）的使用总结

​	此设计模式分为两个部分：观察者与被观察者；可以理解为 服务发布通知者与服务订阅者。属于典型的一对多类型；服务发布者方相当于一个容器，存放订阅者对象，拥有增加，删除，通知等基本方法；订阅者方需要定义一个方法，用于服务发布者的调用。**体系可以分为4部分：抽象主题方**：定义订阅者对象 增加，删除，通知等基本方法；**具体主题方**：用于实现抽象主题方，然后添加具体的业务逻辑，在需要通知订阅者时进行调用通知方法；**抽象订阅方**：定义被通知时方法；**具体订阅方**：实现抽象订阅方，实现被通知方法，写具体需要实现的业务逻辑；**客户端调用方**：创建主题方对象和订阅方对象，进行订阅操作等，这个过程可以以配置的方式完成

​	待优化的点：可以在通知的方法中定义参数，实现待参通知；基本的发布订阅模式不带路由，所以一旦订阅，便可以收到所有通知，可以在体系中设置路由参数，以达到选择性订阅的功能

​	**java中已经实现了发布订阅模式体系，我们可以直接应用而不需要自己再创造这个体系：java.util.Observable和观察者方 Observer接口**

参考链接： https://blog.csdn.net/weixin_43627118/article/details/105715870

### 4-30：善于使用Java中获取操作系统相关信息的API

​	今天在查看开源项目时学习到 人家在操作 系统文件或者 需要有些东西与操作系统挂钩时 调用Java的相关方法最好。比如你在拼接文件路径时，Windows是“\”，但是linux是“/”。比如获取当前操作系统用户的文件夹，或者当前操作系统的名称，或者创建文件夹时需要进行判断权限授权操作，执行shell脚本时，此时使用Java的api进行操作可以让我们从环境切换等问题脱身出来。因为java帮我们做了这些判断操作；

文件路径的分隔符：File.separator

获取当前操作系统用户的文件夹：System.getProperty("user.dir");

判断字符串是否包含文字符号，用于判断字符串是否为全空格字符串：StringUtils.hasText(String str);

### 5-21：关于http协议的请求与响应中，请求体，响应体只能被读写一次（坑。。。）

​	如题。http协议是安全的信息传输协议，服务端收到请求之后，经过tomcat的封装成HttpServletRequest对象。这个对象封装了http协议中必有的相关信息：包括请求头，请求体等。一般情况下，tomcat传递好request对象，经过spring的处理，我们就可以直接拿到请求体中的数据，这个没有问题。但是如果需要对请求进行预处理，比如说在过滤器中对请求进行过滤，除掉非法请求，此时我们需要获取请求体中的内容，需要用到request对象中的getInputStream()方法，一般流中有reSet()方法,可以重置流中读取的指针的位置，这样在流未关闭之前可以重复读，但是request对象中获取的流是一个子类，**不支持reSet()方法，也就是说只能读取一次**；此时如果我们在过滤器中读取之后，后续spring就无法在进行处理了。为了避免这个问题。可以采用两个方案来解决：1，用于安全校验的参数放在请求头里面；2，若已经放在请求体中了没办法则可以重写一个request类继承tomcat的request对象的类，重写getInputStream()等只能读取一次的方法。对requst对象进行包装。具体实现思路是：构造方法中传入request对象对请求体进行读取，转成字节数组存储起来。然后重写getInputStream()方法，对字节数组读取，转换成一个输入流返回。

### 5-21：在tomcat应用中获取请求方的真实IP

​	某些情况下，我们需要对请求方的ip进行分析。但是我们的应用收到的请求一般是走NGINX反向代理过来的，虽然request里面有获取客户端ip的方法，但是这样获取到的是nginx的ip，不是真实客户端请求的ip，此时需要配置nginx在请求转发时，将真实ip带过来。经过几层nginx代理，就要配置几层

nginx配置：

```java
    location ^~ /your-service/ {
            proxy_set_header        X-Real-IP       $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://localhost:60000/your-service/;
    }
    
java代码：
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



### 7-30：实现接口 ApplicationRunner 或 CommandLineRunner 可以在springboot初始化之后，启动成功之前执行代码

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

### 9-27：maven的\<dependencyManagement\>标签“依赖管理”，用法原理

父项目用此标签包起来依赖，不会自动被子项目继承，需要子项目写出依赖来继承，此时可以不用写版本号。若父项目没用此标签包起来的依赖则可以被子项目自动继承，此时不需要显式的写出来。

### 9-28：spring-boot-starter-actuator WEB应用的运行健康状况监控框架。及一些访问接口

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

### 10-15：spring项目中 用自定义注解申明bean注入

在学习springcloud的Ribbon客户端负载均衡器时，从源码分析阶段发现： 在@Bean注解标注的配置方法下的restTemplate如果标注@LoadBalance注解会被ribbon注入负载均衡拦截器，而没有标注的则不会注入负载均衡。发现原因是，标注的在@AutoWired注解的字段下，标注@LoadBalance注解的会被注入然后注入拦截器，其他的则被过滤掉了。因此我怀疑**spring这里有一个隐藏操作：在@AutoWired注解的字段下标注自定义注解时，会只注入标注同样自定义注解的bean对象。**经过我的验证，结果得到了证实。

### 10-21：微服务架构spring-cloud体系各组件总结

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

### 11-06：Springboot-amqp对接rabbitMQ时配置Binding时注意一个坑

​	在配置binding时，需要绑定队列queue到到交换机exchange（顺便指定routingkey）；习惯性操作：在配置类中用@Bean注解标注方法，然后直接 new一个队列，再new一个交换机，然后就直接用BindingBuider开始绑定了。 之后启动测试就会发现 报404：rabbitmq服务器找不到对应的 交换机 和队列。

原因：在客户端 申明的 队列和交换机，都应该有一个 在服务器中创建的操作。但是在spring的封装下，将这一步省略了，所以在自己本地声明的队列和交换机。直接进行绑定肯定是会报错的。需要在申明之后，将对象通过@Bean 注入到IOC容器中。然后springboot-amqp的自动配置中会注入申明的队列，并将之提交到服务器创建，然后才进行绑定。

### 11-09：rabbitMQ基本特性、高级特性 以及整合springboot的用法总结

rabbitmq消息队列是AMQP协议（高级消息队列协议）的一个erlang语言的实现，基于生产者消费者的模式，可以将之前同步处理的逻辑，异步执行，可以对业务解耦，缩短了系统对前台的响应能力，也提升了请求的吞吐量，增加了系统的抗并发能力等等

##### 基本特性

rabbitmq主要可以分为 消息生产者、消息消费者和服务器实例broker，一个rabbitmq可以包含多个broker；

其中broker具体可以分为：connection连接、channel隧道、exchange交换机、queue队列、binding绑定等内容。

其中生产者和消费者 和broker建立连接，然后在连接中建立隧道，每个隧道相互隔离，但是多个隧道复用一个连接，用意是减少创建连接的时间，增加效率；

开启隧道后生产者通过隧道发送消息，服务器接受到消息给予反馈之后将隧道关闭；

交换机的作用是用于接收生产者的消息，并将消息路由到与之绑定的队列中去，

队列用于暂存消息和将消息发送给消费者；

创建好的交换机可以和创建好的队列建立binding，并指定bindingkey。

生产者发送消息时可以指定一个routingkey，交换机根据不同类型将routingkey进行解析，并将消息路由到与bindingkey匹配的队列中。

多个消费者可以同时监听一个队列，此时，消息会通过轮训的方式发送给消费者。当消费端channel中配置了**同时消费消息个数最大值**时，则此时若有消费端未确认的消息达到配置值后，服务器将不再给其发送消息，而选择那些相对空闲一些的消费者发送（这个配置主要考虑到由于轮询发送消息 有的消费者服务机器可能由于硬件原因比较老旧，而导致对消息消费的速度较慢，而另一些消费速度快的机器由于消息都消费完了从而存在空闲，此时若仍然无脑轮询发送消息的话可能会对那些老机器消费端造成消息积压。使用这个最大值配置就可以实现，老的机器少消费一点，新的机器多担当一点，增加整体的吞吐量）

消息的生产与消费的整个流程如下：

​	首先创建好交换机，在创建好队列，然后将队列绑定到交换机上，并定义一个bindingkey。

​	生产者发送消息到交换机，并携带一个routingkey，交换机收到消息之后，若配置了回调，则交换机将成功接收消息信号回调给生产者，然后根据routingkey与bindingkey匹配，将消息路由到对应绑定的队列中暂存，此时也会触发回调将队列接收消息信号回调给生产者。之后队列将消息发送给消费者，若配置为手动确认机制，则此时服务器会将消息标记为带确认状态，当消费者成功处理消息之后，发送成功确认指令（basic.ack）服务器接受到确认消息之后就会将消息标记为成功发送，并将之删除；若接受到失败确认指令（basic.reject）时，会根据状态true将消息返回给队列中，状态false将消息直接丢弃（返回true会有风险，若是由于自身业务异常导致的失败确认可能返回队列中到下次消费时还会失败，这样就会造成死循环）。这个过程结束。

根据交换机的类型，可以将rabbitmq的工作模式分为4种：**direct**、**topic**、**fanout**、**headers**

**direct交换机**：在路由消息时会将routingkey与bindingkey进行精确匹配，匹配上之后就将消息路由到对应队列。

**topic交换机**：在路由消息时会将routingkey与bindingkey进行匹配，允许存在通配符“#”，“*”；并且routingkey以“.”为分隔单词，“#”代表匹配0个或多个单词，“\*”表示1个单词，匹配上之后就将消息路由到对应队列 （这个最灵活，一般建议使用这个）。

**fanout交换机**：在路由消息时会忽视routingkey（传了也不会用），直接将消息路由到所有与之绑定的队列上。

**headers交换机**：此交换机不会匹配routingkey，而是通过判断消息中的headers的键值对是否符合将之路由到对应队列；由于这个模式性能很差，所以一般不建议使用。

##### 高级特性

- **ttl 消息过期机制**

  rabbitmq支持消息过期机制；主要分为两种：一种是给队列设置过期属性（在申明队列时，指定参数 “x-message-ttl” 为xxx，单位为毫秒），此时表示此队列为ttl队列，队列中所有消息都将附带过期属性，主要实现原理：rabbitmq队列会在消息进入队列时标记他的入队时间，然后定期扫描队列出口处的消息是否过期，是否达到设定的过期时间，若过期就将其移除，若此队列绑定了死信交换机，则会将消息转移给死信交换机。为什么只扫描出口处？就是因为队列是先进先出原则，若头部的消息都没过期，后面的肯定也不会过期了。

  另一种为在发送消息时，单独给消息指定过期时间，此时在到达队列后，在队列将消息推送给消费端前，会判断消息是否过期，若消息过期则不会发送而将之移除。

  如果同时设置了上面这两种过期机制，则会以小的准；但是值得注意的是：由于rabbitmq对过期判定的机制，可能会导致消息设置的过期时间到了，消息也不会移除，因为如果队列设置的过期时间大于消息设置的过期时间时，又存在消息积压，此时出口处的消息还没有过期，但是位于后面的消息自设置的过期时间到了，但是没到队列头部，所以此时并不会进行移除。

- **死信队列**

  过期的消息被称为dead message；若ttl队列指定了参数：“x-dead-letter-exchange” 为“<交换机名称>” ，被指定的交换机就被称为死信交换机，其实也就是名称是这么叫而已，他和其他普通交换机没啥区别，一般类型可以设置为fanout类型，然后与死信交换机绑定的队列就称为死信队列。在ttl队列中过期的消息将会被发送到死信交换机，然后死信交换机将之路由到死信队列中。

- **延迟队列**

  rabbitmq本身并不直接支持延迟队列，但是可以通过ttl消息过期机制和死信队列来实现延迟队列；具体做法：创建一个ttl队列，指定参数 “x-message-ttl” 为xxx，单位为毫秒 作为延迟时长，指定参数：“x-dead-letter-exchange” 为“<交换机名称>” ，指定死信交换机，然后绑定上死信队列，之后消费者不直接监听ttl队列，而监听死信队列；这样进入ttl队列的消息在到达延迟时长之后过期，被发送到死信队列来，然后被消费者消费。
  
- **优先级队列**

  在创建队列时指定参数：x-max-priority为 xxx 数字，既指定最大优先级数字，不指定则默认为0，这样所有消息优先级一样。指定了之后也不一定生效，因为如果生产者生产的消息在到达队列之后马上就被消费者接收了，那么优先级数字就没啥意义。所以这个属性一般是在队列中的消息发生了积压时才有效果。这时优先级高的会被调整到头部先进行消费。

- **交换机、队列、消息的持久化**

  交换机的持久化是指创建的交换机元数据是否需要持久化到磁盘上，若设置为false，则每次rabbitmq服务重启时交换机数据会丢失，需要重建

  队列的持久化和交换机的一样，但是需要注意的一点是：队列中保存着消息，若队列不持久化，则队列元数据丢失时，队列中的消息也会跟着丢失

  消息的持久化指的是每次发送到队列中的消息是否需要保存到磁盘上，若保存到磁盘，则可以增加消息消费的可靠性，因为消息不容易丢失了，但是这样由于每次都需要读写磁盘，队列的性能会降低。可以在发送消息时 message.getMessageProperties().setDeliveryMode(MessageDeliveryMode.NON_PERSISTENT);设置为不持久化

- **消息确认机制**

  为了保证消息队列工作的可靠性，我们需要知道生产者在发送消息之后，交换机和队列是否真的成功接受到了消息；还有消费者在接受到消息之后，是否真的成功的消费了消息。而rabbitmq提供了 消息确认机制，但是默认情况下是自动确认的，也就是说消息只要发送了就认为你发成功了，消息被消费者监听到，队列一将消息发出，就将消息标记为成功确认，并将之移除。我们可以修改配置，在创建交换机时打开发送确认开关，并设置回调函数，生产者方有两个回调，一个是消息被交换机成功接收时回调，另一个是消息被路由到对应队列时回调。消费者方需要将配置设置为手动提交模式，然后在接受到消息之后，队列不会移除消息，而是将消息标记为待确认状态，然后消费者将消息处理完成，若业务成功完成则可以调用basic.ack指令将消息设置为成功确认，之后队列会将消息移除，状态传true表示 将消息tag小于等于当前消息tag的消息都置为成功确认，传false表示就确认当前的；若消费者业务处理失败抛出异常，此时可以在catch中 调用 basic.reject指令将消息置为失败确认，此时可以传true，表示当前消息重新会队列，但是要防止苏循环问题，传false表示将消息丢弃；还可以调用basic.nack指令，表示 将消息tag小于等于当前消息tag的消息都置为失败确认（批量操作），传true表示重回队列，传false表示丢弃消息。

##### rabbitmq的用法：

​	在整合springboot之后，操作的api被简化了很多，简单说一下流程：

1. 引入maven依赖：spring-boot-starter-amqp

2. 配置文件指定rabbitmq服务的ip，端口，用户名，密码（默认初始用户名密码为guest，guest，可以访问图形界面之后添加用户，添加之后建议删掉初始的确保安全）

   ```yml
   spring:
     rabbitmq:
       host: 115.159.116.23
       port: 5672  #这里有个坑： 端口号15672是web端管理页面服务端口号；mq服务端口是5672；也就是说rabbitmq服务启动了2个进程。
       username: kun
       password: 123456
   ```

3. 创建配置类，进行交换机的申明、队列申明、交换机与队列绑定申明，此阶段可以申明TTL队列、死信队列、优先级队列等

4. 在配置中注入SimpleRabbitListenerContainerFactory对象，将确认模式设置为手动确认

   ```java
   /**
    * 默认的监听配置工厂，由于默认将消息消费设置为自动确认，但是实际情况中 一般都需要手确认，所以 我们需要注入 此监听工厂配置，将确认模式修改为手动确认
    */
   @Autowired
   SimpleRabbitListenerContainerFactory rabbitListenerContainerFactory;
   
   @PostConstruct
   public void setCloseAutoAck() {
       rabbitListenerContainerFactory.setAcknowledgeMode(AcknowledgeMode.MANUAL);
   }
   ```

5. 生产者端注入rabbitTemplate对象

   ```java
   public void sendMessageToDelay(String message){
       System.out.println("我要发送消息了。。。");
       //向队列 发送消息,指定routingkey
       amqpTemplate.convertAndSend(RabbitConfig.DIRECT_EXCHANGE, "info.warn.debug", message, new MessagePostProcessor() {
           @Override
           public Message postProcessMessage(Message message) throws AmqpException {
               String now = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
               System.out.println("发送时间"+now);
               int i = new Random().nextInt(100);
               //设置消息优先级
               message.getMessageProperties().setPriority(i);
               message.getMessageProperties().setHeader("priority", i);
               //设置消息过期时间
               message.getMessageProperties().setExpiration("20000");
               //设置消息是否持久化
               message.getMessageProperties().setDeliveryMode(MessageDeliveryMode.NON_PERSISTENT);
               return message;
           }
       });
       System.out.println("消息发送过去了");
   }
   ```

6. 消费端消费消息并确认

   ```java
   @RabbitListener(queues = "deadLetterQueue")
       public void consumeMessage2(Message message, Channel channel) {
           long deliveryTag = message.getMessageProperties().getDeliveryTag();
           try {
               String now = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
               System.out.println("接收时间" + now);
               System.out.println("q2message2 = " + new String(message.getBody()) + "--- priority: " + message.getMessageProperties().getHeaders());
   
               //消费成功，手动确认 true 成功确认消息tag小于等于此tag的所有消息，false 成功确认当前的消息
               channel.basicAck(deliveryTag, false);
   //            throw new IOException("sss");
           } catch (Exception e) {
   //            消费失败，标记失败确认，true表示返回队列
   //             false 并不用返回队列，直接丢弃
               try {
                   channel.basicReject(deliveryTag, true);
               } catch (IOException ioException) {
                   ioException.printStackTrace();
               }
               e.printStackTrace();
           }
       }
   ```

### 11-13：开发中常用linux的shell指令

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

### 11-14：数据库优化总结

数据库优化在层次上可以分为硬件优化和软件优化；硬件上：将数据库单独部署在一台机器上增加数据库的资源调度能力；提高cpu和内存和硬盘的硬件水平也可以增加数据库的性能。软件上的优化又可以分为数据库表结构的优化和sql语句的优化：表结构优化层次上，根据具体的业务需求合理的设计表字段，在设计层次上减小sql查询的复杂度（比如一个要求存储树形结构数据的表，在需求上要求查询某节点数据的根节点数据详情，此时可以在设计表的时候将附带增加一个字段用于存储根ID，这样在查询时就可以不用每次都递归查找了，增加了查询的效率）；在查询作为条件需求多的字段上添加索引；如果表的数据量特别大时，根据数据特点建立分区表等等；在sql层次的优化上尽量简化sql语句，不要写太过复杂的sql语句；在查询上要避免慢查询，要提高查询条件命中索引率，避免全盘扫描，比如1、尽量不要用is null 、!=等条件。2、在预期查询结果返回1条时，尽量加上rownum=1或者limit 1。3、在模糊匹配时尽量不要使用全模糊匹配，这将不会使用索引。4、尽量不要在查询条件里面拼接or，应该换成union all。5、查询条件的左边字段上不要用表达式，不然就会导致放弃走索引。6、不要join on 太多表，会降低效率。7、不要使用select * ，需要什么字段就写什么字段。

### 11-16：普惠一站项目总结

普惠一站是一个用于帮助银行理财经理获取客户的app，功能模块上分为新闻资讯、产品管理、朋友管理、语音通话、视频直播、积分商城和营销统计等模块，后台服务上分为 app端、中台端、小程序端、定时任务端、南京银行定制版等5个java后台服务。整体是基于springboot开发，用redis和阿里oss对象存储对早高峰流量削峰，oracle作为数据存储。内部一些功能模块的实现上对接了一些第三方云平台，比如新闻资讯模块需要采集新闻标签，这个对接了百度云的自然语言处理api。语音通话对接了百度云的语音双呼和科大讯飞的语音转写；视频直播对接了腾讯云的云直播服务等等。本人的职责主要是负责这5个java后台的功能迭代还有日常维护，包括但不限于功能代码编写与迭代，第三方平台服务对接，项目bug修改，运营数据收集以及制作报表，生产上线运维。

普惠一站运营数据：

 普惠一站刚上线一年多，目前处于用户积累期，现在用户量在2万左右，日均活跃用户在1万左右，新闻的日均访问量在5万左右，客户累计访问量在1千万以上，tps最高在50左右

项目中曾经遇到了哪些问题？

曾经遇到过一个线上java定时项目服务中线程池的所有进程阻塞问题。

第一个问题最终排查的原因是：linux操作系统中通过一个进程调用另一个子进程时，子进程的输出流会输出到父进程里面，而如果子进程存在错误流时，默认则不会输出到父进程里面，而是会写入到缓冲区里，需要手动将缓冲区的数据进行读取清空，如果缓冲区被占满，则子进程将会被阻塞，因此python进程被阻塞之后，导致调用python的java进程也被阻塞了，直到整个线程池的线程都被阻塞完。解决方法其实jdk也提供了：用java调用第三方进程时，可以将子进程的输出流和错误流合并，一同输出到父进程，这样就不会发生死锁问题。



第三方接口对接流程：

语音双呼：用户在界面上发起呼叫请求，后台收到请求之后将发起者的手机号码与被呼叫者的手机号码作为参数调用百度语音服务接口，调用成功之后，运营商会给发起呼叫方打电话，发起方接听电话之后运营方会呼叫被呼叫方的手机，被呼叫者接听之后双方就可以通话了。

### 11-21：面试总结

这次几家面试下来总结发现：数据框架不在乎学习多少，技术栈该有的都有就行，最重要的问的最多的是 多线程，数据库相关；

所以接下来我的研究方向：java并发编程、mysql从入门到精通，这两本书看完。必须深入学习下这两个方面。

### 11-22：mysql数据库设计及优化总结

mysql数据库的表设计需要遵循数据库设计的三大范式：第一大范式：表中的字段要保证最小粒度，不可拆分。第二大范式：在第一大范式的基础上，非主键字段要对主键字段完整依赖，而不能部分依赖（就是说不要建联合主键）。第三大范式：在第二大范式的基础上，表中的非主键字段只能依赖于主键字段而不能依赖其他非主键字段。在遵循这三大范式的基础之上可以降低数据库的字段的冗余性，但是在某些具体的业务需求之下为了提升性能也是可以允许冗余的。

在创建字段的类型时，要选择合适的格式，打个比方：建立主键能用数字类型就不要用字符串类型，因为数字类型在查找匹配时只需要计算一次，而字符串类型则在匹配时会根据字符串的长度有多长，就计算多少次，在大数据量的时候，这个性能差距就比较大了。另外，在建立字符串类型时，尽量选择可变长度varchar类型，可以减少空间的浪费。

在数据量达到一定规模时，表结构上的优化可以对mysql数据库进行分库分表优化，分库分表可以分为垂直切分和水平切分；一般看情况处理，如果是数据库中表非常多，并且各个业务模块的表都比较独立，那最好采用垂直切分，将数据量大的模块的表分离出来，建到不同的数据库中，达到分库的目的。如果是某些表里面的数据量特别大的话，可以采用水平切分，将一些特征相同的数据分离到多个结构一样的表中去，使一个大表变成多个小表，达到分表的目的。

在sql上的优化，原则就是要避免全盘扫描，要避免表级别的锁，要触发索引，常见的一些优化有：1、当预期查询的结果只有一条时，可以加上limit 1.这样可以让数据库引擎在找到这条数据之后立马返回而不会全表扫描。2、在选择数据库引擎时根据读写的的需求，可以选择合适的数据库引擎，InnoDB支持行锁，支持事务，适合有读写的需求。MyISAM只支持表锁，并且不支持事务，所以在写的需求大的时候效率会比较低，但是它的查询性能比较高，适合只有读的需求（myisam之所以查询速度比innodb快，是因为myisam在缓存上只缓存索引块，并且数据记录的是具体文件的磁盘位置，而innodb则即缓存了数据块，又缓存了索引快，在查找时先找到数据块，再找到对应的行。）3、尽量不要使用not in 而用not Exists ，后者用到了连接，可以出发索引，效率较高。4、不要使用 or、is null、is not null、<> 等条件，这些条件不会触发索引，可以使用union等方式替换，空值不会触发索引，可以设置默认值代替。5、不要使用全模糊匹配，条件字段不要使用函数表达式。

### 11-22：mysql中常用的函数和语法



### 12-22：spring的aop代理类手动触发代理调用

我们知道，spring的aop功能将需要被代理的类创建了代理类，当调用方法时走的是代理类的增强方法，一个典型的场景就是事务控制。一般当A类调用B类的方法时才会触发代理类的方法，因为这样调用时走的就是代理对象的方法。当A类的方法调用A类自己的另一个方法时，由于是内部调用，不会走代理方法。此时需要用AopContext.currentProxy();获取当当前的代理类调用此方法才能走代理方法。这样调用的方式实现了在类的内部调用方法也能触发代理的功能。这样就不必新写一个类来触发代理了。这个还是挺常用的


### 01-10：python语法小结（主要总结其比较特别的地方）

- **可以用三个“（双引号）或者‘（单引号）来定义多行字符串**，，，这点有点类似于html等标记语言中的多行注释； 字符串之间也可以用 +  号拼接
- **变量不需要声明类型，直接就可以写 字母 表示变量 然后对其赋值**（都是弱类型语言，连 js 都要用 var 来声明，，你这连var都省了）
- **在一个字符串中，一个放置在末尾的反斜杠表示字符串将在下一行继续，但不会添加新的一行**（指的就是，代码中字符串若想变成2行，只需将断开的地方 加一个 ”\“符号，字符串就可以写道第二行，但在打印时仍为1行）
- **原始字符串**：即：在 字符串之前加上一个 R或r 表示这个字符串不需要任何处理的字符串，比如正则表达式 或字符串中包含一些 转义符时 这样用就不会对字符串进行转义处理，原封不动的显示
- **逻辑行与物理行**   物理行指的是用户编辑的每一行；逻辑行指的是python所看到的单个语句；默认情况下Python会假定每一物理行会对应一个逻辑行；想要一个物理行对应多个逻辑行则在每条语句后面加上**分号**，想要多个物理行对应一个逻辑行就在每物理行末尾加上**\\**

### 1，消息发送接收端设置语言偏好不生效的问题

问题复现：送货单提交之后发送站内消息。消息参数都有设置多语言，消息模板都有配置多语言版本。但是我在接收人账号将语言偏好设置为英语之后，接到的消息仍然是中文的。

问题排查：检查了消息发送的代码，没有发现哪里与规范约定的不一样，只能将矛头指向消息服务。在申请到消息服务源代码之后，发现消息发送时获取接收人语言偏好的逻辑是：查询redis的用户缓存，并没有直接查询数据库的用户表。因此在前台修改语言偏好到刷新缓存会存在一个时间差。另外我使用的账号是一个多人共享的账号，同一时间我切换语言时，可能别人也会切换语言，就会造成影响。

问题推断：由于消息服务查询接收人语言偏好是通过缓存的方式，生效存在延时问题，在还未生效这期间，由于其他人也在使用这个测试账号，导致其他人登陆后修改了语言偏好，但是我前端页面没有刷新，所以还是英文界面，就造成了后端数据库里仍然是中文，但是我页面是英文的现象，然后等他缓存刷新时，刷进去的当然就还是中文，所以发送过来的消息仍然是中文格式。

问题验证：使用了一个独享账号，切成英文，等待缓存刷新后，创建送货单并提交，发送消息，收到的消息是英文格式。验证推断正确。

### 2，开发的需求中某些点有疑问一定要找业务确认，不能偷懒，不能跟着自己的感觉走，不然肯定就是一个生产问题过来

### 3，适配器引用了未上线的组件，结果需求提前发版，导致运行报错的问题

适配器切环境同步过去之后先试运行，没问题之后再启用。可以先在开发环境造一份测试数据保存。

### 4，适配器 注意要点

​	1，要操作的表（不论是查询还是修改），都要求必须是适配器定义所在的服务里面有对应表的mapper。
​	2，适配器的入参和出参，都必须是“对象”，不能是集合类型的参数。
​	3，适配器里面做 updateOptional操作时，传入的对象必须有“租户id”，“主键id”，“版本号”3个参数。
​	4，后面的修改参数对应为java 的可变数组（此功能11月27上线）
​

### 5，工作流经验总结：

​	1，spuc服务启动工作流会调用工作流服务，工作流服务启动一个流程事例，并在同一线程里执行审批规则获取审批人的任务，当获取审批人任务执行完成，工作流服务才会返回spuc服务响应信息，之后spuc服务执行剩下的逻辑，并提交事务。
​	2，给某个节点设置一个监听器，若类型为“创建”，则当前节点设置审批人时就会执行，若有多个审批人，则执行多次；若类型为：“完成”，则当前节点审批人执行审批之后就会执行，若有多个审批人，则执行多次；若类型为“离开”，则离开当前节点时会执行，只会执行一次，不管审批通过还是拒绝都会执行。

## ES6学习经验总结：
1，Object.freeze()方法。冻结传入的变量。让变量变成只读，不再准许修改。heconst的不同之处就是：const声明的对象内部属性还可以修改，但是这个也不能改。

2，js中用来遍历对象的所有属性（字段）可以使用：for/in。如：for(let key in obj){ let name = obj[key].name; let age = obj[key].age; }。 用for/of 来遍历 数组，map，set等集合。如：for(let v of arr){ console.log(v); }。 这两种遍历的区别就在于 前者拿到的是索引，然后通过中括号的方式取值。 后者是直接拿到的就是值。

3，用 == 做比较时，会自动隐式类型转换。会将左右两边的转换为数字类型进行比较  所以某些看似 为真的比较 会得到 false 的结果；比如： 10 == true 。 非0即为true。这一点我们都知道，所以会觉得这个式子为true， 但是 他在比较的时候会将true 转换为数字类型：1. 所以10不等于1，就得到false的结果。记住：**比较的时候会将左右两边的转换为数字类型进行比较**

4，ES6中的一些原生方法：

 	1. Object.is(‘2/aaaa’,NaN)  判断 前者是否为后者
 	2. Number.isNaN(2/aaa)   判断变量是否为 NaN
 	3. parseInt('222', 10)  类型转换，字符串转为数字
 	4. parseFloat('7777.9876').toFixed(3)   类型转换，字符串转为数字, 保留小数位
 	5. Math.max.apply(null, [1,2,3,4,5])   以一个数组为变量传值，取最大的值
 	6. Math.max(1,2,3,4,5)  可以传多个值，取最大值
 	7. Math.random() 取0到1之间的随机数 左闭右开
 	8. new Date('2020-05-26 20:40:20').valueOf() 创建日期  取时间戳
 	9. console.table(divs) 以表格的形式打印
 	10. Array.from(param) 将传入参数 转化为 一个数组



5，js中将2个数组 合并成 1个 数组  最快的方法：arr = [...arrA,...arrB] （arrA和arrB是另外两个数组）

6，...arr 三个点是展开语法，类似于将数组展开成逗号分隔的形式，总之可以把它当数组也可以当成展开

7，数组的基本操作：1：结尾追加 push()；2：结尾弹出 pop()；3：前面压入：unshift()；4：前面弹出：shift()；5：数组填充或者填充指定位置：fill(param,begin=0,end=arr.length)；

8，数组的slice()函数和 splice()函数：

​	slice() 函数的作用是 截取原数组，返回被截取的部分的一个新数组 ，原数组不变

​		`let arr = [1,2,3,4,5,6].slice(0,3);  console.log(arr);//[1,2,3]`

​	第一个参数是起始位置，第二个位置是结束位置，左闭右开

​	splice()函数的作用是 截取原数组，返回被截取的部分的一个新数组，原数组会减少，并且可以在截取的位置向原数组添加元素。这样可以用这个函数实现对数组的增删改一系列操作

​		`let arrOld =  [1,2,3,4,5,6]; let arr =arrOld.splice(1,2,'kun','lu'); console.log(arr);//[2,3]   console.log(arrOld);//[1,'kun','lu',4,5,6]`

​		第一个参数是 数组截取的起始位置，第二个参数是截取的个数，第三个参数开始 就是添加的元素 （可以用展开语法快速赋值）

9， 清空数组最安全便捷的做法是： arr.length = 0;  有时候只是为了让引用变量清空，数组不清空的话，可以赋值空对象的方式：arr=[]；

10，数组的every方法和 some方法还有filter方法： every方法是数组全部满足条件就返回true，否则返回 false。some方法 是数组至少有一个满足条件就返回true，否则返回false。filter方法是将满足条件的元素过滤出来，返回一个新数组。这些方法和java的stream流式api类似。这些方法的参数函数 可以传入3个参数：第一个是数组元素对象、第二个是数组元素对应的索引，第三个是数组对象本身。

11，数组的reduce方法，可用于线性统计，获取对象最大值 等场景（Math.max()方法只能 获取简单的值类型的最大值，显然这个方法可以适应更复杂的情况）。arr.reduce(function(pre,cur,index,arr){  retrun value;  },init) 。 用法：init 为初始值，遍历开始时会赋值给pre，cur为数组的元素，index为对应的索引，arr为数组本身对象。 返回值value会赋值给pre在遍历下一个元素时传入。

12，Array.from 方法可以将 Set 结构转为数组。

```js
//js数组去重场景： 
const arr = [1,2,3,4,2,3,4];
//创建Set进行去重：
const set = new Set(arr);
//将Set转换为数组：
let uniqueArr = Array.from(set); //uniqueArr= [1,2,3,4]
```

