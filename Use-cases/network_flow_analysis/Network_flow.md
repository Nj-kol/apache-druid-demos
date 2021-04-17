
# Druid realtime use case

## Schema of the input data

```json
{
   "ts":"2018-01-01T01:01:35Z",
   "srcIP":"1.1.1.1",
   "dstIP":"2.2.2.2",
   "srcPort":2000,
   "dstPort":3000,
   "protocol":6,
   "packets":10,
   "bytes":1000,
   "cost":1.4
}
```

## Ingestion spec

network-flow-spec.json

```json
{
   "type":"kafka",
   "spec":{
      "dataSchema":{
         "dataSource":"network_flow",
         "timestampSpec":{
            "format":"iso",
            "column":"ts"
         },
         "dimensionsSpec":{
            "dimensions":[
               {
                  "name":"srcIP",
                  "type":"string"
               },
               {
                  "name":"srcPort",
                  "type":"long"
               },
               {
                  "name":"dstIP",
                  "type":"string"
               },
               {
                  "name":"dstPort",
                  "type":"long"
               },
               {
                  "name":"protocol",
                  "type":"string"
               }
            ]
         },
         "metricsSpec":[
            {
               "type":"count",
               "name":"count"
            },
            {
               "type":"longSum",
               "name":"packets",
               "fieldName":"packets"
            },
            {
               "type":"longSum",
               "name":"bytes",
               "fieldName":"bytes"
            },
            {
               "type":"doubleSum",
               "name":"cost",
               "fieldName":"cost"
            }
         ],
         "granularitySpec":{
            "type":"uniform",
            "segmentGranularity":"HOUR",
            "queryGranularity":"MINUTE",
            "rollup":true
         }
      },
      "ioConfig":{
         "topic":"network-flow",
		 "inputFormat": {
		 	"type": "json"
		 },
         "consumerProperties":{
            "bootstrap.servers":"LM0001680:39092"
         }
      }
   }
}
```

## Post the spec using REST

```bash
curl -X POST -H 'Content-Type: application/json' \
-d @network-flow-spec.json \
http://localhost:30281/druid/indexer/v1/supervisor
```

The host and post should be that of the `coordinator`

# Loading data into kafka

## Sample Dataset

```json
{"ts":"2018-01-01T01:01:35Z","srcIP":"1.1.1.1", "dstIP":"2.2.2.2", "srcPort":2000, "dstPort":3000, "protocol": 6, "packets":10, "bytes":1000, "cost": 1.4}
{"ts":"2018-01-01T01:01:51Z","srcIP":"1.1.1.1", "dstIP":"2.2.2.2", "srcPort":2000, "dstPort":3000, "protocol": 6, "packets":20, "bytes":2000, "cost": 3.1}
{"ts":"2018-01-01T01:01:59Z","srcIP":"1.1.1.1", "dstIP":"2.2.2.2", "srcPort":2000, "dstPort":3000, "protocol": 6, "packets":30, "bytes":3000, "cost": 0.4}
{"ts":"2018-01-01T01:02:14Z","srcIP":"1.1.1.1", "dstIP":"2.2.2.2", "srcPort":5000, "dstPort":7000, "protocol": 6, "packets":40, "bytes":4000, "cost": 7.9}
{"ts":"2018-01-01T01:02:29Z","srcIP":"1.1.1.1", "dstIP":"2.2.2.2", "srcPort":5000, "dstPort":7000, "protocol": 6, "packets":50, "bytes":5000, "cost": 10.2}
{"ts":"2018-01-01T01:03:29Z","srcIP":"1.1.1.1", "dstIP":"2.2.2.2", "srcPort":5000, "dstPort":7000, "protocol": 6, "packets":60, "bytes":6000, "cost": 4.3}
{"ts":"2018-01-01T02:33:14Z","srcIP":"7.7.7.7", "dstIP":"8.8.8.8", "srcPort":4000, "dstPort":5000, "protocol": 17, "packets":100, "bytes":10000, "cost": 22.4}
{"ts":"2018-01-01T02:33:45Z","srcIP":"7.7.7.7", "dstIP":"8.8.8.8", "srcPort":4000, "dstPort":5000, "protocol": 17, "packets":200, "bytes":20000, "cost": 34.5}
{"ts":"2018-01-01T02:35:45Z","srcIP":"7.7.7.7", "dstIP":"8.8.8.8", "srcPort":4000, "dstPort":5000, "protocol": 17, "packets":300, "bytes":30000, "cost": 46.3}
```

* Save the above dataset in a file called `network_flow.json`

## Kafka

**Create a Kafka Topic**

```bash
docker container exec -it kafka-standalone bash

kafka-topics.sh \
--create \
--zookeeper zookeeper-standalone:2181 \
--partitions 1 \
--replication-factor 1 \
--topic network-flow
```
* Push sample JSON into Kafka topic, using the Kafka script from the Kafka download

```bash
vi ${HOME}/volumes/kafka/samples/network_flow.json # copy & paste the above data 

docker container exec -it kafka-standalone bash

kafka-console-producer.sh \
--broker-list localhost:9092 \
--topic network-flow < /opt/samples/network_flow.json
```

# Notes

* Use underscore as a separator istead of dash (-) in defining datasource name 
  ( For example - `network_flow` instead of `network-flow`)

References
==========
https://druid.apache.org/docs/latest/development/extensions-core/kafka-ingestion.html

https://druid.apache.org/docs/latest/tutorials/docker.html