# nacos & MySQL-8.0

## 环境：

​	1.java-8

​	2.nacos-1.1.4（源码）

​	3.mysql-8.0.17

## 源码修改：

​	修改pom文件中mysql驱动版本（mysql-connector-java）

修改为：

```xml
<dependency>    
    <groupId>mysql</groupId>    
    <artifactId>mysql-connector-java</artifactId>    
    <version>8.0.17</version>
</dependency>
```

修改依赖后报错，重新引入

```java
import com.mysql.cj.jdbc.MysqlDataSource;
```

连接MySQL：

​	修改配置文件

```properties
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useTimezone=true&serverTimezone=GMT%2B8
db.user=nacos
db.password=nacos
```

注：

​		1.不连接MySQL的情况下，nacos会使用derby（内存库）

​		2.连接MySQL-8.0+ ，须注设置Timezone

## idea启动：

源码修改后，启动本地工程测试，启动前需要对启动模式进行配置（不配置时启动模式为集群模式）：

```
-Dnacos.standalone=true
```

![image-20191220095723553](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191220095723553.png)

注：此时启动可能不报错，但并不能证明MySQL已连接（derby）

无脑验证法:

未修改端口等配置的情况下，控制台会看到一个端口号8848的地址，勇敢地点进去，会出现一个登录页，首次登录默认的账号密码均为    **nacos**

登陆后进入

![image-20191220100750213](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191220100750213.png)

新建后查看MySQL数据库，tenant_info表，当表中存在数据，则已经成功连接MySQL

![image-20191220100922498](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191220100922498.png)

注：新建命名空间显示成功时，不能起到验证效果，依然是因为derby。

## 打包：

命令：

```maven
mvn -Prelease-nacos clean install -U
```

打包后将naocs文件扔到Linux中

![image-20191220101557495](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191220101557495.png)

按常规方式配置  **/nacos/conf/application.properties 文件  

启动（WSL下ubuntu 18+zsh正常启动命令报错）所以采用一下启动方式：

```other
bash -f ./startup.sh -m standalone
```

## mark

修改源码后可能涉及到MysqlDataSource配置出现问题，踩到坑再议。