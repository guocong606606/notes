# 关于binlog

+ 安装MySQL后首先开启binlog（很多情况默认是开启的）、
  + 开启binlog的情况下，MySQL的每次服务启动会生成一个binlog文件，具体名称由配置决定
  + 生成的binlog会以binlog.000001这种形式存在于数据库的data目录下
  + binlog位置在/usr/bin目录下，可以通过mysqlbinlog命令运行binlog文件
  + 数据丢失时，如果binlog保存完整，可以根据binlog恢复数据库（DDL完整的前提下可直接恢复库）
+ 查看MySQL的binlog开启状态

```mysql
show variables like '%log_bin%';
# 输出结果中log_bin 的value值为on  表示开启
```

+ 如果未开启，编辑my.cnf文件，重启MySQL后可开启

```shell
# 开启binlog日志
log-bin =/app/mysql_binlog/mysql-bin 
# 日志保留天数
expire_logs_days=7
```

+ MySQL查看binlog日志

```mysql
# 查看现有binlog日志
show master logs;
# 查看当前binlog文件，在没有重启过MySQL服务的前提下，近期数据可直接从这一个文件中恢复
show master status;
```

+ 查看binlog文件内容
  + 正常情况下，binlog文件比较大，需要等待
  + 每条记录都有两个pos（position）点，记录的是事件的起止点，前一事件的End_log_pos即为下一条记录（下一事件）的pos点，所以实际使用时只需要处理一个点

```mysql
show binlog events in 'binlog.000003';
```

+ 数据恢复
  + 参数说明：
    + mysqlbinlog：指令
    + --start-position：数据恢复起始点
    + --stop-position：数据恢复截至点，如果只写这个点，恢复此位置之前的所有数据
    + /path/mysql-bin.000003： 指定binlog文件，多个可以都写（  ‘*’  符号应该也可行）
    + | mysql -uroot -proot：恢复的目标链接
    + --start-datetime：起始时间，作用与position基本一致，但不取起始点，而是时间（2020-01-01 00:00:00）
    + --stop-datetime： 截至时间，同上
    + --no-defaults：读取没有选项的文件，有时如果不添加mysqlbinlog会报出无法识别  default-character-set=utf8  `指令的错误

```shell
mysqlbinlog --start-position=4 --stop-position=2399 /path/mysql-bin.000003 | mysql -uroot -proot
```

