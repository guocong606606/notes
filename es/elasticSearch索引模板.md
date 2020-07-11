# jsonelasticSearch索引模板

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



### job

```json
{
	"index_patterns": [
		"search_jobs"
	],
	"settings": {
		"number_of_shards": "5",
		"number_of_replicas": "1",
		"analysis": {
			"analyzer": {
				"optimizeIK": {
					"type": "custom",
					"tokenizer": "ik_max_word",
					"filter": [
						"stemmer"
					]
				}
			}
		}
	},
	"mappings": {
		"properties": {
			"industryCode": {
				"type": "text"
			},
			"jobType": {
				"type": "long"
			},
			"jobEnText": {
				"type": "text",
				"analyzer": "optimizeIK"
			},
			"sexValue": {
				"type": "text"
			},
			"jobChName": {
				"type": "text"
			},
			"salaryMin": {
				"type": "long"
			},
			"sexCh": {
				"type": "text"
			},
			"jobEnName": {
				"type": "text"
			},
			"ageMax": {
				"type": "long"
			},
			"rest": {
				"type": "text"
			},
			"sexEn": {
				"type": "text"
			},
			"ageMin": {
				"type": "long"
			},
			"salaryMax": {
				"type": "long"
			},
			"typeValue": {
				"type": "long"
			},
			"industryEnName": {
				"type": "text"
			},
			"industryChName": {
				"type": "text"
			},
			"jobId": {
				"type": "long"
			},
			"jobChText": {
				"type": "text",
				"analyzer": "optimizeIK"
			},
			"updateTime": {
				"type": "timestamp"
			},
			"countryPasses": {
				"type": "nested",
				"properties": {
					"countryId": {
						"type": "long"
					},
					"passId": {
						"type": "long"
					},
					"passChName": {
						"type": "text"
					},
					"passEnName": {
						"type": "text"
					}
				}
			},
			"subwayBoList": {
				"type": "nested",
				"properties": {
					"subwayId": {
						"type": "long"
					},
					"subwayChName": {
						"type": "text"
					},
					"subwayEnName": {
						"type": "text"
					}
				}
			}
		}
	}
}
```



### customer

```json
{
	"index_patterns": [
		"search_customers"
	],
	"settings": {
		"number_of_shards": "5",
		"number_of_replicas": "1"
	},
	"mappings": {
		"properties": {
			"customerId": {
				"type": "long"
			},
			"cnName": {
				"type": "text"
			},
			"enName": {
				"type": "text"
			},
			"countryValue": {
				"type": "long"
			},
			"countryChName": {
				"type": "text"
			},
			"countryEnName": {
				"type": "text"
			},
			"sexValue": {
				"type": "long"
			},
			"hometownCode": {
				"type": "text"
			},
			"hometown": {
				"type": "text"
			},
			"sexCh": {
				"type": "text"
			},
			"sexEn": {
				"type": "text"
			},
			"age": {
				"type": "long"
			},
			"personalDescription": {
				"type": "text"
			},
			"industries": {
				"type": "nested",
				"properties": {
					"industryCode": {
						"type": "text"
					},
					"industryChName": {
						"type": "text"
					},
					"industryEnName": {
						"type": "text"
					},
					"parentCode": {
						"type": "text"
					},
					"parentChName": {
						"type": "text"
					},
					"parentEnName": {
						"type": "text"
					}
				}
			}
		}
	}
}
```

