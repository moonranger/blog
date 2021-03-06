---
layout: post
title: Space Apps 黑客马拉松
date: 2015-05-03 21:42:22 +1200
comments: true
categories: Tech
tags: hackathon, Auckland, NASA, Spaceapps
keywords: hackathon, Auckland, NASA, Spaceapps
description: 上个月 12 号，我和两个同事参加了 NASA 举办的 Space Apps 黑客马拉松活动。这辈子第一次参加类似活动，感触良多，收获也很多。
---

上个月 11、12 号，我和两个同事一起参加了 NASA 在全球各地组织的
[Space Apps](http://spaceapps.nz/) 黑客马拉松奥克兰站的活动。虽然最后
我们很遗憾地没有拿到任何奖项，但整个过程还是充满了乐趣，我也学习到了很
多，尤其是对待此类活动的心态问题，相信下次会做得更好。

我们选择的项目名叫
[River Flow](https://2015.spaceappschallenge.org/project/river-flow/)，
是一个数据可视化的项目，目标是以一种简单、符合直觉的方式呈现美国 USGC
公开的水质监控数据，供有着不同需求的大众来使用。

我们的想法是抓取 USGC 的数据存储到我们自己的数据库中，然后提供一个 API
来查询具体某个地理位置的河流水质数据。前端以动画的形式呈现这些数据，例
如用颜色深浅代表水的深度，用简单的粒子系统来模拟水的流动，且根据水的流
速来决定动画快慢，再用线条宽度来表示河流宽度等。其他的数据则通过不同的
颜色及辅助的标记来呈现。最后在界面下面放上一个可以拖动的时间轴用于观察
随着时间的变化，且支持类似『播放』的模式，以动画的形式表现数据随着时间
的变化。

<!--more-->

队伍中的开发者都是 Clojure 爱好者，所以我们选择了它作为了主要的实现语
言。后端我们选择了 Clojure 和 Elasticsearch，前端则是 ClojureScript，
Leaflet 和 D3.js。我们使用 clj-http 抓取数据并写到 Elasticsearch 中，
建立好相关的索引以便查询，然后基于 Elastic 的查询 API 来设计我们的 API
供前端使用。除此之外的主要工作量就是在前端实现绘图、动画效果等等了。

想法很好，可惜最后实现出来的只是个半成品。中途我们遇到了很多障碍，先是
第一天上午公司临时有一些紧急的事情要处理，我们的设计师 Martijn 和全栈
工程师 Isaac 都去忙公司的事情了；然后遇到的技术难题也不少，稍稍列举一
二：

1. ClojureScript 中用到的一个关键的库有 bug，加上我们对其不熟悉，只能
   退而使用 ES6
2. Elasticsearch 按字段的 group by 聚合不支持 GeoLoc 类型，只能使用
   embedded script hack 一下，将其先 `toString` 之后再聚合
3. 我们拿到的数据都是基于点的，每个点都是一个监控站，然后我们没办法很
   简单地把这些点连成一个个的河流，这对数据的呈现带来了麻烦

然后最大的麻烦还是数据。我们临时开启了一个 DigitalOcean 的 VPS 当
Server，然而在最后一晚我们准备抓取一些数据的时候，因为产生了不少流量，
VPS 被怀疑是 DDoS 攻击的源头，遭到了断网处理。到最后评判之前没能解决好
问题，导致最后我们都没能拿出一个可工作的成品。

得益于我们设计师的功力，我们还是拿出来一个小小的 Demo 展示了下我们的想
法，最后的效果并不是特别糟糕，但离我们理想中的效果还是差了太远。


## 最大的收获

自己对压力的掌控力太差，这是我参加这次黑客马拉松最大的感触。

由于第一天进展得并不顺利，第二天我们需要完成的工作还有很多。因为周末坐
公交车去 City 耗时太久，我第二天没有去现场，直接远程工作（事实证明反而
影响沟通）。一个人在家写程序调程序，遇到一个又一个的问题，和在现场的同
事沟通又不充分，自己闷头尝试解决而没有想办法绕过问题。进度就这样逐渐落
后，眼看着周日一天又快要过去了，焦虑感油然而生。

我越是焦虑，问题就解决得越慢，然后就更加焦虑，最后甚至开始产生了想放弃
的念头，倒头休息啥也不管了。好在我的队友们始终在坚持，找我讨论绕过问题
的方法，甚至提出如果实在解决不了，直接 fake 一些数据，这样至少可以在
presentation 时有个能拿出来演示的 demo。即便如此，最后因为状态不佳，写
的程序也错误百出，花了很多时间调试，走了很多弯路，直到第二天作品提交的
deadline 之前才勉强拿出一个可以展示出东西的界面，但我们想要的
visualization 效果基本没能实现。

回想那一天我所处的高压状态，是我在工作中都从未遇到过的，而这些压力，其
实全部来源于自己。

总结下来，我的感受是：

1. 黑客马拉松还是需要提前有所准备的。不用准备到具体的代码程度，但至少
   对问题本身要有足够了解，有实现思路，相关的库和工具也要至少会用。在
   黑客马拉松进行过程中尝试不了解的新技术，往往会遇到各种问题。
2. 目标要小而准，抓住核心，先快速实现一个可以工作的极简原型，然后以它
   为基础一步步添加内容。这样能给人以更强的自信心。
3. 无论如何都不要有放弃的念头。
4. 最重要的是 have fun，不要想太多，不要给自己太大压力。
