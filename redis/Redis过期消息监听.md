# Redis过期消息监听

## 场景

+ 硬件设备连接服务端，会定期发送心跳消息，数据上报消息等类型的消息，但出现设备断电/网络不通等场景时，无法获取设备数据，就没有触发点去更新设备的在线状态

## 实现思路

+ 将设备主键作为key值，向redis缓存任意固定值，设置时长为设备最大心跳间隔
+ 服务端每一次收到消息（任意类型）时，对相应key的有效时间进行更新，更新值仍为最大心跳间隔；
+ 配置redis过期监听，当某一key值过期删除时，更新对应数据，即可实现场景需求

### Redis过期时长

+ 关于redis的过期时长问题

```redis
# 使用setex设置key值有效时长
setex test_key 20 test_contant
# expire 命令：设置key的有效时长
expire test_key 60
# persist 命令：移除有效时长（返回1：成功）
persist test_key
# ttl命令： 查看剩余时长(返回-1：当前key值无过期时间，-2：key值已删除，否则返回当前key剩余时长)
ttl test_key
```

### 开启Redis过期监听

+ 修改配置文件  redis.conf  中的事件通知类型（默认为空）后重启redis

  ```shell
  notify-keyspace-events "Ex"
  ```

+ 配置详解

  ```
  ############################# EVENT NOTIFICATION ##############################
  
  # 键空间通知使得客户端可以通过订阅频道或模式，来接收那些以某种方式改动了 Redis 数据集的事件。
  # 因为开启键空间通知功能需要消耗一些 CPU ，所以在默认配置下，该功能处于关闭状态。
  # notify-keyspace-events 的参数可以是以下字符的任意组合，
  # 它指定了服务器该发送哪些类型的通知：
  # K 键空间通知，所有通知以 __keyspace@__ 为前缀
  # E 键事件通知，所有通知以 __keyevent@__ 为前缀
  # g DEL 、 EXPIRE 、 RENAME 等类型无关的通用命令的通知
  # $ 字符串命令的通知
  # l 列表命令的通知
  # s 集合命令的通知
  # h 哈希命令的通知
  # z 有序集合命令的通知
  # x 过期事件：每当有过期键被删除时发送
  # e 驱逐(evict)事件：每当有键因为 maxmemory 政策而被删除时发送
  # A 参数 g$lshzxe 的别名
  # 输入的参数中至少要有一个 K 或者 E，否则的话，不管其余的参数是什么，都不会有任何 通知被分发。
  ```

+ Redis 订阅者模式

  ```
  # 进入订阅者模式（此模式下只能使用订阅者命令）
  psubscribe __keyevent@0__:expired
  # 退出订阅者模式
  punsubscribe
  # PUBLISH 命令进行发布
  publish aa "test1"
  # subscribe 通道订阅命令 可订阅多个
  subscribe aa bb
  # unsubscribe 退订命令
  ```

  

### SpringBoot监听配置

#### 方案1

+ 增加redis配置

```java
package com.cloud.ayc.iot.config;

import com.cloud.ayc.iot.listener.RedisListener;
import org.springframework.boot.autoconfigure.data.redis.RedisProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.listener.PatternTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;

import javax.annotation.Resource;

/**
 * @author guocong
 * @date 2022/2/8 16:26
 */
@Configuration
public class RedisListenerConfig {
    @Resource
    RedisListener redisListener;
    @Resource
    RedisProperties redisProperties;

    private static final String PREFIX = "__keyevent@";
    private static final String TYPE = "__:expired";

    @Bean
    RedisMessageListenerContainer container(RedisConnectionFactory factory) {
        // 获取当前redis库
        int database = redisProperties.getDatabase();

        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(factory);
        container.addMessageListener(redisListener, new PatternTopic(PREFIX + database + TYPE));
        return container;
    }

}
```



+ 监听类

```java
package com.cloud.ayc.iot.listener;

import lombok.extern.slf4j.Slf4j;
import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;

/**
 * @author guocong
 * @date 2022/2/8 16:27
 */
@Slf4j
@Component
public class RedisListener implements MessageListener {

    @Resource
    private RedisTemplate<String, Object> redisTemplate;

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String expiredKey = message.toString();
        // 此处处理业务逻辑
        log.debug("🚄 -> 调试信息：到期key：" + expiredKey);

    }
}

```



#### 方案2

+ 初始化redis监听容器

```java
	/**
    * redis监听器容器初始化
    *
    * @return 容器
    */    
	@Bean
    public RedisMessageListenerContainer redisMessageListenerContainer() {
        RedisMessageListenerContainer redisMessageListenerContainer = new RedisMessageListenerContainer();
        redisMessageListenerContainer.setConnectionFactory(redisConnectionFactory);
        return redisMessageListenerContainer;
    }
```

+ 继承 KeyExpirationEventMessageListener 类

```java
package com.cloud.ayc.iot.listener;

import lombok.extern.slf4j.Slf4j;
import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.listener.KeyExpirationEventMessageListener;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
import org.springframework.stereotype.Component;

import java.nio.charset.StandardCharsets;

/**
 * redis key expire 超时事件监听
 * @author guocong
 * @date 2022/2/8
 */
@Slf4j
@Component
public class RedisKeyExpiredListener extends KeyExpirationEventMessageListener {


    public RedisKeyExpiredListener(RedisMessageListenerContainer listenerContainer) {
        super(listenerContainer);
    }

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String channel = new String(message.getChannel(), StandardCharsets.UTF_8);
        //过期的key
        String key = new String(message.getBody(), StandardCharsets.UTF_8);
        onListener(key);
    }

    public void onListener(String key) {
		// 此处处理业务逻辑
        log.debug("🚄 -> 调试信息：redis key值过期：" + key);

    }

}

```

