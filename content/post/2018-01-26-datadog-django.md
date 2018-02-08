---
layout: post
title: datadog 监控docker中Django应用
modified: 2018-01-26
categories: articles
keywords: [datadog, apm, django, docker]
image:
  #feature: so-simple-sample-image-1.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
comments: true
share: true
---
# 使用datadog中的apm监控容器中django应用

由于最近工作需要，了解了一下[Datadog](https://www.datadoghq.com/)。照着文档对`cassandra``Django`进行了监控。看了一下这方面的中文教程基本没有，并且还是有一些坑，记录一下。这篇博客主要是对于Django。


## 概念

简答介绍下Datadog中的一些概念。


## 部署

`Datadog Agent`是我们获取数据的服务。


