---
layout: post
title: 从dva-1.x antd-2.x react-15.x到dva-2.x antd-3.x react-16.x
modified: 2017-12-08
categories: articles
keywords: [sample-post]
image:
  #feature: so-simple-sample-image-1.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
comments: true
share: true
---
# 从[react-admin](https://github.com/yezihaohao/react-admin)到[Ant Design Pro](https://pro.ant.design/)

本次变化，也是整体技术栈的升级
<!-- Todo -->

## **component, routes** 变化

在之前的`react-admin`中，`components`承接的是`route`的详情化。而现在`ADP`的[component](https://pro.ant.design/docs/new-component)则是用来存放公共组件的地方

## 数据流

虽然两者运用的都是`redux`的方式，但是在实际处理的时候还是有比较大的区别。我们拿获取列表数据，并且能够`filter`进行搜索举个例子。

### react-admin

发起action时候需要先在route中封装`getlist`func。接着在components 中使用

### ADP



