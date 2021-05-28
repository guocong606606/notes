# mybatis-plus相关配置问题

### yml文件配置

+ ```yml
  mybatis-plus:
    mapper-locations:
    	# xml路径问题tag：如果使用逆向工程生成，需要在pom中另行配置
      - classpath*:com/cloud/ayc/**/xml/*.xml
    # 驼峰&下划线自动转换
    map-underscore-to-camel-case: true
    global-config:
      db-config:
        # 数据库写入操作，ignored代表空值处理时忽略，也就是说传入null会将数据字段更新未null，此处建议使用独立配置
        # 使用独立配置时注解处理（@TableField(updateStrategy = FieldStrategy.IGNORED),value="?"）
        insert-strategy: not_empty
        update-strategy: ignored
  ```

  