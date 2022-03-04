# python随手记

## pymysql

+ python-MySQL操作库

+ 常规查询

  + 其中SQL为查询语句
  + 查询结果只显示字段值

  ```python
  # 打开数据库连接
  conn = pymysql.connect(host=host, port=3306, user=user, passwd=passwd, db=db, charset='utf8')
  # 使用 cursor() 方法创建一个游标对象 cursor
  cur = conn.cursor()
  # 使用 execute()  方法执行 SQL 查询
  cur.execute(SQL)
  # 查询多条
  data = cur.fetchall()
  # 查询单数据
  data = cur.fetchone()
  # 关闭连接
  conn.close()
  ```

  + 需要字段名时需要在创建连接时指定游标类型为字典：pymysql.cursors.DictCursor
    + 此时返回的值为json格式

  ```python
  conn = pymysql.connect(host=host, user=user, passwd=passwd,                     port=3306, db='db', charset='utf8', cursorclass=pymysql.cursors.DictCursor)
  ```

  