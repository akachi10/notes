# KeepAlived

## 原理

> Vrrp协议:
>
> ​		基于vrrp 协议：Virtual Router Redundancy Protocol
>
> ​		虚拟路由冗余协议

> 术语: 
>
> ​		虚拟路由器:Virtual Router Redundancy Protocol
>
> ​		虚拟路由标识:VRID(0-255),唯一标识虚拟路由器
>
> ​		物理路由器:
>
> ​				master: 主机
>
> ​				备用设备 priority:优先级
>
> ​		VIP: Virtual IP
>
> ​		VMAC: Virutal MAC

### 应用

- 双路由器堆叠就是靠Vrrp协议完成的
- 使用虚拟路由完成这个功能
- 客户端都指向虚拟路由IP地址

## 安装

### 前置

#### 安装 ansible

> ansible 是一个批量化部署服务的工具
>
> 单独找一台机器

```shell
yum install epel-release -y
yum install ansible -y
```

#### 配置ansible

> 修改 /etc/ansible/hosts

```shell

[keep]
192.168.122.[11:13] ansible_ssh_user="root" ansible_ssh_pass="a" ansible_su_pass="a"
```

> 测试 ansible keep - m ping

> 发生报错
>
> ```shell
> [root@ansible ~]# ansible keep -m ping
> 192.168.2.194 | FAILED! => {
>     "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
> }
> ^C [ERROR]: User interrupted execution
> ```
>
> 修改vi /etc/ansible/ansible.cfg文件
>
> \#host_key_checking = False>>host_key_checking = False

> 成功示例
>
> ```shell
> [root@ansible ~]# ansible keep -m ping -u root
> 192.168.2.193 | SUCCESS => {
>     "ansible_facts": {
>         "discovered_interpreter_python": "/usr/bin/python"
>     },
>     "changed": false,
>     "ping": "pong"
> }
> 192.168.2.194 | SUCCESS => {
>     "ansible_facts": {
>         "discovered_interpreter_python": "/usr/bin/python"
>     },
>     "changed": false,
>     "ping": "pong"
> }
> ```
>
> 



