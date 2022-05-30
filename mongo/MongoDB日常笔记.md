# MongoDB日常笔记

### 权限

+ 创建用户

```json
db.grantRolesToUser('gc',[{role:'readWrite',db:'eplus'}])
```

+ 权限修改

```json
db.createUser(
			{
				 user: 'root',
				 pwd: 'root',
			  	 roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
			 }
);
```

+ 用户权限说明

```
	Read：允许用户读取指定数据库
	
    readWrite：允许用户读写指定数据库
    
	dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
	
	userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
	
	clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
	
	readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
	
	readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
	
	userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
	
	dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
	
	root：只在admin数据库中可用。超级账号，超级权限
```



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



