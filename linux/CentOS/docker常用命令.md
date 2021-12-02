# docker常用命令

## 镜像

+ 查看镜像

```shell
docker search mysql
```

+ 镜像拉取

```shell
docker pull mysql:8.0.23
```

+ 删除

```shell
docker stop $(docker ps -aq)
```

+ 全部删除

```shell
docker rmi -f $(docker images -qa)
```



## 容器

+ 查看容器

```shell
docker ps -a
```

+ 操作

```shell
docker run/stop container-name # id也可以
```

+ 容器运行（e.g.  mysql-8.0.23）

```shell
docker run -id -p 3316:3306 --name mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -v $PWD/my.cnf:/etc/mysql/my.cnf -e MYSQL_ROOT_PASSWORD=root --restart=always -d mysql:8.0.23
# 运行容器前先创建映射目录，如在当前目录下运行可指定映射路径位  $PWD
```

+ 全部停止 （tip：全部操作，相当于一个嵌套，查询全部后进行操作）

```shell
docker stop $(docker ps -aq)
```

+ 全部删除

```shell
docker rm $(docker ps -aq)
```

