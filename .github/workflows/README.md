# Elasticsearch

Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time.

## Index

An index is a collection of documents that have somewhat similar characteristics.

### Create Index `ipadic_lyp`
```
PUT /ipadic_lyp
{
  "mappings": {
    "_doc": {
      "dynamic": "strict",
      "properties": { 
        "title":    { "type": "keyword"  },
        "name":     { "type": "text"  }, 
        "age":      { "type": "integer" },
        "position": { "type": "text" },  
        "created":  {
          "type":   "date", 
          "format": "strict_date_optional_time||epoch_millis"
        }
      }
    }
  },
   "settings": {
     "number_of_shards":"3",
     "number_of_replicas": 1
   }
}
```
* *ipadic_lyp* is the name of this index
* the value of *dynamic* can be true(default), false and strict.   
    >ture   : add new fields dynamically  
    >false  : ignore new fields (can be seen, just cannot be used as search condition)  
    >strict : throw an exception when a new field appear  
* the default values of *number_of_shards* and *number_of_replicas* are 5 and 1, which means the index has 5 primary shards and each primary shard has 5 replicas.

### Delete index `ipadic_lyp`
```
DELETE /ipadic_lyp
```

## Document

A document is a JSON document which is stored in Elasticsearch.

### Create Document

When you use method *PUT* to create documents you need to provide *ID*, when you use method *POST* you needn't.  

* create one document once  
```
PUT /ipadic_lyp/_doc/101
{
  "name":"Ashe",
  "title":"寒冰射手",
  "age":18,
  "created":"2018-12-25"
}
```
* create more than one document once
```
POST /ipadic_lyp/_doc/_bulk
{"index":{"_id":"1"}}
{"name":"Garen","title":"德玛西亚之力","age":37,"position":"top","created":"2019-05-17"}
{"index":{"_id":"2"}}
{"name":"Rivan","title":"放逐之刃","age":27,"position":"top","created":"2019-04-17"}
{"index":{"_id":"3"}}
{"name":"Jax","title":"武器大师","age":40,"position":"top","created":"2019-04-22"}
{"index":{"_id":"4"}}
{"name":"Yi","title":"无极剑圣","age":55,"position":"jun","created":"2019-06-17"}
{"index":{"_id":"5"}}
{"name":"Lee sin","title":"盲僧","age":31,"position":"jun","created":"2019-01-05"}
{"index":{"_id":"6"}}
{"name":"Shieda Kayn","title":"影流之镰","age":55,"position":"jun","created":"2019-07-17"}
{"index":{"_id":"7"}}
{"name":"Yasuo","title":"疾风剑豪","age":34,"position":"mid","created":"2019-08-21"}
{"index":{"_id":"8"}}
{"name":"Zed","title":"影流之主","age":44,"position":"mid","created":"2019-08-29"}
{"index":{"_id":"9"}}
{"name":"Brand","title":"复仇焰魂","age":40,"position":"mid","created":"2019-09-21"}
```

### Delete document

* delete one document once
```
DELETE /ipadic_lyp/_doc/1
```
* delete more than one document once
```
POST /ipadic_lyp/_doc/_bulk
{"delete":{"_id":"1"}}
{"delete":{"_id":"2"}}
```

### Update document

* directly update a whole document  
```
PUT /ipadic_lyp/_doc/1
{
  "name":"Ashe",
  "title":"寒冰射手",
  "age":18,
  "position":"ADC",
  "created":"2018-12-25"
}
```
* directly update part of more than one document
```
POST /ipadic_lyp/_doc/_bulk
{"update":{"_id":"1"}}
{"doc":{"title":"王五","age":15}}
{"update":{"_id":"2"}}
{"doc":{"name":"zhao","title":"燕小六","age":16}}
```
* update part of a document by *_update* API
```
POST /ipadic_lyp/_doc/1/_update
{
  "doc":{
    "name":"this is a test"
  }
}
```
* update part of a document by *_update* API and *script*
```
POST /ipadic_lyp/_doc/1/_update
{
  "script": "ctx._source.age+=1"
}
```

### Term-level Query
You can use term-level queries to find documents based on precise values in structured data.
#### Term query
Returns documents that contain an exact term in a provided field. Avoid using the term query for text fields.
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "term":{
      "title":"无极剑圣"
      
    }
  }
}
```

#### Terms query
Returns documents that contain one or more exact terms in a provided field.
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "terms":{
      "title":["无极剑圣","放逐之刃"]  
    }
  }
}
```

#### Exists query
Returns documents that contain an indexed value for a field.
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "exists":{
      "field":"name"
    }
  }
}
```

#### Range query
Returns documents that contain terms within a provided range.
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "range":{
      "created":{
        "gte":"2019-01-10",
        "lt":"2019-05-20||+2M"
      }
    }
  }
}
```

#### Fuzzy query
Returns documents that contain terms similar to the search term, as measured by a Levenshtein edit distance.
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "fuzzy":{
      "name":{
      "value": "Shiede",
      "fuzziness": "AUTO",
      "max_expansions": 50,
      "prefix_length": 0,
      "transpositions": true,
      "rewrite": "constant_score"
      }
    }
  }
}
```

#### Wildcard query
Returns documents that contain terms matching a wildcard pattern.
* ?, which matches any single character
* *, which can match zero or more characters, including an empty one
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "wildcard":{
      "title":{
      "value": "无*圣",
      "rewrite": "constant_score"
      }
    }
  }
}
```

### Full Text Query
The full text queries enable you to search analyzed text fields such as the body of an email. The query string is processed using the same analyzer that was applied to the field during indexing.
#### Match
Returns documents that match a provided text, number, date or boolean value. The provided text is analyzed before matching.
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "match":{
      "name":{
        "fuzziness": "AUTO",
        "query":"Shiede yii",
        "operator":"or"
      }
    }
  }
}
```

#### Mathc_phrase
The match_phrase query analyzes the text and creates a phrase query out of the analyzed text.
```
GET /ipadic_lyp/_doc/_search
{
  "query":{
    "match_phrase":{
      "name":"Shieda Kayn is"
    }
  }
}
```

#### Multi_match
The multi_match query builds on the match query to allow multi-field queries
```
{
  "query":{
    "multi_match":{
      "query":"Shieda Kayn top ",
      "fields": ["name","position"]
    }
  }
}
```


