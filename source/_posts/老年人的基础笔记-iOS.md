---
title: 老年人的基础笔记-iOS
date: 2018-4-11 16:21:34
top: flase
tags: 
	- note
---



*太久没有动笔了，最近面临着换工作，需要复习一下早已忘记的基础知识了，考虑到老年人只有1周的记忆，所以2018年第一篇博客，献给我自己，方便以后随时查看补充。*



<!-- more -->

## 架构模式

### 什么才是好的架构

- 各部分职责清晰、单一、具体
- 具有可测性，让单元测试尽可能覆盖
- 使用成本低，维护成本也低

### MVC

两张经典的图，第一张为苹果期望中的MVC，第二张为实际项目中的MVC：

![苹果期望的MVC](/assets/ArticleImg/mvc1.png)

![现实中的MVC](/assets/ArticleImg/mvc2.png)

Cocoa中的MVC可以称作重控制器模式，期望中View与Model通过Controller进行间接联系，从而达到解耦，但是实际中View与Controller耦合非常严重，Controller异常庞大，可测试性与Debug效率很低。但是相比其他模式，代码总量偏少，门槛低，便于简单的项目进行快速开发。

### MVP

这是一张关于MVP的经典图：

![mvp](/assets/ArticleImg/mvp.png)

与MVC看似相似，实际上有很大不同，View层实际上是View也是Controller，Presenter层用来负责将Model与View/Controller进行组合，并且不会存在具体的布局，只有数据组装。一般来讲，逻辑部分的处理都在Model与Presenter层，这就造就了一个轻量级的View层，便于进行单元测试，虽然代码量多了很多很多，但是设计逻辑并不会比MVC复杂多少。

### MVVM

还是先放经典图：

![mvvm](/assets/ArticleImg/mvvm.png)

与MVP很相似，



## 类与对象



## KVC与KVO 



## 自动释放池



## Runloop



## 多线程



## 网络协议



## 事件响应与传递



## 计时器相关



## 图片解压缩



## UITableView优化

