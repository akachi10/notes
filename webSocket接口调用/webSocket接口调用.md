# 接口调用

## 0 通用属性

- bussinessCode 业务代码连接唯一，代表作业ID
- target 目标代码，指向需要挂在作业的ID
- refreshRule 执行频率 单位:毫秒

## 1 查询 rms参数

```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY": "FIND_IOTDB_BYTIME",
        "bussinessCode": "TEST2",
        "sql_param": {
                "@param_time;":"60",
                "@param_time_unit;":"s",
                "@param_type;":"a",
                "@param_index;":"0"
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2"
}
```

### 1.1 参数解释

- param_time 查询时间
- param_time_unit 时间单位 支持普遍的时间参数 月份为 mo
- param_type 查询类型 支持三种参数 (a 加速度,t 温度,v 速度)
- param_index 设备编号 (t 只有0 电机，而a和v有 0 电机,1 泵,2 联轴器)

### 1.2 阈值说明

仅在查询 v 速度 的时候需要显示阈值

- 当值小于 2.3 时显示绿色
- 当值大于 2.3 并小于 4.5 时显示黄色
- 当值大于 4.5 并小于 7.1 时显示橙色
- 当值大于 7.1 时显示红色

## 2 查询 健康度

```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY":"REAL_TIME_HEALTH",
        "bussinessCode": "TEST",
        "sql_param": {
        },
        "data_source": {
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2"
}
```

### 2.1 参数解释

- 返回:0-1之间的值
- 阈值 0.6 以下下为红色
- 阈值 0.85 以下为黄色
- 阈值 0.85 以上为绿色

> 注：报警字符串前端处理

##  3 查询维护工单

```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY":"MAINTENANCE_WORK_ORDER",
        "bussinessCode": "TEST",
        "sql_param": {
                "@param_code;":"pressure"
        },
        "data_source": {
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2"
}
```

### 3.1参数解释

- param_code 代码有三个值(axletree 轴承,gearbox 变速箱,pressure 液压泵)

##  4 查询常量

```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY":"DEMO_CONSTANT",
        "bussinessCode": "TEST",
        "sql_param": {
                "@param_business_code;":"MT"
        },
        "data_source": {
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2" 
}
```

### 4.1参数解释

- param_business_code 业务代码有两个值(可靠度内容:reliability,平均故障和平均修复时间以及工作时长:MT)

##  5 傅里叶变换

```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY":"FFT_CONTENT",
        "bussinessCode": "TEST",
        "sql_param": {
                "@param_index;":"0"
        },
        "data_source": {
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2" 
}
```

### 5.1参数解释

- param_index 设备索引从 0 开始到 2

## 6 运行时长
```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY":"RUN_TIME",
        "bussinessCode": "TEST",
        "sql_param": {
        },
        "data_source": {
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2"
}
```

### 6.1参数解释

无参数

## 7 实时健康度_包含所有

```sql
select sum(value <2.3)/count(*)*0.2+(sum(value <4.5)=count(*))*0.2+(sum(value <7.1)=count(*))*0.5+0.02+RAND()*0.03 as health from vrms where 1=@param_all_flag; or id = @param_id;
```

```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY":"REAL_TIME_HEALTH_PARAM",
        "bussinessCode": "TEST",
        "sql_param": {
                "@param_all_flag;":"0",
                "@param_id;":"1"
        },
        "data_source": {
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2"
}
```

### 7.1参数解释

- param_all_flag 是否查询所有健康度 1是所有 0是不生效
- param_id 健康度ID 在param_all_flag为0时生效 值(1,2,3 风别代表设备0 1 2 的健康度)

## 8查询整机工单
```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY":"MAINTENANCE_WORK_ORDER_ALL",
        "bussinessCode": "TEST",
        "sql_param": {
                "@param_number;":"30"
        },
        "data_source": {
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2"
}
```

### 8.1参数解释

- param_number 查询条数
- param_ratio 步长系数


## 9 查询 rms参数 format时间

```json
{
        "command": "com.gqy.ms.socket.entity.event.SqlExecEvent",
        "target": "test",
        "SQL_KEY": "FIND_IOTDB_BYTIME_FORMAT_TIME",
        "bussinessCode": "TEST2",
        "sql_param": {
                "@param_time_unit;":"s",
                "@param_type;":"a",
                "@param_index;":"0",
                "@SYS_DATETIME_FORMAT":"yyyy-MM-dd'T'HH:mm:ss",
                "@SYS_DATETIME_FORMAT_OFFSET":"-60"
        },
        "trans_param": {
        },
        "separate": true,
        "refreshRule": "5000",
        "refreshModel": "2"
}
```

### 9.1 参数解释

- param_time_unit 时间单位 支持普遍的时间参数 月份为 mo
- param_type 查询类型 支持三种参数 (a 加速度,t 温度,v 速度)
- param_index 设备编号 (t 只有0 电机，而a和v有 0 电机,1 泵,2 联轴器)
- @SYS_DATETIME_FORMAT_OFFSET 查询时间
- @SYS_DATETIME_FORMAT 系统变量:格式化字符串 **勿改**

### 9.2 阈值说明

仅在查询 v 速度 的时候需要显示阈值

- 当值小于 2.3 时显示绿色
- 当值大于 2.3 并小于 4.5 时显示黄色
- 当值大于 4.5 并小于 7.1 时显示橙色
- 当值大于 7.1 时显示红色
