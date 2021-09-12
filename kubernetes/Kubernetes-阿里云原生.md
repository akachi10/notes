# Kubernetes-阿里云原生

> 地址:
>
> https://edu.aliyun.com/roadmap/cloudnative

## 介绍

### 什么是云原生

> 云原生是一套指导软件开发的过程。
>
> 能最大化的利用与你的能力、发挥云的价值。
>
> 我们期望未来的软件从创生使就生在云上长在云上。

###  云原生技术范畴

- 云应用的定于与开发流程
  - 应用与镜像制作
  - CI/CD
  - 消息和Streaming
  - 数据库

- 云应用的编排与管理

  kubernetes主要解决的内容

  - 引用编排与调度
  - 服务发现与治理
  - 远程调用
  - API网关
  - Service Mesh

- 监控和可观测新

  - 监控
  - 日志
  - Tracing
  - 混沌工程

- 云原生底层技术

  - 容器运行时
  - 云原生存储技术
  - 云原生网络技术

- 云原生工具

  - 流程自动化与配置管理
  - 容器镜像库
  - 云原生安全技术
  - 云端密码管理

- Server less

  - Faas
  - Baas
  - Servicerless 计费

  serverlees 是pass的一种形态

### 理论基础

> 这些内容究其本质来源于两个理论基础
>
> 1. 不可变基础设施
>
>    目前实现：容器镜像技术
>
> 2. 应用编排理论
>
>    目前实现： 容器设计模式
>
>    ​	google提出的



> ### 意义
>
> 1. 基础设施一致性和可靠性
>
>    容器镜像
>
>    自爆行
>
>    可漂移(无论在哪里部署效果都一样)
>
> 2. 简单可预测的部署与运维
>
>    自描述自运维
>
>    流程自动化
>
>    容易水平扩展
>
>    可快速复制的管控系统与支撑组件

### 关键技术点

![image-20210908155436819](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/08/155452.png)

## 容器的基本概念

## 容器镜像

- 容器与镜像

  - 什么是容器:一个视图隔离、资源可限制、独立文件系统的进程集合

  - 容器镜像:构建一个容器需要的所有资源

    > golang镜像是基于aloine镜像构建的
    >
    > 一次构建多次运行

- 容器的生命周期

  - init进程的生命周期=容器的生命周期

- 容器项目的架构

- 容器 VS VM

## kubernetes 核心功能

- 服务的发现与负载均衡
- 自动滚动发布
- 容器自动装箱
- 配置与密文管理
- 存储编排
- 批量执行
- 自动容器恢复
- 水平伸缩



> 核心能力
>
> 1. 调度容器
> 2. 水平伸缩
> 3. 自动修复

### 架构

![image-20210908225708526](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/08/225720.png)

#### master架构

![image-20210908225807169](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/08/225808.png)

- API Server

  负责组件中的交互 

- Controller

  负责弹性扩容和修复

- Scheduler

  负责完成调度操作，找合适的节点放置容器。

- etcd

  分布式存储系统，APIServer中的内容也放在etcd中

#### Node 架构 

![image-20210908230314339](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/08/230315.png)

> NODE是正真负责业务负载的
>
> node是不会直接与用户通信的

- pod

  > pod中运行多个或一个容器，
  >
  > 同一个pod中的容器可以通过localhost相互访问对方的端口
  >
  > 提供容器共享的网络进程资源、环境变量等
  >
  > 提供容器共享的运行环境(网络、进程)

  - container

    > ![image-20210909214842502](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/214843.png)
    >
    > 容器可以指定核数量和内存大小

  - volume

    > pod中声明容器可以访问的文件目录的
    >
    > 可以挂载在一个或者**多个**容器的指定目录下
    >
    > 本地、ceph、classfs

- kubelet

  > kubelet通过API接收pod需要运行的状态
  >
  > 提交到Container Runtime 组件中在OS上创建容器所需要的环境 

  > 创建pod需要对容器网络、存储进行管理
  >
  > kubelet会call网络或者存储的插件进行操作

- Container Runtime

- Network Plugin

  - kuber proxy

    > 用ipTable

- Storage Plugin

