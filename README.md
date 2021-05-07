[集群搭建步骤](./kubeadm_cluster.md)

# 1 k8s基本介绍

kubernetes，简称 K8s，是用 8 代替 8 个字符“ubernete”而成的缩写。是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes 的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes 提供了应用部署，规划，更新，维护的一种机制。

传统的应用部署方式是通过插件或脚本来安装应用。这样做的缺点是应用的运行、配置、管理、所有生存周期将与当前操作系统绑定，这样做并不利于应用的升级更新/回滚等操作，当然也可以通过创建虚拟机的方式来实现某些功能，但是虚拟机非常重，并不利于可移植性。

新的方式是通过部署容器方式实现，每个容器之间互相隔离，每个容器有自己的文件系统 ，容器之间进程不会相互影响，能区分计算资源。相对于虚拟机，容器能快速部署，由于容器与底层设施、机器文件系统解耦的，所以它能在不同云、不同版本操作系统间进行迁移。

容器占用资源少、部署快，每个应用可以被打包成一个容器镜像，每个应用与容器间成一对一关系也使容器有更大优势，使用容器可以在 build 或 release 的阶段，为应用创建容器镜像，因为每个应用不需要与其余的应用堆栈组合，也不依赖于生产环境基础结构，这使得从研发到测试、生产能提供一致环境。类似地，容器比虚拟机轻量、更“透明”，这更便于监控和管理。

Kubernetes 是 Google 开源的一个容器编排引擎，它支持自动化部署、大规模可伸缩、应用容器化管理。在生产环境中部署一个应用程序时，通常要部署该应用的多个实例以便对应用请求进行负载均衡。

在 Kubernetes 中，我们可以创建多个容器，每个容器里面运行一个应用实例，然后通过内置的负载均衡策略，实现对这一组应用实例的管理、发现、访问，而这些细节都不需要运维人员去进行复杂的手工配置和处理。

# 2 功能与架构

## 2.1  概述

Kubernetes 是一个轻便的和可扩展的开源平台，用于管理容器化应用和服务。通过Kubernetes 能够进行应用的自动化部署和扩缩容。在 Kubernetes 中，会将组成应用的容器组合成一个逻辑单元以更易管理和发现。Kubernetes 积累了作为 Google 生产环境运行工作负载 15 年的经验，并吸收了来自于社区的最佳想法和实践。

## 2.2 功能

> 自动装箱

基于容器对应用运行环境的资源配置要求自动部署应用容器

> 自我修复( 自愈能力 )

当容器失败时，会对容器进行重启当所部署的 Node 节点有问题时，会对容器进行重新部署和重新调度当容器未通过监控检查时，会关闭此容器直到

容器正常运行时，才会对外提供服务

> 水平扩展

通过简单的命令、用户 UI 界面或基于 CPU 等资源使用情况，对应用容器进行规模扩大或规模剪裁

> 服务发现

用户不需使用额外的服务发现机制，就能够基于 Kubernetes 自身能力实现服务发现和负载均衡

> 滚动更新

可以根据应用的变化，对应用容器运行的应用，进行一次性或批量式更新

> 版本回退

可以根据应用部署情况，对应用容器运行的应用，进行历史版本即时回退

> 密钥和配置管理

在不需要重新构建镜像的情况下，可以部署和更新密钥和应用配置，类似热部署。

> 存储编排

自动实现存储系统挂载及应用，特别对有状态应用实现数据持久化非常重要存储系统可以来自于本地目录、网络存储(NFS、Gluster、Ceph 等)、公

共云存储服务

> 批处理

提供一次性任务，定时任务；满足批量数据处理和分析的场景

## 2.3 应用部署架构分类

> 无中心节点架构

GlusterFS

> 有中心节点架构

HDFS

## 2.4 集群架构

<img src="./image/1.png" style="zoom:150%;" />

> Master node

k8s 集群控制节点，对集群进行调度管理，接受集群外用户去集群操作请求

Master Node 由 API Server、Scheduler、ClusterState Store（ETCD 数据库）和Controller MangerServer 所组成

> Worker node

集群工作节点，运行用户业务应用容器

Worker Node 包含 kubelet、kube proxy 和 ContainerRuntime

> Master组件

* `apiserver` 集群统一入口，以restful方式交给etcd存储
* `scheduler` 节点调度，选择node节点应用部署
* `controller-manager` 处理集群中常规后台任务，一个资源对应一个控制器
* `etcd` 存储系统，用于保存集群相关数据

> Node组件

* `kubelet` master派到node节点的代表，管理本机容器
* `kube-proxy` 提供网络代理，负载均衡等操作

# 3 核心概念

## 3.1 pod

* 最小部署单元
* 一组容器的集合
* 共享网络
* 生命周期短暂

## 3.2 controller

* 确保预期的pod副本数量
* 无状态、有状态应用部署
* 确保所有node运行同一个pod
* 一次性任务和定时任务

## 3.3 service

* 定义一组pod的访问规则

# 4  集群搭建(kubeadm方式)

[集群搭建步骤](./kubeadm_cluster.md)

## 4.1 前置知识点

目前生产部署 Kubernetes 集群主要有两种方式：

> kubeadm

Kubeadm 是一个 K8s 部署工具，提供 kubeadm init 和 kubeadm join，用于快速部署 Kubernetes 集群。

[官方地址](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/)

> 二进制包

从 github 下载发行版的二进制包，手动部署每个组件，组成 Kubernetes 集群。Kubeadm 降低部署门槛，但屏蔽了很多细节，遇到问题很难排查。如果想更容易可控，推荐使用二进制包部署 Kubernetes 集群，虽然手动部署麻烦点，期间可以学习很多工作原理，也利于后期维护。

## 4.2 kubeadm部署方式介绍

kubeadm 是官方社区推出的一个用于快速部署 kubernetes 集群的工具，这个工具能通过两条指令完成一个 kubernetes 集群的部署：

1. 创建一个 Master 节点 kubeadm init
2. 将 Node 节点加入到当前集群中 `$ kubeadm join <Master 节点的 IP 和端口 >`

## 4.3 最终目标

（1） 在所有节点上安装 Docker 和 kubeadm

（2）部署 Kubernetes Master

（3）部署容器网络插件

（4）部署 Kubernetes Node，将节点加入 Kubernetes 集群中

（5）部署 Dashboard Web 页面，可视化查看 Kubernetes 资源