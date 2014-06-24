---
layout: post
title: "thinking in d3"
date: 2014-04-28 15:04:06 +0800
comments: true
categories: ["d3", "thinking deeply"]
---
![d3][1]

### 前记
 最近在使用D3做数据可视化开发，学到一些东西，因此写篇文章记录一下。
### D3 是什么？
> D3.js is a JavaScript library for manipulating documents based on data.
> D3.js 是一个基于数据，用于操纵文档的javascript库。

D3中含有3个D：Data, Driven, Documents。 通俗的讲，D3可以用来做一下事务：    

* 数据可视化  
* 数据交互  
* 数据实时更新

如果说大数据是一堆凌乱，毫无秩序的碎片， 那么D3就是可以让这些凌乱不堪的数据变得井然有序的指挥手。

### 为什么选择D3？

* 开源，并且在github中获得star前10位 （BSD licensed (BSD-new)）
* jQuery相似的API
* 背后有商业公司支持（New York Times）
* 社区活跃， 问题可以得到及时的解答, 以下是D3在Github中的实时状态   
	![D3-status][2]
* 方便扩展和调试，可以对Dom直接操作
* 简单，快速，高效
* 完美的动画支持
         
在对比了无数可视化library，发现D3能够满足目前项目的需求，于是果断使用。当然，如果你的银子不在乎， 可以考虑使用highchart， 目前被很多大公司使用， 非常稳定。

### 使用起来方便吗?
![实例][3]

```
// 定义边距值
var margin = {top: 20, right: 20, bottom: 30, left: 50},
    width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom;
    
var parseDate = d3.time.format("%d-%b-%y").parse;

// 定义x坐标的值类型和范围
var x = d3.time.scale()
    .range([0, width]);
    
// 定义y坐标的类型和使用范围
var y = d3.scale.linear()
    .range([height, 0]);

// 定义x轴的样式以及数据源
var xAxis = d3.svg.axis()
    .scale(x)
    .orient("bottom");
    
// 定义y轴的样式和数据源
var yAxis = d3.svg.axis()
    .scale(y)
    .orient("left");

// 定义线段的取值范围
var line = d3.svg.line()
    .x(function(d) { return x(d.date); })
    .y(function(d) { return y(d.close); });

// 向Dom body元素中加入一个svg元素并指定svg的大小
var svg = d3.select("body").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

// 加载数据源
d3.tsv("data.tsv", function(error, data) {
  
  // 数据预处理
  data.forEach(function(d) {
    d.date = parseDate(d.date);
    d.close = +d.close;
  });
  
  //定义x,y坐标领域值范围
  x.domain(d3.extent(data, function(d) { return d.date; }));
  y.domain(d3.extent(data, function(d) { return d.close; }));
  
  //添加g元素用于组合x,y轴（svg中部分元素必须置于g中才能正常显示）
  svg.append("g")
      .attr("class", "x axis")
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis);
  
  svg.append("g")
      .attr("class", "y axis")
      .call(yAxis)
    .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 6)
      .attr("dy", ".71em")
      .style("text-anchor", "end")
      .text("Price ($)");
  
  // 创建线段
  svg.append("path")
      .datum(data)
      .attr("class", "line")
      .attr("d", line);
});
```
上面的代码是不是很容易懂？ 如果你对SVG概念有个大概的了解， 并且会使用jQuery， 那么你会很容易上手。

### 如何开始学习使用？
* 首先，你需要对SVG有足够的了解和认识
 <iframe width="100%" height="300" src="http://jsfiddle.net/zjhiphop/zF38f/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
* 然后，学习D3基本语法规则
* 查看[example][10]， 实践
* 优化， 实践...

### 简单解析D3源码
为什么要看源码？我的想法是，知其然还不够，知其所以然可以让我们更快更好的发现问题并解决。同时可以让我们学习到不同的方法来创建，管理一个framework。

* 文件结构
	+ bin 基础性的配置工具，bower, uglyfy等
	+ lib 第三方的库以及版权信息
	+ src 所有D3领域编程源码，在所有的源码中，每个目录都会有一个index.js文件，用来定义所有在当前目录中的模块以及以来顺序
	+ test 所有的单元测试文件
