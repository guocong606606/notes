# elasticSearch索引模板

## 内容

+ settings: 指定index的配置信息, 比如分片数、副本数, tranlog同步条件、refresh策略等信息;

+ mappings: 指定index的内部构建信息

## 作用

+ 当需要频繁建立索引时，只需要维护索引模板即可
+ es有同步数据进入时，自动根据索引模板创建索引

## 创建

```
PUT _template/template_name
```

## 查看

- 查看

```
GET _template                // 查看所有模板
GET _template/temp*          // 查看与通配符相匹配的模板
GET _template/temp1,temp2    // 查看多个模板
GET _template/template_name  // 查看指定模板
```

- 验证是否存在

```
HEAD _template/template_name
```

- 结果

```
存在, 响应结果是: 200 - OK
不存在, 响应结果是: 404 - Not Found
```

## 删除

```
DELETE _template/template_name	// 删除上述创建的模板
```

