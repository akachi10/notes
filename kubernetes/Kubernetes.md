# Kubernetes

## 介绍

### 官方地址

https://kubernetes.io/

### 架构图

![image-20210809171432327](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/09/171433.png)

### 集群的组成部分 Master-node

- master

  - Api server

    通过restful的形式调用，所有针对k8s的操作都是通过Api server镜像控制的

  - controller-manager 控制管理器

    处理集群中常规的任务，一个资源对应控制器

  - scheduler 日志调度

  - etcd 存储

    用于保存集群中的状态数据

- node

  - kubelet 

    是一个agent

  - kube-proxy

    提供网络代理、网络规则、负载均衡

  - docker

    容器和操作
    
## 核心概念

### pod 

Tps

- k8s中最小的部署单元
- 一组容器的集合
- 他们是共享网络的
- pod的生命周期是短暂的

### controller

- 确保pod副本数量
- 无状态与有状态的部署
- 确保所有的node都运行同一个pod
- 一次性任务和定时任务

### Service

- 定义一组pod的访问规则

### 总结

k8s对于任务的访问的基本模式是,**通过controller编排和创建一组可以通过Service所定义的规则进行访问的Pod**



## 搭建一个kubernetes

### 规划

1. 搭建k8s环境平台规划

   1. 单master集群
   2. 通过负载均衡多master

   先搭建一个单master集群然后迁移到多master

2. 服务器硬件配置要求

   1. 服务器硬件配置

3. 搭建k8s集群部署方式

### 部署方式

1. kuberadmin

   kuberadmin是官方推出的一个快速部署kubernetes的工具

2. 二进制

### 安装要求

1. SentOS 7.x
2. 2GB RAM,2CPU,30G存储
3. 可以访问外网并且各个节点互通
4. 禁止swap分区

### 初始化操作系统

1. 禁止swap分区

   1. 删除 swap 区所有内容

   ```undefined
   swapoff -a
   ```

   2. 删除 swap 挂载，这样系统下次启动不会再挂载 swap

   ```bash
   # 注释 swap 行
   vim /etc/fstab
   ```

   3. 重启系统，测试

   ```cpp
   reboot
   ```

   4. swap 一行应该全部是 0

   ```cpp
   free -h
   				total        used        free      shared  buff/cache   available
   Mem:           3.7G        203M        3.1G        8.5M        384M        3.3G
   Swap:            0B          0B          0B
   ```

2. 安装工具

   - 安装wget

     ```shell
     yum install wget -y
     ```

3. 关闭firewalld

   - 关闭单次

     ```shell
     systemctl stop firewalld
     ```

   - 禁用

     ```shell
     systemctl disable firewalld
     ```

4. 关闭selinux

   - 关闭

     ```shell
     sed -i 's/enforcing/disabled/' /etc/selinux/config
     setenforce 0
     ```

5. 修改linux主机名称

   ```shell
   vi /etc/hostname
   ```

   

6. 在hosts中添加各个主机或创建dns服务器

   ```shell
   vi /etc/hosts
   ```

   

7. 配置内核参数，将桥接的IPv4流量传递到iptables的链

   ```shell
   cat > /etc/sysctl.d/k8s.conf << EOF
   net.bridge.bridge-nf-call-ip6tables = 1
   net.bridge.bridge-nf-call-iptables = 1
   net.ipv4.ip_forward = 1
   EOF
   
   sysctl --system
   ```

   > 需要解释

8. 同步时间服务器

   ```shell
   yum install ntpdate -y
   ntpdate time.windows.com
   ```

### 安装dockers

1. 安装docker

2. 配置监听方式

   ```shell
   vi /usr/lib/systemd/system/docker.service
   #修改ExecStart
   ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H fd:// --containerd=/run/containerd/containerd.sock
   ```

   

3. 配置docker源

   ```shell
   cat >> /etc/docker/daemon.json << EOF
   {
     "registry-mirrors": [
       "https://s796m7v3.mirror.aliyuncs.com"
     ]
   }
   EOF
   ```

### 安装kubernetes

1. kubernetes 国内yum源

   ```shell
   cat <<EOF > kubernetes.repo
   [kubernetes]
   name=Kubernetes
   baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
   enabled=1
   gpgcheck=1
   repo_gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
   EOF
   mv kubernetes.repo /etc/yum.repos.d/
   ```

2. 刷新元

   ```shell
   yum makecache fast
   ```

3. 安装kubernetes

   ```shell
   yum install -y kubelet kubeadm kubectl
   #启动
   systemctl enable kubelet && systemctl start kubelet
   ```

### 部署kubernetes

