# MongoDB日常笔记

### 表结构调整

+ 新增字段
  + table_name：修改目标表名称
  + column： 新增字段名 ，后面指定为字段类型
  + updateMany：修改范围，如果用updateOne只修改找到的第一条，一些版本用update+multi:true控制

```json
db.getCollection('table_name')
.updateMany({}, {$set:{col:NumberInt('-1')}})
```

+ 删除字段

```json
db.getCollection('device_status').updateMany({},{$unset:{channel:null}},false, true)
```

