---
layout: post
title: OpenNebula的架构
category: cloud
tags: [openNebula]
keywords: OpenNebula
description: OpenNebula的架构

---
<p><p align="left">OpenNebula是一款为云计算而打造的开源工具箱。它允许你与Xen，KVM或VMware ESX一起建立和管理私有云，同时还提供Deltacloud适配器与Amazon EC2相配合来管理混合云。除了像Amazon一样的商业云服务提供商，在不同OpenNebula实例上运行私有云的Amazon合作伙伴也同样可以作为远程云服务供应商。</p>
<p align="left">目前版本，可支持XEN、KVM和VMware，以及实时存取EC2和 ElasticHosts，它也支持印象档的传输、复制和虚拟网络管理网络。</p>
<p style="text-align: center;"> <a href="http://blog.javachen.com/files/2011/07/one-cloud.png"><img class="size-full wp-image-2106 alignnone" title="one cloud" src="http://blog.javachen.com/files/2011/07/one-cloud.png" alt="" width="332" height="166" /></a></p>
<p align="center">图1  OpenNebula总体架构图</p>
<p align="left">OpenNebula可以构建私有云、混合云、公开云。<!--more--></p></p>

<p><h2>私有云</h2>
私有云的目的是给本地的用户和管理员提供了一个灵活和敏捷的私人基础设施，以在可管理的域内运行虚拟化服务。 OpenNebula虚拟基础设施暴露虚拟化、网络、图像和物理资源的配置、管理、监督和会计的功能接口。
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/private-cloud1.png"><img class="size-medium wp-image-2107 alignnone" title="private cloud" src="http://blog.javachen.com/files/2011/07/private-cloud1-300x187.png" alt="" width="300" height="187" /></a></p>
<p align="center">图2 私有云</p>
一个OpenNebula私有云为平台基础设施的用户提供了一个快速交付和可扩展性的平台，以满足最终用户的动态需求。服务托管在虚拟机，然后提交，监视和被云控制，通过使用OpenNebula运营中心或OpenNebula的任何接口。
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/private-cloud2.png"><img class="size-medium wp-image-2108 aligncenter" title="private cloud2" src="http://blog.javachen.com/files/2011/07/private-cloud2-300x206.png" alt="" width="300" height="206" /></a></p>
<p align="center">图3 私有云内用户视图</p>
&nbsp;
<h2>混合云</h2>
<p align="left">OpenNebula提供Deltacloud适配器与Amazon EC2相配合来管理混合云。</p>
<p style="text-align: center;"> <a href="http://blog.javachen.com/files/2011/07/hyb-cloud2.png"><img class="size-medium wp-image-2109 aligncenter" title="hyb cloud2" src="http://blog.javachen.com/files/2011/07/hyb-cloud2-300x203.png" alt="" width="300" height="203" /></a></p>
<p align="center">图4 混合云</p>
&nbsp;
<h2>公开云</h2>
<p align="left">OpenNebula公有云是私有云的一个扩展，是在私有云的基础上对外暴露REST接口。如果你要让合作伙伴或外部用户能够访问您的基础设施，或出售你的服务，云接口可以被添加到您的私有或混合云。显然，一个本地的云解决方案是任何公共云自然后端。</p>
<p style="text-align: center;"> <a href="http://blog.javachen.com/files/2011/07/public-cloud.png"><img class="size-medium wp-image-2110 aligncenter" title="public cloud" src="http://blog.javachen.com/files/2011/07/public-cloud-300x175.png" alt="" width="300" height="175" /></a></p>
<p align="center">图5 公开云</p>
<p align="left">OpenNebula的构架包括三个部分：驱动层、核心层、工具层。驱动层直接与操作系统打交道，负责虚拟机的创建、启动和关闭，为虚拟机分配存储，监控物理机和虚拟机的运行状况。核心层负责对虚拟机、存储设备、虚拟网络等进行管理。工具层通过命令行界面/浏览器界面方式提供用户交互接口，通过API方式提供程序调用接口。</p>
<p style="text-align: center;"><a href="http://blog.javachen.com/files/2011/07/3-cloud.png"><img class="size-medium wp-image-2111 aligncenter" title="3 cloud" src="http://blog.javachen.com/files/2011/07/3-cloud-300x178.png" alt="" width="300" height="178" /></a></p>
<p align="center">图6 三层架构图</p>
<p style="text-align: center;"> <a href="http://blog.javachen.com/files/2011/07/3-cloud2.png"><img class="size-medium wp-image-2112 aligncenter" title="3 cloud2" src="http://blog.javachen.com/files/2011/07/3-cloud2-300x181.png" alt="" width="300" height="181" /></a></p>
<p align="left">OpenNebula使用共享存储设备（例如NFS）来提供虚拟机映像服务，使得每一个计算节点都能够访问到相同的虚拟机映像资源。当用户需要启动或者是关闭某个虚拟机时，OpenNebula通过SSH登陆到计算节点，在计算节点上直接运行相对应的虚拟化管理命令。这种模式也称为无代理模式，由于不需要在计算节点上安装额外的软件（或者服务），系统的复杂度也相对降低了。</p>
<p style="text-align: center;"> <a href="http://blog.javachen.com/files/2011/07/one-services.png"><img class="size-medium wp-image-2113 aligncenter" title="one services" src="http://blog.javachen.com/files/2011/07/one-services-300x189.png" alt="" width="300" height="189" /></a></p>
<p align="center">图7 前端节点和集群节点之间交互</p>
&nbsp;
<h2>网络架构</h2>
OpenNebula使用桥连接来构建虚拟网络，每个节点的IP和MAC地址在一定范围内生成。一个网络会连接到一个特定的桥。每一个网络有他自己的拥有者并且可以对外公开或私有。每一个虚拟网络之间是相互隔离的。</p>

<p>虚拟网络里使用Ebtables来过滤数据链路层数据包。</p>

<p>&nbsp;
<div class="infor">
<h2>参考资料</h2>
* 1.虚拟化管理软件比较 －－ 构架篇：<a href="http://www.qyjohn.net/?p=1263">http://www.qyjohn.net/?p=1263</a>
* 2.opennebula.org：<a href="http://opennebula.org/">http://opennebula.org/</a>
* 3.OpenNebula Workshop：<a href="http://hpc.uamr.de/wissen/opennebula-workshop/">http://hpc.uamr.de/wissen/opennebula-workshop/</a>
</div></p>
