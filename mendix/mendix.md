# Mendix学习

``` 
#官网URL
https://sprintr.home.mendix.com/index.html
```



## 敏捷开发

### 怎么描述敏捷需求

- 为了什么。

- 做什么。

- 商业价值。

### 敏捷开发流程

![image-20210518111433734](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518111433734.png)



- 任务池(Product backlog)

我们可以想象怎个系统像是一个流水线负责生产和实现需求。

首先我们有一个需求池子这里面我们会放入所有应该被实现的需求。

- 产品经理

  我们有一个产品经理或需求经理专门负责维护这个需求池。

  利益相关方是最终的需求提供方。他们负责吧需求相关的内容提供给产品经理，由产品经理决定是否将需求放入需求池。

  - 产品经理描述需求时应该采用

    哪个角色( ) 做什么( ) 这么做是为了什么商业上的价值()

    这样的描述方式以让研发人员分析并实现需求。

我们有一个产品经理或需求经理专门负责维护这个需求池。

利益相关方是最终的需求提供方。他们负责吧需求相关的内容提供给产品经理，由产品经理决定是否将需求放入需求池。

- 教练

  有一个负责安排工作和解决问题的人他是研发团队的教练。

他会负责将需求切分成为可实现的功能并交给具体的研发人员完成。

- (Sprint)周期

  敏捷开发的项目会被拆分为1到2周一个Sprint教练会把需求拆分成为任务并放进每个Sprint中交给研发人员开发。这些任务必须是在一个周期内能够完成的任务。

- 发布

  每个周期的研发成果最终都应该能发布为一个小的可执行的功能。这些功能会交给用户和利益相关方、产品经理审查。

- 用户

  用户会快速的查看哪些以及被完成的需求并且敏捷的提出对此业务的看法或调整业务实现方式，以方便团队快速完成客户需求。

### 敏捷开发成员

![image-20210518112719908](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518112719908.png)



- SME

  外部专业人员

  这些人员通常不常驻在团队中。而是作为团队的技术支持加入团队的。

- Dev Team

  研发团队

- Business Owner

  业务所有者

- Stakeholders

  利益相关方

- Scrum Master

  教练

- Product Owner

  产品经理



## Mendix ecosystem(生态系统)

- online project management (在线项目管理门户)

- app Store

  基本上使用java 和JavaScript，

- Atlas UI

  mandix 自生的前端设计语言

- Mendix Studio(无代码 开发环境)

  这是一个online版本的开发环境

- Mendix Studio Pro(低代码开发平台)

  主要编程中使用的是这个

- Support (客服)

- Community (社区)

- Academ (院校模块)

  https://academy.mendix.com/link/certification/intermediate

  https://academy.mendix.com/link/path

- mendix docs(文档)

### Platform Architecture Components

![image-20210518153104380](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518153104380.png)



- Project Protal 环境的设置
- Team Server 基于SVN
- APP Store 应用商店
- Cloud Portal 基于Cloud Foundry的多实例
- Cloud Foundry 容器化部署 部分地区使用k8s

- Build Server 构建服务器
- Package Repository (IMAGES仓库)
- Desktop Modeler(桌面开发环境 Mendix studio pro)
- Web Modeler(在线开发环境 Mendix Studio)



## 做一个小项目

预计为一个小的培训机构完成数字化转型的IT项目。



![image-20210518142753378](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518142753378.png)



- 课程管理
  - 增删修查
- 场地管理
- 讲师管理
- 学员管理
  - 

![image-20210518144246591](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518144246591.png)





### user story





#### #1 首页  Create the Homepage

![image-20210518161050637](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518161050637.png)



### #2 link the page

![image-20210518162148958](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518162148958.png)





### Entity

![image-20210518164407035](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210518164407035.png)





### Assonciations

![image-20210519111350426](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210519111350426.png)

- One to Many (1-*)
- One to One (1-1)
- Reference Set(\* - \*)



### 安全

可以在项目和模块级进行安全设置

页面模式也可以进行设置



#### 安全的设计

![image-20210520105214194](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210520105214194.png)

## 备注

### 拼接SQL方法

``` sql
(Chart_Name,Chart_Des,ChartType,JsonData,JsonLayout) Values(
'''+$anychart_table/Chart_Name+''',
'''+$anychart_table/Chart_Des+''',
'''+$anychart_table/ChartType+''',
'''+$anychart_table/JsonData+''',
'''+$anychart_table/JsonLayout+''');'
```

### 微流中能用的运算符

![image-20210519172809699](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210519172809699.png)



### 微流中的运算函数

![image-20210519192040188](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210519192040188.png)



### 验证

![image-20210519192122381](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210519192122381.png)

![image-20210519192003082](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210519192003082.png)



#### 验证加在哪

![image-20210519192242157](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210519192242157.png)

### cascading Delete （级联删除）



![image-20210520102701951](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210520102701951.png)



### 系统级的事件

![image-20210521142037918](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20210521142037918.png)

- after startup
- before shutdown 最后需要调用shutdown 不过最后都需要调用一个 叫做 before shutdown的 Mac flow 
