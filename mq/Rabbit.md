# Rabbit

## 安装

### Ubuntu

+ RabbitMq需要erlang语言支持，所以mq安装前需要安装erlang

```shell
apt-get install erlang-nox
```

+ erlang语言信息

```shell
erl
```

+ 添加公钥

```shell
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
```

+ 更新包

```shell
apt-get update
```

+ RabbitMq安装

```shell
apt-get install rabbitmq-server
```

+ 状态查看

```shell
systemctl status rabbitmq-server   #Active: active (running) 说明处于运行状态
service rabbitmq-server status     #用service指令查看，同systemctl指令
```

+ 启动，停止，重启

```shell
service rabbitmq-server start  # stop restart
```

+ 启用插件，可视化界面（address:15672）

```shell
rabbitmq-plugins enable rabbitmq_management   # 启用插件
```

+ 查看用户

```shell
rabbitmqctl list_users
```

+ 添加用户+ 授权

```
rabbitmqctl add_user admin yourpassword   # 增加普通用户
rabbitmqctl set_user_tags admin administrator    # 给普通用户分配管理员角色
```

