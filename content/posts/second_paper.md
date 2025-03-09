---
title: "Second_paper"
subtitle: ""
date: 2025-03-08T21:25:36+08:00
lastmod: 2025-03-08T21:25:36+08:00
draft: false
author: ""
authorLink: ""
description: ""
license: ""
images: []

tags: []
categories: []

featuredImage: ""
featuredImagePreview: ""

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
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
1






1


1

1
1
1
1
1
1
1
1
1

1
1
1
1
1
1
1
1
1s
s
s
s
s
s
s
s
s
s

s
s
ss



s
s
s
s
s
s
s
s
s
s
sw

1
2
3
4
5
6
7
8
# 用于 Hugo 输出文档的设置
[outputs]
# 
home = ["HTML", "RSS", "JSON"]
page = ["HTML"] # 如果都想输出，则设置为["HTML", "MarkDown"]，页面底部有"查看网页原文"
section = ["HTML", "RSS"]
taxonomy = ["HTML", "RSS"]
taxonomyTerm = ["HTML"]
我个人是倾向不输出，所以删去了“MarkDown”字段。

文章摘要
文章摘要是用简短的一句话，对标题进行补充解释，让读者在看文章之前就对文章有个大体的印象。

详细设置部分，可参考官方文档-摘要部分 。

我选择的手动摘要，做以下步骤：

在archetypes/default.md文本内新增description字段，以描述为摘要；
新增摘要分隔符＂＂；（可参考本文default内容模板章节）
比如本篇文章的摘要设置如下：

1
description: "摘要：LoveIt主题从Github模板到个性化所需要做的重要配置"
这样每篇新建的文章只要填写description字段内容即可，注意，使用纯文本，不要加入代码块、字体加粗格式等富文本，容易引起渲染错误。

文章封面图
LoveIt主题让使用者选择是否设置文章预览图，如果想设置，在markdown文档的最开头的配置部分，填写featuredImage字段，引用的图片可以是网络URL地址的图片，也可以是本博客本地图片。

我选择的是本地图片的static方式加载(还有asset方式)，资源相对路径的起始位置是根目录下的static目录，比如本文的图片目录是static/img/Chore-Pic/LoveIt-Theme.webp，那么这个字段应该填写：

1
featuredImage: "/img/Chore-Pic/LoveIt-Theme.webp"
🤔图片的分辨率建议是1000x300或者长宽比相近的分辨率，可以避免预览被裁剪。

图片格式尽量选择webp格式(或使用ffmpeg等工具将其他图片格式转换为webp)。

webp格式的图是新一代的压缩格式，保证最大原图清晰度的情况下，尽可能地缩小了图片内存占用，加快加载速度。

Algolia站内搜索
Algolia是法国的初创公司，类似于存储云服务，计算云服务，提供的是搜索云服务，用户建立应用，接着通过sdk推送数据，然后通过sdk就可以搜索了。个人用户是免费使用，还有个最大的优点是速度快，比LoveIt主题支持的默认站内搜索工具lunar.js快了一个数量级。

按照以下步骤实施即可：

在Algolia官网注册账号后登录，或使用 GitHub、Google 帐号登录；

点击左侧边栏的OverView/dashboard，可以看到自动创建了一个App，可以重新命名，我们新建一个Index，比如命名为My_Blog，点击进去，准备上传index.json作为搜索元数据。

Algolia 为我们提供了三种方式来增加记录：手动添加、上传 json 文件、API自动添加。下面演示自动添加的方式：

生成index.json并上传：

每次更新博客内容并发布时，需要更新index.json，可以做到自动化脚本化，借助第三方工具Atomic-Algolia即可。安装方法：

安装node.js后，可以执行npm指令；

执行 npm install atomic-algolia，可以安装 atomic-algolia；

在hugo根目录修改或新建文件 package.json ，添加如下内容：

1
2
3
4
5
6
{
"scripts": {
"test": "echo \"Error: no test specified\" && exit 1",
"algolia": "atomic-algolia"
}
}
根目录下新建.env文件,添加如下内容：

1
2
3
4
ALGOLIA_APP_ID=你的Application ID
ALGOLIA_INDEX_NAME=你的algolia-index名字
ALGOLIA_INDEX_FILE=public/index.json
ALGOLIA_ADMIN_KEY=你的Admin API Key
上述的关键信息 Application ID 和 Admin API Key可以在algolia的DashBoard页面获取，“API Keys”按钮，点进去后，就能看到，前者是大概10位长度的大写英文与数字，后者大概30位的小写英文与数字。

Index的名字是自己重命名的，index-file的路径指的是你的发布路径和Hugo根目录的相对位置，上述示例是public为发布目录。

⚠️ ALGOLIA_ADMIN_KEY可以用来管理你的索引，所以最好不要提交到公共仓库。

我的做法是Hugo博客内容为Private仓库，发布的仓库kissingfire123.github.io为public，添加为Private仓库的submodule，一来隔离了隐私数据，二来避免每次更新草稿内容都触发page-deploy。

配置config.toml

找到 [params.search]模块，至少配置以下几个值(以下略去其他内容)：

1
2
3
4
5
6
7
8
[params.search]
enable = true
# 搜索引擎的类型 ("lunr", "algolia")
type = "algolia"
[params.search.algolia]
index = "Benjamin-Blog" # index-name,这个的确是我的，个人自行命名
appID = "B45S568G91" # app-id，内容我随意写的
searchKey = "c00000d11111e22222f444e2333"#admin-key，内容此处为演示，随意写的
更新索引并自动上传：npm run algolia

输出类似如下内容，“Benjamin-Blog"是我的index-name。

1
2
3
4
5
6
7
8
9
10
$ npm  run algolia

> algolia
> atomic-algolia

[Algolia] Adding 0 hits to Benjamin-Blog
[Algolia] Updating 0 hits to Benjamin-Blog
[Algolia] Removing 0 hits from Benjamin-Blog
[Algolia] 219 hits unchanged in Benjamin-Blog
{}
至此，algolia功能启用成功，使用示范如下：


评论系统Valine
LoveIt主题支持多种评论系统，例如Disqus，GitTalk，valine，前2者都需要用户登录后评论且数据托管更方便，valine不需要登录且无后端。

关于valine更详细的介绍，有兴趣的朋友可以看看valine中文文档。

下面只说如何在LoveIt主题上用起来这个评论系统：

valine基于LeanCloud登录，注册并登录；

选择国际版，不要选择华北或华东，后面这2个要上传身份证备案的，比较麻烦。(Ps：第一次我就不清楚情况，弄的华北版，各种麻烦，实名认证)

新建应用，比如我的是 Benjamin-Personal-Blog ,然后获取2个关键信息：AppID 和 AppKey。

这2个关键信息在侧边栏设置==>


sd
s
s

1
1
1
1
1
1
1
1
<!--more-->
