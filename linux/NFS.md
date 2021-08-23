# NFS

- unmout 移除挂载

- mount -t nfs -o proto=tcp -o nolock -o vers=3 ip:/img /nfs/docker/volumes/

  ```shell
  mount -t nfs -o proto=tcp -o nolock -o vers=3 ip:/img /nfs/docker/volumes/
  带权限挂载
  ```

- ``` shell
  # 配置服务root访问有权限
  vi /etc/exports
  /nfs/docker/volumes 192.168.2.0/24(rw,no_root_squash)