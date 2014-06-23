---
layout: post
title: "杭JS 小记"
date: 2014-06-23 16:28:17 +0800
comments: true
categories: ['thinking in life']
---

提要
====
   今年已经是第三届China JS峰会了， 前两次分别在北京和上海举办，今年移步到杭州举办，票价也是一年接一年的飙升，哥今年花了500大洋参加，不过总体来说还是值得的， 有学到新的方法， 技术， 认识新的朋友， 扩大了知识面。主要可以分为以下几部分： 
   
* Library & Framework
* 流程改善
* 工具
* 大数据
* 性能优化
* 架构设计
* 嵌入式NodeJS： EdgeJS -> CLR＋V8
* 移动设备传感器
* Hybird API
* 离奇的idea： Node OS
* 开源社区
* 程序员软实力： 时间管理
* JS 编译器 （不知道应用场景）

## Library & Framework

#### Then.JS
   这部分最精彩的莫过于`then.js`的分享，`then.js`是一个用来处理移步请求的库，据作者所说本来打算直接用`AsyncJS`， 但由于`AsyncJS`不支持链式调用，于是就整了这么一个库。从bench mark来看`then.js`确实有很大优势，主要有以下特点：  

* 体积小
* 速度快
* 使用`thunk`函数而不是用`promise`实现，不会出现栈溢出错误	

#### TroopJS

其次， 我不得不提`TroopJS`，这是一个非常好用的framework。目前已经到3.0版本，主要特点有：

* 推崇异步编程方式
* 使用声明式事件绑定
* 使用`Mixin`的方式继承
* 完善的组件生命周期管理机制
* 3.0 会支持`Web Component`

#### Rabbit.js

`Rabbit.JS` 是一个非常早期的Node MVC框架，主要目标是简化配置，实现快速开发。

#### Edge.js
`Edge.JS` 是由微软开发的一个用来填平CLR和NodeJS之间的沟壑的库。个人不太喜欢， 不解释。


## 流程

#### 淘宝的前后端分离
流程一直是小公司不太注意的问题， 尤其是创业公司，追求速度而忽略了流程的构建， 造成资源浪费，效率低下。淘宝的前后端有严重的耦合，经常是前段开发好HTML分发给后端，后端还要进行处理，甚至也要写前段代码。于是淘宝加了一个中间层NodeJS来进行解藕，主要做了以下改变：   

* 使用NodeJS产生静态页面到CDN
* 前后端路由共享
* 接口服务化
* 模块化Everything
* 组件化 （多环境）
* [框架化][1]

#### 点对点的协作 (Peer to peer collaborate)

点对点的协作是由NodeJS社区大神`SubStack`提出的，主要用于开源社区中和成千上万的`npm package`开发者协作，核心思想是:

* 功能最小化模块
* 不重复造轮子


## 工具

#### Atom 编辑器

`Atom`编辑器是由Github引领的一款转为Web Developer打造的开源编辑器，深度定制Chromium底层和NodeJS结合，程序员可以通过NodeJS来对编辑器进行自定制，相信未来应该是前段开发利器。

#### NodeJS 命令行工具
随着NodeJS越来越普及，可以用NodeJS做的事情也越来越多了，比如各种小工具。做一个小工具是如此的easy。下面的作品是来自new relic的大神 `Jacob@newrelic.com `的作品：

* `jscurl` 一个建议的类curl工具
* `node-nsh`一个类bash shell命令行
* `node-wssh` 一个类ssh的实现， 可同时用于服务器和客户端
* `node-os` 一个使用nodejs编写的操作系统，http://node-os.com 

实现一个npm package是非常的easy:

   * Run `npm init` 
   * Add a new file with node env put on the top
   * Write your function
   * Add bin config point to the file.  e.g.: {`package name`: “target js file”}
   * Run `npm link` and test with `package name`

## 大数据
大数据是近两年流行起来的一个词， 各种大数据技术层出不穷，其中以Google （Big Table）， Facebook （Hadoop）牵头， twitter （Storm）随后跟上。

#### Google Big Query
BigQuery是一个海量数据管理工具， 可以用来查询海量数据， 目前查询免费， 操作新表会收费。目前已经有NodeJS BigQuery模块。有兴趣的朋友可以搜索BigQuery了解。

#### Twitter Storm
来自twitter的一位后端女工程师带来了storm的分享，看起来很牛叉，原理类似map -> reduce, 现在也已经有NodeJS模块可以使用， 有兴趣的可以查询了解。


## 性能优化
性能优化似乎是永久不变的话题， 这也是更好的用户体验使然。Angular近年来非常的火， 但众所周知， angular使用的dirty check非常的吃性能， 这就不得不对大型的angular进行优化。
#### Angular JS 优化
以下是一位讲师的经验所得：

* 代码组织
	+ angular seed
	+ 数据共享 （Event ／ Service） 
	+ 共享template (directive)
