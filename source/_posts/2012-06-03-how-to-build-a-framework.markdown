---
layout: post
title: "How to build a framework"
date: 2012-06-03 15:40
comments: true
categories: [thinking in JS]
---

If let u to build a javascript framework,how will you do?  
对比几个比较流行的jQuery等框架，不难发现，有以下几个基础部分：  
* 浏览器的兼容性检测  
* 干净，可重用的API设计  
* 有统一的基准和性能  
* 编写有好的轻量的javascript  
* 使用Github  

为了便于合作，我们必须订立号项目的目标和开发的风格,以下是一种尝试：
>
复杂性Verbose: 变量和方法名必须是有意义的，易懂和易理解  
移植性Portable: 浏览器和控制台  
显式的Explicit: 代码应该是可以快速理解的  
注释Comments: 注释需要简洁明了的。TODO 和 FIXME 可以详细一点.  
简单Simple:   保持代码简单  
缩进Indentation: 两个空格  
分号Semicolons:  添加分号以便人们可以方便压缩  
质量Quality: JsLint和读者留言!  
测试Testing: 在浏览器和控制台中都要测试  
版本控制Versioning: 使用GitHub  

高级框架结构  

