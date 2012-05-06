---
layout: post
title: "JavaScript Byte-saving Technique"
date: 2012-05-06 16:46
comments: true
categories: JS
LC_ALL:	zh_CN.UTF-8
LANG: zh_CN.UTF-8
---

<h2>Byte-saving Technique</h2>
<p>
下面展示的是一系列javascript魔法,它可以剥掉一层你的JS代码!这些技术参考自"140bytes",以下所说明的技术会帮助你减少JS的代码量,但是在性能方面还有很多方面有待考究!
</p>
<ul>
	<li>
		<h3>Arguments</h3>
		<h4>使用按字母排序的一个字母一个位置的参数</h4>
		<p>
我们总是希望参数越短越好,这样可以在生命周期中尽可能的重用他们.最佳的处理方法是仅仅将他们用作一个占位符而不是总是尝试给予一个变量有意义的名字.同时使用一个字母命名法会稍微改变在单独函数中的可读性,保持一致的做法会对函数交互时的可读性有帮助!
		</p>
	</li>
</ul>