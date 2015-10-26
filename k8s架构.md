# Kubernetes 架构 #

一个K8s集群由agents和master构成：下图展示了我们希望的K8s最终的状态，但是我们正在将k8s的全部组件运行在容器中，这样可以使k8s组件100%可插拔

![](http://i.imgur.com/3ePNjvx.png)

# The Kubernetes Node 节点 #

在k8s系统架构中，我们将把控制层面划分为运行在工作节点的服务和和运行控制平面的服务两种。
The Kubernetes node 节点必须运行应用容器，并且被master管理，每个节点运行 Docker, Docker关心images和containers

# Kubelet #
The Kubelet 管理 pods 以及 他们的 containers, their images, their volumes等等

# Kube-Proxy #
每个节点运行一个简单的网络代理和负载平衡器（详情参见服务FAQ）。这反映了服务（详情请参阅服务API DOC）中定义的每个节点上的Kubernetes可以做简单的TCP和UDP流转发为一组后台。译者个人理解就是提供被互联网用户访问应用容器的代理。

Service endpoints are currently found via DNS or through environment variables (both Docker-links-compatible and Kubernetes {FOO}_SERVICE_HOST and {FOO}_SERVICE_PORT variables are supported). These variables resolve to ports managed by the service proxy.

目前通过DNS服务终结点或通过环境变量(包括 Docker-links-compatible and Kubernetes {FOO}_SERVICE_HOST and {FOO}_SERVICE_PORT variables are supported). 这些变量是由服务代理托管的端口来解决。

# The Kubernetes Control Plane 控制平面 #

Kubernetes控制平面由若干组件组成。目前，它们都运行在一个单一的主节点，但预计将很快改变，以支持高可用性集群。这些组件一起工作，以提供一个统一的视图的集群。

# etcd #
所有持久master状态存储在etcd的一个实例,这提供了一种可靠的存储来保存配置数据，有了watch支持，协同组件可以很快得到通知

# Kubernetes API Server  #


apiserver 提供了Kubernetes Api, 他是一个 CRUD-y 服务,多数业务逻辑在其中实现


他主要处理REST 操作,验证以及etcd的更新

# Scheduler #
  调度器绑定未被调度的pod到node通过API。调度程序是可插拔的，我们希望支持多集群调度和用户自己定义的调度。

# Kubernetes Controller Manager Server #


所有其他群集级别的功能由控制器管理器执行。例如，Endpoints的创建和更新通过 endpoints controller，nodes被发现，管理和监控通过node controller。这些最终可能会分裂成单独的组件使他们可以独立可插拔。

replicationcontroller 是一种简单的分层 pod API机制，我们最终计划将做成一个通用的插件