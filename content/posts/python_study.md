---
title: "Python学习手记"
subtitle: ""
date: 2023-09-15T13:01:03+08:00
lastmod: 2024-06-17T14:04:06+08:00
draft: false
author: ""
authorLink: ""
description: ""
license: ""
images: []

tags: [Python]
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

### 01-10：python语法小结（主要总结其比较特别的地方）

- **可以用三个“（双引号）或者‘（单引号）来定义多行字符串**，，，这点有点类似于html等标记语言中的多行注释； 字符串之间也可以用 +  号拼接
- **变量不需要声明类型，直接就可以写 字母 表示变量 然后对其赋值**（都是弱类型语言，连 js 都要用 var 来声明，，你这连var都省了）
- **在一个字符串中，一个放置在末尾的反斜杠表示字符串将在下一行继续，但不会添加新的一行**（指的就是，代码中字符串若想变成2行，只需将断开的地方 加一个 ”\“符号，字符串就可以写道第二行，但在打印时仍为1行）
- **原始字符串**：即：在 字符串之前加上一个 R或r 表示这个字符串不需要任何处理的字符串，比如正则表达式 或字符串中包含一些 转义符时 这样用就不会对字符串进行转义处理，原封不动的显示
- **逻辑行与物理行**   物理行指的是用户编辑的每一行；逻辑行指的是python所看到的单个语句；默认情况下Python会假定每一物理行会对应一个逻辑行；想要一个物理行对应多个逻辑行则在每条语句后面加上**分号**，想要多个物理行对应一个逻辑行就在每物理行末尾加上**\\**
