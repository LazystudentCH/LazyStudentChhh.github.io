---
layout: post
title:  "[Kubernetes]1.快速入门"
date:   2020-09-06 13:12
tags: Kubernetes
color: rgb(0,199,140)
cover: '../coverimages/k8s.jpg'
---

## 1.基本概念


**Pod**
> * Pod是K8S中能被运行的最小逻辑单元
> * 1个Pod中能够运行多个容器，他们共享UTS+IPC+NET名称空间
> * Pod和容器的关系就像是豌豆荚和豌豆的关系，一个Pod可以有多个容器

**Pod控制器**
> * Pod控制器是Pod启动的一种模板，用来保证K8S里启动的Pod能按照预期始终按照使用者的期望来运行（其中包括副本数，生命周期，健康状态检查...）
> * K8S提供了多种Pod控制器，常用的有如下几种
	> Deployment
	> DaemonSet
	> ReplicaSet
	> StatefulSet
	> Job
	> CronJob

**Name**
> 由于在K8S内部，使用“资源”来定义每一种逻辑概念（功能），所以每种资源，都应该有自己的名称
> “资源” 有API版本，类别，元数据，定义清单，状态等信息
> 名称通常定义在元数据内

**NameSpace**
> 随着项目增多、人员增加、集群规模的扩大，需要一种能够隔离K8S内部各种资源的办法，这就是名称空间
> 名称空间可以理解为K8S内部的集群组
> 不同名称空间内的资源，名称可以相同，相同名称空间内的资源，名称不可以相同
> K8S有一些默认的名称空间
> 查询K8S中的特定资源要带上相应的名称空间

**Label**
> 标签是K8S的特色管理方式，便于分类管理资源对象
> 一个标签可以对应多个资源，一个资源可以对应多个标签，他们是多对多的关系
> 一个资源可以拥有多个标签，可以实现不同维度的管理
> 标签的组成：Key=value

**Label选择器**
>  给资源打上标签后，可以使用标签过滤器过滤指定的标签
> 标签选择器目前有两个，基于等值关系（等于、不等于）和基于集合关系（存在、属于、不属于）
> 许多资源支持内嵌标签选择器字段

**Service**
>在K8S的世界中，虽然每个Pod都会被分配一个单独的IP地址，但是这个IP地址会随着Pod的销毁而消失
>Service就是用来解决这个问题的核心概念
>一个Service可以看作一组提供相同服务的Pod的对外访问接口
>Service作用于哪些Pod是通过标签选择器来实现的

**Ingress**
> Ingress是K8S集群里工作在OSI网络参考模型下，第7层的应用，对外暴漏接口
> Service只能进行第四层的流量调度，表现形式是ip+port（如果没有Ingress，只能用Iptable等方法来实现）
> Ingress可以调度不同业务域，不同URl访问路径的业务流量

## 2.核心组件

**配置中心 ETCD**

**主控节点(master)**
> * kube-apiserver服务
	> 提供了管理集群的restapi接口，包括鉴权、数据校验、集群状态变更
	> 负责其他模块之间的数据交互，承担通信枢纽功能
	> 是资源配额的入口
	> 提供完备的集群机制
> * kube-controller-manager服务
	> 控制器的管理者，由一堆控制器组成，通过apiserver监控整个集群的状态，确保集群处于预期的状态
> * kube-scheduler服务
	> 包含很多调度算法，主要功能是调度pod到适合的运算节点上

**运算节点(node)**
> * kube-kubelet服务
	> 简单的说，kubelet的主要功能就是从某个地方获取pod的期望状态（运行什么容器，副本数量等等）并调用容器平台（docker）的接口达到这个状态
	> 定时汇报当前节点状态给apiserver，以供调度的时候使用
	> 镜像和容器的清理工作，保证节点上的镜像不会占满磁盘，退出的容器不会占用太多资源
> * kube-proxy
	> 是K8S在每个节点上运行的网络代理，service资源的载体
	> 建立了pod网络和集群网络的关系
	> 常用三种流量调度模式，Userspace(废弃)，Iptables(濒临废弃)，Ipvs（推荐）
	> 负责建立和删除包括更新调度规则，通知apiserver自己的更新，或者从apiserver获取其他kube-proxy的调度规则变化来更新自己的

![enter description here](https://raw.githubusercontent.com/LazystudentCH/blogImage/master/2020/9/8/[Kubernetes]1.快速入门/1599577553379.png)

**K8S逻辑架构图**

![enter description here](https://raw.githubusercontent.com/LazystudentCH/blogImage/master/2020/9/8/[Kubernetes]1.快速入门/1599577579512.png)