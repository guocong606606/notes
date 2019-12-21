## mysql

### 用户：

#### 创建用户：

```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

（ host ：指定登录ip，不限制时用%）

#### 授权：

```sql
GRANT privileges ON databasename.tablename TO 'username'@'host'
```

（ privileges：权限，全给all；databasename：数据库名；tablename：可操作表权限，全给*.*）

#### 删除权限：

```sql
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```

#### 删除用户：

```sql
DROP USER 'username'@'host';
```

