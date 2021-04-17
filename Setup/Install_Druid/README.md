
# Install druid on Docker

* Create an external network

```bash
docker network create sandbox
```

* Start all components

```bash
docker-compose up -d
```

* Shut down all components

```bash
docker-compose down
```

## UI

http://localhost:30888

The host and port should be that of the `router` process

# Notes

* Add extention `druid-kafka-indexing-service` to the `druid_extensions_loadList` in the 
  `environment` file

	```
	druid_extensions_loadList=["druid-kafka-indexing-service","druid-histogram", "druid-datasketches", "druid-lookups-cached-global", "postgresql-metadata-storage"]
	```

* Ports to be exposed for

 - coordinator
 - router
 - broker


