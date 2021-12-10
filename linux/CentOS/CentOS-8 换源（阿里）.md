# CentOS-8 换源（阿里）

## 备份原有源文件

```shell
cd /etc/yum.repos.d/
mv /etc/yum.repos.d/CentOS-AppStream.repo /etc/yum.repos.d/CentOS-AppStream.repo.bak
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
mv /etc/yum.repos.d/CentOS-centosplus.repo /etc/yum.repos.d/CentOS-centosplus.repo.bak
mv /etc/yum.repos.d/CentOS-Extras.repo /etc/yum.repos.d/CentOS-Extras.repo.bak
mv /etc/yum.repos.d/CentOS-PowerTools.repo /etc/yum.repos.d/CentOS-PowerTools.repo.bak
```

## 下载阿里源文件

- 如果有wget，使用如下命令

```shell
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
```

+ 没有wget的情况下适用curl安装

```shell
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
```

+ 如果出现无法解析域名的错误，先配置域名解析服务器

```shell
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
```

- 安装完成后，查看yum.repos.d目录下是否存在不是`.bak`后缀的源文件，至此安装完成
- 执行`cat`命令，验证安装结果 -- 成功的前提下会提示阿里云镜像相关信息

```shell
ls -l /etc/yum.repos.d/
cat /etc/yum.repos.d/CentOS-Base.repo
```

- 生成缓存

```shell
yum makecache
```