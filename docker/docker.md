# docker 学习笔记



## 安装

### 资源

#### linux安装 ubuntu 

- 解决ssh没有问题
- sudo service ssh start
- 解决 ifconfig 没有问题
- sudo apt-get install net-tools
- 安装docker命令
- URL:https://www.runoob.com/docker/ubuntu-docker-install.html

#### windows安装

- URL:https://www.runoob.com/docker/windows-docker-install.html
- Windows wsl2 问题解决 https://blog.csdn.net/weixin_39806603/article/details/111166146
  - 在powershell中执行
  - Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
  - Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
  - 并且到Windows官网中(以下链接)中在"步骤4的1.下载最新包"下载安装包
  - https://docs.microsoft.com/zh-cn/windows/wsl/install-win10

### 笔记

- 官方文档在这

![image-20210413211446239](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210413211446239.png)

- 位置



![image-20210413211846809](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210413211846809.png)

#### 安装

```shell
# 1、安装包
sudo yum install -y yum-utils
# 2、用国内的仓库
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    #https://download.docker.com/linux/centos/docker-ce.repo
# 3、安装docker引擎
# 3.1、更新索引
yum makecache fast
# 4、 安装docker相关的 docker-ce是社区版 ee是企业版
sudo yum install docker-ce docker-ce-cli containerd.io
# 5、启动docker
systemctl start docker
# 6、使用docker version查看是否安装成功
# 7、运行hello world
docker run hello world
# 8、查看 hello image
```

#### 卸载

```shell
# 1、卸载依赖
sudo yum remove docker-ce docker-ce-cli containerd.io
# 2、删除资源
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
# /var/lib/docker docker的默认工作路径
```

#### 使用加速器

```shell
# 1、阿里云docker加速器配置文件
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://s796m7v3.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```



## docker 原理

### 调用hello时系统做了什么

![image-20210414095356937](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210414095356937.png)



### docker 是怎么工作的



![image-20210414100919038](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210414100919038.png)



### docker 为什么比VM快

![image-20210414102011258](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210414102011258.png)



- 虚拟机流程
  - OS 硬件虚拟层 1-1 虚拟机libs 1-1 app
- docker流程 
  - lib 1-* 系统 



## docker中的概念

### docker 分层下载

- UnionFS

  分层的文件系统就好比我们使用git下载代码

  并不是每次都需要下载所有的代买只需要下载最新更新的那部分我们差的就可以了。

  - Layers 每个层在系统中都是一个Layers 

    Layers我们不应该理解为一个文件而应该理解为一系列操作也可以是删除或修改其中的内容。

    比如如下的一个例子中我们有一个自研系统。其中MySQL也是非标的那么我先按照MySQL作为一个层而自研系统作为另一个层

    

  ![image-20210505214716880](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210505214716880.png)

  如果我们要对这个系统进行升级那么会出现以下情况

  ![image-20210505214824616](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210505214824616.png)

  在这种情况下一个已经使用过我们自研系统的用户是不需要下载Layers 0中的内容的

  而只需要下载Layers 2 而实际上我们也可以直接基于Layers0上构建系统这样即便是新也不需要下载Layers1了

### docker中的镜像

就是一个软件包吧环境、变量、等等打包在一起运行的镜像

如何得到镜像

- 远程下载

- 拷贝

- 自己制作一个镜像

  



## 附录

### 资源地址
| 名称 | 地址 |
|:-----:|:--------|
|官方网址|https://docs.docker.com/ |
|仓库地址|https://hub.docker.com/  |

### 名词概念

| 英文       | 翻译       | 命令 | 含义               |
| ---------- | ---------- | ---- | ------------------ |
| Hyper-V    | Hyper-V    | ---  | 微软虚拟机相当于VM |
| PowerShell | PowerShell | ---  | 微软的shell        |
| repository | 仓库       | ---  |                    |

### 命令

#### 镜像命令

##### docker images

