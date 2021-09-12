# nginx+keepalived

## 介绍

> 通过nginx做负载均衡，keepalived做IP漂移解决nginx的单点故障问题
>
> 这是是基于哔哩哔哩课程做的笔记
>
> 课程大纲:
>
> ![image-20210911122412194](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/122424.png)
>
> 课程地址
>
> https://www.bilibili.com/video/BV1eb411G7fT?p=1
>
> ![image-20210911122448588](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/122449.png)



### nginx

> nginx是一个高性能的HTTP和反向代理服务器同时是一个邮件服务器
>
> 官方网址:http://nginx.org
>
> 文档地址:https://nginx.org/en/docs/

#### 安装

- 安装依赖

  ```shell
  yum install -y gcc-c++
  yum install -y pcre pcre-devel
  yum install -y zlib zlib-devel
  yum install -y openssl openssl-devel
  yum install -y wget
  ```

  > 成功会显示Complete!

  > 安装jdk
  >
  > 1、查看本机是否自带java：rpm -qa|grep java
  >
  > 2、有则卸载：rpm -e --nodeps java*
  >
  > 3、查看yum中jdk版本：yum search jdk
  >
  > 发现有java11，列出java11：yum list |grep java-11

  ```shell
  yum install -y java-11-openjdk.x86_64 java-11-openjdk-headless.x86_64 java-11-openjdk-devel.x86_64
  ```

- 关闭防火墙

  ```shell
  systemctl stop firewalld
  systemctl disable firewalld
  sed -i 's/enforcing/disabled/' /etc/selinux/config
  setenforce 0
  ```

  > 安装jdk x86、headless、devel三个rpm 
  >
  > ![image-20210911124056691](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/124057.png)
  >
  > 配置环境变量
  >
  > jdk地址：/usr/lib/jvm/java-11-openjdk

  ```shell
  vi /etc/profile
  
  #set java environment
  JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.12.0.7-0.el7_9.x86_64
  JRE_HOME=$JAVA_HOME
  CLASS_PATH=.:$JRE_HOME/lib
  PATH=$PATH:$JAVA_HOME/bin
  export JAVA_HOME JRE_HOME CLASS_PATH PATH
  
  ```

  > 生效
  >
  > source /etc/profile
  >
  > 测试
  >
  > cd $JAVA_HOME

- 到官网下载tar.gz包

  > 地址：http://nginx.org/en/download.html
  >
  > ![image-20210911123303738](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/123305.png)
  >
  > 点击这里下载最新linux稳定版

  ```shell
  wget http://nginx.org/download/nginx-1.20.1.tar.gz
  ```

- 解压文件

  tar -zxvf nginx-1.20.1.tar.gz

- 安装

  > 进入解压目录

  ```shell
  #配置
  ./configure
  #安装
  make && make install
  ```

  > whereis nginx 查看安装目录
  >
  > /usr/local/nginx

- 加入环境变量

  > 修改文件/etc/profile

  ```shell
  NGINX_HOME=/usr/local/nginx/
  PATH=$PATH:$NGINX_HOME/sbin
  ```

  > source /etc/profile
  >
  > 测试: nginx -v

  

#### 启动nginx

> 到/usr/local/nginx/sbin中启动nginx

```shell
cd /usr/local/nginx/sbin
./nginx
```

![image-20210911130639763](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210911130639763.png)

##### systemctl启动

> 创建文件
>
> vi /usr/lib/systemd/system/nginx.service

