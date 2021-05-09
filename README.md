[kubeadm集群搭建步骤](./kubeadm_cluster.md)

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

1. 在所有节点上安装 Docker 和 kubeadm
2. 部署 Kubernetes Master
3. 部署容器网络插件
4. 部署 Kubernetes Node，将节点加入 Kubernetes 集群中
5. 部署 Dashboard Web 页面，可视化查看 Kubernetes 资源

# 5. 集群搭建(二进制方式)

1. 设置多台虚拟机，安装Linux
2. 初始化系统，包括防火墙等设置，保证机器之间可通信
3. 为etcd和apiserver自签证书
4. 部署etcd集群
5. 部署master组件
6. 部署node组件
7. 部署集群网络

# 6. K8s集群命令行工具kubectl

## 6.1 kubectl概述

kubectl是k8s集群的命令行工具，通过kubectl能够对集群本身进行管理，并能够在集群上进行容器化应用的安装部署。

## 6.2 命令语法

```bash
kubectl [command] [TYPE] [NAME] [flags]
command: 指定要对资源执行的操作，如create, get, describe, delete
TYPE: 指定资源类型，资源类型是大小写敏感的，开发者能够以单数、复数和缩略的形式。
例如:
	kubectl get pod pod1
	kubectl get pods pod1
	kubectl get po pod1
NAME: 指定资源的名称，名称也大小写敏感的。如果省略名称，则会显示所有的资源
例如:
	kubectl get pods
flags: 指定可选的参数。例如，可用-s 或者–server 参数指定 Kubernetes APIserver 的地址和端口
```

使用`kubelctl --help`获取更多信息

<img src="image/2.png" style="zoom:125%;" />

<img src="image/3.png" style="zoom:125%;" />

<img src="image/4.png" style="zoom:125%;" />

# 7. 核心概念 -- pod

## 7.1 pod概述

Pod 是 k8s 系统中可以创建和管理的最小单元，是资源对象模型中由用户创建或部署的最小资源对象模型，也是在 k8s 上运行容器化应用的资源对象，其他的资源对象都是用来支撑或者扩展 Pod 对象功能的，比如控制器对象是用来管控 Pod 对象的，Service 或者Ingress 资源对象是用来暴露 Pod 引用对象的，PersistentVolume 资源对象是用来为 Pod提供存储等等，k8s 不会直接处理容器，而是 Pod，Pod 是由一个或多个 container 组成

Pod 是 Kubernetes 的最重要概念，每一个Pod都有一个特殊的被称为”根容器“的 Pause容器。Pause 容器对应的镜像属于 Kubernetes 平台的一部分，除了Pause容器，每个Pod还包含一个或多个紧密相关的用户业务容器

类似于docker-compose

<img src="image/5.png" style="zoom:125%;" />

<img src="image/6.png" style="zoom:125%;" />

> Pod vs 应用

每个 Pod 都是应用的一个实例，有专用的 IP

> Pod vs 容器

一个 Pod 可以有多个容器，彼此间共享网络和存储资源，每个 Pod 中有一个 Pause 容器保存所有的容器状态， 通过管理 pause 容器，达到管理 pod 中所有容器的效果

> Pod vs 节点

同一个 Pod 中的容器总会被调度到相同 Node 节点，不同节点间 Pod 的通信基于虚拟二层网络技术实现

> Pod vs Pod

普通Pod和静态Pod

## 7.2 pod实现机制

> 资源共享

一个 Pod 里的多个容器可以**共享存储和网络**，可以看作一个逻辑的主机。共享的如namespace, groups 或者其他的隔离资源。

共享网络：多个容器共享同一 network namespace，由此在一个 Pod 里的多个容器共享 Pod 的 IP 和端口 namespace，所以一个 Pod 内的多个容器之间可以通过 localhost 来进行通信,所需要注意的是不同容器要注意不要有端口冲突即可。不同的 Pod 有不同的 IP,不同 Pod 内的多个容器之前通信，不可以使用 IPC（如果没有特殊指定的话）通信，通常情况下使用 Pod的 IP 进行通信。

共享存储：一个 Pod 里的多个容器可以共享存储卷volume，这个存储卷会被定义为 Pod 的一部分，并且可以挂载到该 Pod 里的所有容器的文件系统上。

> 生命周期短暂

