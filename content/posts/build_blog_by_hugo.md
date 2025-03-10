---
title: "用Hugo和GitHub Pages搭建个人网站"
subtitle: "记录我的个人博客网站搭建全过程"
date: 2022-03-10T13:03:10+08:00
lastmod: 2023-01-22T13:01:10+08:00
draft: false
author: ""
authorLink: ""
description: "本文详细记录了使用 Hugo 静态网站生成器和 GitHub Pages 免费搭建个人博客的全过程。涵盖 Hugo 安装、主题配置、GitHub Pages 仓库创建、自动化部署流水线搭建，以及本地调试技巧。通过 GitHub Action 实现「源码推送 → 自动构建 → 静态页面发布」的完整 CI/CD 流程，无需服务器即可拥有高性能个人网站。"
license: ""
images: []

tags: [建站, Hugo, GitHub Pages]
categories: [个人成长]

featuredImage: "/images/build_blog_by_hugo_head.png"
featuredImagePreview: "/images/build_blog_by_hugo_pre.png"

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
## 前言
一直想搭个个人网站，但是服务器贵啊，还要买域名啥的，但是GitHub Pages功能真是我等个人开发者的福音，可以利用GitHub的域名部署一个静态网站，
相当于可以白嫖一个服务器，用来记录一些博客文章，个人分享啥的还是不错的。言归正传，静态网站生成器比较火的有Hugo、Jekyll、Gatsby等等，
试用了一下，最后选择了Hugo，其他的安装啊 环境啊，发布部署啥的都比较麻烦，Hugo最简便，Go语言开发的，部署速度特别快，配置文件，命令使用也都很简洁
折腾一天就弄好了，并整理了这边文章记录一下。

