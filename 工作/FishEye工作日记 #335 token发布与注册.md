# #335 **token发布与注册**

![白色](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/15/095704.jpg)



## 需求图

![image-20210615100042643](https://raw.githubusercontent.com/akachi10/notes/master/pic/2021/06/15/100043.png)

## 流程分解 Use Case 与 内部逻辑

- MessageService init时自检是否存在base Token
  - **不存在则创建**
- Message通过接口创建出客户端Key
- 通过请求获得客户端Key
- MessageServiceToken相关接口单独设置白名单
- 下载Key
- 客户端载入Token并与MessageService取得连接
- FishEyeService通过客户端Token确认注册是否被消费
  - 改造注册通知机制，通知所有相关着Token



## 接口

- 创建Token接口
- 修改Token
- 删除Token接口
  - 必须没有被使用
- Token列表包含边缘机信息
- 注冊



## 动手

- 先做一个只能被特定服务器和本地调用的生成Token的接口





## 备注

1. ~~SSL创建流程~~
   1. ~~创建服务器jks~~
   2. ~~创建客户端jks~~
   3. ~~创建服务器cer~~
   4. ~~加入cer到客户端~~
2. 加载SSL流程
   1. 服务器加载服务器JKS和要连接的客户JKS
   2. 客户端加载客户端JKS