> 例子
>
> ![image-20210909213159091](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/213200.png)
>
> 1. 通过CLI或UI提交POD给API
> 2. API写入信息到ETCD
> 3. 之后Scheduler回去看(watch)API Service
> 4. 之后Scheduler会根据内存状态写入这个pod的节点调度信息
> 5. API Server写入Scheduler的决策信息到etxd中
> 6. Kubelet 会Watch etcd然后将pod提交给C Ontainer runtime7 

#### 其他核心概念

- Deployment

  ![image-20210909215757776](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/215759.png)

  > Deployment是在pod之上的一个抽象
  >
  > 一帮用于做应用的管理

  1. 定义一组pod的副本目录、版本

  2. 通过控制器Controller维持pod的数目、回复失败的pod

  3. 通过控制器指定策略控制版本

  4. 滚动升级、重新升级、回滚

- Service

  ![image-20210909220204228](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/220205.png)

  支持多种访问方式的实现 

  1. ClusterIP
  2. NodePort
  3. LoadBalancer

- Namespaces

  ![image-20210909221255064](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/221256.png)

  - 资源需要属于一个namespaces
  - 提供集群内的逻辑隔离机制鉴权、资源额度
  - 同一个namespace中的资源命名唯一
  - 不同Namespace中的资源可重名

### API 基础知识

![image-20210909221325091](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/221326.png)

- Api使用HTTP协议发送json或yaml来执行命令
- 通过kubectl、UI、CURL进行交互

```YAML
apiVersion: v1
kind: pod
metadata:
  name: nginx
  Labels: 
    name: nginx
spec:
  containers: 
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80

```

> apiVersion:
>
> ​	API版本对应docker-compose的版本
>
> kind: 
>
> ​	描述资源
>
> metadata:
>
> ​	描述pod的名字
>
> ​	打label用去区分和管理pod实例比如蓝绿发布
>
> spec:
>
> ​	希望pod达到的预期状态
>
> ​	containers:
>
> ​	-	name: cntainers的名字
>
> ​		 image:
>
> ​		ports:
>
> ​		- containerPort: 80

#### label介绍

![image-20210909223338992](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/223340.png)

- 一组Key:value
- 可以被selector所查询
  - select color=red
- 资源集合的默认表达形式
  - 列入Service对应的一组pod

## 例子

### 玩minikube

![image-20210909230155358](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/09/230156.png)

> https://kubernetes.io/docs/tasks/tools/install-minikube/
>
> https://minikube.sigs.k8s.io/docs/start/

#### 安装

![image-20210910002638793](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/10/002640.png)

```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

##### 安装驱动程序

![image-20210910005002547](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/10/005003.png)

> 安装linux docker
>
> 这里可能要跟新yum中加入阿里云

![image-20210910010331226](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/10/010332.png)

> 执行命令加入驱动
>
> ```shell
> minikube config set driver docker
> ```



##### 启动集群

```shell
minikube start
```

> ![image-20210910010928284](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/10/010929.png)
>
> 启动失败
>
> 由于docker不能使用root用户调用balabala
>
> 解决方案:

```shell
useradd minikube
password minikube
# 修改sudo 权限
vi /etc/sudoers
#找到这行 root ALL=(ALL) ALL,在他下面添加
minikube    ALL=(ALL)       NOPASSWD:ALL
#sudo到minikube
#添加minikube到docker用户组中
sudo usermod -aG docker $USER && newgrp docker
#重新启动
minikube start
```

> 成功！
>
> ![image-20210910013348857](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/10/013350.png)

##### 阿里版本

> 由于太慢了换阿里版本
>
> https://developer.aliyun.com/article/221687

```shell
sudo rm /usr/local/bin/minikube

curl -Lo minikube https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.18.1/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
## 还是不行试试
minikube start --registry-mirror=https://f1z25q5p.mirror.aliyuncs.com
## 我的
minikube start --cpus=4 --memory=4096m --registry-mirror=https://s796m7v3.mirror.aliyuncs.com
```

##### 还是不行再折腾回原版

![image-20210910022755128](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/10/022756.png)

> **用到了代理**



##### 安装kubectl命令

> 由于我们没有安装kubectl可以用minikube kubectl来替代
>
> https://minikube.sigs.k8s.io/docs/handbook/kubectl/

```shell
alias kubectl="minikube kubectl --"
```

#### 玩命令

![image-20210910014906202](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/10/014907.png)