# mysql swarm docker

- 搭建nfs存储配置文件

- 配置nfs拥有root权限

```
/nfs/docker/volumes 192.168.2.0/24(rw,no_root_squash)
```

## mysql-compose.yml 配置

```yml
version: "3"
services:
  mysql-1:
    image: mysql:5.7
    environment:
      - TZ=CST-8
      - MYSQL_ROOT_PASSWORD=123456
    ports:
      - 33061:3306
    volumes:
      - mysql-data:/var/lib/mysql
      - /nfs/docker/volumes/rainbowfish_mysql/conf/my1.cnf:/etc/mysql/my.cnf
    command: --character-set-server=utf8 --collation-server=utf8_general_ci --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
    deploy:
      placement:
        constraints:
          - node.hostname == Docker-swarm-worker04
  mysql-2:
    image: mysql:5.7
    environment:
      - TZ=CST-8
      - MYSQL_ROOT_PASSWORD=123456
    ports:
      - 33062:3306
    volumes:
      - mysql-data:/var/lib/mysql
      - /nfs/docker/volumes/rainbowfish_mysql/conf/my2.cnf:/etc/mysql/my.cnf
    command: --character-set-server=utf8 --collation-server=utf8_general_ci --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
    deploy:
      placement:
        constraints:
          - node.hostname == Docker-swarm-worker05
  mysql_nginx:
    image: nginx:1.19.2
    ports:
      - 33060:3306
    volumes:
      - /nfs/docker/volumes/rainbowfish_mysql/nginx/nginx.conf:/etc/nginx/nginx.conf
      - /nfs/docker/volumes/rainbowfish_mysql/nginx/data:/var/www/html/upload
#      - nginx-data:/var/www/html/upload
    deploy:
      mode: replicated
      replicas: 2
      placement:
        constraints: [node.role == manager]
    depends_on:
      - mysql-1
volumes:
  mysql-data:
    external: true
  nginx-data:
    external: true
```

## 配置文件

- my1.cnf

```config
[mysqld]
server_id=1
log-bin= mysql-bin
 
replicate-ignore-db=mysql
replicate-ignore-db=sys
replicate-ignore-db=information_schema
replicate-ignore-db=performance_schema
 
read-only=0
relay_log=mysql-relay-bin
log-slave-updates=on
auto-increment-offset=1
auto-increment-increment=2
 
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
```

- my2.cnf

```config
[mysqld]
server_id=2
log-bin= mysql-bin
 
replicate-ignore-db=mysql
replicate-ignore-db=sys
replicate-ignore-db=information_schema
replicate-ignore-db=performance_schema
 
read-only=0
relay_log=mysql-relay-bin
log-slave-updates=on
auto-increment-offset=2
auto-increment-increment=2
 
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/

```

- negix

```config
user www-data;
worker_processes auto;
pid /run/nginx.pid;
 
events {
    use   epoll;
    worker_connections  1024;
}
 
 
stream {
    upstream mysql {
        #backup为备用mysql,当mysql-1故障后自动切换mysql-2,达到主备效果，
        server mysql-1:3306 max_fails=3 fail_timeout=30s;
        server mysql-2:3306      backup;
    }
 
    server {
        listen    3306;
        proxy_connect_timeout 3000s;
        proxy_timeout 6000s;
        proxy_pass mysql;
    }
}
```

## 启动

```shell
docker stack deploy -c mysql-compose.yml rainbowfish_mysql
```

## 双活

### 原理图

![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ub3RlLnlvdWRhby5jb20veXdzL3B1YmxpYy9yZXNvdXJjZS9iZTFmYTQ4OTBkYzkyZTZlZDY1OWRiYmIzMTZmMzExMS94bWxub3RlLzlBODBFRDczRkQzRjRERDZBNUE1OTg2MjA2MkQ5QkQ5LzE0MDg)

- 创建用户

```shell
GRANT REPLICATION SLAVE ON *.* to 'slave'@'%' identified by '123456';
```

- 停止同步服务(如果有)

```SQL
stop slave
```

> 注:等待一分钟后再操作

- 同步数据库

> 注:如果两个数据库不是完全同步的会失败。
>
> 如果不是完全一直需要先同步数据

- 分别查看两台服务器的binlog文件状态

```sql
show master status;
```

![img](https://img-blog.csdnimg.cn/2020121620464518.png)

- **mysql-2**执行以下命令，开启IO线程监听**mysql-1**的binlog文件**，**黄色信息为**mysql-1**记录的信息

```SQL
#在mysql-1执行
change master to 
master_host='mysql-2',
master_user='slave',
master_password='123456',
master_log_file='mysql-bin.000011',
master_log_pos=1,
master_port=3306;
#在mysql-2上执行
change master to master_host='mysql-1',
master_user='slave',
master_password='123456',
master_log_file='mysql-bin.000012',
master_log_pos=1815,
master_port=3306;
```

> 解释：
>
> master_host：因为在同个docker swarm集群所以可以指定容器名
>
> master_password: 访问mysql-1或者mysql-2的密码
>
> master_log_file:查询到的文件名
>
> master_log_pos:查询到的当前条目(注意要停止所有的写入)

- 启动同步

```SQL
start slave;
```

- 查看同步状态

```SQL
show slave status
```

> 注:看到![image-20210812110234991](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/12/110245.png)
>
> 这两项为Yes为同步成功
>
> 如果看到connection是指系SQL或IO读取着正在连接
>
> 如果长时间没有连接成功或者出现NO大概率是出现问题了可以查看last error
>
> 通常都是一些简单的问题比如无法登陆、对方hostname 无法访问之类的。

## 其他

- 部署情况

![image-20210812112426446](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/12/112427.png)

## 参考资料

- mysql swarm 高可用性资料
- https://blog.csdn.net/weixin_42533856/article/details/111301631
- nfs 权限资料
- https://www.cnblogs.com/panwenbin-logs/articles/7106386.html
