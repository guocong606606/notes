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



## http接口

+ 临时接收数据使用

+ demo

  ```python
  from flask import Flask, request, jsonify
  
  app = Flask(__name__)
  
  
  @app.route('/py1', methods=["POST"])
  def r_post():
      """
      post 接口
      :return:
      """
      params = request.form if request.form else request.json
      print(params)
  
      res = {"result": 'received successfully'}
      return jsonify(content_type='application/json;charset=utf-8',
                     reason='success',
                     charset='utf-8',
                     status='200',
                     content=res)
  
  
  @app.route('/py2', methods=["GET"])
  def r_get():
      """
      get 接口
      :return:
      """
      params = request.values
      a = params.get("a")
      b = params.get("b")
      print(b + ' : ' + str(a))
  
      res = {"result": 'received successfully'}
      return jsonify(content_type='application/json;charset=utf-8',
                     reason='success',
                     charset='utf-8',
                     status='200',
                     content=res)
  
  
  if __name__ == '__main__':
      app.run(host='0.0.0.0',
              threaded=True,
              debug=True,
              port=1235)
  
  ```

  