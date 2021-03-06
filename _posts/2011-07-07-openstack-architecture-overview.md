---
layout: post
title: OpenStack架构预览
category: cloud
tags: [openStack]
keywords: openStack
description: OpenStack架构预览
---
<p><h2>What is OpenStack?</h2>
OpenStack提供开放源码软件，建立公共和私有云。 OpenStack是一个社区和一个项目，以及开放源码软件，以帮助企业运行的虚拟计算或者存储云。 OpenStackd开源项目由社区维护，包括OpenStack计算（代号为Nova），OpenStack对象存储（代号为SWIFT），并OpenStack镜像服务（代号Glance）的集合。 OpenStack提供了一个操作平台，或工具包，用于编排云。
<h2>Components of OpenStack</h2>
OpenStack当前主要有三个组件：计算，存储，镜像。<br />
OpenStack计算是一个云控制器，用来启动一个用户或一个组的虚拟实例，它也用于配置每个实例或项目中包含多个实例为某个特定项目的联网。<br />
OpenStack对象存储是一个在具有内置冗余和容错的大容量系统中存储对象的系统。对象存储有各种应用，如备份或存档数据，存储图形或视频（流媒体数据传输到用户的浏览器），储存二级或三级静态数据，发展与数据存储集成新的应用程序，当预测存储容量困难时存储数据，创造弹性和灵活的云存储Web应用程序。<br />
OpenStack镜像服务是一个查找和虚拟机图像检索系统。它可以配置三种方式：使用OpenStack对象存储来存储图像;使用亚马逊S3直接存储，或使用S3对象存储作为S3访问中间存储。
<h2>OpenStack Project Architecture</h2>
OpenStack当前包括三个子项目，三个项目相会独立，可以单独安装。<br />
• Swift 提供对象存储。这是大致类似于Rackspace云文件（从它派生）或亚马逊S3。<br />
• Glance 提供OpenStack Nova虚拟机镜像的发现，存储和检索。<br />
• Nova 根据要求提供虚拟服务。这与Rackspace云服务器或亚马逊EC2类似。<br />
将来会出现web 接口的子项目以及队列服务的子项目。
<h2>Cloud Provider Conceptual Architecture</h2>
构建自己的Iaas云环境并将其提供给用户，需要提供以下几个特性：<br />
1. 允许应用用户注册云服务、查看使用情况以及账单。<br />
2. 允许开发商和开发人员创建和存储自定义的镜像。<br />
3. 允许开发商和开发人员启动、监控、停止虚拟机实例。<br />
4. 允许操作人员配置和操作云基础设施。<!--more--></p>

<p>上面只列出了基本的4个特性，当然还有其他一些特性，将这些特性列在一起，展示如下：
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/Cloud-Provider-Conceptual-Architecture.png"><img class="size-medium wp-image-2123 aligncenter" title="Cloud Provider Conceptual Architecture" src="http://blog.javachen.com/files/2011/07/Cloud-Provider-Conceptual-Architecture-300x219.png" alt="" width="300" height="219" /></a></p>
在上面的模型中，假定了与云交互的四种人员（开发商、开发人员、操作员、用户），还定义了三层架构（表现、逻辑、资源）和两个正交领域（集成和管理）。<br />
表现层，组件与用户交互，接受并显示用户的信息。在这一层，为非开发人员提供了一个web 图形界面，为开发人员提供了API。在这一层，还存在负载均衡、控制台代理、安全、命名服务。<br />
逻辑层，为我们的云和控制功能提供情报。这层内包括部业务流程（工作流程复杂的任务），调度（确定作业对资源的映射），政策（配额等），镜像注册表（例如镜像的元数据），日志（事件和计量）。<br />
集成功能，大多数服务提供商已经有一个客户的身份和计费系统。任何云架构将需要与这些系统集成。<br />
管理层，提供一个API来管理云并提供监控功能。<br />
资源层，因为这是一个计算云，我们需要实际的计算，网络和存储资源，以提供给客户。这一层提供这些服务，他们可能是服务器，网络交换机，网络附加存储或其他资源。
<h2>OpenStack Compute Logical Architecture</h2>
OpenStack 中有两个守护进程：<br />
接收和调解API调用的WSGI应用程序 （nova-api，glance-api等等）。<br />
进行编排任务的工人守护进程（nova-compute， nova-network,，nova-schedule）。<br />
OpenStack中还包含两个组件：消息队列服务和数据库。这两个组件方便异步编排复杂的任务通过消息传递和信息共享。
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/OpenStack-Compute-Logical-Architecture.png"><img class="size-medium wp-image-2124 aligncenter" title="OpenStack Compute Logical Architecture" src="http://blog.javachen.com/files/2011/07/OpenStack-Compute-Logical-Architecture-300x220.png" alt="" width="300" height="220" /></a></p>
这个复杂的，但不是太翔实的图表可以概括为三句话：<br />
 终端用户通过nova-api 接口与Openstack 计算交互。<br />
 OpenStack计算守护进程通过队列的交换信息（行动）和数据库（信息）进行API请求。<br />
 OpenStack Glance是一个完全独立的基础上设施。
