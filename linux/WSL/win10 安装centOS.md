# win10 安装centOS

## 移除原有centOS

+ 查看当前WSL信息

```shell
wslconfig /l
```

+ 卸载

```
wslconfig /l name # name为查看信息显示的名字
```

+ tip：卸载后任务栏锁定的原有的wsl不要点，点开就重新安装原有版本了，去开始菜单里顺便也卸载一下

## 安装centOS8

+ 包在阿里云盘上
+ 一条命令搞定一切

```
wsl --import CentOS D:\centOS D:\ys\centos8.tar
```

