## 索引模板：

### wiki：

```json
{
 "index_patterns": ["search_wiki"],
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
            "id": {
                "type": "long"
            }, 
            "type": {
                "type": "long"
            }, 
            "secondaryType": {
                "type": "long"
            }, 
            "question": {
                "type": "text",
                "fields": {
                	"raw":{
                		"type": "completion"
                	}
                },
				"analyzer":"optimizeIK"
            }, 
            "question_description": {
                "type": "text",
                "analyzer":"optimizeIK"
            },
            "answer": {
                "type": "text",
                "analyzer":"optimizeIK"
            }
        }
    }
}

```

### log：

```json
{
 "index_patterns": ["search_log"],
    "settings": {
        "number_of_shards": "5", 
        "number_of_replicas": "1"
    }, 
    "mappings": {
        "properties": {
            "messageId": {
                "type": "text"
            }, 
            "routingKey": {
                "type": "text"
            }, 
            "level": {
                "type": "text"
            }, 
            "categoryName": {
                "type": "text"
            }, 
            "body": {
                "type": "text"
            },
            "timeStamp": {
                "type": "date"
            }
        }
    }
}
```

