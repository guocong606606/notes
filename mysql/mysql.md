# mysql

## 用户：

### 创建用户：

```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

（ host ：指定登录ip，不限制时用%）

### 授权：

```sql
GRANT privileges ON databasename.tablename TO 'username'@'host'
```

（ privileges：权限，全给all；databasename：数据库名；tablename：可操作表权限，全给*.*）

### 删除权限：

```sql
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```

### 删除用户：

```sql
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

```sql
select user,host,plugin,authentication_string from user;
```

此时，root用户已修改，可以通过navicat连接：

![image-20191224095932907](mysql.assets\image-20191224095932907.png)

修改：

```sql
alter user 'username'@'%' identified with mysql_native_password by 'password';
```

