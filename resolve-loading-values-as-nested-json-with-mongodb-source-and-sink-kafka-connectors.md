# How to resolve MongoDB Kafka connector Loading documents as nested JSON
## The issue:

> "{\"_id\": {\"_id\": {\"$oid\": \"628382b3a2a49a0b559630e3\"}, \"copyingData\": true}, \"operationType\": \"insert\", \"documentKey\": {\"_id\": {\"$oid\": \"628382b3a2a49a0b559630e3\"}}, \"fullDocument\": {\"_id\": {\"$oid\": \"628382b3a2a49a0b559630e3\"}, \"itemid\": \"Item_184\", \"address\": {\"zipcode\": {\"$numberLong\": \"94041\"}, \"city\": \"Mountain View\", \"state\": \"CA\"}, \"orderid\": {\"$numberLong\": \"18\"}, \"ordertime\": {\"$numberLong\": \"1497014222380\"}}, \"ns\": {\"db\": \"test-db\", \"coll\": \"test-collection\"}}"

> "{\"_id\": {\"_id\": {\"$oid\": \"628382b3a2a49a0b559630e4\"}, \"copyingData\": true}, \"operationType\": \"insert\", \"documentKey\": {\"_id\": {\"$oid\": \"628382b3a2a49a0b559630e4\"}}, \"fullDocument\": {\"_id\": {\"$oid\": \"628382b3a2a49a0b559630e4\"}, \"itemid\": \"Item_184\", \"address\": {\"zipcode\": {\"$numberLong\": \"94041\"}, \"city\": \"Mountain View\", \"state\": \"CA\"}, \"orderid\": {\"$numberLong\": \"18\"}, \"ordertime\": {\"$numberLong\": \"456\"}}, \"ns\": {\"db\": \"test-db\", \"coll\": \"test-collection\"}}"

## Step-1: publish.full.document.only=true on MongoDB Source Connector

```
name=mongo-source
connector.class=com.mongodb.kafka.connect.MongoSourceConnector
tasks.max=1

#Connection and source configuration
connection.uri=mongodb+srv://<username>:<password>@<clusternaame>.mongodb.net
database=test-db
collection=test-collection

topic.prefix=
#topic.suffix=
#poll.max.batch.size=1000
#poll.await.time.ms=5000

# Change stream options
#pipeline=[]
#batch.size=0
#change.stream.full.document=updateLookup
#collation=


#copy.existing=true
publish.full.document.only=true
```

## Step-2: document.id.strategy= com.mongodb.kafka.connect.sink.processor.id.strategy.ProvidedInValueStrategy on MongoDB Sink Connector

```
name=mongo-sink
topics=test-db.test-collection
connector.class=com.mongodb.kafka.connect.MongoSinkConnector
tasks.max=1

# Specific global MongoDB Sink Connector configuration
connection.uri=mongodb+srv://<username>:<password>@<clusternaame>.mongodb.net
database=test-db
collection=sink-collection


## Document manipulation settings
#key.projection.type=none
#key.projection.list=
#value.projection.type=none
#value.projection.list=

#field.renamer.mapping=[]
#field.renamer.regex=[]

document.id.strategy=com.mongodb.kafka.connect.sink.processor.id.strategy.ProvidedInValueStrategy
```

## Screenshots after implementing the above changes

![MongoDB nested JSON issue](https://user-images.githubusercontent.com/73946498/183078404-ff54ae6b-723b-418a-8f3b-bdbcffcdd8c1.png)

