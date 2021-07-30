# docker 学习笔记

![白色](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/15/095704.jpg)



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
# 2、用国内的仓库y
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
# 6.5、使用 docker login登陆dockerhub
# 7、运行hello world
docker run --rm hello-world
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

### 小技巧

```shell
循环命令
i=0 ; while true ;do i=$(($i+1));sleep 1s;echo ${i};done
```



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

##### docker history

- docker history

  ```shell
  [akachi@AKACHI-PC-2018 dell]$ docker history mysql
  IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
  c0cdc95609f1   4 weeks ago   /bin/sh -c #(nop)  CMD ["mysqld"]               0B
  <missing>      4 weeks ago   /bin/sh -c #(nop)  EXPOSE 3306 33060            0B
  <missing>      4 weeks ago   /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B
  <missing>      4 weeks ago   /bin/sh -c ln -s usr/local/bin/docker-entryp…   34B
  <missing>      4 weeks ago   /bin/sh -c #(nop) COPY file:345a22fe55d3e678…   14.5kB
  <missing>      4 weeks ago   /bin/sh -c #(nop) COPY dir:2e040acc386ebd23b…   1.12kB
  <missing>      4 weeks ago   /bin/sh -c #(nop)  VOLUME [/var/lib/mysql]      0B
  <missing>      4 weeks ago   /bin/sh -c {   echo mysql-community-server m…   420MB
  <missing>      4 weeks ago   /bin/sh -c echo 'deb http://repo.mysql.com/a…   55B
  <missing>      4 weeks ago   /bin/sh -c #(nop)  ENV MYSQL_VERSION=8.0.25-…   0B
  <missing>      4 weeks ago   /bin/sh -c #(nop)  ENV MYSQL_MAJOR=8.0          0B
  <missing>      4 weeks ago   /bin/sh -c set -ex;  key='A4A9406876FCBD3C45…   2.61kB
  <missing>      4 weeks ago   /bin/sh -c apt-get update && apt-get install…   52.2MB
  <missing>      4 weeks ago   /bin/sh -c mkdir /docker-entrypoint-initdb.d    0B
  <missing>      4 weeks ago   /bin/sh -c set -eux;  savedAptMark="$(apt-ma…   4.17MB
  <missing>      4 weeks ago   /bin/sh -c #(nop)  ENV GOSU_VERSION=1.12        0B
  <missing>      4 weeks ago   /bin/sh -c apt-get update && apt-get install…   9.34MB
  <missing>      4 weeks ago   /bin/sh -c groupadd -r mysql && useradd -r -…   329kB
  <missing>      4 weeks ago   /bin/sh -c #(nop)  CMD ["bash"]                 0B
  <missing>      4 weeks ago   /bin/sh -c #(nop) ADD file:7362e0e50f30ff454…   69.3MB
  ```


##### docker tag 重命名镜像

​	这会Copy出一个镜像来

```shell
docker tag --help

Usage:  docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
```



##### docker save

保存镜像文件

##### docker load

载入镜像文件

##### docker import 

载入容器成为镜像文件



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


##### docker export

导出容器成为一个容器文件

相当于虚拟机快照

但是这个操作会与docker save不同，他会丢失掉历史数据。

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

##### docker login

登陆 hub docker命令

```shell
Log in to a Docker registry or cloud backend.
If no registry server is specified, the default is defined by the daemon.

Usage:
  docker login [OPTIONS] [SERVER] [flags]
  docker login [command]

Available Commands:
  azure       Log in to azure

Flags:
  -h, --help              Help for login
  -p, --password string   password
      --password-stdin    Take the password from stdin
  -u, --username string   username

Use "docker login [command] --help" for more information about a command.
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

##### dockers build

1. 地址 hub.docker.com

2. 使用 dockers login 登陆

3. 在服务器上提交镜像

   ```shell
   Log in to a Docker registry or cloud backend.
   If no registry server is specified, the default is defined by the daemon.
   
   Usage:
     docker login [OPTIONS] [SERVER] [flags]
     docker login [command]
   
   Available Commands:
     azure       Log in to azure
   
   Flags:
     -h, --help              Help for login
     -p, --password string   password
         --password-stdin    Take the password from stdin
     -u, --username string   username
   
   Use "docker login [command] --help" for more information about a command.
   ```

4. 登陆后提交 镜像使用 docker push

   ```shell
   #注意在使用docker push 时push的镜像必须是 [本用户名]/imagename[tag]
   docker push akachi/mytomcat:1.0
   The push refers to repository [docker.io/akachi/mytomcat]
   44793e2a4b5c: Pushed
   7ccebed91949: Pushed
   5f70bf18a086: Pushed
   2653d992f4ef: Pushed
   1.0: digest: sha256:e7b4e3a3536c69ba9cebbef5cd3f73c4a8d68eecb4fd21a75e0dd6339aedfbf6 size: 1160
   ```

##### docker network inspect

```shell
# 查看网络
[akachi@AKACHI-PC-2018 dell]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
4c060768ae23   bridge    bridge    local
920827256eba   host      host      local
30723751be30   none      null      local
2a2f74e8b124   thenet    bridge    local
```

```shell
#查看网络
[akachi@AKACHI-PC-2018 dell]$ docker network inspect thenet
#查看docker具体的网络情况
[akachi@AKACHI-PC-2018 dell]$ docker inspect 
```

##### docker network create

```shell
# 创建docker网络
[akachi@AKACHI-PC-2018 dell]$ docker network create --help

Usage:  docker network create [OPTIONS] NETWORK

Create a network

Options:
      --attachable           Enable manual container attachment
      --aux-address map      Auxiliary IPv4 or IPv6 addresses used by Network driver (default map[])
      --config-from string   The network from which to copy the configuration
      --config-only          Create a configuration only network
  -d, --driver string        Driver to manage the Network (default "bridge")
      --gateway strings      IPv4 or IPv6 Gateway for the master subnet
      --ingress              Create swarm routing-mesh network
      --internal             Restrict external access to the network
      --ip-range strings     Allocate container ip from a sub-range
      --ipam-driver string   IP Address Management Driver (default "default")
      --ipam-opt map         Set IPAM driver specific options (default map[])
      --ipv6                 Enable IPv6 networking
      --label list           Set metadata on a network
  -o, --opt map              Set driver specific options (default map[])
      --scope string         Control the network's scope
      --subnet strings       Subnet in CIDR format that represents a network segment
