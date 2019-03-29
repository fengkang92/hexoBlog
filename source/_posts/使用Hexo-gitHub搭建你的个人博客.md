---
title: 使用Hexo+gitHub搭建你的个人博客
date: 2019-01-11 17:11:54
tags:
- hexo
- 博客
categories: 
- 博客
---
从开始工作到现在，大大小小做了不少项目，经验也算是积累了一点，期间也遇到了不少的坑，所以最近想自己搭建一个博客，把自己遇到的坑和总结的经验记录在上面， 并将自己以前在博客园，简书等写的文章迁移过来，也希望可以帮到更多的小伙伴。

<!--more-->

## 在本地搭建hexo博客

###  环境搭建
首先我们需要安装node，npm，git，安装方法 这里就不在说了，自行百度就好。然后我们需要一个github账号，大家可以用邮箱去注册一个，很简单的。

### 安装hexo
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

hexo的安装可以参考官网 [hexo](https://hexo.io/zh-cn/)

如果您的电脑已经安装了node和npm, 请打开终端，使用npm全局安装hexo-cli
```
$ npm install -g hexo-cli
```
进入电脑D盘或者mac电脑的documents或者随便什么地方，新建一个文件夹hexoBlog，并初始化Hexo
```
$ mkdir hexoBlog
$ cd hexoBlog
$ npm install
```
安装完之后，文件目录应该如下：
```
.
├── _config.yml       //根网站配置文件，可以在此修改关于 网站的大部分配置
├── package.json      //应用程序的信息
├── scaffolds         //模版文件夹
├── source            //资源文件夹,存放用户资源的地方
|   ├── _drafts
|   └── _posts        //博客的文章，默认有hello-world.md
└── themes            //主题文件夹，用于存放主题
|   ├── landscape     //博客默认主题
```
这样，一个最基础的博客已经安装完成。

### 运行博客
 接下来我们要让博客在浏览器上运行起来。
```
$ hexo generate
```
这条命令可以 简写成`$ hexo g`,目的是生成静态文件，将.md转化为html文件，可选参数为

选项|描述
--|:--:|--:
-d, --deploy| 文件生成后立即部署网站
-w, --watch | 监视文件变动

然后启动一个服务器，默认地址为http://localhost:4000/
```
$ hexo server
```
可简写为`$ hexo s`

然后在浏览器打开http://localhost:4000/ ，就可以看到自己的博客了

##  使用next主题
如果想美化自己的博客的话，可以选用一些主题，当然你也可以自己开发一个独一无二的博客主题。这里我们采用next主题。[next地址](https://github.com/theme-next/hexo-theme-next)
在这个github仓库中我们可以预览下同样使用这个主题的别人的博客样式。
###  下载next
克隆该主题到博客的themes文件夹下
```
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```
这时的 文件目录结构应该为：
```
.
├── _config.yml       //根网站配置文件，可以在此修改关于 网站的大部分配置
├── package.json      //应用程序的信息
├── scaffolds         //模版文件夹
├── source            //资源文件夹,存放用户资源的地方
|   ├── _drafts
|   └── _posts        //博客的文章，默认有hello-world.md
└── themes            //主题文件夹，用于存放主题
|   ├── landscape     //博客默认主题
|   └── next          //next主题
|       ├── ...       //其它文件
|       └── _config.yml   //主题配置文件，可以在此修改关于 网站的大部分配
```

### 使用next主题
 打开网站根目录下面的_config.yml 文件，注意区分主题目录下的_config.yml 文件，搜索`theme: landscape`,找到这一行，并且将`landscape`改成`next`,然后就可以了。

### 选择next主题的样式
next主题有四种样式可供选择，分别是Muse scheme，Mist scheme，Pisces scheme，Gemini scheme， 其具体样式可以在github上找到相应链接去预览下，并选择自己喜欢的。
打开`themes/next`文件夹下面的_config.yml 文件，注意这里不是根目录下面的，找到`scheme: Muse`一行，可以通过将`Muse`改为`Mist`，`Pisces`，`Gemini`来使用不同的样式，注意冒号后面的空格一定要有。

### 启动博客
在Bash依次输入以下命令
```
$ hexo clean        //删除本地已经生成的所有静态文件 
$ hexo g            //生成静态文件
$ hexo s            //启动服务器后  
```
然后在浏览器打开http://localhost:4000/ ，就可以看到博客已经穿上新衣服啦

## 部署到GitHub
### 建立GitHub仓库
这里使用的是github提供的Gitpage功能
 
新建一个名字为`< 你的用户名>.github.io`的仓库,注意 用户名一定要是自己的用户名，比如我的就是`fengkang92.github.io`哈哈。然后打开`Settings ->GitHub Pages` -->

### 部署
 以下方法均需要你已经安装git，并且在本地和github之间配置了公钥。

首先安装`hexo-deployer-git`
```
$ npm install hexo-deployer-git --save

```
然后打开根目录下的_config.yml文件，找到`deploy`项，并修改为
```
deploy:
  type: git
  repo:  
    github: git@github.com:<你的用户名>/<你的用户名>.github.io.git
    branch: [branch] #published
    message: [message]
```
选项|描述
--|:--:|--:
repo| 库（Repository）地址
branch| 分支名称，默认master
message|自定义提交信息

执行如下命令：
```
hexo clean && hexo deploy
```
前者清除站点文件，后者重新生成站点文件并将之推送到指定的库， 稍等片刻， 打开https://<你的用户名>.github.io/ 


PS： 由于刚刚大号博客，还没有加入七牛CDN等，所以这篇文章就没有图片了，等我我博客常用的东西都弄好，后续再补充图片啥的吧，哈哈。 










