# Look Back

传统部署：

早期，企业直接将应用程序部署在物理机上。由于物理机不能为应用程序定义资源使用的边界，我们也就很难合理的分配计算资源。如果多个应用程序运行在同一台物理机上，可能会：其中的一个应用程序消耗了大多数的计算资源，导致其他应用程序不能正常运行。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20230204202528416.png" alt="image-20230204202528416" style="zoom:50%;" />

虚拟化部署：

针对上述问题，虚拟化技术应运而生。用户可以在单台物理机上运行多个虚拟机，虚拟化技术使得应用程序被虚拟机相互分隔开，限制了应用之间的非法访问，提供了一定程度的安全性。每一个虚拟机可以认为是被虚拟化的物理机之上的一台完整的机器。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20230204202935559.png" alt="image-20230204202935559" style="zoom:50%;" />

容器化部署时代：

容器与虚拟机类似，但是降低了隔离层级，共享了操作系统，因此，容器可以认为是轻量级的。每个容器都有自己的文件系统，cpu，内存，进程空间等。运行应用程序所需要的资源都被容器包装，并和底层基础架构解耦。容器化的应用程序可以跨云服务商，跨Linux操作系统进行部署。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20230204203414075.png" alt="image-20230204203414075" style="zoom:50%;" />

# Kubernetes

容器是一个非常好的打包并运行应用程序的方式。在生产环境中，需要管理容器化应用程序，并且确保其不停机的运行下去。例如，一个容器故障停机，另外一个容器需要立刻启动以替补停机的容器。类似的这种对容器的管理由系统来执行会更快更好。

K8S针对此类问题，提供了容器化编排解决方案，可以非常健壮的运行分布式系统。K8S可以处理应用程序的伸缩、failover、部署模式等多种需求。

## 功能

- 服务发现和负载均衡。
- 存储编排：可以自动挂载指定的存储系统，例如 local storage，nfs，云存储。
- 自动发布和回滚。
- 自愈：重启已经停机的机器。替换，kill那些不满足自定义健康检查条件的容器。在容器就绪之前，避免调用者发现该容器。
- 密钥及配置管理：可以更新容器应用程序的密钥、配置等信息，而无需重新构建容器的镜像。

## 组件

### Master组件

master组件是集群的控制平台，主要负责集群中的全局决策，例如调度。还负责探测并响应集群事件。通常在同一台机器上运行所有的master组件，并且不在此机器上运行用户的容器。

#### kube-apiserver

#### etcd

支持一致性和高可用性的键值对存储组件，K8S集群的所有配置信息都存储在etcd上。

#### kube-scheduler

此master组件监控所有新创建但未分配到节点的pod，并且自动为pod选择一个合适的节点去运行。

#### kube-controller-manager

#### cloud-controller-manager

# K8S入门

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20230211142104356.png" alt="image-20230211142104356" style="zoom:50%;" />

上图是拥有一个master节点和六个worker节点的k8s集群。

<img src="https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20230211142506232.png" alt="image-20230211142506232" style="zoom:50%;" />

worker节点又叫作node，是虚拟机或者物理计算机，充当k8s集群中的工作计算机。每个node都有一个kubelet，它管理该节点并且与master通信。

