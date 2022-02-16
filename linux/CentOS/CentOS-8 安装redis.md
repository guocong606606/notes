# CentOS-8 安装redis

## 安装

+ 添加epel仓库

```shell
yum install epel-release
```

+ 安装

```shell
yum install redis
```

## 配置

+ redis配置文件位置

```path
/etc/redis.conf
```

+ 相关配置

```
# ip限制
bind 0.0.0.0
# 密码登录
requirepass password
```

+ systemctl 标签
```
redis.service
```