[akachi@AKACHI-PC-2018 dell]$
# 例子创建一个thenet
[akachi@AKACHI-PC-2018 dell]$ docker network create --driver bridge --subnet 192.114.2.0/24 --gateway 192.114.2.200 thenet
195dd09926a8b48d224441aef769a55f8fff6c55dbf2578619e0a5ae7c562693
[akachi@AKACHI-PC-2018 dell]$ docker network inspect thenet
[
    {
        "Name": "thenet",
        "Id": "195dd09926a8b48d224441aef769a55f8fff6c55dbf2578619e0a5ae7c562693",
        "Created": "2021-06-19T08:44:53.7318806Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.114.2.0/24",
                    "Gateway": "192.114.2.200"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
```

##### docker network remove thenet

```shell
#移除网络
[akachi@AKACHI-PC-2018 dell]$ docker network remove thenet
thenet
```

打包命令

##### docker network connect

connect命令的功能是连接一个容器到一个网络

 ```shell
 Commands:
   connect     Connect a container to a network
   
 
 [akachi@AKACHI-PC-2018 dell]$ docker network connect --help
 
 Usage:  docker network connect [OPTIONS] NETWORK CONTAINER
 
 Connect a container to a network
 
 Options:
       --alias strings           Add network-scoped alias for the container
       --driver-opt strings      driver options for the network
       --ip string               IPv4 address (e.g., 172.30.100.104)
       --ip6 string              IPv6 address (e.g., 2001:db8::33)
       --link list               Add link to another container
       --link-local-ip strings   Add a link-local address for the container
       
 #使用方式参考文档
 docker network connect [参数] 网络名称 容器名或ID
 ```



#### 命令备注

​	在 官网的 reference(查询) docker cli(docker客户端下有所有命令)



##### docker命令图

![image-20210609154440482](https://raw.githubusercontent.com/akachi10/notes/master/pic/20210614215658.png)
### 容器数据卷

#### 什么是容器数据卷

吧数据放到容器外面，持久化数据

``` shell
docker run -it -v /home/test:/home centos /bin/bash
# 测试下来这种情况不能从外部修改数据
```

- 操作MySQL容器挂载

``` shell
# 运行一个docker
docker run -d -p 3316:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name Test_V_MySQL mysql:5.7
# 查看所有的卷
docker volume ls
# 查看卷具体位置
docker volume inspect [name]
```

#### 具名挂载

``` shell
#指定外部挂载
docker run -d -P --name negix02 -v juming-ngix:/etc/nginx nginx
```



#### 匿名挂载

```shell
# 不指定外部挂载
docker run -d -P --name negix02 -v /etc/nginx nginx
```

#### 概况

- 具名挂载 -v 容器内路径
- 匿名挂载 -v 卷名:容器名
- 指定路径挂载 -v /宿主机路径:/容器内路径

#### 特殊情况

```shell
# 改变读写权限
RO 只读
RW 可读写
docker run -d -p --name negix03 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -p --name negix03 -v juming-nginx:/etc/nginx:rw nginx

# RO指容器无法从内部改变
# RW指容器有读写权限
```

#### 多MySQL同步数据

![image-20210608172454714](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/21/114739.png)

```shell
# docker run -it --name docker01 akachi/centos
# docker run -it --name docker02 --volumes-from docker01 akachi/centos
# docker run -it --name docker03 --volumes-from docker01 akachi/centos
# 在以上情况下实际上是docker 02 与03 都挂载卷到了宿主机的相同目录下

# 测试MySQL
docker run -d -p 3316:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
# 启动mysql2
docker run -d -p 3326:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from  mysql01 mysql:5.7
```

### docker 网络

![image-20210615234938506](https://raw.githubusercontent.com/akachi10/notes/master/pic/20210615234938.png)



**使用docker网络使不同容器中的对象能够互相调用，比如tomcat调用mysql**

![image-20210615235125452](https://raw.githubusercontent.com/akachi10/notes/master/pic/20210615235125.png)

> 查看tomcat所在地址

``` shell
# 启动tomcat
[akachi@AKACHI-PC-2018 dell]$ docker run -d -P --name tomcat01 tomcat

Status: Downloaded newer image for tomcat:latest
#查看tomcat本机地址
[akachi@AKACHI-PC-2018 dell]$ docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/sit 0.0.0.0 brd 0.0.0.0
9: eth0@if10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
[akachi@AKACHI-PC-2018 dell]$
# 系统分配了一个eth0@if10地址给这个dockers服务器
```

>每启动一个dockers都会多出一对网卡

![image-20210616001554868](https://raw.githubusercontent.com/akachi10/notes/master/pic/20210616001554.png)

> 原理

1. 每启动一个dockers容器docker就会为它分配一个IP地址
2. 只要安装了dockers就会启动一个docker0
3. docker0网卡默认使用桥接模式。
4. docker0使用的是evth-pair技术！

> 什么是evth-pair 技术！

```shell
#evth-pair 技术就是一堆虚拟设备接口
#他们是成对出现的连接着协议。
#Bridge OVS都是基于evth-pair的
```

> 网络模型图
>
> ![image-20210616003446277](https://raw.githubusercontent.com/akachi10/notes/master/pic/20210616003446.png)
>
> 大概率是以上情况，使用的是evth-pair技术的 Bridge(桥接模式)进行连接，Linuxbriged 充当一个路由器。
>
> 在桥接的情况下，LinuxBridge要么使用广播要么使用路由表的形式去转发请求



> 思考: 能不能再docker环境中使用dns服务

#### --link 

``` shell
[akachi@AKACHI-PC-2018 dell]$ docker run -d -P --name tomcat03 --link tomcat01 tomcat
09e36e36926cb49f546f33558803e09f9928d3e83a2cef4abbdbf2960b970e38
[akachi@AKACHI-PC-2018 dell]$ doc^C
[akachi@AKACHI-PC-2018 dell]$ dc^C
[akachi@AKACHI-PC-2018 dell]$ docker exec tomcat03 ping tomcat01
PING tomcat01 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat01 (172.17.0.3): icmp_seq=1 ttl=64 time=0.055 ms
64 bytes from tomcat01 (172.17.0.3): icmp_seq=2 ttl=64 time=0.028 ms
```

> 会有很多坑，在--link的情况下  

```shell
#通过network命令查看docker网络
[akachi@AKACHI-PC-2018 dell]$ docker network --help

Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

Run 'docker network COMMAND --help' for more information on a command.
[akachi@AKACHI-PC-2018 dell]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
511e6530ee56   bridge    bridge    local
920827256eba   host      host      local
30723751be30   none      null      local
[akachi@AKACHI-PC-2018 dell]$ docker network inspect 511e6530ee56
#通过以上命令我们可以看到所有网络
```

> 查看dockers容器冲在哪里显示了links的信息

```shell 
#通过dockers inspect tomcat03
#可以看到一个Links :[]
```

![image-20210616012504737](https://raw.githubusercontent.com/akachi10/notes/master/pic/20210616012504.png)

>实现原理

```shell
#实际上是通过hosts来实现的连接
root@09e36e36926c:/etc# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.3      tomcat01 627b81dd0837
172.17.0.4      09e36e36926c
```



> 现在不用--link了

#### dockers网络原理

> 最新的情况下我们以及不建议用docker0 来作为docker的网络了
>
> 正真的容器互联技术:

```shell
#查看所有docker网络
[akachi@AKACHI-PC-2018 ~]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
511e6530ee56   bridge    bridge    local
920827256eba   host      host      local
30723751be30   none      null      local

```

> 网络有几种模式？

- bridge: 桥接(默认)

- none:不配置网络

- host: 主机模式

- container:容器内网络联通(用的少)

``` shell
docker run -d -P --name tomcat11 --net bridge tomcat
#通过这个方式启动的tomcat与默认的情况下启动是等价的，只是显示指定了bridge
```

> 创建一个子网

```shell
# driver bridge
# -subnet 192.114.2.0/24
# --gateway 192.114.2.200
[root@localhost ~]#  docker network create --driver bridge --subnet 192.114.2.0/24 --gateway 192.114.2.200 thenet
3a6644ad4aaa6b236a42149ed9212b7dcd9ad1fda4a2309ac4ae664d0cedc7b9

[root@localhost ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
594bdad93d28   bridge    bridge    local
1578412b80fd   host      host      local
653c0a1495dd   none      null      local
3a6644ad4aaa   thenet    bridge    local
# 现在ip addr中多了一条数据
[root@localhost ~]# ip addr
102: br-3a6644ad4aaa: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:10:d3:1b:22 brd ff:ff:ff:ff:ff:ff
    inet 192.114.2.200/24 brd 192.114.2.255 scope global br-3a6644ad4aaa
       valid_lft forever preferred_lft forever
       
# 看到了自己的网络
[akachi@AKACHI-PC-2018 dell]$ docker network inspect thenet
[
    {
        "Name": "thenet",
        "Id": "195dd09926a8b48d224441aef769a55f8fff6c55dbf2578619e0a5ae7c562693",
        "Created": "2021-06-19T08:44:53.7318806Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.114.2.0/24",
                    "Gateway": "192.114.2.200"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

#### 测试一个自己的tomcat

``` shell
#在新的网络thenet中创建两个tomcat
[akachi@AKACHI-PC-2018 dell]$ docker run -d -P --name tomcat-thenet01 --net thenet tomcat
96a4ccf5a2504e4539e11e825e065129aaa1c88483c41b6b84ad3544ccb9f3ed
[akachi@AKACHI-PC-2018 dell]$ docker run -d -P --name tomcat-thenet02 --net thenet tomcat
a75d75b18d1532e6b89d4e81eeae685534a9d3f2c287e50cea4b2277e57bd0ce
#查看新的网络
[akachi@AKACHI-PC-2018 dell]$ docker network inspect thenet
[
    {
        "Name": "thenet",
        "Id": "195dd09926a8b48d224441aef769a55f8fff6c55dbf2578619e0a5ae7c562693",
        "Created": "2021-06-19T08:44:53.7318806Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.114.2.0/24",
                    "Gateway": "192.114.2.200"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "96a4ccf5a2504e4539e11e825e065129aaa1c88483c41b6b84ad3544ccb9f3ed": {
                "Name": "tomcat-thenet01",
                "EndpointID": "09d10adc87108b9bb7b26b95bcd64b92ee3c7af3c578f667867f64a16da198fc",
                "MacAddress": "02:42:c0:72:02:01",
                "IPv4Address": "192.114.2.1/24",
                "IPv6Address": ""
            },
            "a75d75b18d1532e6b89d4e81eeae685534a9d3f2c287e50cea4b2277e57bd0ce": {
                "Name": "tomcat-thenet02",
                "EndpointID": "d82e07548a9b9990fc31e82d9e47d10c0e691bde6aa38c470772e792982fbd67",
                "MacAddress": "02:42:c0:72:02:02",
                "IPv4Address": "192.114.2.2/24",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
#我们发现多了两个节点分别是2.1和2.2
#我们再进入其中一台ping另外一台
[akachi@AKACHI-PC-2018 dell]$ docker exec -it tomcat-thenet02 /bin/bash
root@a75d75b18d15:/usr/local/tomcat# ping tomcat-thenet01
PING tomcat-thenet01 (192.114.2.1) 56(84) bytes of data.
64 bytes from tomcat-thenet01.thenet (192.114.2.1): icmp_seq=1 ttl=64 time=0.087 ms
64 bytes from tomcat-thenet01.thenet (192.114.2.1): icmp_seq=2 ttl=64 time=0.032 ms
#真神奇这个网络中的对象直接可以相互ping通
#自定义的桥接修复了docker0的问题
```

#### 网络连通

我们已经知道在新建的桥接之上的容器是可以相互连通的。

现在要联通不同网络之间的操作

```shell
#创建一个tomcat
[akachi@AKACHI-PC-2018 dell]$ docker run -d -P --name tomcat01 tomcat
58f4ddd56c9c3e02033817b411b4650758cd855e25ee2056d1cf0ce03e43f5f4
#查看命令
[akachi@AKACHI-PC-2018 dell]$ docker network connect --help

Usage:  docker network connect [OPTIONS] NETWORK CONTAINER

Connect a container to a network

Options:
      --alias strings           Add network-scoped alias for the container
      --driver-opt strings      driver options for the network
      --ip string               IPv4 address (e.g., 172.30.100.104)
      --ip6 string              IPv6 address (e.g., 2001:db8::33)
      --link list               Add link to another container
      --link-local-ip strings   Add a link-local address for the container
      
#我们得知查看是通过先网络名后容器命来调用连接的
[akachi@AKACHI-PC-2018 dell]$ docker network connect thenet tomcat01
#调用连接tomcat01 连接到thenet上
#查看容器网络
[akachi@AKACHI-PC-2018 dell]$ docker inspect tomcat01
  "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "7b530392865cc2f1dc9ec6c3aeacc68193959bead9b0e841acb8230f080094fa",
                    "EndpointID": "f2e7077db1ef912bbad2a1633f238702b85e079d8d221614aeb3415e5b86cb61",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03",
                    "DriverOpts": null
                },
                "thenet": {
                    "IPAMConfig": {},
                    "Links": null,
                    "Aliases": [
                        "58f4ddd56c9c"
                    ],
                    "NetworkID": "195dd09926a8b48d224441aef769a55f8fff6c55dbf2578619e0a5ae7c562693",
                    "EndpointID": "dd68289ac0e60f31eef960173709271c8f8f69a0d5964f1415a47edbdb1c1d65",
                    "Gateway": "192.114.2.200",
                    "IPAddress": "192.114.2.3",
                    "IPPrefixLen": 24,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:c0:72:02:03",
                    "DriverOpts": {}
                }
           }
#我们查看网络得知这个tomcat01已经拥有两个网络并且IP风别显示出来了
#测试双向是否能ping同双向IP是否能ping通
[akachi@AKACHI-PC-2018 dell]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
58f4ddd56c9c   tomcat         "catalina.sh run"        6 minutes ago    Up 6 minutes    0.0.0.0:49155->8080/tcp, :::49155->8080/tcp            tomcat01
a75d75b18d15   tomcat         "catalina.sh run"        22 minutes ago   Up 22 minutes   0.0.0.0:49154->8080/tcp, :::49154->8080/tcp            tomcat-thenet02
96a4ccf5a250   tomcat         "catalina.sh run"        22 minutes ago   Up 22 minutes   0.0.0.0:49153->8080/tcp, :::49153->8080/tcp            tomcat-thenet01
c191a5546e2b   mysql:8.0.25   "docker-entrypoint.s…"   4 weeks ago      Up 3 hours      0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql_test
[akachi@AKACHI-PC-2018 dell]$ docker exec -it tomcat01 ping tomcat-thenet02
PING tomcat-thenet02 (192.114.2.2) 56(84) bytes of data.
64 bytes from tomcat-thenet02.thenet (192.114.2.2): icmp_seq=1 ttl=64 time=0.099 ms
^C
--- tomcat-thenet02 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.099/0.099/0.099/0.000 ms
[akachi@AKACHI-PC-2018 dell]$ docker exec -it tomcat-thenet02 ping tomcat01
PING tomcat01 (192.114.2.3) 56(84) bytes of data.
64 bytes from tomcat01.thenet (192.114.2.3): icmp_seq=1 ttl=64 time=0.032 ms
^C
--- tomcat01 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.032/0.032/0.032/0.000 ms
[akachi@AKACHI-PC-2018 dell]$ docker exec -it tomcat-thenet02 ping 192.114.2.3
PING 192.114.2.3 (192.114.2.3) 56(84) bytes of data.
64 bytes from 192.114.2.3: icmp_seq=1 ttl=64 time=0.040 ms
64 bytes from 192.114.2.3: icmp_seq=2 ttl=64 time=0.051 ms
^C
--- 192.114.2.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 73ms
rtt min/avg/max/mdev = 0.040/0.045/0.051/0.008 ms
[akachi@AKACHI-PC-2018 dell]$ docker exec -it tomcat01 ping 192.168.2.2
PING 192.168.2.2 (192.168.2.2) 56(84) bytes of data.
64 bytes from 192.168.2.2: icmp_seq=1 ttl=37 time=5.10 ms
64 bytes from 192.168.2.2: icmp_seq=2 ttl=37 time=5.49 ms
^C
--- 192.168.2.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 2ms
rtt min/avg/max/mdev = 5.095/5.290/5.486/0.208 ms
#我们先查看了所有容器
#先在tomcat01中用容器名ping tomcat-thenet02
#再用tomcat-thenet02中用容器名ping tomcat01
#再试ip结果都是通的
#当然ping tomcat01的另一个网络docker0 中的IP必然是不通的这里就不试了
```



### dockerFile

#### 初步认识dockerFile



Dockerfile是官方命名创建Dockerfile文件时使请使用这个



dockerFile就是构建docker镜像的文件。是一段命令脚本，通过脚本可以生成镜像



``` shell
# 创建一个dockerfile1 文件
# 内容如下
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "---end---"
CMD /bin/bash
# build一个docker image
docker build -f dockerfile1 -t akachi/centos:1.0 .
```

```shell
# 启动容器
docker run -it akachi/centos:1.0 /bin/bash
```

##### dockerFile 构建步骤

1. 编写一个dockerfile文件
2. 构建一个镜像
3. docker run 运行镜像
4. docker push 发布镜像 DockerHub

##### DockerFile 命令 

- 官方地址

  https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

- 常用命令

  ![image-20210609154456622](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/21/114655.png)

  ![image-20210610135844359](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/21/114658.png)

  ```shell
  FROM		#镜像基于谁
  MAINTAINER	#作者 akachi<zsts@hotmail.com>
  RUN			#谁构建
  CMD			#操作 指定这个容器启动的时候要运行的命令,只有最后一个会生效，可被替代
  ADD			#添加内容如压缩包 会自动解压
  WORKDIR		#镜像的工作目录
  VOLUME		#容器卷
  EXPOSE		#暴露端口
  ENTRYPOINT	#操作 指定这个容器启动的时候要运行的命令。
  ONBUILD		#当构建一个被继承的DockerFile这个时候会运行一个ONBUILD的指令。
  COPY		#类似ADDD，将我们的文件拷贝到镜像中
  ENV			#构建的时候设置环境变量 -e
  ```

  > 创建一个自己的centOS
  >
  > :可以抄袭hub.docker.com

  数据

  

- 地方



![image-20210609154447560](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/21/114702.png)





##### 基础知识

- 每一个保留的关键字都是大写
- 执行从上到下
- #表示注释
- 每一个指令都会创建并提交一个新的镜像层。

![image-20210609154807565](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/21/114705.png)

dockerfile是面向开发的，我没交付项目都是用docker的。

步骤：上线、部署、运维

DockerFile：定义了步骤

DockerImages:通过dockerFile构建生成镜像

Docker容器：容器就是运行的镜像

##### 练习



```shell
#创建  mydockerfile-centos文件
FROM centos
MAINTAINER akachi<zsts@hotmail.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 8084

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

#执行docker build
docker build -f mydockerfile-centos -t mycentos:0.1 .

#在这个练习中我们加入了config命令，设定了用户工作路径为MYPATH并且创建了一个docker images


```



> CMD和 ENTRYPOINT的区别

```shell
CMD			#操作 指定这个容器启动的时候要运行的命令,只有最后一个会生效，可被替代
ENTRYPOINT	#操作 指定这个容器启动的时候要运行的命令。
```

###### 测试CMD

```shell
# 创建test
FROM centos
CMD /bin/bash
CMD ls -a
# 运行起来可以
```

###### 测试ENTRYPOINT

```shell
FROM centos
ENTRYPOINT ["ls","-a"]
# 在这种情况下 可以追加-l命令
[akachi@AKACHI-PC-2018 dockerfile]$ docker run -it test:3 -l
total 56
drwxr-xr-x   1 root root 4096 Jun 11 09:28 .
drwxr-xr-x   1 root root 4096 Jun 11 09:28 ..
```

#### 发布自己的镜像

1. 地址 hub.docker.com

2. 在服务器上提交镜像

   ```shell
   Log in to a Docker registry or cloud backend.
   If no registry server is specified, the default is defined by the daemon.
   
   Usage:
     docker login [OPTIONS] [SERVER] [flags]
     docker login [command]
   
   Available Commands:
     azure       Log in to azure
   
   Flags:
     -h, --help              Help for login
     -p, --password string   password
         --password-stdin    Take the password from stdin
     -u, --username string   username
   
   Use "docker login [command] --help" for more information about a command.
   ```

3. 登陆后提交 镜像使用 docker push

   ```shell
   #注意在使用docker push 时push的镜像必须是 [本用户名]/imagename[tag]
   docker push akachi/mytomcat:1.0
   The push refers to repository [docker.io/akachi/mytomcat]
   44793e2a4b5c: Pushed
   7ccebed91949: Pushed
   5f70bf18a086: Pushed
   2653d992f4ef: Pushed
   1.0: digest: sha256:e7b4e3a3536c69ba9cebbef5cd3f73c4a8d68eecb4fd21a75e0dd6339aedfbf6 size: 1160
   ```


### Docker Compose

操作dockersComposeke以操作多个dockers文件

![image-20210624144530010](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/24/144544.png)



官方文档:

dockerCommpose是一个定义多个容器的，

可以通过YAML进行配置的工具。

single command 看看有多少命令

1. 我们要有一个Dockerfile
2. 我们要写一个docker-compose.yml
3. 运行 docker compose up 

**dockercompose的作用是:批量容器编排**

> 理解 
>
> compose是docker官方的开源项项目 需要独立安装

`docker-compose.yml` looks like this:

```
version: "3.9"  # optional since v1.27.0
services:
  web: #Web服务
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis #依赖
  redis: #redis 服务
    image: redis
volumes:
  logvolume01: {}
```



> 概念
>
> services= 容器
>
> project = 项目 一组关联的容器
>
> volumes:挂载 

#### linux安装docker compose

```shell
uname -a
查看系统

sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```



#### 体验

[官方体验步骤] docs.docker.com/compose/gettingstarted/

1、编写应用

```shell
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```

2、载入依赖程序

```shell
flask
redis
```

3、编写dockerfile文件

```shell
FROM centos
MAINTAINER akachi<zsts@hotmail.com>

ENV yum -y install vim
ENV MYPATH /usr/local
ENV REDIS_NAME=redis
WORKDIR $MYPATH

ADD addfile/jdk-15_linux-x64_bin.tar.gz $MYPATH

ADD *.jar $MYPATH/app.jar
#ADD addfile/apache-tomcat-10.0.6.tar.gz $MYPATH

ENV JAVA_HOME $MYPATH/jdk-15
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
#ENV CATALINA_HOME $MYPATH/apache-tomcat-10.0.6
#ENV CATALINA_BASE $CATALINA_BASE

ENV PATH $PATH:$JAVA_HOME/bin

EXPOSE 8080

# CMD startup.sh && tail -f $CATALINA_HOME/logs/catalina.out
CMD java -jar app.jar
```

4、编写docker-compose.yml文件

```shell
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

5、运行使用docker-compose up

```shell
[akachi@AKACHI-PC-2018 composetest]$ docker-compose up
Starting composetest_redis_1 ... done
Starting composetest_web_1   ... done
Attaching to composetest_web_1, composetest_redis_1
redis_1  | 1:C 07 Jul 2021 08:20:20.647 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 07 Jul 2021 08:20:20.648 # Redis version=6.2.4, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 07 Jul 2021 08:20:20.648 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis_1  | 1:M 07 Jul 2021 08:20:20.648 * monotonic clock: POSIX clock_gettime
redis_1  | 1:M 07 Jul 2021 08:20:20.648 * Running mode=standalone, port=6379.
redis_1  | 1:M 07 Jul 2021 08:20:20.648 # Server initialized
redis_1  | 1:M 07 Jul 2021 08:20:20.648 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_1  | 1:M 07 Jul 2021 08:20:20.648 * Loading RDB produced by version 6.2.4
redis_1  | 1:M 07 Jul 2021 08:20:20.649 * RDB age 94 seconds
redis_1  | 1:M 07 Jul 2021 08:20:20.649 * RDB memory usage when created 0.79 Mb
redis_1  | 1:M 07 Jul 2021 08:20:20.649 * DB loaded from disk: 0.000 seconds
redis_1  | 1:M 07 Jul 2021 08:20:20.649 * Ready to accept connections
web_1    |  * Serving Flask app 'app.py' (lazy loading)
web_1    |  * Environment: production
web_1    |    WARNING: This is a development server. Do not use it in a production deployment.
web_1    |    Use a production WSGI server instead.
web_1    |  * Debug mode: off
web_1    |  * Running on all addresses.
web_1    |    WARNING: This is a development server. Do not use it in a production deployment.
web_1    |  * Running on http://172.18.0.3:5000/ (Press CTRL+C to quit)
web_1    | 172.18.0.1 - - [07/Jul/2021 08:20:25] "GET / HTTP/1.1" 200 -
```



> dockercompose会做哪些事？
>
> 1、创建并命名进项[dirname]-[imgname]-[NUMBER]
>
> ​	这种设计是为了高可用性服务的
>
> 2、创建一个与[dirname]-default同名的网络
>
> 3、创建相关的镜像
>
> ![image-20210707163348766](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210707163348766.png)
>
> 
>
> 4、启动的容器默认会绑定到新网络啊
>
> ![image-20210707165140898](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210707165140898.png)
>
> 



#### 停止



```shell
[akachi@AKACHI-PC-2018 composetest]$ docker-compose down
Stopping composetest_web_1   ... done
Stopping composetest_redis_1 ... done
Removing composetest_web_1   ... done
Removing composetest_redis_1 ... done
# 使用docker-compose down 时会主动删除所有倍docker-compose创建的内容包括网络
```



#### 配置解析

docker-compose.yaml

> docker-compose中主要内容分为三块
>
> 1、指定版本、不同的版本对应不同的dockerengine，不能乱写在以下文档中会有明确介绍
>
> 2、配置服务
>
> 3、配置全局的内容包括network与、volumes





> 官方文档位置
>
> ![image-20210707171425670](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210707171425670.png)
>
> https://docs.docker.com/compose/compose-file/compose-file-v3/
>
> ![image-20210707171532591](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210707171532591.png)



```shell
# 例子

version: "3.9"# 版本 	
services: #服务
    redis: #服务1
        image: redis:alpine #指定镜像
        ports: #端口
          - "6379"
        networks: #网络
          - frontend
        deploy: #部署相关内容 
          replicas: 2 #拷贝
          update_config: #跟新内容
            parallelism: 2 #并行
            delay: 10s
          restart_policy:
            condition: on-failure

    vote:
        image: dockersamples/examplevotingapp_vote:before
        ports:
          - "5000:80"
        networks:
          - frontend
        depends_on: #比较关键的内容 指定他们的依赖
          - redis
        deploy: #副本
          replicas: 2
          update_config:
            parallelism: 2
          restart_policy:
            condition: on-failure

```

#### worldpress

```shell
#https://docs.docker.com/samples/wordpress/


```

```shell
#这里有一个docker-compose.yml文件

version: "3.9"

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
  
#使用 docker-compose up -d 可以后台运行
```

#### 写一个spring boot 复刻以下计数器



![image-20210707180015756](C:/Users/dell/AppData/Roaming/Typora/typora-user-images/image-20210707180015756.png)



```shell
#1.写好计数器项目
#2.准备文件
[akachi@AKACHI-PC-2018 addfile]$ ls
apache-tomcat-10.0.6.tar.gz  jdk-15_linux-x64_bin.tar.gz

[akachi@AKACHI-PC-2018 composetest.bak]$ ls
addfile StudyDockerCompose-0.0.1-SNAPSHOT.jar

#3 写一个Dockerfile
FROM centos
MAINTAINER akachi<zsts@hotmail.com>

ENV yum -y install vim
ENV MYPATH /usr/local
ENV REDIS_NAME=redis #指定环境变量
WORKDIR $MYPATH

ADD addfile/jdk-15_linux-x64_bin.tar.gz $MYPATH

ADD *.jar $MYPATH/app.jar
#ADD addfile/apache-tomcat-10.0.6.tar.gz $MYPATH

ENV JAVA_HOME $MYPATH/jdk-15
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
#ENV CATALINA_HOME $MYPATH/apache-tomcat-10.0.6
#ENV CATALINA_BASE $CATALINA_BASE

ENV PATH $PATH:$JAVA_HOME/bin

EXPOSE 8080

# CMD startup.sh && tail -f $CATALINA_HOME/logs/catalina.out
CMD java -jar app.jar
#4 写一个docker-compose.yml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:8080"
  redis:
    image: "redis:alpine"
#5 启动它 
docker-compose up -d
# 如果使用 docker-compose up -d --build 的话会重新打包，这样可以保证在更改后仍然可以获得最新的镜像

#6 测试
[akachi@AKACHI-PC-2018 composetest.bak]$ curl http://localhost:5000/count/getAndRefurbish
7
#7 结束它
docker-compose down
```



 ### Docker Swarm

集群方式部署

- 购买阿里云服务器

### Dockers Stack

```shell
docker-compose 单机部署

docker stack部署,集群部署

docker-compose up -d aaa.yaml

#

docker stack deploy aaa.yaml
[root@swarm01 ~]# docker stack --help

Usage:  docker stack [OPTIONS] COMMAND

Manage Docker stacks

Options:
      --orchestrator string   Orchestrator to use (swarm|kubernetes|all)

Commands:
  deploy      Deploy a new stack or update an existing stack
  ls          List stacks
  ps          List the tasks in the stack
  rm          Remove one or more stacks
  services    List the services in the stack

Run 'docker stack COMMAND --help' for more information on a command.

```



### Docker Secret

```shell
[root@swarm01 ~]# docker secret

Usage:  docker secret COMMAND

Manage Docker secrets

Commands:
  create      Create a secret from a file or STDIN as content
  inspect     Display detailed information on one or more secrets
  ls          List secrets
  rm          Remove one or more secrets

Run 'docker secret COMMAND --help' for more information on a command.

```



### Docker  Config

```shell
[root@swarm01 ~]# docker config

Usage:  docker config COMMAND

Manage Docker configs

Commands:
  create      Create a config from a file or STDIN
  inspect     Display detailed information on one or more configs
  ls          List configs
  rm          Remove one or more configs

Run 'docker config COMMAND --help' for more information on a command.

```



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

- 在Dockerfile的CMD中 && 表示之后要执行。

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



### 作业5 Dockerfile tomcat

```shell
# 创建Dockerfile 
FROM centos
MAINTAINER akachi<zsts@hotmail.com>

ENV yum -y install vim
ENV MYPATH /usr/local
WORKDIR $MYPATH

ADD addfile/jdk-15_linux-x64_bin.tar.gz $MYPATH
ADD addfile/apache-tomcat-10.0.6.tar.gz $MYPATH

ENV JAVA_HOME $MYPATH/jdk-15
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME $MYPATH/apache-tomcat-10.0.6
ENV CATALINA_BASE $CATALINA_BASE

ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD startup.sh && tail -f $CATALINA_HOME/logs/catalina.out

# build Dockerfile
# 由于命名Dockerfile 在docker build的时候是不需要指定-f 的
docker build -t mytomcat .
# 运行dockersfile
# 指定两个
docker run -d -p 8080:8080 -v /home/akachi/dockerfile/webapps:/usr/local/apache-tomcat-10.0.6/webapps -v /home/akachi/dockerfile/logs:/usr/local/apache-tomcat-10.0.6/logs --name tomcat01 mytomcat
```



### 测试6 在阿里云镜像服务器中上传docker镜像

1. 找到阿里云的镜像服务器

   搜索 "镜像容器服务" > 点击管理控制台 >点击个人或企业实例

   或者通过URL直接进入 

   https://cr.console.aliyun.com/cn-hangzhou/instances > 点击个人实例或企业实例

2. 查看文档 

   点击仓库管理>镜像仓库/某个仓库，并且参考文档

3. 文档内容

   ## 1. 登录阿里云Docker Registry

   ```
   $ docker login --username=z****@hotmail.com registry.cn-hangzhou.aliyuncs.com
   ```

   用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

   您可以在访问凭证页面修改凭证密码。

   ## 2. 从Registry中拉取镜像

   ```shell
   $ docker pull registry.cn-hangzhou.aliyuncs.com/akachi/tomcat:[镜像版本号]
   ```

   ## 3. 将镜像推送到Registry

   ```shell
   $ docker login --username=z****@hotmail.com registry.cn-hangzhou.aliyuncs.com
   $ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/akachi/tomcat:[镜像版本号]
   $ docker push registry.cn-hangzhou.aliyuncs.com/akachi/tomcat:[镜像版本号]
   ```

   请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

   ## 4. 选择合适的镜像仓库地址

   从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

   如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-hangzhou.aliyuncs.com 作为Registry的域名登录。

   ## 5. 示例

   使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

   ```shell
   $ docker images
   REPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
   registry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB
   $ docker tag 37bb9c63c8b2 registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
   ```

   使用 "docker push" 命令将该镜像推送至远程。

   ```shell
   $ docker push registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
   ```

### 作业7 Redis

分片+高可用+负载均衡

这是一个三主三从的集群

![image-20210621135359081](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/21/135402.png)

1. 创建网络

   ```shell
   [akachi@AKACHI-PC-2018 dell]$ docker network create --driver bridge --gateway 192.114.7.15 --subnet 192.114.7.0/24 redisnet
   0320e12a36b17917fc16a16ebe2584ecf53e1a92cd8aece99c004626b28feea9
   ```

2. 创建配置文件

   ```shell
   #执行这个脚本创建配置文件
   for port in $(seq 1 6);\
   do \
   mkdir -p /home/akachi/redis/node-${port}/conf
   touch /home/akachi/redis/node-${port}/conf/redis.conf
   cat << fatherakachi >/home/akachi/redis/node-${port}/conf/redis.conf
   port 6379
   bind 0.0.0.0
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 5000
   cluster-announce-ip 192.114.7.${port}
   cluster-announce-port 6379
   cluster-announce-bus-port 16379
   appendonly yes
   fatherakachi
   done
   ```

3. 创建 docker

   ```shell
   #创建redis
   for id in $(seq 1 6)
   do
   docker run -p 637${id}:6379 -p 1637${id}:16379 --name redis-${id} \
   -v /home/akachi/redis/node-${id}/data:/data \
   -v /home/akachi/redis/node-${id}/conf/redis.conf:/etc/redis/redis.conf \
   -d --net redisnet --ip 192.114.7.${id} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
   done
   
   #移除失败的
   for id in $(seq 1 6); 
   do 
   docker rm -f redis-${id};
   done
   
   ```

4. 进入 docker1

   ```shell
   C:\Users\dell>docker exec -it redis-1 /bin/sh
   /data #
   ```

5. 创建redis集群

   ```shell
    redis-cli --cluster create 192.114.7.1:6379 192.114.7.2:6379 192.114.7.3:6379 192.114.7.4:6379 192.114.7.5:6379 192.114.7.6:6379 --cluster-replic
   as 1
   >>> Performing hash slots allocation on 6 nodes...
   Master[0] -> Slots 0 - 5460
   Master[1] -> Slots 5461 - 10922
   Master[2] -> Slots 10923 - 16383
   Adding replica 192.114.7.5:6379 to 192.114.7.1:6379
   Adding replica 192.114.7.6:6379 to 192.114.7.2:6379
   Adding replica 192.114.7.4:6379 to 192.114.7.3:6379
   M: d87b93316c2ed32a38bae18a09bb26343adc8cad 192.114.7.1:6379
      slots:[0-5460] (5461 slots) master
   M: 182a391d7956ff6a9acbda0fa386946de1b09020 192.114.7.2:6379
      slots:[5461-10922] (5462 slots) master
   M: 9d6568bd86aa5f6b4a4d14cc5be2c7b0c1d677ac 192.114.7.3:6379
      slots:[10923-16383] (5461 slots) master
   S: dd813964879499ee1f6ef5395cb9b45e7d32a005 192.114.7.4:6379
      replicates 9d6568bd86aa5f6b4a4d14cc5be2c7b0c1d677ac
   S: 7da7072595561b550096ba9e6b3ea93b91024887 192.114.7.5:6379
      replicates d87b93316c2ed32a38bae18a09bb26343adc8cad
   S: 4df881acdcece81949615c8dc1ac1c1f9ba081c3 192.114.7.6:6379
      replicates 182a391d7956ff6a9acbda0fa386946de1b09020
   Can I set the above configuration? (type 'yes' to accept): yes
   >>> Nodes configuration updated
   >>> Assign a different config epoch to each node
   >>> Sending CLUSTER MEET messages to join the cluster
   Waiting for the cluster to join
   ...
   >>> Performing Cluster Check (using node 192.114.7.1:6379)
   M: d87b93316c2ed32a38bae18a09bb26343adc8cad 192.114.7.1:6379
      slots:[0-5460] (5461 slots) master
      1 additional replica(s)
   M: 182a391d7956ff6a9acbda0fa386946de1b09020 192.114.7.2:6379
      slots:[5461-10922] (5462 slots) master
      1 additional replica(s)
   S: dd813964879499ee1f6ef5395cb9b45e7d32a005 192.114.7.4:6379
      slots: (0 slots) slave
      replicates 9d6568bd86aa5f6b4a4d14cc5be2c7b0c1d677ac
   S: 4df881acdcece81949615c8dc1ac1c1f9ba081c3 192.114.7.6:6379
      slots: (0 slots) slave
      replicates 182a391d7956ff6a9acbda0fa386946de1b09020
   M: 9d6568bd86aa5f6b4a4d14cc5be2c7b0c1d677ac 192.114.7.3:6379
      slots:[10923-16383] (5461 slots) master
      1 additional replica(s)
   S: 7da7072595561b550096ba9e6b3ea93b91024887 192.114.7.5:6379
      slots: (0 slots) slave
      replicates d87b93316c2ed32a38bae18a09bb26343adc8cad
   [OK] All nodes agree about slots configuration.
   >>> Check for open slots...
   >>> Check slots coverage...
   [OK] All 16384 slots covered.
   /data #
   ```

6. 测试高可用性是否成功

   ```shell
   #连接集群
   redis-cli -c
   #查看集群信息
   127.0.0.1:6379> cluster
   (error) ERR wrong number of arguments for 'cluster' command
   127.0.0.1:6379>
   127.0.0.1:6379> cluster info
   cluster_state:ok
   cluster_slots_assigned:16384
   cluster_slots_ok:16384
   cluster_slots_pfail:0
   cluster_slots_fail:0
   cluster_known_nodes:6
   cluster_size:3
   cluster_current_epoch:6
   cluster_my_epoch:1
   cluster_stats_messages_ping_sent:601
   cluster_stats_messages_pong_sent:618
   cluster_stats_messages_sent:1219
   cluster_stats_messages_ping_received:613
   cluster_stats_messages_pong_received:601
   cluster_stats_messages_meet_received:5
   cluster_stats_messages_received:1219
   #查看连接节点
    cluster nodes
   182a391d7956ff6a9acbda0fa386946de1b09020 192.114.7.2:6379@16379 master - 0 1624264719541 2 connected 5461-10922
   dd813964879499ee1f6ef5395cb9b45e7d32a005 192.114.7.4:6379@16379 slave 9d6568bd86aa5f6b4a4d14cc5be2c7b0c1d677ac 0 1624264719041 4 connected
   4df881acdcece81949615c8dc1ac1c1f9ba081c3 192.114.7.6:6379@16379 slave 182a391d7956ff6a9acbda0fa386946de1b09020 0 1624264719541 6 connected
   9d6568bd86aa5f6b4a4d14cc5be2c7b0c1d677ac 192.114.7.3:6379@16379 master - 0 1624264719541 3 connected 10923-16383
   7da7072595561b550096ba9e6b3ea93b91024887 192.114.7.5:6379@16379 slave d87b93316c2ed32a38bae18a09bb26343adc8cad 0 1624264720044 5 connected
   d87b93316c2ed32a38bae18a09bb26343adc8cad 192.114.7.1:6379@16379 myself,master - 0 1624264718000 1 connected 0-5460
   #set一个数据
   set a b
   -> Redirected to slot [15495] located at 192.114.7.3:6379
   OK
   #到外部去关闭192.114.7.3这台机器
   docker stop redis redis-3
   #重新连接由于刚刚断了杀死的很可能是redis-3
   redis-cli -c
   #查看数据
    get a
   -> Redirected to slot [15495] located at 192.114.7.4:6379
   "b"
   #由7.4提供了服务
   #重新查看节点
   192.114.7.4:6379> cluster nodes
   4df881acdcece81949615c8dc1ac1c1f9ba081c3 192.114.7.6:6379@16379 slave 182a391d7956ff6a9acbda0fa386946de1b09020 0 1624265161000 6 connected
   9d6568bd86aa5f6b4a4d14cc5be2c7b0c1d677ac 192.114.7.3:6379@16379 master,fail - 1624264870285 1624264869083 3 connected
   7da7072595561b550096ba9e6b3ea93b91024887 192.114.7.5:6379@16379 slave d87b93316c2ed32a38bae18a09bb26343adc8cad 0 1624265161556 5 connected
   d87b93316c2ed32a38bae18a09bb26343adc8cad 192.114.7.1:6379@16379 master - 0 1624265161000 1 connected 0-5460
   182a391d7956ff6a9acbda0fa386946de1b09020 192.114.7.2:6379@16379 master - 0 1624265161958 2 connected 5461-10922
   dd813964879499ee1f6ef5395cb9b45e7d32a005 192.114.7.4:6379@16379 myself,master - 0 1624265160000 8 connected 10923-16383
   192.114.7.4:6379>
   #我们发现node4变成了myself而 node3 变成了fial。
   #高可用性集群测试成功
   ```

   

### 作业8 吧MessageService发布成docker镜像并且提交

1. 创建Dockerfile

   需求:

   - 定义开放5566端口
   - 设定日志文挂载卷
   - Dockerfile文件创建再src同级目录下也就是项目的根目录在这里就是gqy-vis-ms目录

   ```shell
   FROM openjdk:11-jre-slim
   MAINTAINER akachi<zsts@hotmail.com>
   
   ENV MYPATH /usr/local
   WORKDIR $MYPATH
   
   COPY target/gqy-vis-ms-0.0.1-SNAPSHOT.jar gqy-vis-ms-0.0.1-SNAPSHOT.jar
   COPY /src/main/resources/application.properties application.properties
   COPY /src/main/resources/application-dev.properties application-dev.properties
   
   EXPOSE 5566
   ENTRYPOINT ["/bin/bash"]
   ENTRYPOINT ["chmod","/usr/local/logs"]
   ENTRYPOINT ["java","-jar","gqy-vis-ms-0.0.1-SNAPSHOT.jar"]
   ```

   

2. 构建docker

   - 通过windows docker找到仓库

     ![image-20210621173225830](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/21/173228.png)

   ```shell
   #到达对应目录
   cd D:\idea_workspace\vis-nbga-parent\gqy-vis-ms>
   #构建镜像
   D:\idea_workspace\vis-nbga-parent\gqy-vis-ms>docker build -t 192.168.2.129:5000/web-socker-message-service:1.0 .
   [+] Building 21.8s (11/11) FINISHED
    => [internal] load build definition from Dockerfile                                                               0.0s
    => => transferring dockerfile: 508B                                                                               0.0s
    => [internal] load .dockerignore                                                                                  0.0s
    => => transferring context: 2B                                                                                    0.0s
    => [internal] load metadata for docker.io/library/openjdk:11-jre-slim                                            21.1s
    => [1/6] FROM docker.io/library/openjdk:11-jre-slim@sha256:b4cb057e8e7f534c3ae279b5f3205e28d6520c0bdb1f80bbf61c0  0.0s
    => [internal] load build context                                                                                  0.0s
    => => transferring context: 355B                                                                                  0.0s
    => CACHED [2/6] WORKDIR @MYPATH                                                                                   0.0s
    => [3/6] COPY target/gqy-vis-ms-0.0.1-SNAPSHOT.jar gqy-vis-ms-0.0.1-SNAPSHOT.jar                                  0.1s
    => [4/6] COPY exe/start.sh start.sh                                                                               0.1s
    => [5/6] COPY /src/main/resources/application.properties application.properties                                   0.1s
    => [6/6] COPY /src/main/resources/application-dev.properties application-dev.properties                           0.1s
    => exporting to image                                                                                             0.3s
    => => exporting layers                                                                                            0.2s
    => => writing image sha256:c2423248540cf2a54d890e40290f8d308db62c9db94d9cf6bb8e7528e17b1b0e                       0.0s
    => => naming to 192.168.2.129:5000/web-socker-message-service:1.0
   ```

3. 测试启动镜像

   ```shell
   docker run -d -p 5577:5566 --name ms01 192.168.2.129:5000/web-socker-message-service:1.0
   ```

4. 发布镜像

   ```shell
   docker push 192.168.2.129:5000/web-socker-message-service:1.0
   ```

### Swarm 

单独在

### compose 



### jenkins

### CI-CD