# OpenVPN

## 安装服务

```shell
wget http://swupdate.openvpn.org/as/openvpn-as-2.0.10-CentOS7.x86_64.rpm

rpm -Uvh openvpn-as-2.0.10-CentOS7.x86_64.rpm
```



## 关闭防火墙

```shell
systemctl stop firewalld
systemctl disable firewalld
sed -i 's/enforcing/disabled/' /etc/selinux/config
setenforce 0
```

## 异常解决

1. 无法启动服务

   ```shell
   Error:
   process started and then immediately exited: ['Sat Oct 7 13:28:47 2017 Linux ifconfig failed: could not execute external program']
   ```

   - 解决方案安装net-tools

     ```shell
     yum install net-tools -y
     ```

## 卸载

```shell
#查看vpnservice
[root@DNS00 OpenVPN]# rpm -qa|grep vpn
openvpn-as-2.0.10-CentOSrelease.x86_64

```



```shell
 rpm -e openvpn-as-2.0.10-CentOSrelease.x86_64
```



## 下载安装vpn客户端

> URL地址:https://openvpn.net/vpn-client/

安装客户端