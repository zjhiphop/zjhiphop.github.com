---
layout: post
title: "emoji in web"
date: 2014-03-03 11:17
comments: true
categories: [thinking in life]
---

![Emoji][1]

### 什么是Emoji？
---------------
> emoji就是表情符号，在外国的手机短信里面已经是很流行使用的一种表情。这些符号可以让您的消息变得更有趣味并为您的生活增添亮点，在您添加Emoji键盘之后，点击空格键左边的“Globe”按钮来转换键盘，在短消息、电子邮件、注释、联系人和日程表中使用独特的Emoji图标——在使用键盘的iPhone或iPod touch的任何应用中都可以使用。

### Web Emoji
Web Emoji指的是在web网页中实现和手机Message相同功能的emoji功能。

### Web友好的Emoji编辑器
![Gmail Emoji Editor][2]

单纯的emoji的显示还是听简单的，但是如果放到编辑器中还是有很多挑战的：  

1. 编辑器中显示emoji
2. emoji和文本同时显示
3. emoji插入到光标的位置
4. 删除光标位置的emoji
5. 各个系统的emoji准确的显示

### 我的思路
1. 使用contenteditable实现简易的web编辑器
2. 动态检测鼠标位置并进行记录， 在需要插入emoji的时候，插入到保存的鼠标位置。
3. 将各个系统发送的emoji进行unicode转义，然后映射到指定的图片

看起来很美，但是其中有些部分很难实现：  

1. 获取emoji在编辑器中的位置和文本可以用相同的方法吗？
2. emoji unicode图片都一一进行单独制作吗？
3. 插入emoji到指定的光标位置可以正确的工作吗？

经过多种方法尝试，发现浏览器在获取emoji和文本光标位置的时候，用业界流行的方法无法正确获取， 这样就导致插入或者删除emoji并不是我们所期望位置的。例如连续插入多个emoji，会发现后插入的emoji竟然显示到了之前插入的emoji之前。大概看了一下gmail的源码，也找不错特别的端倪，于是只好采用比较ugly的方式：  

* 在每个emoji之后默认加入一个空格符（如： &nbsp；）， 这样浏览器就可以正确到emoji的位置，但是问题是加入本来内容就是空格的情况将不适用
* 每次插入emoji的时候，记录所插入的emoji dom元素的位置，这样在连续插入emoji的时候可以使用原生的dom api插入新的节点到后面，这样做的好处是没有多余的空格的元素
* 对于转义各个系统的emoji到unicode的问题，参考了[字符编码 unicode 及其在javascript 中的使用][3]和[Emoji Unicode Tables][4]。解决方法是：首先遍历editor内容，找出所有超出正常字符（中英文，标点符号等）显示范围的特殊字符，然后统转义到unicode 16，然后从emoji unicode table中引用对应的图片，完美解决，具体实现是：![Implement Code][7]


### 结论
对于如何正确找出emoji所在editor的位置，始终没有找到好的办法，目前只是处于能用的程度，未来计划有时间深入研究一下[Dom Mutation Observer][5]看看能不能有新的突破，欢迎大家留言讨论。对于性能问题也有过对比新浪和QQ的策略，最终觉得使用[Emoji Sprite][6]比较合适，这个也是需要改进的一个地方。

[1]:http://static.brit.co.s3.amazonaws.com/wp-content/uploads/2013/11/8-Emoji-645x644.jpg
[2]:http://www.diariocambio.com.mx/2014/media/k2/items/cache/92fd807de68e4e398db43705165d2815_XL.jpg
[3]:http://www.cnblogs.com/ecalf/archive/2012/09/04/unicode.html
[4]:http://apps.timwhitlock.info/emoji/tables/unicode
[5]:http://jsfiddle.net/zjhiphop/wFx9k/
[6]:http://jsfiddle.net/8r2b7/
[7]:https://www.diigo.com/item/p/qsobdaszcaqapqapezbddpsccq