## 安装Hugo
### macOS安装Hugo
Homebrew 是一个 macOS 的软件包管理器，可以从 brew.sh 安装。命令如下：
```shell
# 使用 Homebrew 进行安装
brew install hugo

# 验证是否安装成功安装
hugo version
```
### Windows11安装Hugo
按 Win + R，输入 cmd，打开命令提示符窗口。输入命令：
```shell
# 直接下载安装
winget install Hugo.Hugo.Extended

# 验证是否安装成功安装
hugo version
```
## 创建博客站点项目仓库
接下来需要创建一个站点空项目，名字尽量取成xxx-source形式，或者xxx-blog也行，主要是这个会成为博客网站
的源代码仓库
### 生成站点项目仓库
```shell
hugo new site kun-blog
cd kun-blog
```
### Git仓库初始化和选择主题
我喜欢简洁干净的主题，LoveIt这款挺好的，还有很多主题可以在 [Hugo Themes](https://themes.gohugo.io/) 选择，最好是网上搜一下Hugo主题推荐，可以找到好用好看的主题
```shell
# git初始化仓库
git init

# 将LoveIt作为仓库子模块clone
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt
```
**如果不想 LoveIt 随版本更新，也可以下载主题的 [发布版本:(fa-regular fa-file-zipper):.zip](https://github.com/dillonzq/LoveIt/releases) 文件 并且解压放到 themes 目录.**


### 配置文件初始化
* 把主题目录下的`themes/LoveIt/archetypes/default.md`文件复制替换到项目文件夹下的`archetypes/`下面，这个文件是日后创建文章时要用到的模版；
* 把主题目录下的`themes/LoveIt/exampleSite/hugo.toml`文件复制替换掉项目文件夹下的`hugo.toml`配置文件，这个文件是网站的整体设置，里面的一些配置项后面会说到。

### 本地仓库关联Github远程仓库
在 GitHub 上创建新仓库，仓库名尽量和此项目名保持一致
```shell
# 关联远程仓库
git remote add origin https://github.com/jk5555/kunblog.git # 改为具体的仓库clone地址
git push -u origin master
```

## 创建GitHub Pages仓库
接下来需要在GitHub上创建GitHub Pages仓库，这个是用来承接构建好的静态网站文件的，并且可以通过域名访问。![repo.png](/images/build_blog_by_hugo_01.png)
如图所示，只需要创建一个仓库，命名格式为`username.github.io`，这个`username`是Github账户名称，我这里已经创建了一个，所以不能重复创建了，我的GitHub账户名称为`jk5555`，
之后我的GitHub Pages网站就可以通过 [https://jk5555.github.io](https://jk5555.github.io) 来访问。这里创建好仓库就可以不用管它了。

## 配置GitHub Action流水线
### 添加GitHub Action配置文件
在项目根文件夹下创建`.github/workflows/gh-pages.yml`配置文件，内容如下：
```yml
name: GitHub Pages

on:
  push:
    branches:
      - master  # 填写需要被构建推送的分支名
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-22.04
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          submodules: recursive  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.145.0' # 改为本地Hugo的版本号，防止兼容性问题
          extended: true # 是否启用hugo extended
          working-directory: . # 填写要构建的项目目录，我这里就是项目根文件夹

      - name: Build
        run: hugo --minify
        working-directory: . # 填写要构建的项目目录，我这里就是项目根文件夹

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/master' # 意思是如果构建的是master分支，则执行后面的推送过程
        with:
          PERSONAL_TOKEN: ${{secrets.ACTION_TOKEN}} # 这个是访问密钥，在GitHub账户里配置好即可
          PUBLISH_DIR: ./public  # 推送目录
          EXTERNAL_REPOSITORY: jk5555/jk5555.github.io # Github Pages远程仓库名
          PUBLISH_BRANCH: master # 推送到目标仓库的分支
#          force_orphan: true  # 强制清空目标分支历史（可选）
```

### 创建ACTION_TOKEN密钥
GitHub Action流水线在执行推送任务时需要得到仓库的访问授权，这个token在账户设置的 [**Personal access tokens**](https://github.com/settings/tokens) 这里配置
![token.png](/images/build_blog_by_hugo_02.png) 记得一定要勾选`repo`和`workflow`权限，token的命名定为 **ACTION_TOKEN**，这样上面添加的配置文件就能生效了。
后面只需要push博客项目仓库的文件，就能实现线上自动构建发布，速度很快。


## 本地调试或线上预览
### hugo.toml配置文件说明
这个其实配置文件里面已经有很详细的说明了，还可以看看官方的配置文档 [配置说明](https://hugoloveit.com/zh-cn/theme-documentation-basics/#3-%E9%85%8D%E7%BD%AE) 。

我这里提一下部分参数：
```toml
baseURL = "https://jk5555.github.io" # 这个填写网站地址

# 公共 git 仓库路径，仅在 enableGitInfo 设为 true 时有效
gitRepo = "https://github.com/jk5555/kun-blog"  #这个填写当前仓库的git地址，用处是可以在文章的末尾展示GitHub的变更记录，需要配合后面的开关配置使用
# 是否使用 git 信息
enableGitInfo = true # 结合上面的 gitRepo 配置，控制是否展示文章的git提交记录
# 日期格式
dateFormat = "2006-01-02" # 这个就固定填2006-01-02，别改，改了时间展示就乱码，应该是主题作者的bug
```

### default.md文件说明
前面说的复制的`default.md`这个文件是文章的配置模版，通过此模版创建文章时能继承一些预设置，比较方便；

复制过来的模版大部分都不用动，值得注意的是：
* 文章的摘要部分可以通过`description`参数配置，但是有一个`<!--more-->`分隔符，在这个分隔符之前的文本会被定义为文章摘要，他的优先级比`description`高。
* `featuredImage`和`featuredImagePreview`两个参数是用来设置文章封面图的，`featuredImage`参数展示在文章头部，`featuredImagePreview`展示在文章外部；建议图片分辨率在**1000x300**左右，不然可能会被自动截取。
* `draft`参数，有两个值：`true`和`false`，为`true`时表示文章为草稿，不会在页面上展示，为`false`时才会展示。
* `tags: []` 和 `categories: []` 参数用来给文章打标签和分类，然后就能在标签和分类菜单栏看到分类好的文章了，这个是主题自动支持的功能。

### 文章存放目录
文章的存放目录为项目根文件夹下的`content/posts`，实际上`content`目录就是用来存放文章的，下面的一级目录名称与菜单配置名称对应，这个在项目配置文件里面配置。
之后只需要往这里添加md格式的文章即可。

### 本地启动
#### 基于文章模板来创建文章
```shell
hugo new posts/first_post.md
```

#### 图标库
LoveIt 主题使用 [Font Awesome](https://fontawesome.com/) 作为图标库. 我们可以在文章中轻松使用这些图标.

从 Font Awesome 网站 上获取所需的图标 class.
```markdown
去露营啦! ：(fas fa-campground fa-fw)： 很快就回来. 
真开心! ：(far fa-grin-tears)：
```

呈现的输出效果如下:

> 去露营啦! :(fas fa-campground fa-fw): 很快就回来.
> 
> 真开心!:(far fa-grin-tears):

#### 本地启动
```shell
hugo serve
```
去查看 [http://localhost:1313](http://localhost:1313) 

修改文章时，页面会自动实时刷新。

### 线上预览
当本地修改好了之后，就可以git push 到远程仓库了，记得修改文章设置参数`draft`，置为`false`。

推送完毕后，Github Action 会自动构建并发布，大约等个几秒钟就可以在线上看到了！

## 其他辅助性工具或文档
* loveit 主题 相关语法，配置帮助文档查看：[主题文档 - 内容](https://hugoloveit.com/zh-cn/theme-documentation-content/)

* md基础语法：[Markdown 基本语法](https://hugoloveit.com/zh-cn/basic-markdown-syntax/)

* LoveIt内置支持的icon图标网站：[Font Awesome](https://fontawesome.com/)

## TODO事项
至此一个博客网站基本搭建完成，但是还有一些事情需要去做。比如：站内搜索，评论系统，SEO，站点信息统计 等等。这些优先级可以缓缓，先把内容做好，差不多了时候再去接入。


