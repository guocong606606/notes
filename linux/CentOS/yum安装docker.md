# yum安装docker

## 清理docker

+ 查看现有docker

```shell
yum list installed | grep docker
```

+ 卸载

```shell
yum -y remove ** (卸载目录)
```

----

## 设置

+ 设置阿里源

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新yum
yum update
```



+ 安装必要包

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 安装

+ 查看yum源中的版本

```shell
yum list docker-ce --showduplicates | sort -r
```

+ 安装

```shell
yum install docker-ce  # 此处不添加任何版本，默认安装最新稳定版，需要安装指定版本时指定版本号即可：docker-ce-20.10.11.ce
```

+ 查看

```shell
docker --version # 查看当前docker版本
```



## 基础操作

+ 设置开机自启

```shell
systemctl enable docker
```

+ 启动/停止

```shell
systemctl start/stop/restart docker
```

+ 查看启动状态

```shell
systemctl status docker
```

## docker配置

+ 阿里镜像加速（配置etc/docker/daemon.json文件）

```json
{  
"registry-mirrors": ["https://790odqyb.mirror.aliyuncs.com"] 
} 
```

+ 生效

```shell
systemctl daemon-reload
# 重载后重启docker
```