- docker images 查看主机上的镜像

  ```shell
  # docker images
  REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
  hello-world   latest    d1165f221234   5 weeks ago   13.3kB
  
  #解释
  REPOSITORY    镜像仓库源
  TAG           镜像的标签
  IMAGE ID      ID
  CREATED       创建时间
  SIZE          大小
  
  # 可选项
  -a            #列出所有镜像
  -f            #过滤
  -q            #镜像的ID（用于情况）
  -aq           #所有镜像
  ```


##### docker search

- docker search 搜索命令 

  ```shell
  # docker search mysql
  NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
  mysql                             MySQL is a widely used, open-source relation…   10748     [OK]
  mariadb                           MariaDB Server is a high performing open sou…   4047      [OK]
  mysql/mysql-server                Optimized MySQL Server Docker images. Create…   791                  [OK]
  
  # 可选项
   -f, --filter filter   Filter output based on conditions provided
        --format string   Pretty-print search using a Go template
        --limit int       Max number of search results (default 25)
        --no-trunc        Don't truncate output
  
  #可选项注释
  -f STARS=2000
   
  ```

##### docker pull

- docker pull 下载镜像

  ```shell
    # docker pull mysql 	
  Using default tag: latest
  latest: Pulling from library/mysql
  f7ec5a41d630: Pull complete
  9444bb562699: Pull complete
  6a4207b96940: Pull complete
  181cefd361ce: Pull complete
  8a2090759d8a: Pull complete
  15f235e0d7ee: Extracting [==============>                                    ]  3.932MB/13.45MB
  
  docker.io/library/mysql:latest
  
  
  #可选项
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --platform string         Set platform if server is multi-platform capable
  -q, --quiet                   Suppress verbose output
  
  #备注
  docker.io/library/mysql:latest是真实地址
  docker pull docker.io/library/mysql:latest 等同于 docker pull mysql
  ```

##### docker rm rmi

- docker rm|rmi

  ```shell
  # docker rmi mysql:5.7
  Untagged: mysql:5.7
  
  #可选项
    -f, --force      Force removal of the image
        --no-prune   Do not delete untagged parents
  
  #docker rmi -f $(docker images -aq)
  删除所有
  
  ```



#### 容器命令

##### docker run

- docker run

  ```shell
  # docker run [可选参数] image
  
  #可选参数
  -- name="Name" 容器名字
  -d 后台方式运行 nohup
  -it 交互方式运行，进入容器查看内容
  -p 指定容器端口 8080:8083
    	-p 容器端口
    	-p ip:port:容器端口	
  -v dir:dir 挂载卷
  
  # 启动并进入容器
  [root@localhost ~]# docker run -it centos /bin/bash
  [root@1b15ac179c28 /]#
  
  ```


##### docker ps

- 查看正在运行的命令 

  ```shell
  # docker ps -a
  CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                        PORTS     NAMES
  1b15ac179c28   centos    "/bin/bash"   15 minutes ago   Exited (0) 12   minutes ago             magical_mayer
  
  -a 列出当前正在运行的容器+ 历史运行过得程序
  -n=1 显示最近运行的1个程序
  -q 只显示编号
  
  ```

##### Ctrl+P+Q

- 容器不停止退出

  ```shell
  Ctrl+P+Q # 容器不停止退出
  ```

##### docker rm

- 删除命令

  ```shell
  #docker rm $(docker ps -aq)
  
  
  #查询后删除命令
  docker ps -a -q|xargs docker rm #删除所有容器
  #如果要强制删除要rm -f
  ```

##### docker start & restart & kill & stop

- 启动和停止操作
  
  ```shell
  docker start    容器ID
  docker restart  容器ID
  docker kill     容器ID
  docker stop     容器ID
  ```

##### docker stats

