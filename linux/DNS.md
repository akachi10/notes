# 安装Bind DNS服务

## 安装服务

```shell
yum install bind -y
```



> 也可以使用bind-chroot ，可以限制bind只在对于目录下有权限
>
> yum install bind bind-chroot caching-nameservice -y

## 关闭防火墙

```shell
systemctl stop firewalld
systemctl disable firewalld
sed -i 's/enforcing/disabled/' /etc/selinux/config
setenforce 0
```



## 配置

- 配置一览

  - /etc/named.conf

    端口相关的全局性的内容

  - /etc/named.rfc1912.zones

    >  声明域的文件,数据库文件保存在那个位置？
    >
    > 数据库文件名字是自定义的

  - /var/named/自定义zone文件

    >  zone文件的名字必须和rfc1912.zones一致

### 配置一个正向解释

- 修改/etc/named.conf

  > 修改lisent-on改成any
  >
  > 这样才能监听自己以外的内容
  >
  > allow-query 也修改为any
  >
  > 允许访问本DNS的权限

  ```conf
  options {
          listen-on port 53 { any; };
          listen-on-v6 port 53 { ::1; };
          directory       "/var/named";
          dump-file       "/var/named/data/cache_dump.db";
          statistics-file "/var/named/data/named_stats.txt";
          memstatistics-file "/var/named/data/named_mem_stats.txt";
          recursing-file  "/var/named/data/named.recursing";
          secroots-file   "/var/named/data/named.secroots";
          allow-query     { any; };
  
  ```

- 修改 vi /etc/named.rfc1912.zones

  > 假设要加入一个域名 test01.kubernetes.rainbowfish

  ```shell
  zone "kubernetes.rainbowfish" IN{
          type master;
          file "kubernetes.rainbowfish.zone";
          allow-update {none;};
  };
  # 由于这里没有加入;导致named无法启动，这里千万要注意
  ```
  
  > 格式我们可以直接抄写文件中的其他例子
  >
  > **//要解释的域名**
  >
  > zone "kubernetes.rainbowfish" IN{ 
  >
  > ​		**//这个服务器是本域名的主服务器就写master 当然还有从服务器**
  >
  > ​        type master; 
  >
  > ​		**//这是指具体数据库文件地址，没有明确限制但是建议直接使 用域名加.zone来命名**
  >
  > ​        file "kubernetes.rainbowfish.zone";
  >
  > ​		**//这里直接抄写**
  >
  > ​        allow-update {none;};
  > }
  
- 创建对应的zone文件

  > 可以拷贝named.localhost 来作为示例

  ```shell
   cd /var/named
   cp -a named.localhost kubernetes.rainbowfish.zone
   #这里命名忘记了.zone导致无法启动，
  ```

  > 但是拷贝时请使用 -a 参数这样能保证拷贝的文件中所有者中有named
  >
  > 否则dns无法使用这个文件

  - 解释文件含义

    ![image-20210819141247212](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/19/141248.png)

    - A 表示有效期1天

    - B 要解释的域名

      > 必须以“.”结尾

    - C 管理员的邮箱

      > 必须以"."结尾
      >
      > @必须替换为.

    - D 也是需要解释的域名

    - E 子域名

    - 虽然没有标注但是192.168.2.60是本地IP需要写出来在第一个A后面

  - 示例

    ```shell
    $TTL 1D
    @       IN SOA  kubernetes.rainbowfish. zsts.hotmail.com. (
                                            0       ; serial
                                            1D      ; refresh
                                            1H      ; retry
                                            1W      ; expire
                                            3H )    ; minimum
            NS      kubernetes.rainbowfish.
            A       192.168.2.60
            AAAA    ::1
    test01  A       192.168.2.80
    test02  A       192.168.2.81
    test03  A       192.168.2.82
    test04  A       192.168.2.83
    test05  A       192.168.2.84
    test06  A       192.168.2.85
    
    ```

- 启动服务

  ```shell
  systemctl status named
  systemctl enable named
  systemctl start named
  #启动遇到致命问题一定要注意结尾加入;符号
  #这里命名忘记了.zone导致无法启动，
  #如果出现问题使用journalctl -xe查看服务哪里有问题
  ```

  > 这个配置本地是无法使用本地的dns服务的

- 测试功能

  ```shell
  配置网络 指定DNS01为本DNS服务
  
  ```

  



### 配置一个反向解析

- 配置/etc/named.rfc1912.zones

  ```shell
  vi /etc/named.rfc1912.zones
  ```

  ```shell
  zone "2.168.192.in-addr.arpa" IN{
          type master;
          file "192.168.2.zone";
          allow-update {none; };
  };
  ```

- 配置 /var/named/*.zone

  ```shell
  cp -a named.loopback 192.168.2.zone
  ```

  > 修改192.168.2.zone 文件

  ```shell
  
  $TTL 1D
  @       IN SOA  kubernetes.rainbowfish. zsts.hotmail.com. (
                                          0       ; serial
                                          1D      ; refresh
                                          1H      ; retry
                                          1W      ; expire
                                          3H )    ; minimum
          NS      kubernetes.rainbowfish.
          A       192.168.2.60
          AAAA    ::1
  80      PTR     test01.kubernetes.rainbowfish
  81      PTR     test02.kubernetes.rainbowfish
  82      PTR     test03.kubernetes.rainbowfish
  83      PTR     test04.kubernetes.rainbowfish
  84      PTR     test05.kubernetes.rainbowfish
  85      PTR     test06.kubernetes.rainbowfish
  #kubernetes.rainbowfish.
  ```

- 重启 named 服务

  ```shell
  systemctl start named
  ```

- 测试

  ```shell
  yum -y install bind-utils
  #没有完成测试！！！、
  #https://www.bilibili.com/video/BV1JK411p7uz
  到这里去看怎么回事
  ```
  
  

### 正向解释

> 通过域名来查询IP地址

![image-20210819112637775](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/19/112649.png)

### 反向解释

> 通过IP地址来查询域名