* 性能监测
   * bindonce
   * limitTo
   * Batarang
   * 分页
* 缓存HTTP请求
* 实时数据绑定 （feed ／ Firebase ／ cloda）
* SEO
   * \#!/url/path => /?_escape_fragment_=/url/path
   * HTML5 meta fragment
  
  
## 架构设计
架构设计还是属EF Lab‘s Mikael带来的演讲非常精彩。随着前端越来越可以胜任大规模的复杂的业务逻辑， 架构设计也变得越来越重要。

Mikael的架构哲学：


   * 模块化 (Code , template, styles , data)  
      
   * 开发小的模块 (easy to read, maintain , use)  -> 分离注点
   
   * 部署 bigish 
      * 合并可重用的代码，函数和小的应用程序
    
     * 移动设备上有一个文件
      
   * 解藕

      * 向后端开发者学习

         * 事件总线:  EIP, ESB, micro services

   * 前端 ?
      * 连接components

   * 集成 

      * 订阅改变而不影响整个应用程序
      * 使用假的component进行快速开发

   * 程序异步化？ why？

      * 连接速度
      * 连接的可用性

   * 变得事件驱动化

      * Nodejs emitter
      * Dom event
      * web sockets
      * akka http://akka.io/
      * erlang  - event driven languages

   * 使程序自表达，自解释, 并且保持不重复

      * 自表达 - 方法名，链式操作， URL的结构
      * 自声明 - what do we want to do instead of how do we want to do
      * DRY - not repeat yourself
      * congnitive load （直觉加载， 一种方法学） ?? 

   * AKA - one application to rule them all ? ［一个java后台框架］

## 移动设备传感器

传感器绝对使整个大会的一个亮点， 我们每天都在用HTML5，每天都在用Mobile，但是我看没有多少人真正在意各种奇妙的传感器。未来这个技术一定会越来越火， 建议每个前端开发人员都要了解传感器的概念。

#### Hybrid API 传感器

   * 动作 

      * 陀螺仪

         * 水平仪

      * 加速计 －> acceleration or accelerationWithGravity

         * 小猫快跑

      * 距离传感

         * 锁屏


   * 环境

      * 指南针
      * GPS ＋ 磁力计
      * 智能感光 

         * Css light-level : normal …

      * 其他

         * 气压 － 海拔
         * 温度， 湿度
         * 音频 

            * 呐喊
            * 支付宝 修修修
            * 语音识别 SIRI

         * 视频

            * 试衣间 ？
            * 试戴
            * 肤色识别
            * 虚拟现实 VR？ 沉浸式技术联盟 （ITA）
            * 增强现实 AR

   * 目标

      * 功能封装
      * 跨平台使用

   * W3C device API

## Web Component  -  web开发的未来

Web Component的概念已经提出来有段时间了， 但是还没有完全兼容，它主要用来组件化各个功能Dom模块，使开发效率变高，维护成本变低。目前IE全员不支持，Ya的， 不解释。

   * 新增的template元素
      * 内容被解析， 但是不显示  
      * 图片不会下载
      * 脚本不会下载
   * `document.registerElement(‘element name’, {prototype: “prototype object”})`
   * `shadow dom`
   * import 引用 `<link rel=“import” href=“target.html”>`
   
   * polyfill
      * [polymer][4]
      * [x-tag][5]
      * Amaze UI
      
## 程序员的软实力

程序员可能天天与新技术和代码打交道，实际上我们做这一切都是未来生活， 如何平衡好生活和工作的关系对于程序员的身心健康也是非常的重要。我推荐大家多了解：

* 时间管理
* 人际关系拓展
* 关于生活的书： 《生存之道》《高效程序员习惯》
* TED： 《幸福的定义》

## 开源社区
我对开源一直持有开放的态度， 希望极可能加入到开源项目中，我觉得这会是程序员的附加价值，不仅丰富生活，还可以和全世界各地的开发者一起共事，想想就很amazing。所以我觉得每个程序员都应该选择一个自己感兴趣的领域多做共享，实现自己更高的价值。

来自开源社区贡献者[turingou][3]的分享：

* 垂直领域 `Express-scaffold`, `Theme`, `Mails`
* 有趣的  `Douban.fm`, `candy`, `o3o`
* 有成长的 `NodeJS & NPM`, `Koa`, `Smart Hardware`
* 爆发性的 `swift` `bitcoin`


结论
===
前端开发变得原来越庞大，复杂，每天都会有新技术涌现出来，我们必须时刻准备好迎接新的挑战，多关注开源项目，平衡好工作和生活。总之一句话， 未来属于前端Full Stack Developer的！


[1]:http://ued.taobao.org/blog/tag/%E5%89%8D%E5%90%8E%E7%AB%AF%E5%88%86%E7%A6%BB/
[2]: http://www.allmobilize.com/
[3]:https://github.com/turingou
[4]:http://www.polymer-project.org
[5]:http://x-tag.org



   
   