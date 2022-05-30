# Docker-Portainer



## 基础配置

+ 多个具由docker的服务器（linux）
+ 服务器开放2375端口
+ 其中一台服务器有portainer容器

```dockerfile
docker run -d -p 8900:9000 -v /var/run/docker.sock:/var/run/docker.sock --name portainer portainer/portainer
```

## 节点配置

+ 修改配置文件

```shell
vim /usr/lib/systemd/system/docker.service
```

+ 修改内容如下

```shell
ExecStart= xxx -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
# xxx代表原有的参数，追加 -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock 内容
```

+ 保存后重载配置，重启docker

```shell
systemctl daemon-reload
systemctl restart docker
```

+ 检查是否生效

```shell
ss -unlpt | grep 2375 
```

![image-20220104145401234](D:\self\linux\docker\Docker-Portainer.assets\image-20220104145401234.png)

## 多节点配置

![image-20220104145547648](D:\self\linux\docker\Docker-Portainer.assets\image-20220104145547648.png)

+ 添加信息如下

![image-20220104145713590](D:\self\linux\docker\Docker-Portainer.assets\image-20220104145713590.png)

+ 至此，可使用单节点portainer管理多个docker