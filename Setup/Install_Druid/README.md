
# Install druid on Docker

* Create an external network

```bash
docker network create sandbox
```
* Install

```bash
docker-compose up -d
```

* Ports to be exposed for

 - coordinator
 - router
 - broker

* Add extention `druid-kafka-indexing-service` to the `druid_extensions_loadList` in the 
  environment file

```
druid_extensions_loadList=["druid-kafka-indexing-service","druid-histogram", "druid-datasketches", "druid-lookups-cached-global", "postgresql-metadata-storage"]
```