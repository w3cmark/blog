---
title: AMS：基于类json配置的管理后台前端解决方案
date: 2019-08-28 09:25:26
categories: AMS
tags: [AMS, Vue, Element-UI, 前端]
---

今天给大家分享一个开源项目——AMS，一个由唯品会开源的，基于类json配置的管理后台前端解决方案。

![logo](https://h5rsc.vipstatic.com/ams/ams-logo.png)

<!-- more -->

## 背景

我们先来回顾一下，管理后台的搭建方式。

1. 最原始的方式，前后端还未分离，前后端代码融合一起，往往是后端把整个管理后台负责了
2. 发展到前后端分离和JQ兴起，前端可以完全手写实现，也可以使用类似bootstrap这些框架进行快速搭建
3. 在发展到React/Vue这些MVVM框架的兴起，也产生了很多对应的配套，比如Element-ui、Ant Design

由于管理后台对UI要求不高以及功能通用，UI框架的使用给前端带来极大的方便，前端可以不需要再关心UI组件的实现，只需要把UI框架提供的组件像搭积木一样搭建，然后再去写数据交互逻辑，就可以比较快的实现一个管理后台。

这样看好像很完美，特别是用着高质量的UI框架，坑少～

但是！积木搭久了，你会发现还是要写不少代码：
+ UI组件代码。比如你写一个列表，拿Element-ui举例，你可能需要用到`el-table`、`el-table-column`，然后再来个分页`el-pagination`。可能90%的场景都是这样的，但是你每次都要写，即使复制，可能也要微调
+ 搭完UI，然后要写数据逻辑交互，比如请求个列表接口，梳理接口字段，把数据塞到表格，然后处理分页时的数据交互逻辑。同理，每个项目每个列表都要这样。

上面举例的只是一个列表场景，还有很多比如后台router、表单、图表、搜索、筛选、查增删改等等，这些都是很常见的后台功能吧。

有没有更简单的搭积木呢？最后这个积木能带一些常见的数据交互逻辑！

**或者你可以尝试一下AMS！！**

## AMS

AMS 是 `Admin Materiel System` 的首字母缩写，意为管理后台物料系统，是通过JSON配置的形式来快速搭建管理后台的一整套解决方案。

特性：
+ 底层基于Vue + Element-UI （AMS并不是想做一套新的UI框架）
+ 通过JSON配置的形式来快速搭建管理后
+ 内置常见的数据交互逻辑，比如查增删改

下面来看一个简单Demo：

<iframe width="100%" height="300" src="//jsrun.pro/sehKp/embedded/all/light/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>


你也可以在 在 Scrimba 上尝试 [AMS入门Demo>>](https://scrimba.com/c/cmkya6Tp)


![ams-about](/images/ams.png)

## 项目信息

开源项目地址：https://github.com/vipshop/ams

开源项目作者：唯品会团队

官方文档地址：https://vipshop.github.io/ams/
