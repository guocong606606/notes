# docker 安装留存

### rabbit

+ ```sh
  docker run -di --name rabbitmq -p 9986:5672 -p 9988:15672 -v /home/rabbit:/var/lib/rabbitmq --hostname rabbit -e RABBITMQ_DEFAULT_USER=gc -e RABBITMQ_DEFAULT_PASS=gc rabbitmq:management
  
  ```

### mongo

+ ```sh
  docker run --name mongo -p 9982:27017 -d mongo --auth
  ```

+ 进入容器内mongo控制台

+ ```sh
  docker exec -it [id] mongo admin
  ```


### redis

+ ```shell
  docker run -p 9736:6379 --name redis -v /home/redis/data:/data -v /home/redis/conf/redis.conf:/etc/redis/redis.conf -d redis redis-server /etc/redis/redis.conf
  ```

  