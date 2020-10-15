# mysql

## 用户：

### 创建用户：

```mysql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

（ host ：指定登录ip，不限制时用%）

### 授权：

```MySQL
GRANT privileges ON databasename.tablename TO 'username'@'host';
-- 所有权限
GRANT privileges ON *.* TO 'username'@'%';
```

（ privileges：权限，全给all；databasename：数据库名；tablename：可操作表权限，全给*.*）

### 删除权限：

```mysql
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```

### 删除用户：

```mysql
DROP USER 'username'@'host';
```

## 配置

### 身份验证：

MySQL8.0+ 使用的身份验证插件为caching_sha2_password ，使用数据连接工具时，须修改改为mysql_native_password；

登录后执行:

```sql
use mysql;
```

查询身份验证插件：

```mysql
select user,host,plugin,authentication_string from user;
```

此时，root用户已修改，可以通过navicat连接：

![image-20191224095932907](mysql.assets\image-20191224095932907.png)

修改：

```mysql
alter user 'username'@'%' identified with mysql_native_password by 'password';
```

----



## 数据库基础相关

### 字符串默认排序规则

+ mysql字符串默认排序不一致

1.  执行复杂语句，尤其是union时，易出现非法混合排序的问题（mix of collations）
2.  例如（utf8_genera_ci 和utf8_general_cs）两种排序方式前者大小写不敏感，后者大小写敏感，混合排序结果很可能不符合预期


+ 场景，使用pd设计表的时候，默认排序规则为：utf8mb4_0900_ai_ci；而库默认排序规则为：utf8mb4_unicode_ci


+ 方案：

```mysql
-- 字符串类型数据排序规则修改
SELECT
	TABLE_SCHEMA '数据库',
	TABLE_NAME '表',
	COLUMN_NAME '字段',
	CHARACTER_SET_NAME '原字符集',
	IS_NULLABLE '非空属性',
	COLLATION_NAME '原排序规则',
	CONCAT(
	'ALTER TABLE ',
	TABLE_NAME,
	' MODIFY COLUMN ',
	COLUMN_NAME,
	' ',
	COLUMN_TYPE,
	' CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci',
	( CASE WHEN IS_NULLABLE = 'NO' THEN ' NOT NULL' ELSE '' END ),
	';' 
) '修正SQL' 
FROM
	information_schema.`COLUMNS` 
WHERE
	COLLATION_NAME RLIKE 'utf8mb4_0900_ai_ci' 
	AND TABLE_SCHEMA = 'qsc-v2-test';
```

+ mark：如数据库有外键设计，此方案不适用

----



### 查询表宽度（列数）

```mysql
select count(1) as column_count from information_schema.COLUMNS where TABLE_SCHEMA='库名' and table_name = '表名'
```


