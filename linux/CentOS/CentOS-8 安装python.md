# CentOS-8 安装python

## 环境

+ centOS-8.2

## 安装方式

+ yum+wget

## 安装步骤

### 安装相关依赖

```shell
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make
```

### 安装

+  下载python压缩包

```shell
wget https://www.python.org/ftp/python/3.8.3/Python-3.8.3.tgz
```

+ 解压

```shell
tar xvf Python-3.8.3.tgz
```

+ 进入python目录 进行编译

```shell
cd Python-3.8.3

# 通过运行配置脚本来安装安装程序
./configure --enable-optimizations

# 编译安装
make altinstall
```

+ 结尾看到如下信息，表示安装成功

```
Successfully installed pip-19.2.3 setuptools-41.2.0
```

+ 此时命令

```shell
python3.8 --version
pip3.8 --version
```

### 软链接

```shell
ln -s /usr/local/bin/python3.8 /usr/bin/python

ln -s /usr/local/bin/pip3.8 /usr/bin/pip
```

