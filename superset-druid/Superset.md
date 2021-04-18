
# Superset

## Pull the latest image

```bash
docker pull amancevice/superset:1.0.0
```

## Spin up a container
 
```bash
docker container run -d -p 30092:8088  \
--network sandbox \
--name superset \
amancevice/superset:1.0.0
```

## Create an admin account ( one time activity )
 
```bash
docker exec -it superset superset-init
```

## URL for Superset UI

http://localhost:30092

## Druid URL for Database creation

druid://LM0001680:30282/druid/v2/sql

The host and port should be that of the `coordinator` node