<strong>各个组件的介绍：</strong></p>

<p><code><strong>nova-api</strong></code><code>：</code>是对外的接口。OpenStack 云计算的核心控制器（CloudController定义在trunk/nova/api/ec2/cloud.py）。它提供了一个为所有的API查询（OpenStack API或EC2 API）的端点，引发多数业务流程的活动（如运行一个实例），并实施一些政策（主要是配额检查）。</p>

<p><code><strong>nova-schedule</strong></code><code><strong>：</strong></code>根据当前资源使用情况，决定计算节点分布到哪台计算节点上。目前实现很薄，目前已支持插件方式扩展，方便后面可能有采用更复杂算法。</p>

<p><code><strong>nova-compute</strong></code><code><strong>：</strong></code>接收队列中的动作，然后执行一系列的系统命令（如启动KVM实例），同时更新数据库中的状态。</p>

<p><code><strong>nova-volume</strong></code><code><strong>：</strong></code>给虚拟机分配额外持久化的存储，管理持久卷到计算实例的创建，连接和分离。</p>

<p><code><strong>nova-network</strong></code><code><strong>：</strong></code>网络管理，给虚拟机分配网络和管理，使外部 PC 可以可直接访问。它接受队列中的网络任务，然后执行任务操纵网络（如设立桥接接口或更改iptables规则）。</p>

<p><code><strong>queue</strong></code><code><strong>：</strong></code>提供了一个守护进程之间传递消息的中央枢纽。当前由 <a href="http://www.rabbitmq.com/" target="_top">RabbitMQ</a>实现，理论上可以是Python的ampqlib支持的任何AMPQ消息队列。</p>

<p><code><strong>SQL database</strong></code><code><strong>：</strong></code>存储云基础设施的编译时和运行时的状态。这包括可用的实例类型，在使用中的实例，可用的网络和项目。</p>

<p><code><strong>OpenStack Glance：</strong></code>OpenStack 单独的一个项目。
<h2>Nova Conceptual Mapping</h2>
<p align="left">OpenStack的架构示意图和目前已实现情况，蓝色是要 openstack概念上的架构图，红色是目前已实现的。</p>
上面的功能模块对应上面模型的映射：
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/Nova-Conceptual-Mapping.png"><img class="size-medium wp-image-2125 alignnone" title="Nova Conceptual Mapping" src="http://blog.javachen.com/files/2011/07/Nova-Conceptual-Mapping-300x209.png" alt="" width="300" height="209" /></a></p></p>

<p><h2>Service Architecture</h2>
管理和使用是走两个通道的。管理必须要经由 nova-api转发过去。而运行时，直接连接计算节点上的虚拟机即可。<strong></strong>
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/Service-Architecture.png"><img class="size-medium wp-image-2126 aligncenter" title="Service Architecture" src="http://blog.javachen.com/files/2011/07/Service-Architecture-300x295.png" alt="" width="300" height="295" /></a></p></p>

<p><h2>部署</h2>
部署时，除了Dashboard 必须部署在 nova-api server 上以外，所有的其它进程都可以部署在不同的机器上。
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/Service-Architecture2.png"><img class="size-medium wp-image-2127 aligncenter" title="Service Architecture2" src="http://blog.javachen.com/files/2011/07/Service-Architecture2-300x140.png" alt="" width="300" height="140" /></a></p>
OpenStack提供了基于 Puppet 的自动部署工具。经过简单配置，就可以把各个组件部署到不同机器上。
<h2>镜像管理</h2>
<p align="left">OpenStack的镜像创建并没有纳入其职责列表。<br />
你可以使用Ubuntu的已有image (https://help.ubuntu.com/community/UEC/)，或者直接重新自己通过KVM安装  ：</p>
<p align="left"><a href="http://cssoss.wordpress.com/2011/04/27/openstack-beginners-guide-for-ubuntu-11-04-image-management/">http://cssoss.wordpress.com/2011/04/27/openstack-beginners-guide-for-ubuntu-11-04-image-management/</a></p></p>

<p><h2>网络模型</h2>
Flat Network Manager, Flat DHCP Network Manager, VLAN Network Manager.<br />
VLAN Network Manager 这种方式适合于共有云。<br />
在私有云方面， IP充足，而且为了方便的互联互通，简单的Flat结构网络比较适合。<br />
OpenStack支持 Floating IPs ,该特性可以方便的通过更改IP来Failover(容错转移）或者迁移。
<div class="note">
<h2>参考文章</h2>
OpenStack 架构：<a href="http://blog.csdn.net/anghlq/article/details/6543880" target="_blank">http://blog.csdn.net/anghlq/article/details/6543880</a>
nova code:<a href="https://bugs.launchpad.net/nova" target="_blank">https://bugs.launchpad.net/nova</a>
openstack-nova-architecture：<a href="http://ken.pepple.info/openstack/2011/04/22/openstack-nova-architecture/" target="_blank">http://ken.pepple.info/openstack/2011/04/22/openstack-nova-architecture/</a>
</div></p>
