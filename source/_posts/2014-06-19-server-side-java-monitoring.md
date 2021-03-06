---
layout: post
title: Java 服务端监控方案（一. 综述篇）
date: 2014-06-19 21:18:09 +0800
comments: true
categories: Java
tags: Java, 监控, Linux, Ganglia
keywords: Java, Monitor, Linux, Server, Ganglia, Nagios, JMX, jmxtrans
description: 服务端应用的重要一环是监控，本文介绍 Java 的服务端监控解决方案，基于 Ganglia，Nagios 和 JMX。
---

换成 Octopress 后，写文章比以前简单多了，博客访问起来速度也快了不少，借
这个把过去一段时间的技术积累总结整理一下。作为开始，我准备写一个系列文
章来总结一下去年到今年在公司做的一些关于 Java 服务端监控的工作。

对于任何一个服务端应用来说，监控都是至关重要的一环。一个系统在运行过程
当中太容易出现故障，网络、存储、系统负载、软件 Bug，任何一个点出现问题
都有可能影响到整个系统的稳定运行，因此，监控必不可少。一个完善的系统监
控方案要从两个方面帮助我们：

<!--more-->

1. 不断检查各项服务的稳定性，出现问题第一时间通知相关人员
2. 记录系统运行的各项指标，帮助运维人员全面掌握系统运行状况，从而做到
   防患于未然

对于第一个方面，其实就是要做到出了故障第一时间告警，从而能让系统在半夜
出现问题的时候能一条短信把可怜的运维同学叫起来恢复服务。这件事，
[Nagios](http://www.nagios.org/)
基本上是最佳解决方案。它有花样繁多的各
种插件，并且编写自定义插件也极其简单，可以很方便地监控从操作系统到应用
的方方面面。其配置文件简洁易懂，且十分强大，稍加熟悉就可以很容易根据自
己的需要去配置。

对于第二个方面，Nagios 就不是特别适合了。它的工作方式是每隔一段时间去检
查服务是否正常，无法记录一些指标，不方便运维去观察系统各种指标的变化情
况。当然，通过一些插件似乎能实现这个功能，但是我们选择了别的解决方案：
在常用的 [Graphite](http://graphite.wikidot.com/) 和
[Ganglia](http://ganglia.sourceforge.net/) 之间，我最终选择了 Ganglia。

Ganglia 是加州大学伯克利分校发起的系统监控项目，为大规模高性能计算集群
而设计，采用了 RRD、XML 等成熟的技术实现，单节点开销很小，提供了相当靠
谱的容错机制，且很容易扩展和加入自定义的 Metric。Ganglia 的一个比较有名
的用户是维基百科，可以通过
[这里](https://ganglia.wikimedia.org/latest/) 访问他们的 Ganglia 实例，
从上面可以看到维基百科的集群运行状况。

Ganglia + Nagios，这就是我们的监控系统所选用的解决方案。不过这样做有一
个问题是两者都有自己的监控 Agent，Nagios 需要 NPRE，Ganglia 则需要
gmond。显然在每个机器上都安装两个监控系统不好，安装维护麻烦不说，也不
符合一个系统只做一件事的原则。

好在这个问题早就有人想到了，办法很简单，在两者之间建立起一座桥梁，让
Nagios 可以直接使用 Ganglia 的数据就好了。解决方案有很多，我会在这个系
列的 Nagios 部分详细介绍。

Ganglia 和 Nagios 可以直接监控到系统层面的运行状况如 CPU，Load，磁盘，
内存等，但对于 Java 应用，我们还需要额外的手段来收集数据。Java 监控的
标准解决方案是 JMX，JVM 本身的很多运行时参数都通过 JMX 暴露出来了，如
内存、GC 等相关参数，开发者也可以很简单地自定义 MBean 来将应用本身的参
数暴露出来。借助一些工具如 jmxtrans，我们可以定时获取一个 JVM 实例的
JMX 数据，并发送给 Ganglia 或者 Graphite 这种后端。在 Java 和 JMX 部分，
我会详细介绍。

最近还发现了一个名叫 [Metrics](http://metrics.codahale.com/) 的 Java 库，
可以方便开发者来编写应用监控相关代码，并且它直接支持 Ganglia 和
Graphite 后端。如果不是必须使用 JMX 的话，这会是一个更简洁的替代方案。

简要介绍就到这里了，请期待后面针对 Ganglia、Nagios 和 Java 监控的更详
细介绍。

### 系列文章导航

- [Java 服务端监控方案（二. Ganglia 篇）]({% post_url 2014-07-04-server-side-java-monitoring-ganglia %})
- [Java 服务端监控方案（三. Nagios 篇）]({% post_url 2014-07-22-server-side-java-monitoring-nagios %})
- [Java 服务端监控方案（四. Java 篇）]({% post_url 2014-08-08-server-side-java-monitoring-java %})
