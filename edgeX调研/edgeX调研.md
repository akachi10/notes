# EDGEX调研

## 问题

### 问题 1 数据如何进入云端
可以考虑使用 App Service Configurable 
这个一个官方的configurable
https://docs.edgexfoundry.org/2.0/microservices/application/AppServiceConfigurable/

### 问题 2 如何注册一个微服务

调用官方微服务管理框架 System Management Micro Services
#数据如何从管道中读取



### 问题3 kuedge设计中的 eventBus 是否比较于EdgeX更灵活

EventBus直接与EdgeHub接触



## 资料
    在官方的微服务中提到微服务可以考虑支持k8s 或其他解决方案


```http request 
https://docs.edgexfoundry.org/2.0/microservices/system-management/Ch_SystemManagement/
```



### k8edge 架构

![img](https://upload-images.jianshu.io/upload_images/20018449-fef7e38d7d7d6080.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)





#### 云边通讯

- CloudHub 是一个 Web Socket 服务端，用于大量的 edge 端基于 websocket 或者 quic 协议连接上来。**监听云端的变化, 缓存并发送消息到 EdgeHub**。

- EdgeHub 是一个 Web Socket 客户端，负责将接收到的信息转发到各edge端的模块处理；同时将来自个edge端模块的消息通过隧道发送到cloud端。

#### 云上部分

- 用于控制 Kubernetes API Server 与边缘的节点、应用和配置的状态同步。
- DeviceController 是一个扩展的 Kubernetes 控制器，管理边缘设备，确保设备信息、设备状态的云边同步。

#### 边缘部分

- MetaManager: 模块后端对应一个本地的数据库（sqlLite），所有其他模块需要与 cloud 端通信的内容都会被保存到本地 DB 种一份，当需要查询数据时，如果本地 DB 中存在该数据，就会从本地获取，这样就避免了与 cloud 端之间频繁的网络交互；同时，在网络中断的情况下，本地的缓存的数据也能够保障其稳定运行（比如你的智能汽车进入到没有无线信号的隧道中），在通信恢复之后，重新同步数据。是**边缘节点自治能力的关键**；
- Edged: 是运行在边缘节点的代理，用于管理容器化的应用程序。算是个重新开发的**轻量化 Kubelet**，实现 Pod，Volume，Node 等 Kubernetes 资源对象的生命周期管理
- EventBus: 是一个与 MQTT 服务器（mosquitto）交互的 MQTT 客户端，为其他组件提供订阅和发布功能。
- ServiceBus: 是一个运行在边缘的HTTP客户端，接受来自云上服务的请求，与运行在边缘端的HTTP服务器交互，提供了云上服务通过HTTP协议访问边缘端HTTP服务器的能力。
- DeviceTwin:负责存储设备状态并将设备状态同步到云，它还为应用程序提供查询接口。
- ServiceBus: 是一个运行在边缘的HTTP客户端，接受来自云上服务的请求，与运行在边缘端的HTTP服务器交互，提供了云上服务通过HTTP协议访问边缘端HTTP服务器的能力。
- DeviceTwin: 负责存储设备状态并将设备状态同步到云，它还为应用程序提供查询接口。



### EdgeX

![image](https://docs.edgexfoundry.org/1.3/general/EdgeX_architecture.png)



##### CORE SERVICE 核心服务

![图像](https://docs.edgexfoundry.org/1.3/general/EdgeX_CoreServicesLayer.png)

- Core services provide the intermediary between the north and south sides of EdgeX. As the name of these services implies, they are “core” to EdgeX functionality. Core services is where most of the innate knowledge of what “things” are connected, what data is flowing through, and how EdgeX is configured resides in an EdgeX instance. Core consists of the following micro services:
  - Core data: a persistence repository and associated management service for data collected from south side objects.
    - 从南侧收集数据
  - Command: a service that facilitates and controls actuation requests from the north side to the south side.
    - 从北端到南端的请求入口
  - Metadata: a repository and associated management service of metadata about the objects that are connected to EdgeX Foundry. Metadata provides the capability to provision new devices and pair them with their owning device services.
    - 匹配新设备并记录其MataData
  - Registry and Configuration: provides other EdgeX Foundry micro services with information about associated services within EdgeX Foundry and micro services configuration properties (i.e. - a repository of initialization values).
    - 微服务的控制属性
- Core services provide intermediary communications between the things and the IT systems.

##### APPLICATION SERVICE 	

##### SUPPORTING SERVICE



##### DEVICE SERVICE

##### SECURITY

##### MANAGEMENT



### 支持协议

- Modbus
- REST
- OPC-UA
- OPC-DA
- MQTT
- BlueTooth
- 串口
- SNMP
- VIRTUAL
- VIRTUAL 自研



### 待研发的内容

#### VIRTUAL 自研模块

- 导入数据
- 模拟定时器
- 随机数生成器
- 模拟数据加盐
- 指定输出协议
- 各协议模拟器
  - 串口
  - OPC DA
  - OPC UA
  - MQTT
  - REST
  - ...