Pod 属于生命周期比较短暂的组件，比如，当 Pod 所在节点发生故障，那么该节点上的 Pod会被调度到其他节点，但需要注意的是，被重新调度的 Pod 是一个全新的 Pod,跟之前的Pod 没有任何关系。

> 平坦的网络

K8s 集群中的所有 Pod 都在同一个共享网络地址空间中，也就是说每个 Pod 都可以通过其他 Pod 的 IP 地址来实现访问

## 7.3 yaml配置

```yaml
apiVersion: v1             #指定api版本，此值必须在kubectl apiversion中  
kind: Pod                  #指定创建资源的角色/类型  
metadata:                  #资源的元数据/属性  
  name: django-pod         #资源的名字，在同一个namespace中必须唯一  
  labels:                  #设定资源的标签，使这个标签在service网络中备案，以便被获知
    k8s-app: django
    version: v1  
    kubernetes.io/cluster-service: "true"  
  annotations:             #设置自定义注解列表  
     - name: String        #设置自定义注解名字  
spec:                      #设置该资源的内容  
  restartPolicy: Always    #重启策略:
                             #Always: 当容器终止退出后，总是重启容器，默认
                             #OnFailure: 当容器异常退出时才重启容器
                             #Never: 当容器终止退出后，从不重启容器
  nodeSelector:            #可以根据环境选择node，比如生产和测试环境
  containers:  
  - name: django-pod       #容器的名字  
    image: django:v1.1     #容器使用的镜像地址  
    imagePullPolicy: Always #三个选择Always、Never、IfNotPresent，每次启动时检查和更新（从registery）images的策略，
                             #Always: 每次都检查
                             #Never: 每次都不检查（不管本地是否有）
                             #IfNotPresent: 如果本地有就不检查，如果没有就拉取
    command: ['sh']        #启动容器的运行命令，将覆盖容器中的Entrypoint,对应Dockefile中的ENTRYPOINT  
    args: ["$(str)"]       #启动容器的命令参数，对应Dockerfile中CMD参数  
    env:                   #指定容器中的环境变量  
    - name: str            #变量的名字  
      value: "/etc/run.sh" #变量的值  
    resources:             #资源管理
      requests:            #容器运行时，最低资源需求，也就是说最少需要多少资源容器才能正常运行  
      cpu: 0.1             #CPU资源（核数），两种方式，浮点数或者是整数+m，0.1=100m，最少值为0.001核（1m）
        memory: 32Mi       #内存使用量  
      limits:              #资源限制  
        cpu: 0.5  
        memory: 32Mi  
    ports:  
    - containerPort: 8080    #容器开发对外的端口
      name: uwsgi          #名称
      protocol: TCP  
    livenessProbe:         #pod内容器健康检查的设置
      httpGet:             #通过httpget检查健康，返回200-399之间，则认为容器正常  
        path: /            #URI地址  
        port: 8080  
        #host: 127.0.0.1   #主机地址  
        scheme: HTTP  
      initialDelaySeconds: 180 #表明第一次检测在容器启动后多长时间后开始  
      timeoutSeconds: 5    #检测的超时时间  
      periodSeconds: 15    #检查间隔时间  
      #也可以用这种方法  
      #exec: 执行命令的方法进行监测，如果其退出码不为0，则认为容器正常  
      #  command:  
      #    - cat  
      #    - /tmp/health  
      #也可以用这种方法  
      #tcpSocket: //通过tcpSocket检查健康   
      #  port: number   
    lifecycle:             #生命周期管理(钩子)  
      postStart:           #容器运行之前运行的任务  
        exec:  
          command:  
            - 'sh'  
            - 'yum upgrade -y'  
      preStop:             #容器关闭之前运行的任务  
        exec:  
          command: ['service httpd stop']  
    volumeMounts:          #挂载设置
    - name: volume         #挂载设备的名字，与volumes[*].name 需要对应    
      mountPath: /data     #挂载到容器的某个路径下  
      readOnly: True  
  affinity:
  	nodeAffinity:										#节点亲和性
  		requiredDuringScheduingIgnoreDuringExecution: 	#硬亲和性，表示必须满足条件。软亲和性已弃用
  			nodeSelectorTerms:
  			- matchExpressions:
  				- key: env role
  				  operator: In
  				  values:
  				  - dev
  				  - test
  volumes:                 #定义一组挂载设备  
  - name: volume           #定义一个挂载设备的名字  
    #meptyDir: {}  
    hostPath:  
      path: /opt           #挂载设备类型为hostPath，路径为宿主机下的/opt
```