- 统计容器中的程序占用率

  ```shell
  # docker stats [dockerId]
  [akachi@AKACHI-PC-2018-wsl dell]$ docker stats 9d1b7b6f042469df8f229d3b565483bd2319061de268b8f980fda5a9c71fa369
  CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O     BLOCK I/O   PIDS
  9d1b7b6f0424   elasticearch_test   0.74%     3.366GiB / 24.95GiB   13.49%    766B / 0B   0B / 0B     46
  ```

  

#### 常用的其他命令



##### docker run

- 后台启动容器

  ```shell
  # 命令 docker run -d 镜像
  
  # 问题docker ps 发现centos停止了
  
  #常见问题
  docker发现没有应用了，就会自动停止。
  #启动NGINX 容器后发现自己没有内容在运行就直接停止了
  ```

##### docker logs

- 查看日志命令

  ```shell
  #docker logs --help
  
  Usage:  docker logs [OPTIONS] CONTAINER
  
  Fetch the logs of a container
  
  Options:
        --details        Show extra details provided to logs
    -f, --follow         Follow log output
        --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
    -n, --tail string    Number of lines to show from the end of the logs (default "all")
    -t, --timestamps     Show timestamps
    
    #运行一个有工作的linux
    docker run -d centos /bin/bash -c "while true;do echo akachi;sleep 1;done"
    #查看日志
    docker logs -ft --tail 10 55e74d0ad3ad
  ```

  

  

##### docker top

- 查看系统内的进程

  ```shell
  # docker top 55e74d0ad3ad
  UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
  root                735                 715                 0                   00:07               ?                   00:00:00            /bin/bash -c while true;do echo akachi;sleep 1;done
  root                2327                735                 0                   00:14               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
  ```

##### docker inspect

