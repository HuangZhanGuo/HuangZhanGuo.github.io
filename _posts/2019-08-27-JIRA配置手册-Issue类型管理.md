---
layout: photo
title: JIRA配置手册：问题类型管理
tags:
- Scrum
- JIRA
categories: 
- JIRA
- Scrum
description: JIRA是Atlassian公司出品的项目与事务跟踪工具，被广泛应用于缺陷跟踪、客户服务、需求收集、流程审批、任务跟踪、项目跟踪和敏捷管理等工作领域。
---

> JIRA的Issue管理

<!-- more -->

作者：Misha 

来源：原力注入

[**你想学习Java ？资源都在这里，点击查看**](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485700&idx=1&sn=5f1792227f31f73c9310eb45e4011795&chksm=9bd0a49caca72d8a60f530c8f23f6ecb0d8c69b715350075a43ebf5b79fb16eb5d3ad9ed701b&scene=21#wechat_redirect)

最近在工作中频繁接触Jira，从一脸懵逼的小白摸索成为一个准管理员，爬了不少坑，特此整理如下，望有助于在座各位~

\* 注：以下配置基于 JIRA 7.1.9

**项目管理**



新建项目后首先需要配置的内容包括：问题类型方案、工作流配置方案、字段配置方案、问题类型界面方案，一般完成这四个配置项目即可使用，如果还需要其它的，可以继续配置这个项目的权限、问题安全方案、通知方案等，还包括其它管理内容，如模块、项目角色。



这里先说明问题类型方案、工作流配置方案、字段配置方案、问题类型界面方案的配置规范。



下面是项目和问题类型、界面、工作流、字段之间的相互关系图：



![img](https://huangzhanguo.github.io/blog_img/jira_config/640.webp)

 图 1项目与方案关系图



- **1.1 创建项目**



一个项目是对一系列相关问题的综合管理。在Jira 中，可以通过以下方式创建项目。首先，需要具有项目创建权限的人登录后台管理界面，然后选择项目，通过创建项目按钮进入到项目创建的界面。



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (1).webp)

图 2 进入项目管理页



进入到项目管理界面，点击页面右上角“添加项目” 按钮，显示如下图：选择项目所属类别和相关信息点击提交，即可创建一个新项目。



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (2).webp)

图 3 选择项目



一般的，可以根据项目实际的用途，创建以上不同项目的类别。所选类型基本上没有太大差异，只是会根据你所选的类型，显示一些已配置的基本方案，比如包含标准类型的问题类型、工作流、界面、通知方案、权限方案等。



填写项目的名称、键值、以及项目的负责人即可创建。



如果需要对项目名称、键值修改，可以在Jira 管理后台的项目列表中找到本项目，点击对应的编辑即可，如下图：



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (3).webp)

图 4 编辑项目



注意：项目的名称、键值和项目负责人在本项目管理配也可以进行变更。另外对于键值的变更，在 jira6 之前的版本中是不允许修改的，在 jira6 后续的版本中得到了支持。键值是对一个项目的标识，比如DEVOPS ,所有项目中的问题的编号都将以DEVOPS进行打头。



项目创建后，一般会将系统会将默认的问题类型方案、工作流方案、界面方案、字段配置方案、权限方案、通知方案等默认分配到这个项目中。这些默认的配置，可能不是我们需要的，这时就需要我们对项目进行个性化的配置。



- **1.2 问题类型**



**1.2.1 新建问题类型**



具有系统管理权限的人员登录进入jira 管理中，选择问题模块，即可进入到问题类型管理界面，如下图：



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (4).webp)

图 5问题类型管理



点击添加问题类型按钮，在弹出的会话框中填写问题类型的名称即可。



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (5).webp)图 6 新建问题类型



注意:标准问题类型是指可以直接创建的问题类型，子任务类型不能单独创建，需要在标准问题中创建。



**1.2.2 新建问题类型方案**



一个项目是不能直接引用问题类型的，它需要引用一个问题类型方案。问题方案主要是将所有的问题进行整合。



我们在后台问题菜单中的左导航点击问题类型方案的标签：



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (6).webp)

图 7 问题类型方案管理



我们新增加一个问题方案名称为: demoNew



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (7).webp)图 8 新建问题类型方案



我们填写名称，然后从右边已创建的可用的问题类型中选择出几个我们这个项目所需要的问题类型，从左边拖动到左边即可，也可以对已拖进来的进行排序，以便我们在创建问题的时候下拉问题类型的时候有序的进查找。



从右边拖到左边后，可以在上面的默认问题类型中选择我们创建时最常用是哪一个问题类型。信息没有问题后，点击下方的保存即。



如果保存后需要进行修改，可以在问题类型方案列表中，找到刚才新建的，在列表中选择编辑即可。



**1.2.3 问题类型方案关联项目**



问题类型方案我们创建好了，那么我们需要将它关联到我们刚才创建的项目（SC）中， 在问题类型方案列表中，我们找到刚刚创建的方案（demoNew），点击关联的按钮，点击进入。



![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (8).webp)

图 9 关联问题类型方案



选择我们需要关联的项目，点击关联按钮即可。

注意：我们也可在项目管理中，查看问题类型，验证我们已经关联的问题类型方案。

