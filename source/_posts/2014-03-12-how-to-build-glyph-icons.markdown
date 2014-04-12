---
layout: post
title: "How to build Glyph icons fonts"
date: 2014-03-12 14:25:05 +0800
comments: true
categories: [think in life]
---

![Glyph ICON Fonts][7]
什么是Glyph？
-----------
> A glyph /ˈɡlɪf/ is an element of writing: An individual mark on a written medium that contributes to the meaning of what is written.
> 字形－一个可以辨认的抽象的图形符号，它不依赖于任何特定的设计

为什么要用Glyph ICON？
-------------------
*  扁平化设计
*  响应性设计
*  更灵活的调整元素的各种属性但不失真（大小，颜色）

简言之， Glyph ICon font就是一种用font的方式来设计web元素的背景。

有什么神器可以用？
--------------
* [Iconmoon][1] *一款非常实用的在线工具,但前提是你要准备好SVG*
* [Glyphsapp][3]*一款非常强大的编辑器，项目化整个ICON制作流程, 但不是免费的*
* [GlyphStudio][2] *一个免费的基于HTML5的字体编辑器*

Glyph Studio
------------
![Glyph Studio](http://glyphrstudio.com/resources/linked_shapes.png) 

* 可以在线或者离线方式访问
* 可以创建或者导入项目， 基于文件存贮
* 自由定制

使用Glyph Studio制作字体的完整流程
------------------------------
* 访问[Glyphr_Studio_Beta_3.2][4]后者直接将页面存贮到本地
* 拖拽项目文件到页面，或者点击“Start a new font from scratch”创建一个新的项目
* 设计，修改字符集（拉丁字符）
* 完成后，选择“export font”->"Generate TTX XML File",之后会生成一个以项目名命名TTX的文件
* 访问[TTX][5]安装相应软件(Windows和Mac平台均支持)
* TTX 是一个可以从XML转换OpenType或TrueType字体，或者将字体转换成XML文件 `ttx [options] inputfile1 [... inputfileN]`
* 运行`ttx [project-name].ttx`会生成ttf字体文件
* 访问[font-to-web][6]导出所有格式的字体文件（.ttf, .otf, .eot, .woff and .svg）


Notes
-----
* Mac上安装TTX时候需要提升权限 sudo
* 为了方便只用TTX， 最好在你的命令行中配置: `alias ttx=/Library/Frameworks/Python.framework/Versions/2.7/bin/ttx`,这样你就可以直接使用ttx来操作了


[1]:http://icomoon.io/
[2]:http://glyphrstudio.com/
[3]:http://www.glyphsapp.com/
[4]:http://glyphrstudio.com/download/Glyphr_Studio_Beta_3.2.html
[5]:http://www.glyphrstudio.com/ttx/
[6]:http://www.font2web.com/
[7]:http://dribbble.s3.amazonaws.com/users/2804/screenshots/641668/jrf-icons-dribbble.png
