---
layout: post
title: "Use OpenTag Management system"
date: 2014-04-12 10:24:01 +0800
comments: true
categories: [think in life]
---
![Qubit OpenTags][1]
### 什么是Tag管理？
----------------
这里的Tag指的是第三方的JavaScript tags，这些tag你可以用来增强网站功能，比如说Google Analytics。国内的某些大型网站可能会使用成千上万的tag，这样就需要一个中心化的Tag管理系统来统一管理一个或多个网站的tags。

### 为什么需要管理tags？
1. 统一跟踪管理
2. 随时更新版本
3. 全局性的添加，删除
4. 高可用性

### 使用[Qubit tag管理系统][2]
> Qubit Opentag is available as a self-service or managed tag manager, tailored to your business needs.

* 完全免费
* 允许百万PV每月
* 友好的UI配置管理
* 使用简单
* 服务背后使用AWS，高可用性

Qubit允许你使用一系列加载规则加载不同的tag应用于不同的网站， 并且你只需要在你的网站中引用一个可以从CDN加载的JavaScript文件就可以了。

### 使用步骤：
1. 首先你需要[注册][3]一个免费的账户
2. 创建一个tag[容器][5], 一个容器可以管理多个tag ![Qubit容器][4]
3. 设置tag加载规则，Qubit有许多默认的模版供你使用，同时你也可以自定制加载脚本。
    * **AB & Muti-Variate Tesing**
	![A/B Testing][6]
	* **Custom Script**
	![Custom Script][7]
4. 配置加载规则 [Filters][8]
5. 删除你当前使用的Tag脚本
6. 添加Qubic生成的脚本到head标签中 ![Qubit Track][9]
7. 重新测试网站，确保一切都可以正常工作
8. Done！


更多细节，可以访问：[Qubit Opentag DashBoard][10]
	



[1]:https://www.diigo.com/item/p/qsobdaszcaqaoaaoczbddpqpbo
[2]:http://www.qubitproducts.com/tag-management
[3]:http://penguininitiatives.com/register/qubit
[4]:https://www.diigo.com/item/p/qsobdaszcaqaobsrbzbddprpep
[5]:http://support.qubitproducts.com/customer/portal/articles/1115790-what-is-a-container-
[6]:https://www.diigo.com/item/p/qsobdaszcaqaocdqszbddprpqb
[7]:https://www.diigo.com/item/p/qsobdaszcaqaocerbzbddprprb
[8]:https://www.diigo.com/item/p/qsobdaszcaqaocqadzbddprpsp
[9]:https://www.diigo.com/item/p/qsobdaszcaqaocssqzbddprqbe
[10]:https://opentag.qubitproducts.com/QDashboard/