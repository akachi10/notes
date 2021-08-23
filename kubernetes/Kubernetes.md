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
   baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
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
2. 加入 Kubernetes Node

