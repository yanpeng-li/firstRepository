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
>false  : ignore new fields  
>>strict : throw an exception when a new field appear  
* 