## 7.4 Pod的分类

Pod  有两种类型

> 普通  Pod

普通 Pod 一旦被创建，就会被放入到 etcd 中存储，随后会被 Kubernetes Master 调度到某个具体的 Node 上并进行绑定，随后该 Pod 对应的 Node 上的 kubelet 进程实例化成一组相关的 Docker 容器并启动起来。在默认情况下，当 Pod 里某个容器停止时，Kubernetes 会自动检测到这个问题并且重新启动这个 Pod 里某所有容器， 如果 Pod 所在的 Node 宕机，则会将这个 Node 上的所有 Pod 重新调度到其它节点上。

> 静态  Pod

静态 Pod 是由 kubelet 进行管理的仅存在于特定 Node 上的 Pod,它们不能通过 API Server进行管理，无法与 ReplicationController、Deployment 或 DaemonSet 进行关联，并且kubelet 也无法对它们进行健康检查。

# 8. 核心技术 -- controller

## 8.1 Replication Controller

Replication Controller(RC)是 Kubernetes 系统中核心概念之一，当我们定义了一个 RC并提交到 Kubernetes 集群中以后，Master 节点上的 Controller Manager 组件就得到通知，定期检查系统中存活的 Pod,并确保目标 Pod 实例的数量刚好等于 RC 的预期值，如果有过多或过少的 Pod 运行，系统就会停掉或创建一些 Pod.此外我们也可以通过修改 RC 的副本数量，来实现 Pod 的动态缩放功能。

```bash
kubectl scale rc nginx --replicas=5
```

由于 Replication Controller 与 Kubernetes 代码中的模块 Replication Controller 同名，所以在 Kubernetes v1.2 时， 它就升级成了另外一个新的概念 Replica Sets,官方解释为下一代的 RC，它与 RC 区别是:Replica Sets 支援基于集合的 Label selector,而 RC 只支持基于等式的 Label Selector。我们很少单独使用 Replica Set,它主要被 Deployment 这个更高层面的资源对象所使用，从而形成一整套 Pod 创建、删除、更新的编排机制。最好不要越过 RC 直接创建 Pod， 因为 Replication Controller 会通过 RC 管理 Pod 副本，实现自动创建、补足、替换、删除 Pod 副本，这样就能提高应用的容灾能力，减少由于节点崩溃等意外状况造成的损失。即使应用程序只有一个 Pod 副本，也强烈建议使用 RC 来 定义 Pod

## 8.2 Replica Set

ReplicaSet 跟 ReplicationController 没有本质的不同，只是名字不一样，并且ReplicaSet 支持集合式的 selector（ReplicationController 仅支持等式）。

Kubernetes 官方强烈建议避免直接使用 ReplicaSet，而应该通过 Deployment 来创建 RS 和Pod。由于 ReplicaSet 是 ReplicationController 的代替物，因此用法基本相同，唯一的区别在于 ReplicaSet 支持集合式的 selector。

## 8.3 Horizontal Pod Autoscaler

Horizontal Pod Autoscal(Pod 横向扩容 简称 HPA)与 RC、Deployment 一样，也属于一种Kubernetes 资源对象。通过追踪分析 RC 控制的所有目标 Pod 的负载变化情况，来确定是否需要针对性地调整目标 Pod 的副本数，这是 HPA 的 实现原理。Kubernetes 对 Pod 扩容与缩容提供了手动和自动两种模式，手动模式通过 kubectl scale命令对一个 Deployment/RC 进行 Pod 副本数量的设置。自动模式则需要用户根据某个性能指标或者自定义业务指标，并指定 Pod 副本数量的范围，系统将自动在这个范围内根据性能指标的变化进行调整。

1. 手动扩容和缩容

   ```bash
   kubectl scale deployment frontend --replicas 1
   ```

   

2. 自动扩容和缩容

HPA 控制器基本 Master 的 kube-controller-manager 服务启动参数 --horizontal-pod-autoscaler-sync-period 定义的时长(默认值为 30s),周期性地监测 Pod 的 CPU 使用率，并在满足条件时对 RC 或 Deployment 中的 Pod 副 本数量进行调整，以符合用户定义的平均Pod CPU 使用率。