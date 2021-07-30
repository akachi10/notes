# Docker swarm集群



![白色](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/15/095704.jpg)



> swarm的教程不是很好找
>
> 在prodect的engine中将容器编排和集群部署的地方讲
>
> https://docs.docker.com/engine/swarm/swarm-mode/

# swarm工作模式

Docker Engine 1.12 引入了 swarm 模式，使您能够创建一个由一个或多个 Docker 引擎组成的集群，称为 swarm。swarm 由一个或多个节点组成：在 swarm 模式下运行 Docker Engine 1.12 或更高版本的物理或虚拟机。

有两种类型的节点：[**manager**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#manager-nodes)和 [**workers**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#worker-nodes)。

![Swarm 模式集群](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/12/130506.png)



## swarm 命令

```shell
Usage:  docker swarm COMMAND

Manage Swarm

Commands:
  ca          Display and rotate the root CA
  init        Initialize a swarm
  join        Join a swarm as a node and/or manager
  join-token  Manage join tokens
  leave       Leave the swarm
  unlock      Unlock swarm
  unlock-key  Manage the unlock key
  update      Update the swarm

Run 'docker swarm COMMAND --help' for more information on a command.

```

## 创建swarm流程

> 以下的命令都可以通过--help

```shell
#1 创建一个swarm

[root@swarm01 docker]# docker swarm init --advertise-addr 192.168.0.209
Swarm initialized: current node (jwmxshbfi37h95nxzdopqjc7c) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3g6i2kidh816zhhdunl4pqkqhagn5qs5dvh7zmym61lmx3p3kf-00b6sdcusdfk2p6hkfm49jxsw 192.1

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

#给了我们两个命令
# docker swarm join --token SWMTKN-1-3g6i2kidh816zhhdunl4pqkqhagn5qs5dvh7zmym61lmx3p3kf-00b6sdcusdfk2p6hkfm49jxsw 192.168.0.209:2377
#docker swarm join-token manager
#docker swarm join-token swarm

#2 加入一个节点
[root@swarm02 docker]# docker swarm join --token SWMTKN-1-3g6i2kidh816zhhdunl4pqkqhagn5qs5dvh7zmym61lmx3p3kf-00b6sdcusdfk                                                                             2p6hkfm49jxsw 192.168.0.209:2377
This node joined a swarm as a w 
#3 查看加入的节点
[root@swarm01 ~]# docker node ls
ID                            HOSTNAME     STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
jwmxshbfi37h95nxzdopqjc7c *   swarm01.my   Ready     Active         Leader           20.10.7
yihzta1nqf9nm93wug46zkxv6     swarm02.my   Ready     Active                          20.10.7
#4 生成新的token以供加入

[root@swarm01 ~]# docker swarm join-token warker
unknown role warker
[root@swarm01 ~]# docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3g6i2kidh816zhhdunl4pqkqhagn5qs5dvh7zmym61lmx3p3kf-00b6sdcusdfk2p6hkfm49jxsw 192.168.0.209:2377

#5 晋升一个node为manager
#这里可以用hostname来操作
docker node promote swarm04.my


```

## 部署应用

> 弹性扩缩容
>
> 告别docker run!
>
> 容器就是玩具脱离了编排没有任何意义
>
> dockercompose是单机的
>
> 集群:swarm docker service
>
> k8s service pods
>
> 容器> 服务！
>
> 灰度发布



```shell
#1创建一个服务
docker service create -p 2888:80 --name my-nginx nginx
# 启动时通过 --mode 可以设置是否只在服务器上运行
# Service mode (replicated, global, replicated-job, or global-job) (default "replicated")

#扩缩容
 docker service update --replicas 3 my-nginx
 #或者使用scale
[root@swarm04 ~]# docker service scale  my-nginx=4
#
```









## 附件

### RAFT

保证大多数节点存活拆可以用

```shell
#试验证明至少要有多数节点存活才行
#docker swarm leave
#离开节点
```

![swarm defines](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/13/143642.png)

命令>管理节点>api>调度>工作节点

### 内部原理

![image-20210713144136140](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/07/13/145048.png)

### 关键命令组

- docker swarm 管理群

  - swarm  create
  - docker swarm join-token worker
  - docker swarm join-token manager

- docker node 管理节点

  - docker node promote swarm04.my

- docker service 管理服务

  - docker service ps
  - docker service rm
  - docker service update
  - docker service scale
  
  这是一个集群管理命令，必须在 swarm manager 节点上执行。要了解管理器和工作器，请参阅文档中的 [Swarm 模式部分](https://docs.docker.com/engine/swarm/)。

### 网络模式

网络模式PubishMode : ingress

Swarm:

Overlay:

ingress: 是一个特殊的Overlay 网络！ 防护在均衡的功能! IPVS VIP



```shell
[root@swarm01 ~]# docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
53d6fb06a043   bridge            bridge    local
570ddbe94b59   docker_gwbridge   bridge    local
c560c2278f88   host              host      local
j91ffhouw8ux   ingress           overlay   swarm
e3a99632f17f   none              null      local
[root@swarm01 ~]#  docker network inspect ingress
[
    {
        "Name": "ingress",
        "Id": "j91ffhouw8ux79z1yv5ga6aai",
        "Created": "2021-07-12T14:11:49.819431278+08:00",
        "Scope": "swarm",
        "Driver": "overlay",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "10.0.0.0/24",
                    "Gateway": "10.0.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": true,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "2c3a204eff19cf40311ea28e12255e1c9a3c8589704ca54b35cf78636ad84eaf": {
                "Name": "my-nginx.8.tms5zqp0u7r165dkfqj0ojmrk",
                "EndpointID": "be78193d469556014dfaae362f08ad694944dd64d2404bc7fdd94f8a0723f77f",
                "MacAddress": "02:42:0a:00:00:26",
                "IPv4Address": "10.0.0.38/24",
                "IPv6Address": ""
            },
            "465b82da4ad355e09217b7637e9140ed18bff365c45f3cbc758669599f31ba6a": {
                "Name": "my-nginx.15.vw0cnqn2512livwdxbb9glwwh",
                "EndpointID": "05b9b7d864ab5f40d9488ac9c39d29a97195d01ecb578a27779d3b9ffec69df2",
                "MacAddress": "02:42:0a:00:00:19",
                "IPv4Address": "10.0.0.25/24",
                "IPv6Address": ""
            },
            "734064a7cb4998db2ead32935d537c53013568d81e8467a50bb0a3997e70539c": {
                "Name": "my-nginx.3.k0j4jw0ysbgfr8re2v2pyifew",
                "EndpointID": "cd357482b3607a00f1191d67f41cd366ba58f3cd090e2ee11d6fcf12d5563909",
                "MacAddress": "02:42:0a:00:00:1a",
                "IPv4Address": "10.0.0.26/24",
                "IPv6Address": ""
            },
            "ingress-sbox": {
                "Name": "ingress-endpoint",
                "EndpointID": "f9ff657f2ee6214ba64d332e502b6fffd73e9be9b8c0010c406151183daa1872",
                "MacAddress": "02:42:0a:00:00:02",
                "IPv4Address": "10.0.0.2/24",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.driver.overlay.vxlanid_list": "4096"
        },
        "Labels": {},
        "Peers": [
            {
                "Name": "738c0b866305",
                "IP": "192.168.0.211"
            },
            {
                "Name": "09004037ccff",
                "IP": "192.168.0.209"
            },
            {
                "Name": "917bbf92496d",
                "IP": "192.168.0.210"
            },
            {
                "Name": "90cba7fc201a",
                "IP": "192.168.0.212"
            }
        ]
    }
]

```

