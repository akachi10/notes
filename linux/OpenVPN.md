# OpenVPN

> 官方文档:
>
> https://openvpn.net/download-open-vpn/
>
> ![image-20210907224630819](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/07/224642.png)
>
> ![image-20210907224648232](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/09/07/224649.png)



## 前置

1. 需要科学上网

   > 具体方请参考《网络配置》
   >
   > 测试下来只有全局代理能解决问题

## 安装服务

```shell
yum -y install https://as-repository.openvpn.net/as-repo-centos7.rpm
yum -y install openvpn-as
```

### 全局代理设置

> 修改文件 /etc/profile

```shell
#加入以下内容
# add proxy for network
export http_proxy="http://192.168.3.7:17074"
export https_proxy=$http_proxy
export ftp_proxy=$http_proxy
#生效环境变量
source /etc/profile
```

## 创建密码

> 一开始会有一个admin用户但是不能登陆需要在服务器上运行
>
> passwd openvpn

## 管理

> 教程页中会有一些基本的教学
>
> https://openvpn.net/vpn-server-resources/finishing-configuration-of-access-server/#setting-up-authentication



## 关闭防火墙

```shell
systemctl stop firewalld
systemctl disable firewalld
sed -i 's/enforcing/disabled/' /etc/selinux/config
setenforce 0
```

## 异常解决

如果无法连接外网可看《linux网络设置》

## 卸载

```shell
yum -y remove openvpn-as
rm -rf /usr/local/openvpnas
```

## 服务名

## 下载安装vpn客户端

> URL地址:https://openvpn.net/vpn-client/

安装客户端