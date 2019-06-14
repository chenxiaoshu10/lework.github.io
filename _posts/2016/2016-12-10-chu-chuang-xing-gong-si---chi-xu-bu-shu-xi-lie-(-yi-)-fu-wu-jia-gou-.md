---
layout: post
title: 初创型公司-持续部署系列（一）服务架构
date: 2016-12-10 19:06:01
categories: 初创型公司运维专题
tags:
excerpt: 本人是一个运维工程师，所以不会从软件层面上去设计架构。网上很少有.net为例的持续交付，这次我们就以.net服务来说明初创型公司怎么做持续交付。...
auth: lework
---
* content
{:toc}

>本人是一个运维工程师，所以不会从软件层面上去设计架构。网上很少有.net为例的持续交付，这次我们就以.net服务来说明初创型公司怎么做持续交付。

##   本次采用最基础的可扩展的集群架构


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3629406-f2da1dc4086c0f00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这也是我建议初创型公司使用的架构，网站静态页面使用cdn缓存，与后端交互则走nginx负载层。nginx把外网流量均衡到后端各个服务器，服务器连接后端数据库，处理数据后并返回给用户。


** 如果预算允许的话，还可以加强下高可用性，比如使用下列架构。**


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3629406-cb6feeca4cae2190.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


面开始介绍线上环境持续交付的操作。测试环境稍后会说明。任何复杂的事情，画画流程图就显得清晰明了。下面请看线上持续交付的流程图


分为两步骤操作

** 第一步：编译打包** 

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3629406-42d1d70d467a89aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


编译任务由jenkins操作，而不是由开发操作，一是为了节省开发的时间，而是确保版本控制里的代码是正确的，可编译得。

此处需要用到的东西：
- git    用来拉去代码
- msbuild   用来编译代码
- 7z  用来压缩代码
- powershell   用来传送代码到后端服务器


**第二步：发布**


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/3629406-d38505975c3f2802.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打包和发布分开来的原因，一是减少代码发布时间，而是避免在发布过程中出现问题，我们在初创型公司也知道，到处都是坑，随时都会出现问题。

此处需要用到的东西：
- powershell   用来执行后端服务器上的发布脚本
- 7z  用来解压代码
- curl 用来操作consul
- bat  用来发布iis站点

对于持续交付的需求：
1. 在发布过程中，服务不能中断；采用灰度发布。
2. 提供维护界面；在出现发布错误的时候，自动的将维护界面展现给用户。
3. 当部署代码出错时，可以回滚至上一版代码。