```shell
[Unit]
Description=nginx - high performance web server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s stop
ExecQuit=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

> 刷新service并重启项目

```shell
systemctl daemon-reload
systemctl restart nginx.service
```



#### 简单命令介绍

![image-20210911130957249](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/130958.png)

#### 配置文件介绍

> 配置文件分为主模块
>
> http模块
>
> 。。。。

##### 主模块

1. 修改配置文件

   > 修改配置文件 $NGINX_HOME/conf/nginx.conf
   >
   > ！！必须加上;结尾
   >
   > 默认是

   - 语法 daemon  on;

     - 开启守护进程
   - master_proccess on;
   
     - 多线程;
   - worker_processes x
     - 线程数量
     - 建议与CPU保持同数

> 文件 /var/log/nginx.pid写了当前运行的进程号

##### http模块

![image-20210911152414603](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/152429.png)

1. include

   > 指向表示静态资源的文件

2. error_log

   ```shell
    #access_log  logs/host.access.log  main;
   ```

##### server模块

- listen [port]

- location 本地位置

  ![image-20210911152913978](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/152915.png)

  ![image-20210911153218255](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/153219.png)

  - root 根目录位置

  - index index访问文件

  - deny 禁止访问

    > deny [ip/netmask]
    >
    > deny 192.168.2.0/24
    >
    > allow 10.0.0.0/16
    >
    > denyall;

#### 负载均衡应用

>  也是基于server来写的

``` 例子
http{
    upstream backend {
        server 192.168.3.181:8080;
        server 192.168.3.182:8080;
        server 192.168.3.183:8080;
    }

    server {
        location /{
            proxy_pass http://backend;
        }
    }
}
```

> upstream 策略 
>
> - wight 权重
> - ip_hash 更具访问者IP来决定由谁处理
>
> ```shell
> upstream backend{
> ip_hash;
> [servers...]
> }
> ```

- upstream 参数

  > 多个server
  >
  > 或者ip_hash;
  >
  > IP_HASH 由问题好像不能很好的生效
  >
  > 网络上说是由于他们使用前三段网段来决定访问谁的，测试无果
  >
  > !!!注意不要用TAB键要用空格

- server 参数

  > server [ip or 域名: 端口] max_fails=[超时次数] fail_timeout=[超时时间][时间单位 s/m/h]

#### 图片应用

```shell
  server {
        listen       81;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
    }
```

### keepalived

#### 原理

> 使用keepalived做主备原理


![image-20210911172030692](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/172033.png)

#### 方案

![image-20210911173826611](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/173829.png)

> 仅供参考 IP再说

#### 安装

> 参考官方
>
> https://www.redhat.com/sysadmin/keepalived-basics

`Keepalived`在标准包存储库中可用，并且可以使用`yum`以下方式轻松安装：

```shell
[root@server1 ~]# yum install -y keepalived
# 
[root@server1 ~]# keepalived --version
Keepalived v2.0.10 (11/12,2018)
# 查看状态
[root@server1 ~]# systemctl status keepalived
keepalived.service - LVS and VRRP High Availability Monitor
Loaded: loaded (/usr/lib/systemd/system/keepalived.service; disabled; vendor preset: disab
Active: inactive (dead)
```

#### 配置

> 文件地址
>
> /etc/keepalived/keepalived.conf
>
> 例子:MASTER

```shell
global_def {
  router_id nginx01.akachi;
}

verp_script chk_nginx{
 script "/etc/keepalived/nginx_check.sh"
 interval 2
 weight -20
}

vrrp_instance VI_1 {
    state MASTER
    interface ens32
    virtual_router_id 146
    mcast_src_ip 192.168.3.146
    priority 100
    nopreempt
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }

    track_script {
     chk_nginx
    }
    virtua_ipaddress {
        192.168.3.200
    }
}
```

> BACKUP:

```shell
global_def {
  router_id nginx02.akachi;
}

verp_script chk_nginx{
 script "/etc/keepalived/nginx_check.sh"
 interval 2
 weight -20
}

vrrp_instance VI_1 {
    state BACKUP
    interface ens32
    virtual_router_id 147
    mcast_src_ip 192.168.3.147
    priority 100
    nopreempt
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }

    track_script {
     chk_nginx
    }
    virtua_ipaddress {
        192.168.3.200
    }
}
```





> 解释：
>
> global_def {
>   router_id nginx01.akachi; //最好用主机名要能够访问本机的名字
> }
>
> verp_script chk_nginx{  //指向脚本监控
>  script "/etc/keepalived/nginx_check.sh" //验证脚本
>  interval 2 //两秒钟执行一次
>  weight -20 //改变权重
> }
>
> vrrp_instance VI_1 { 
>     state MASTER //主机
>     interface eth0 //网卡名
>     virtual_router_id 146 //路由ID尽量等于IP
>     mcast_src_ip 192.168.3.146 //本机IP
>     priority 100 // 权重
>     nopreempt //重启后仍然做主节点
>     advert_int 1 //组播时间
>     authentication { //鉴定
>         auth_type PASS 
>         auth_pass 1111
>     }
>
> ​    track_script {
> ​       chk_nginx //要执行的脚本在上面
> ​    }
>    virtua_ipaddress {
> ​      192.168.3.200 //虚拟IP
>    }
>
> }

> chk_nginx:

```shell
#!/bin/bash
A=`ps -C nginx -no-header |wc -l`
if [ $A -eq 0];then
    /usr/local/nginx/sbin/nginx
    sleep 2
    if [ `ps -C nginx --no-header |wc -l` -eq 0];then
        killall keepalived
    fi
