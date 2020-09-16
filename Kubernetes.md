## Kubernetes



> ##### Kubernetes是什么

+ Kubernetes是一个可移植、可扩展、开源的**容器编排工具**
+ Kubernetes由Google开源，基于Google的内部Borg项目
+ 名称Kubernetes源于希腊语，意为“舵手”或“飞行员”，一般简称为K8s



> ##### Kubernetes能做什么

+ 服务发现和负载均衡
+ 存储编排
+ 自动部署和回滚
+ 自我修复
+ 自动二进制打包
+ 密钥与配置管理



> ##### Kubernetes组件

当部署完Kubernetes之后，即可拥有一个完整的**集群**

一个Kubernetes集群由一组被称为`节点(node)`的机器组成。机器可以是物理机，也可以是虚拟机。

在这些节点上运行Kubernetes所要管理的容器化应用。

节点分为`主节点(control plane)`和`工作节点(worker node)`。

主节点管理集群中的工作节点和`Pod`。工作节点托管作为应用程序组件的Pod。



![Kubernetes组件](https://raw.githubusercontent.com/Menah3m/interview-win/master/images/Kubernetes1.png)

一个Kubernetes集群的主要组件有：

+ 控制平面组件Control Plane Components

  + kube-apiserver

    主节点上负责提供API服务的组件。

  + kube-controller-manager

    主节点上运行控制器的组件。

    逻辑上来说，每个控制器都是单独的进程，但是为了降低复杂度，它们都被编译到同一个可执行文件，并在一个进程中运行。

    这些控制器包括：

    + 节点控制器 Node Controller  ：节点出现故障时进行通知和响应
    + 副本控制器 Replication Controller ：维护正确的副本数量
    + 端点控制器 Endpoints Controller ： 加入Service和Pod
    + 服务账户和令牌控制器 Service Account & Token Controllers ： 为新的NameSpace创建默认账户和API访问令牌

  + kube-scheduler

    主节点上执行调度的组件。

    该组件对新创建的未指定运行节点的Pod进行监视，并选择节点让Pod在上面运行。

  + etcd 

    兼具一致性和高可用性的key-value数据库。

    用于保存Kubernetes集群的信息和数据。

  + cloud-controller-manager

    

+ 节点组件

  + kubelet

    节点上运行的代理。保证容器运行在Pod中。

    kubelet接收一组通过各类机制提供给它的`PodSpecs`，确保这些`PodSpecs`中描述的容器处于运行状态且健康。

    kubelet不会管理不是Kubernetes创建的容器。

  + kube-proxy

    节点上运行的网络代理，实现`service`概念的一部分。

    kube-proxy维护节点上的网络规则。允许从集群内部或外部的网络会话与Pod进行网络通信。

  + Container Runtime

    负责运行容器的软件



> 节点

Kubernetes通过将容器放入在节点上运行的Pod中来执行工作负载。

节点可以是一个虚拟机或者物理机，取决于所在的集群配置。

每个节点都包含用于运行Pod所需要的服务，这些服务由`控制面(Control Plane)`负责管理。

通常集群中会有若干个节点。在学习或者资源受限的环境中，也可能只有一个节点。

节点上的组件包括`kubelet`，`kube-proxy`和`Container runtime`



+ 节点的管理

  + 节点自注册
  + 手动管理节点

+ 节点状态

  一个节点的状态包含：

  + 地址
    + HostName
    + ExternalIp
    + InternalIp
  + 状况
  + 容量与可分配
  + 信息

  可以使用`kubectl`来查看节点状态和其他细节信息

  ```shell
  kubectl describe node <节点名称>
  ```

  