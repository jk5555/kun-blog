---
title: "数据库索引原理"
subtitle: "索引原理简单总结"
date: 2023-04-23T16:03:46+08:00
lastmod: 2023-04-26T16:03:46+08:00
draft: false
author: ""
authorLink: ""
description: "而且在查询的时候先通过此字段的索引查找到对应数据的主键id, 然后再走聚集索引查找到主键id对应的磁盘上的具体数据!因为索引信息记录了每一条具体数据的信息, 当对表进行增删改时,肯定会破坏索引结构的完整性,此时还需要对索引信息进行维护, 增加了一些耗时, 所以性能就下降了啊!此时创建索引时会将索引字段信息和id字段信息都存入索引, 当你通过其中一个字段查找另一个索引字段的数据时, 走完了 非聚集索引流程就直接把结果返回了, 就不会走聚集索引的流程了!数据库的索引分为聚集索引和非聚集索引;"
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

当我们在查询数据库的数据时,一般情况下不走索引就是一条一条找,直到找到目标数据,时间复杂度为O(n),会比较费时,特别是当数据量很大时更是毁灭性打击! 所以我们要在数据量大的情况下给常用来查询的字段设置索引,增加查询速度! 但是索引的原理是什么?为什么就可以增加速度?

索引的底层数据结构是B+tree, 也就是平衡树, (树型结构天生就是为查询而生的)

数据库的索引分为聚集索引和非聚集索引;

**聚集索引**指的就是表主键和表这个整体(可以理解为主键id这个字段就是聚集索引), 当通过主键查询时, 先从根节点查找直到查找到具体叶节点对应的具体数据;查找次数就减少到树的层数; 相比于一个一个的找,查找次数肯定降低非常多, 时间复杂度为: log(树的层数) (数据条数)  =查找次数   这就是索引快的原因
![suoyin](/images/suoyin1.png "suoyin")

**非聚集索引**指的是手动创建非主键的其他字段的索引; 当你想通过其他字段查询数据时,为了效率可以为其创建索引! 这种索引成为非聚集索引; 它的区别就是,数据库会将此字段信息提取创建索引信息保存在磁盘上,因此会增加表的体积; 而且在查询的时候先通过此字段的索引查找到对应数据的主键id, 然后再走聚集索引查找到主键id对应的磁盘上的具体数据! 因此相对于通过主键来查找来说, 速度还是慢了一点,不过比不建索引相比肯定强太多了!

![suoyin2](/images/suoyin2.png "suoyin2")

另外,也可以看出无论如何,都是要走聚集索引的

但是有一种例外情况: 当你创建的是 覆盖索引(复合索引)时, 如两个表字段联合的索引; 此时创建索引时会将索引字段信息和id字段信息都存入索引, 当你通过其中一个字段查找另一个索引字段的数据时, 走完了 非聚集索引流程就直接把结果返回了, 就不会走聚集索引的流程了!

**还有一点就是: 当为表字段添加了索引之后,会影响表数据的增删改效率! 为什么呢?**

因为索引信息记录了每一条具体数据的信息, 当对表进行增删改时,肯定会破坏索引结构的完整性,此时还需要对索引信息进行维护, 增加了一些耗时, 所以性能就下降了啊! 特别是当索引字段越多时, 性能越低,因为每一个非聚集索引都会单独占用一部分空间, 维护起来也是相对独立嘛! 因此索引也不是建的越多越好!