fi
```

> 不知道为啥失败了



## 官方 keepalived

使用 Keepalived 设置 Linux 集群：基本配置

在三篇 Linux HA 集群文章中的第二篇中，您将探索 Keepalived 安装和配置的基础知识。

发布时间： 2020 年 3 月 25 日 | **经过**[安东尼·克里特利（苏多尔）](https://www.redhat.com/sysadmin/users/anthony-critelli)

图片

![系统管理员配置开关](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/11/202642.jpeg)



更多 Linux 资源[面向开发人员的高级 Linux 命令备忘单](https://developers.redhat.com/cheat-sheets/advanced-linux-commands/?intcmp=701f20000012ngPAAQ)[开始使用红帽洞察](https://access.redhat.com/products/red-hat-insights/?intcmp=701f20000012ngPAAQ)[立即下载：基本 Linux 命令备忘单](https://developers.redhat.com/cheat-sheets/linux-commands-cheat-sheet/?intcmp=701f20000012ngPAAQ)[Linux系统管理技能评估](https://rhtapps.redhat.com/assessment/?intcmp=701f20000012ngPAAQ)

在本系列的第一篇文章中，[使用 Keepalived 管理集群中的简单故障转移](https://www.redhat.com/sysadmin/ha-cluster-linux)，您了解了将 IP 地址从一台机器[故障转移](https://www.redhat.com/sysadmin/ha-cluster-linux)到另一台机器`Keepalived`的`VRRP`协议。现在是时候亲自动手并了解`Keepalived`. 在本文中，您将设置`Keepalived`启用两台服务器之间的故障转移。让我们开始吧！

### 安装keepalived

`Keepalived`在标准包存储库中可用，并且可以使用`yum`以下方式轻松安装：

```shell
[root@server1 ~]# yum install -y keepalived

[root@server1 ~]# keepalived --version
Keepalived v2.0.10 (11/12,2018)