- 查看

  ```shell
  # docker inspect --help
  
  
    -f, --format string   Format the output using the given Go template
    -s, --size            Display total file sizes if the type is container
        --type string     Return JSON for specified type
  
  #例子
  docker inspect 55e74d0ad3ad
  
  [
      {
          "Id": "55e74d0ad3aded452ce53a564a62654e2da81e4ace24e8b349c7e341c65a3e8f",
          "Created": "2021-04-15T16:07:48.143683187Z",
          "Path": "/bin/bash",
          "Args": [
              "-c",
              "while true;do echo akachi;sleep 1;done"
          ],
          "State": {
              "Status": "running",
              "Running": true,
              "Paused": false,
              "Restarting": false,
              "OOMKilled": false,
              "Dead": false,
              "Pid": 735,
              "ExitCode": 0,
              "Error": "",
              "StartedAt": "2021-04-15T16:07:48.528694485Z",
              "FinishedAt": "0001-01-01T00:00:00Z"
          },
          "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
          "ResolvConfPath": "/var/lib/docker/containers/55e74d0ad3aded452ce53a564a62654e2da81e4ace24e8b349c7e341c65a3e8f/resolv.conf",
          "HostnamePath": "/var/lib/docker/containers/55e74d0ad3aded452ce53a564a62654e2da81e4ace24e8b349c7e341c65a3e8f/hostname",
          "HostsPath": "/var/lib/docker/containers/55e74d0ad3aded452ce53a564a62654e2da81e4ace24e8b349c7e341c65a3e8f/hosts",
          "LogPath": "/var/lib/docker/containers/55e74d0ad3aded452ce53a564a62654e2da81e4ace24e8b349c7e341c65a3e8f/55e74d0ad3aded452ce53a564a62654e2da81e4ace24e8b349c7e341c65a3e8f-json.log",
          "Name": "/youthful_payne",
          "RestartCount": 0,
          "Driver": "overlay2",
          "Platform": "linux",
          "MountLabel": "",
          "ProcessLabel": "",
          "AppArmorProfile": "",
          "ExecIDs": null,
          "HostConfig": {
              "Binds": null,
              "ContainerIDFile": "",
              "LogConfig": {
                  "Type": "json-file",
                  "Config": {}
              },
              "NetworkMode": "default",
              "PortBindings": {},
              "RestartPolicy": {
                  "Name": "no",
                  "MaximumRetryCount": 0
              },
              "AutoRemove": false,
              "VolumeDriver": "",
              "VolumesFrom": null,
              "CapAdd": null,
              "CapDrop": null,
              "CgroupnsMode": "host",
              "Dns": [],
              "DnsOptions": [],
              "DnsSearch": [],
              "ExtraHosts": null,
              "GroupAdd": null,
              "IpcMode": "private",
              "Cgroup": "",
              "Links": null,
              "OomScoreAdj": 0,
              "PidMode": "",
              "Privileged": false,
              "PublishAllPorts": false,
              "ReadonlyRootfs": false,
              "SecurityOpt": null,
              "UTSMode": "",
              "UsernsMode": "",
              "ShmSize": 67108864,
              "Runtime": "runc",
              "ConsoleSize": [
                  0,
                  0
              ],
              "Isolation": "",
              "CpuShares": 0,
              "Memory": 0,
              "NanoCpus": 0,
              "CgroupParent": "",
              "BlkioWeight": 0,
              "BlkioWeightDevice": [],
              "BlkioDeviceReadBps": null,
              "BlkioDeviceWriteBps": null,
              "BlkioDeviceReadIOps": null,
              "BlkioDeviceWriteIOps": null,
              "CpuPeriod": 0,
              "CpuQuota": 0,
              "CpuRealtimePeriod": 0,
              "CpuRealtimeRuntime": 0,
              "CpusetCpus": "",
              "CpusetMems": "",
              "Devices": [],
              "DeviceCgroupRules": null,
              "DeviceRequests": null,
              "KernelMemory": 0,
              "KernelMemoryTCP": 0,
              "MemoryReservation": 0,
              "MemorySwap": 0,
              "MemorySwappiness": null,
              "OomKillDisable": false,
              "PidsLimit": null,
              "Ulimits": null,
              "CpuCount": 0,
              "CpuPercent": 0,
              "IOMaximumIOps": 0,
              "IOMaximumBandwidth": 0,
              "MaskedPaths": [
                  "/proc/asound",
                  "/proc/acpi",
                  "/proc/kcore",
                  "/proc/keys",
                  "/proc/latency_stats",
                  "/proc/timer_list",
                  "/proc/timer_stats",
                  "/proc/sched_debug",
                  "/proc/scsi",
                  "/sys/firmware"
              ],
              "ReadonlyPaths": [
                  "/proc/bus",
                  "/proc/fs",
                  "/proc/irq",
                  "/proc/sys",
                  "/proc/sysrq-trigger"
              ]
          },
          "GraphDriver": {
              "Data": {
                  "LowerDir": "/var/lib/docker/overlay2/86c7812852ca518c6e817da6d5172d601c202919c98a4ecd3c138ae3d5d1fec9-init/diff:/var/lib/docker/overlay2/c1ed3c290e00393e38bd627a5d46775616d83e3ee7168372ea4858497f4e09f5/diff",
                  "MergedDir": "/var/lib/docker/overlay2/86c7812852ca518c6e817da6d5172d601c202919c98a4ecd3c138ae3d5d1fec9/merged",
                  "UpperDir": "/var/lib/docker/overlay2/86c7812852ca518c6e817da6d5172d601c202919c98a4ecd3c138ae3d5d1fec9/diff",
                  "WorkDir": "/var/lib/docker/overlay2/86c7812852ca518c6e817da6d5172d601c202919c98a4ecd3c138ae3d5d1fec9/work"
              },
              "Name": "overlay2"
          },
          "Mounts": [],
          "Config": {
              "Hostname": "55e74d0ad3ad",
              "Domainname": "",
              "User": "",
              "AttachStdin": false,
              "AttachStdout": false,
              "AttachStderr": false,
              "Tty": false,
              "OpenStdin": false,
              "StdinOnce": false,
              "Env": [
                  "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
              ],
              "Cmd": [
                  "/bin/bash",
                  "-c",
                  "while true;do echo akachi;sleep 1;done"
              ],
              "Image": "centos",
              "Volumes": null,
              "WorkingDir": "",
              "Entrypoint": null,
              "OnBuild": null,
              "Labels": {
                  "org.label-schema.build-date": "20201204",
                  "org.label-schema.license": "GPLv2",
                  "org.label-schema.name": "CentOS Base Image",
                  "org.label-schema.schema-version": "1.0",
                  "org.label-schema.vendor": "CentOS"
              }
          },
          "NetworkSettings": {
              "Bridge": "",
              "SandboxID": "82fa2f4ee45534cbcd77652e6b25199b5bbfd5c3f4c4658f8ca83d1f30615021",
              "HairpinMode": false,
              "LinkLocalIPv6Address": "",
              "LinkLocalIPv6PrefixLen": 0,
              "Ports": {},
              "SandboxKey": "/var/run/docker/netns/82fa2f4ee455",
              "SecondaryIPAddresses": null,
              "SecondaryIPv6Addresses": null,
              "EndpointID": "43d8c4ef3e9040fa1fa63811781d5296425e1337519a8e53f6e7393aacf44bb5",
              "Gateway": "172.17.0.1",
              "GlobalIPv6Address": "",
              "GlobalIPv6PrefixLen": 0,
              "IPAddress": "172.17.0.2",
              "IPPrefixLen": 16,
              "IPv6Gateway": "",
              "MacAddress": "02:42:ac:11:00:02",
              "Networks": {
                  "bridge": {
                      "IPAMConfig": null,
                      "Links": null,
                      "Aliases": null,
                      "NetworkID": "594bdad93d28de604c79b8139ce4a5f610b3fd8b3d624fd744def8838b996f36",
                      "EndpointID": "43d8c4ef3e9040fa1fa63811781d5296425e1337519a8e53f6e7393aacf44bb5",
                      "Gateway": "172.17.0.1",
                      "IPAddress": "172.17.0.2",
                      "IPPrefixLen": 16,
                      "IPv6Gateway": "",
                      "GlobalIPv6Address": "",
                      "GlobalIPv6PrefixLen": 0,
                      "MacAddress": "02:42:ac:11:00:02",
                      "DriverOpts": null
                  }
              }
          }
      }
  ]
  
  ```

