# CentOS-8 安装MySQL 

## 基础环境

+ centOS-8.2

## 安装方式

+ yum

## 步骤

### 查看现有MySQL版本

+ Linux命令：
  + 未安装的情况下，无信息
  + 如已安装，需要先使用yum卸载  `yum remove XXX`

```shell
rpm -qa | grep mysql
```

	+ 如须彻底移除之前MySQL配置，使用 `find / -name mysql` 命令查找后使用 `rm -rf` 命令移除

### 配置yum源

+ centos的yum源中默认是没有MySQL的，所以需要先配置MySQL的repo源

  + 官网地址：[MySQL](https://dev.mysql.com/downloads/repo/yum/)
  + 拉取rpm

  ```shell
  wget https://dev.mysql.com/get/mysql80-community-release-el8-2.noarch.rpm
  ```

+ 安装rpm

```shell
rpm -ivh mysql80-community-release-el8-2.noarch.rpm
```

+ 更新yum

```shell
yum clean all 
yum update # 需要等
```

+ 安装MySQL

```shell
yum install -y mysql-server
```

+ 验证（也可使用ps）

```shell
mysqladmin --version
```

### 基础项

```shell
systemctl start mysqld
systemctl enable mysqld
systemctl status mysqld
```

+ 相关路径
  + 命令：/usr/bin
  + 配置文件：/usr/share/mysql
  + 数据库文件：/var/lib/mysql
  + 基础配置：/etc/my.cnf 
    + 包含d文件
+ 基础配置信息   path ： /etc/my.cnf

```shell
#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d


[mysqld]
bind-address=0.0.0.0
port=3326
# remove ONLY_FULL_GROUP_BY to resolve columns not in select cells
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION

```