* 开发流程（根据文件结构及构建脚本推断）
	1. 首先定义好所有领域原子模块，如： svg, xhr, event, color, geo等
	2. 对于每个原子模块在进行细分，如： svg中的线段，圆弧等基本图标属性
	3. 定义index.js来包含原子模块，并定义依赖关系
	4. 使用Make脚本测试，打包生成生产环境可以使用的代码
	5. 部署到travis CI server进行集成测试
* 工具使用
	* [smash][16] 根据index.js中定义的规则合并原子模块
	* Uglyfy 用于minify JS
	* [vows][17] 用于单元测试
	* [jsdom][18]在node环境中模拟Dom tree
在看了一些D3的源码之后，我的感觉是非常的简洁，模块化，非常值得借鉴，如果你有想法要构建一个XXX library的时候，不妨参考一些D3的源码。欣赏一下D3的构建脚本：

```
GENERATED_FILES = \
	d3.js \
	d3.min.js \
	bower.json \
	component.json

all: $(GENERATED_FILES)

.PHONY: clean all test

test:
	@npm test

src/start.js: package.json bin/start
	bin/start > $@

d3.js: $(shell node_modules/.bin/smash --ignore-missing --list src/d3.js) package.json
	@rm -f $@
	node_modules/.bin/smash src/d3.js | node_modules/.bin/uglifyjs - -b indent-level=2 -o $@
	@chmod a-w $@

d3.min.js: d3.js bin/uglify
	@rm -f $@
	bin/uglify $< > $@

%.json: bin/% package.json
	@rm -f $@
	bin/$* > $@
	@chmod a-w $@

clean:
	rm -f -- $(GENERATED_FILES)
```
虽然目前前端开发有比较成熟的Grunt， 但是在某些场景下有点过于繁琐和笨重， 这时候使用Make file会是比较好的选择。

### 学习资料
* [搜索实例][10]
* [快速入门][11]
* [SCOTT MURRAY'S TUTORIALS][4]
* [作者的个人博客][5]
* [GooleGroups][7]
* [StackOverflow][6]
* [d3 wiki][8]
* [d3 cheetsheet][9]

### 结论
D3的使用是非常优雅， 但是学习曲线非常陡峭，有很多领域知识需要学习，如果不想学习D3， 但又想快速使用D3来构建分析图表，那么可以考虑使用 [NVD3][12] 或者 [D4][13]。同时，众所周知SVG不支持<＝IE8, 那么在IE8中可以使用：

* [aight][14] 让D3可以在IE8中工作
* [R2D3][15] 同时使用Raphael和D3
* 通过特性检测选择使用不同的library `document.implementation.hasFeature
 ("http://www.w3.org/TR/SVG11/feature#BasicStructure", "1.1"); `
 
对于性能方面，如果页面节点过多，还是考虑使用canvas。另外，为了更好的使用D3，可以试用D3的领域知识加上AngularJS创建custom的component, 这样在未来的开发中可以加快开发进度， 并且拥抱web component。

[1]:https://www.diigo.com/item/p/qsobdaszcascrsodszbdocdoas
[2]:http://cl.ly/image/1z151B3o2K0U/Image%202014-04-28%20at%2015.56.13.png
[3]:https://www.diigo.com/item/p/qsobdaszcascsesaazbdocdqqd
[4]:http://alignedleft.com/tutorials
[5]:http://bl.ocks.org/mbostock
[6]:http://stackoverflow.com/questions/tagged/d3.js
[7]:https://groups.google.com/forum/?fromgroups#!forum/d3-js
[8]:https://github.com/mbostock/d3/wiki/
[9]:http://www.jeromecukier.net/wp-content/uploads/2012/10/d3-cheat-sheet.pdf
[10]:http://bl.ocksplorer.org
[11]:https://www.dashingd3js.com/
[12]:http://nvd3.org/
[13]:https://github.com/heavysixer/d4
[14]:https://github.com/shawnbot/aight
[15]:https://github.com/mhemesath/r2d3/
[16]:https://github.com/mbostock/smash
[17]:http://vowsjs.org/
[18]:https://github.com/tmpvar/jsdom