##### docker exec & docker attach

- 进入容器

  ```shell
  #docker exec -it 容器id bashShell
  #开启行的 terminal
  
  #Options:
    -d, --detach               Detached mode: run command in the background
        --detach-keys string   Override the key sequence for detaching a container
    -e, --env list             Set environment variables
        --env-file list        Read in a file of environment variables
    -i, --interactive          Keep STDIN open even if not attached
        --privileged           Give extended privileges to the command
    -t, --tty                  Allocate a pseudo-TTY
    -u, --user string          Username or UID (format: <name|uid>[:<group|gid>])
    -w, --workdir string       Working directory inside the container
    
  #docker attach 容器ID
  #进入当前正在运行的terminal
  ```

##### docker cp

- 主机拷贝数据到容器内

  ```shell
  #docker cp 容器ID 路径 主机路径
   docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
  #未来使用数据卷技术联通
  ```

#### 打包命令

##### docker commit

```shell
    #docker commit -m "message" -a "author" 容器ID 目标镜像名:[TAG]  
```

**创造一个自己的tomcat**

``` shell
#docker commit -a="akachi" -m="add webapps and remove at webapps.dist" 4fe4a0777a29 tomcat_akachi:1.0
  -a, --author string    Author (e.g., "John Hannibal Smith <hannibal@a-team.com>")
  -m, --message string   Commit message
  -c, --change list      Apply Dockerfile instruction to the created image
  -p, --pause            Pause container during commit (default true)

```





#### 命令备注

​	在 官网的 reference(查询) docker cli(docker客户端下有所有命令)