![img](https://huangzhanguo.github.io/blog_img/jira_config/640 (9).webp)

图 10 关联问题类型



至于工作流，那就进入了下一个比较大的话题，今天就先不展开了~



[**近期送书，点击查看**](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485729&idx=1&sn=b930f354dd833ccc251082f95e750f18&chksm=9bd0a4b9aca72dafde4ab3cecfe44cd8c3b35c0f02cee9137d7cef74319202b02eb4e4cddc01&scene=21#wechat_redirect)



**-END-**



 **近期热文：**

- [HashMap? ConcurrentHashMap? 相信看完这篇没人能难住你！](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485701&idx=1&sn=471710ed79f15283edc0f341f17364ba&chksm=9bd0a49daca72d8bd4269f8980f412bbd5de0367e2bb9889694cedeefb9bbf8dace6165c6118&scene=21#wechat_redirect)

- ## [你真的了解lambda吗？一文让你明白lambda用法与源码分析](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485682&idx=1&sn=f3fb281b49a029b607f9377853a644bf&chksm=9bd0a56aaca72c7c8beebbea8f9471446cb444bd8e1e7d21016e906d1227e8f87770e2f8f31e&scene=21#wechat_redirect)

- ## [程序员格斗指南](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485675&idx=1&sn=2b8dd938e7efffd9562bfd8e11872ae9&chksm=9bd0a573aca72c65d2b376c22a2ef3bcce72175faebe1f5f9b3058cee5bb435619e6decefc3a&scene=21#wechat_redirect)

- ## [设计RPC接口时，你有考虑过这些吗？](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485654&idx=1&sn=5f054bd1d38413a1ab5a295d1fa72298&chksm=9bd0a54eaca72c588ff10050600b1ae6d9bedeffdbae6b7b39927b46ecff67507e2d67208b08&scene=21#wechat_redirect)

- ## [阿里巴巴硅谷 Istio 专家解读Istio 1.0 发布](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485644&idx=1&sn=4e7cce2bc626f1e5407c41235906d018&chksm=9bd0a554aca72c421bfb8a90da77e1220ba6140c643a259c724f7599e69cfed9de739345066c&scene=21#wechat_redirect)

- ## [如何用图像识别玩忍术？](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485631&idx=1&sn=247ed25e3df25e45d577d0bb398663a3&chksm=9bd0a527aca72c317fe00a6800bbd55efb3e3b2ddb8f5e60fbd738d0165c74c12c5fb19c4e65&scene=21#wechat_redirect)

- ## [来自95后的天池中间件大赛总结](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485626&idx=1&sn=d56673dc752f54cec5e276ef81af98e3&chksm=9bd0a522aca72c34e5c97f502bc96ab97bacb613d2e16dcf969027666bdff316833d97d975ec&scene=21#wechat_redirect)

- ## [为Spring Cloud Config插上管理的翅膀 ](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485616&idx=1&sn=f6e7a2a5df18b9f0722b7c747eb82d62&chksm=9bd0a528aca72c3eddb2e600b8a0dd989f2cc3702b60a54d2d74c23d251ff00df203d71b91b9&scene=21#wechat_redirect)

- ## [Spring Cloud Config采用Git存储时两种常用的配置策略](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485591&idx=1&sn=d1f39f13b7a40f8990a141103d8433f8&chksm=9bd0a50faca72c19cf66dd45dd6b378eb3ed0268bfeaadb6f223f16fa5766361712a4f8c660b&scene=21#wechat_redirect)

- ## [Hystrix降级逻辑中如何获取触发的异常？](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485575&idx=1&sn=5a48abe4ec57e4b0daf2edcd70bdeae2&chksm=9bd0a51faca72c091b1d10ad3f9c00c6f9385527f62abd64b45f7f975e60a595a0f32b076613&scene=21#wechat_redirect)

- ## [重磅剧透！阿里巴巴计划开源 Nacos，为Dubbo生态发展铺路](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485564&idx=1&sn=f8d631e35ebc5e7555a752c79a4807fd&chksm=9bd0a5e4aca72cf22b797b4bbcce61c074ef024f2bd69463502a55bcb8d08f54583673090965&scene=21#wechat_redirect)

- ## [Spring Cloud Config采用数据库存储配置内容](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485553&idx=1&sn=be1216506421b932befcf21ca64a55d0&chksm=9bd0a5e9aca72cff589721588ab806d28dc69aaa616df05d5e8b5dc6318f6c6530b94b9c9087&scene=21#wechat_redirect)

- ## [你可能会忽略的 Git 提交规范](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485537&idx=1&sn=7ae89867ba67c50b2d9eb7bf5e5fea18&chksm=9bd0a5f9aca72cef98877c22e6e7b92f115959faa1ad6b22cf804c5dac8a952d2190780315c2&scene=21#wechat_redirect)

- ## [SpringBoot应用部署于外置Tomcat容器](http://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247485515&idx=1&sn=3f135ecd12ace3be13eb7b435d03e0b4&chksm=9bd0a5d3aca72cc5e1997ef6fcf7b31d3fc76e1d4368fa77f43600720316d832c48b989aec18&scene=21#wechat_redirect)

**关注我**

![img](https://mmbiz.qpic.cn/mmbiz_jpg/R3InYSAIZkFvXwTmnwvffrdlHiajE55naQkt3Y1hODSGaQkwmVEt7ib1U2FLyUYkcSDExqicIXjqUwaoZqe8XBZdA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)