[root@server1 ~]# systemctl status keepalived
keepalived.service - LVS and VRRP High Availability Monitor
Loaded: loaded (/usr/lib/systemd/system/keepalived.service; disabled; vendor preset: disab
Active: inactive (dead)
```

您还应该知道如何`Keepalived`从源代码进行编译。`Keepalived`是一个积极维护的项目，它会[定期收到](https://www.keepalived.org/changelog.html)您需要的包管理器版本中可能没有的新功能和错误修复。在编写本系列时，我什至遇到了包存储库中当前版本的错误，我必须按照这个确切的过程来使用最新版本的`Keepalived`.

```shell
# Install prerequisites
yum install -y gcc openssl-devel

# Download the latest version of the code. Be sure to check the downloads page for the most recent version:https://www.keepalived.org/download.html
[root@localhost ~]# wget https://www.keepalived.org/software/keepalived-2.0.20.tar.gz

# Extract the code
[root@localhost ~]# tar -xf keepalived-2.0.20.tar.gz

# Run the configure scripts
[root@localhost ~]# cd keepalived-2.0.20
[root@localhost keepalived-2.0.20]# ./configure

# Build and install keepalived
[root@localhost keepalived-2.0.20]# make
[root@localhost keepalived-2.0.20]# make install

# Test your installation
[root@localhost keepalived-2.0.20]# keepalived --version
Keepalived v2.0.20 (01/22,2020)
```

#### 基本配置

图片

![server1 server2 故障转移](https://www.redhat.com/sysadmin/sites/default/files/styles/embed_large/public/2020-03/Picture1_2.png?itok=dNBqt2gQ)

*图中的网络符号可通过 [VRT 网络设备扩展](https://extensions.libreoffice.org/extensions/vrt-network-equipment)， [CC BY-SA 3.0 获得](https://creativecommons.org/licenses/by-sa/3.0/)。*

的配置文件`Keepalived`位于`/etc/keepalived/keepalived.conf`. 正如在上一篇文章中所讨论的，`Keepalived`不仅仅是实现基本的`VRRP`. 如果您查看[Keepalived 手册页](https://www.keepalived.org/manpage.html)，这会导致配置文件看起来令人生畏。但是，可以通过最少的配置实现类似上述的简单拓扑。

最基本的`Keepalived`配置在两台服务器之间启用共享 IP 地址。在上面的拓扑中，server1 为主，server2 为备份。两台服务器的配置都很简单。

**服务器 1 配置：**

```shell
server1# cat /etc/keepalived/keepalived.conf
vrrp_instance VI_1 {
        state MASTER
        interface eth0
        virtual_router_id 51
        priority 255
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345
        }
        virtual_ipaddress {
              192.168.122.200/24
        }
}
```

**服务器2配置：**

```shell
server2# cat /etc/keepalived/keepalived.conf
vrrp_instance VI_1 {

        state BACKUP
        interface eth0
        virtual_router_id 51
        priority 254
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345
        }
        virtual_ipaddress {
              192.168.122.200/24
        }
}
```

配置指令从它们的命名约定应该是显而易见的，但我将逐一介绍：

- `vrrp_instance`定义`VRRP`在接口上运行的协议的单个实例。我随意命名了这个实例 VI_1。
- `state` 定义实例应该开始的初始状态。
- `interface`定义`VRRP`运行的接口。
- `virtual_router_id` 是您在本系列的第一篇文章中了解到的唯一标识符。
- `priority`是您在本系列的第一篇文章中了解到的广告优先级。您将在下一篇文章中了解到，可以在运行时调整优先级。
- `advert_int` 指定发送广告的频率（在本例中为 1 秒）。
- `authentication`指定参与的服务器`VRRP`相互验证所需的信息。在这种情况下，定义了一个简单的密码。
- `virtual_ipaddress`定义负责的 IP 地址（可以有多个）`VRRP`。

如果您使用基于主机的防火墙，例如`firewalld`或`iptables`，则需要添加必要的规则以允许 IP 协议 112 流量。否则，`Keepalived`的广告方法将不起作用。配置基于主机的防火墙超出了本文的范围，但请务必查看 Enable Sysadmin 的其他一些关于[iptables](https://www.redhat.com/sysadmin/iptables)和[firewalld 的](https://www.redhat.com/sysadmin/secure-linux-network-firewall-cmd)文章以获取更多信息。

有了上述配置，您可以`Keepalived`在两台服务器上启动`systemctl start keepalived`并观察每台机器上的 IP 地址。请注意，server1 已作为`VRRP`master启动并拥有共享 IP 地址 (192.168.122.200)，而 server2 的 IP 地址保持不变：

```shell
server1# ip -brief address show
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.101/24 192.168.122.200/24 fe80::5054:ff:fe82:d66e/64
server2# ip -br a
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.102/24 fe80::5054:ff:fe04:2c5d/64
```

一旦您确认`Keepalived`已在两台服务器上启动并且 server1 是活动主服务器，您可以通过将 VIP“翻转”到另一台服务器来测试故障转移功能。通过`Keepalived`在 server1 上停止，活动 master 停止发送广告，server2 接管 VIP：

```shell
server1# systemctl stop keepalived

server1# ip -brief address show
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.101/24 fe80::5054:ff:fe82:d66e/64
server2# ip -brief address show
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.102/24 192.168.122.200/24 fe80::5054:ff:fe04:2c5d/64
```

就是这样！您现在拥有一对基本的冗余服务器。

### 监控 VRRP 流量

正如本系列的第一篇文章中所讨论的，了解 的协议级行为`VRRP`很重要，以便您可以正确配置和排除故障。如果您已经阅读了 Enable Sysadmin 之前关于[分析网络流量的](https://www.redhat.com/sysadmin/packet-sniffer-basics)文章，那么您可能会很舒服地使用`tcpdump`. 使用命令行数据包捕获`tcpdump`可以揭示您需要了解的有关`VRRP`配置的所有信息，包括活动主设备的 VRID 和优先级：

```shell
server1# tcpdump proto 112
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode

listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes

16:51:01.353224 IP 192.168.122.102 > 224.0.0.18: VRRPv2, Advertisement, vrid 51, prio 254, authtype simple, intvl 1s, length 20

16:51:02.353673 IP 192.168.122.102 > 224.0.0.18: VRRPv2, Advertisement, vrid 51, prio 254, authtype simple, intvl 1s, length 20

16:51:03.353753 IP 192.168.122.102 > 224.0.0.18: VRRPv2, Advertisement, vrid 51, prio 254, authtype simple, intvl 1s, length 20


3 packets captured
3 packets received by filter
0 packets dropped by kernel
```

通过`tcpdump`在两个服务器之间来回翻转 VIP 的同时运行，亲自尝试一下。 
