# CentOS 安装mongo 4.4

## yum安装

### 配置yum源

+ ```shell
  vim /etc/yum.repos.d/mongodb-org-4.4.repo
  ```

  ```shell
  [mongodb-org-4.4]
  name=MongoDB Repository
  baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
  gpgcheck=1
  enabled=1
  gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
  ```

### 安装

+ ```shell
  yum update 
  yum install -y mongodb-org
  ```

### 启动

+ ```shell
  # 启动（systemctl标准命令）
  systemctl start mongod.service
  # 自启
  systemctl enable mongod.service
  ```

### 远程链接配置

+ 编辑mongod.conf

  + ```shell
    vim /etc/mongod.conf
    ```

+ 修改信息

  + ```shell
    # 0.0.0.0 不限制登录ip
    net:
      port: 27777
      bindIp: 0.0.0.0
    ```

### 连接配置

+ 命令行连接

  + ```shell
    mongo --host 127.0.0.1 --port 27777
    ```

+ 用户配置：

  + 指定数据库

    ```mongo
    use admin;
    ```

  + 创建管理员用户

    ```json
    db.createUser(
       {
         user: "root",
         pwd: "root",
         roles: [ { role: "userAdminAnyDatabase",db: "admin" } ]
       }
     )
    # 成功后显示
    Successfully added user: {
            "user" : "root",
            "roles" : [
                    {
                            "role" : "userAdminAnyDatabase",
                            "db" : "admin"
                    }
            ]
    }
    ```

+ 查看用户

  + ```
    show users;
    ```

  + ```json
    {
            "_id" : "admin.root",
            "userId" : UUID("f7f000f1-6019-4eb4-a066-44072772cbf4"),
            "user" : "root",
            "db" : "admin",
            "roles" : [
                    {
                            "role" : "userAdminAnyDatabase",
                            "db" : "admin"
                    }
            ],
            "mechanisms" : [
                    "SCRAM-SHA-1",
                    "SCRAM-SHA-256"
            ]
    }
    ```

+ 至此：mongodb管理员用户配置完毕

+ tip：创建普通用户和管理员用户语句相同，但需要在创建前先切换到对应的数据库下

+ mongodb用户权限说明：

  + ```
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

    

### 开启访问控制

+ 编辑mongod.service

  + ```shell
    vim /usr/lib/systemd/system/mongod.service
    ```

  + 修改如下信息

    ```shell
    # 原信息
    Environment="OPTIONS=-f /etc/mongod.conf"
    # 修改后的信息
    Environment="OPTIONS=--auth -f /etc/mongod.conf"
    ```

+ 修改后重载配置并重启

  ```shell
  # 配置重载
  systemctl daemon-reload
  # 重启mongoDB
  systemctl restart mongod.service
  ```

+ 至此，进入mongoDB需要输入用户密码

  ```shell
  mongo --host 127.0.0.1 --port 27777 -u root -p root
  ```