1. 创建 Master

   ```
   #创建配置文件
   kubeadm config print init-defaults > init.default.yaml
   ```

   ```shell
   vi init.default.yaml
   #
   apiVersion: kubeadm.k8s.io/v1beta2
   kind: ClusterConfiguration
   kubernetesVersion: v1.16.4
   apiServer:
     certSANs:    #填写所有kube-apiserver节点的hostname、IP、VIP
     - test01.kubernetes.akachi
     - test02.kubernetes.akachi
     - test03.kubernetes.akachi
     - test04.kubernetes.akachi
     - test05.kubernetes.akachi
     - test06.kubernetes.akachi
     - 192.168.0.141
     - 192.168.0.142
     - 192.168.0.143
     - 192.168.0.144
     - 192.168.0.145
     - 192.168.0.146
   controlPlaneEndpoint: "192.168.0.131:6443"
   networking:
     podSubnet: "10.244.0.0/16"
   ```

   

2. 创建 Master

   ```shell
   kubeadm init \
   --apiserver-advertise-address=192.168.1.141 \
   --image-repository registry.aliyuncs.com/google_containers \
   --kubernetes-version v1.22.1 \
   --service-cidr=10.96.0.0/12 \
   --pod-network-cidr=10.244.0.0/16
   ```

   > 参数解释
   >
   > apiserver-advertise-address 当前服务器IP
   >
   > image-repository 镜像地址
   >
   > kubernetes-version 版本
   >
   > service-cidr k8s相关内容的IP
   >
   > pod-network相关的IP

3. 加入kubernetes node

#### 错误排查

1. [ERROR Swap]: running with swap on is not supported. Please disable swap. 

   swap没有关闭

   ```shell
   [root@test01 ~]# kubeadm init \
   > --apiserver-advertise-address=192.168.1.141 \
   > --image-repository registry.aliyuncs.com/google_containers \
   > --kubernetes-version v1.22.1 \
   > --service-cidr=10.96.0.0/12 \
   > --pod-network-cidr=10.244.0.0/16
   [init] Using Kubernetes version: v1.22.1
   [preflight] Running pre-flight checks
   error execution phase preflight: [preflight] Some fatal errors occurred:
           [ERROR Swap]: running with swap on is not supported. Please disable swap
   [preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
   To see the stack trace of this error execute with --v=5 or higher
   [root@test01 ~]#
   
   ```

   > 解决方法
   >
   > 1. 关掉swapoff
   >
   > ```shell
   > swapoff -a
   > ```
   >
   > 2. 注释掉配置
   >
   > ```shell
   > vi /etc/fstab
   > 
   > 
   > #/dev/mapper/centos-swap swap                    swap    defaults        0 0
   > ```
   >
   > 注释掉最后一行
   >
   > 原因:
   >
   > 之前关闭了swap(虚拟内存)但是并未关闭服务

2. 创建 Master时报错拉取不到镜像

   [ERROR ImagePull]: failed to pull image registry.aliyuncs.com/google_containers/coredns:v1.8.4: output: Error response from daemon: manifest for registry.aliyuncs.com/google_containers/coredns:v1.8.4 not found: manifest unknown: manifest unknown

   

   > 报错
   >
   > ```shell
   > [preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
   > error execution phase preflight: [preflight] Some fatal errors occurred:
   >         [ERROR ImagePull]: failed to pull image registry.aliyuncs.com/google_containers/coredns:v1.8.4: output: Error response from daemon: manifest for registry.aliyuncs.com/google_containers/coredns:v1.8.4 not found: manifest unknown: manifest unknown
   > , error: exit status 1
   > [preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
   > To see the stack trace of this error execute with --v=5 or higher
   > 
   > ```
   >
   > 原因:拉取不到镜像
   >
   > ```shell
   > registry.aliyuncs.com/google_containers/coredns不能用
   > ```
   >
   > 解决方案
   >
   > 安装ShadowsocketR 并开启代理功能
   >
   > ![image-20210828204034971](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/28/204044.png)
   >
   > ```shell
   > # 安装wget
   > install wget -y
   > vi /etc/profile
   > #插入
   > http_proxy=192.168.1.3:17074
   > https_proxy=$http_proxy
   > no_proxy=*.abc.com,10.*.*.*,192.168.*.*,*.local,localhost,127.0.0.1  ,*.akachi
   > export http_proxy https_proxy no_proxy
   > #内容很直白 
   > #wge 测试谷歌能否下载下来
   > #测试是否成功
   > kubeadm init \
   > --apiserver-advertise-address=192.168.1.141 \
   > --image-repository k8s.gcr.io \
   > --kubernetes-version v1.22.1 \
   > --service-cidr=10.96.0.0/12 \
   > --pod-network-cidr=10.244.0.0/16
   > ```
   >
   > 

3. other