##### docker命令图

![image-20210416004829718](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210416004829718.png)

### docker 网络

### dockerFile

### 容器数据卷

### 可视化工具

#### Portainer 

​	**什么是Rancher ？**

​	是一个Docker的图形化管理工具

 - 安装代码

   ``` shell
   #启动命令
   #docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
   
   #参数解释
   # 一直重启
   --restart=always
   # 挂在卷
   -v /var/run/docker.sock:/var/run/docker.sock 
   # 给一个特殊授权 给与了内部的ROOT权限 有了这个授权我们甚至可以在docker内运行docker
   --privileged=true 
   ```

- 查看localhost:8088

  

#### Rancher

## 符号

官方文档中使用的符号

```shell
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```



- [] 中功能括号表示参数
- :TAG表示其中一种情况 :表示知道TAG
- | 代表或者的意思
- @DIGEST 大概率是版本编号的意思 @有可能同:TAG 的:为了区分开其含义所以使用@



## 作业

### 作业1

docker nginx安装

```shell
查询nginx
#docker search nginx
安装指定版本
#docker pull nginx:1.19.10
运行起来并指定名称和映射端口
#docker run -d --name niginx01 -p 4454:80 nginx:1.19.10

#whereis nginx
询问系统路径

```



![image-20210416140123508](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210416140123508.png)





### 作业2

​	安装一个Tomcat



```shell
#docker run -it --rm tomcat:9.0
--rm是指用完就删除
#docker run -d -p 8031:80 tomcat:9.0 /bin/bash

#怎么在容器我提供路径
```



### 作业3

补助 ES+kibana

```shell
# ES 暴露了很多端口
# ES 十分消耗内存
# ES 的数据需要挂在到外部
#  docker pull elasticsearch:7.12.0
7.12.0: Pulling from library/elasticsearch
7a0437f04f83: Already exists
2b674c951ca3: Pull complete
06baeb69f25f: Pull complete
eeff01d19ce5: Pull complete
a994306398ca: Pull complete
2c002d76c1f6: Pull complete
6286f2196f9b: Pull complete
Digest: sha256:383e9fb572f3ca2fdef5ba2edb0dae2c467736af96aba2c193722aa0c08ca7ec
Status: Downloaded newer image for elasticsearch:7.12.0
docker.io/library/elasticsearch:7.12.0
#docker run -d --name test_elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.io/library/elasticsearch:7.12.1

#--net somenetwork? 关系到docker 网络配置
#使用 docker status 查看状态


#由于elasticsearch 消耗资源特别高所以我们需要通过环境变量加入一个参数 限制内存的使用，这里我们直接操作java的环境变量
# -e ES_JAVA_OPTS="-Xms64m -Xmx4g"

#dockerun --name elasticearch_test -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms3g -Xmx4g" elasticsearch:7.12.1
# 查看系统占用率
[akachi@AKACHI-PC-2018-wsl dell]$ docker stats 9d1b7b6f042469df8f229d3b565483bd2319061de268b8f980fda5a9c71fa369
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O     BLOCK I/O   PIDS
9d1b7b6f0424   elasticearch_test   0.74%     3.366GiB / 24.95GiB   13.49%    766B / 0B   0B / 0B     46

#最后查看是否成功

[akachi@AKACHI-PC-2018-wsl dell]$ curl localhost:9200
{
  "name" : "9d1b7b6f0424",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "BavyqNNXSzOoO3lEXtQSDA",
  "version" : {
    "number" : "7.12.1",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "3186837139b9c6b6d23c3200870651f10d3343b7",
    "build_date" : "2021-04-20T20:56:39.040728659Z",
    "build_snapshot" : false,
    "lucene_version" : "8.8.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```



### 作业4

安装一个 Kibana 并且连接elasticsearch



- docker之间互相调用基本原理图

![image-20210505195050614](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210505195050614.png)



### Swarm 

### compose 

### jenkins

### CI-CD