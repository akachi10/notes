## 关于开发环境中的 CI/CD 流程整理

### 说明

因为 Coding 平台 CI/CD 功能限量收费而且对开源插件的支持性比较差，所以我建议充分使用 GitLab 来完成项目的 CI/CD 流程。

### 建议的软件

- Docker (容器)
- GitLab CI (持续集成工具)
- Nexus (软件仓库)
- Kubernetes (容器云平台)
- Rancher (容器管理平台)

### 建议的部署流程

1. 运维人员构建 gitlab-ci 配置文件模板
2. 用户编辑基础镜像，用于打包相应项目的代码
3. 用户手动编辑 gitlab-ci 配置文件
4. 项目负责人员开启 gitlab-ci 功能

### gitlab-ci 文件结构

我建议将 gitlab-ci 文件切分为如下阶段(stage)

- check (单元测试及代码监测阶段)
	- 在此处运行单元测试确保项目可以正常执行
- page (文档打包阶段)
	- 使用 springfox 等工具编译项目文档，并将其托管至 gitlab-pages 中，方便内网访问
- deploy (部署阶段)
	- 与 Rancher 集成，根据当前推送分支确定部署位置和部署命令

> 注：在 gitlab-ci 部署过程中可以使用 shell 或者 Python 脚本来优化流程。
