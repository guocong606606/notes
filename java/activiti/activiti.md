# activiti

## 前期准备

### 工作流引擎核心

+ ProcessEngine对象，这是Activiti工作的核心。负责生成流程运行时的各种实例及数据、监控和管理流程的运行

### BPMN

+ 业务流程建模与标注（Business Process Model and Notation，BPMN)，描述流程的基本符号，包括这些图元如何组合成一个业务流程图（Business Process Diagram）

### idea插件

+ idea插件，图形化流程 - actiBPM（插件商店查询不到的前提下直接取idea官方插件商店找）

```http
https://plugins.jetbrains.com/
```

----

## SpringBoot整合

### 配置

####  28张表

| 前缀     | 说明       | 备注                                                         |
| :------- | ---------- | ------------------------------------------------------------ |
| ACT_RE_* | repository | 表包含了流程定义和流程静态资源 （图片，规则，等等）。        |
| ACT_RU_* | runtime    | 包含流程实例，任务，变量，异步任务，等运行中的数据。 Activiti只在流程实例执行过程中保存这些数据， 在流程结束时就会删除这些记录。 这样运行时表可以一直很小速度很快。 |
| ACT_ID_* | identity   | 这些表包含身份信息，比如用户，组等等。                       |
| ACT_HI_* | history    | 这些表包含历史数据，比如历史流程实例， 变量，任务等等。      |
| ACT_GE_* | general    | 通用数据， 用于不同场景下，如存放资源文件。                  |

####  7大接口

| 接口名            | 说明                                     |
| ----------------- | ---------------------------------------- |
| RepositoryService | 仓库服务类，用于管理bpmn文件与流程图片   |
| RuntimeService    | 流程运行服务类，用于获取流程执行相关信息 |
| TaskService       | 任务服务类，用户获取任务信息             |
| HistoryService    | 查询历史数据                             |
| ManagementService | 流程引擎的管理与维护，与具体业务无关     |
| IdentityService   | 创建、更新、删除，查询群组和用户         |
| FormService       | 读取流程，任务表单数据                   |

#### RepositoryService

```

```



#### RunTimeService

+ 创建流程

```java
// key  流程模块id
processEngine.getRuntimeService().startProcessInstanceByKey(String key)
```

#### TaskService

```

```



#### HistoryService

```

```



#### ManagementService

```

```



#### IdentityService

```

```



#### FormService

```

```

