# Hexo搭建一个github上的博客

## 前置安装

- node

- npm
- git

## 安装hexo

```shell
#测试 node -v
#测试 npm -v
#测试 git -v
npm install hexo-cli -g
#hexo -v
```

## 创建一个git仓库

> 仓库名必须是 [username].github.io
>
> 如果可以通过[username].github.io则创建成功

## 绑定SSH

1. 查看ssh是否存在

2. 创建证书

   ```shell
   ssh-keygen -t rsa -C "[email]"
   ```

   > 如果成功会创建一个证书在c:/Users/[systemuser]/.ssh
   >
   > ![image-20210831000820274](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/31/000821.png)

3. 拷贝id_rsa.pub中的内容

4. 绑定证书到github

   > 登录GitHub 
   >
   > 进入 [头像]->[settings]->[SSH add GPG keys]->[new SSH key]
   >
   > 在key中粘贴第三步拷贝的内容

   ![image-20210831001343902](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/31/001345.png)

5. 测试是否绑定成功

   ```shell
   #在bash或者terminal中输入
   ssh -T git@github.com
   $  ssh -T git@github.com
   The authenticity of host 'github.com (52.74.223.119)' can't be established.
   RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
   This key is not known by any other names
   Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
   Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
   Hi [username]! You've successfully authenticated, but GitHub does not provide l access.
   
   ```

   > 在系统询问"Are you sure you want to continue connecting (yes/no/[fingerprint])?"时输入yes
   >
   > 如果看到 successfully 表示成功

## 创建博客

1. 选择一个想要部署博客的文件夹

2. 在文件夹内打开Git bush或wsl terminal

3. 初始化hexo

   ```shell
   # 在某个目录下 比如 E:/DBank/blog
   $ hexo init
   INFO  Cloning hexo-starter https://github.com/hexojs/hexo-starter.git
   INFO  Install dependencies
   INFO  Start blogging with Hexo!
   
   ```

   > 发生报错
   >
   > FATAL X:\path\Blog not empty, please run `hexo init` on an empty folder and then copy your files into it
   >
   > 由于文件夹内不是空的

4. 启动hexo

   ```shell
   $ hexo s
   INFO  Validating config
   INFO  Start processing
   INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
   (node:26084) Warning: Accessing non-existent property 'lineno' of module exports inside circular dependency
   (Use `node --trace-warnings ...` to show where the warning was created)
   (node:26084) Warning: Accessing non-existent property 'column' of module exports inside circular dependency
   ```

   > 通过http://localhost:4000可以访问就是成功

## 部署到github上

1. 修改_config.yml

   > 在最后deploy上加入一夏内容

   ```yml
   deploy:
     type: git
     repository: https://github.com/[username]/[username].github.io.git
     branch: main
   ```

2. 安装hexo-deployer-git 插件

   > 打开git bash如果无法正确安装成功就打开CMD安装

   ```shell
   $ npm install hexo-deployer-git --save
   added 53 packages in 2s
   ```

3. hexo生成

   ```shell
   hexo g
   ```

4. hexo 部署

   ```shell
   $ hexo d
   INFO  Validating config
   INFO  Deploying: git
   INFO  Setting up Git deployment...
   Initialized empty Git repository in X:/[path]/Blog/.deploy_git/.git/
   [master (root-commit) 5fa3584] First commit
    1 file changed, 0 insertions(+), 0 deletions(-)
   
   ```

5. 创建令牌并登陆

   > 如果弹出需要git登陆而且你么有token可按照以下流程创建一个token
   >
   > [Settings]->[Developer settings]->[Personal access tokens]->Generate new token
   >
   > ![image-20210831011616380](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/31/011617.png)
   >
   > 如果你很懒可以创建一个无期限的token
   >
   > 选择Expiration为No Expiration
   >
   > 拷贝Token并输入

6. 验证

   > 访问https://[username].github.io/
   >
   > 看到以下内容就是博客部署成功了
   >
   > ![image-20210831011203606](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/08/31/011214.png)
   >
   > 我是 访问https://artificial-deus.github.io/

## 写博客

> 到blog目录内
>
> 通过命令 hexo n "[name]"
>
> 文件会被创造在 source/_posts/内
>
> 创建的文件中有默认代码块其中包含 title、date、tags三个字段 是yml格式，不按格式写会打包失败。
>
> tags 是个数组或字符串，可以用于检索文章。
>
> 例子：

```shell
---
title: 基于swarm集群搭建mysql主备
date: 2021-09-12 23:00:42
tags: 
  - mysql
  - swarm
  - dokcer
  - 容器化
  - 高可用
  - HA
---
```



> 完成后执行:

```shell
# 清理
hexo clean
# 重新生成
hexo g
# 部署到github上
hexo d
```

## 自定义主题

> 在官方的主题中看到内容 [Lite](https://github.com/HeskeyBaozi/hexo-theme-lite) 主题不错
>
> 官方关于主题的文档中描述
>

![image-20210913010943361](Hexo搭建一个github上的博客.assets/image-20210913010943361.png)

> https://github.com/HeskeyBaozi/hexo-theme-lite.git
>
> 下载主题![image-20210913205359022](Hexo搭建一个github上的博客.assets/image-20210913205359022.png)

```shell
E:\DBank\Blog>git clone https://github.com/HeskeyBaozi/hexo-theme-lite.git themes/lite
Cloning into 'themes/lite'...
remote: Enumerating objects: 2020, done.
remote: Total 2020 (delta 0), reused 0 (delta 0), pack-reused 2020
Receiving objects: 100% (2020/2020), 8.00 MiB | 5.43 MiB/s, done.
Resolving deltas: 100% (1182/1182), done.
```

> 修改文档_config.yml的 theme: 为主题文件夹名称

```shell
theme: lite
```

> 修改其中EMail之类的内容

### Butterfly 主题

> 有些特殊设置必须基于

https://butterfly.js.org/posts/dc584b87/

才能用
