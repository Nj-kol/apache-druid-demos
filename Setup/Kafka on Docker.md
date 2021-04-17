
# Kafka on Docker

## Launch external Zookeeper instance

```bash
mkdir -p ${HOME}/volumes/zookeeper
chmod -R ugo+rwx ${HOME}/volumes/zookeeper

ZK_HOME=${HOME}/volumes/zookeeper

docker pull zookeeper:3.6.1

docker container run \
-p 30181:2181 \
--network=sandbox \
--name zookeeper-standalone \
--restart always \
-e ZOO_LOG4J_PROP="INFO,ROLLINGFILE" \
-v ${ZK_HOME}/datalog:/datalog \
-v ${ZK_HOME}/data:/data \
-v ${ZK_HOME}/logs:/logs \
-d zookeeper:3.6.1
```

## Housekeeping

```bash
docker container logs zookeeper-standalone

docker container start zookeeper-standalone

docker container stop zookeeper-standalone

docker container rm zookeeper-standalone
```

## Zk CLI

```bash
docker container exec -it zookeeper-standalone bash

bin/zkCli.sh
```

## Launch Kafka

```bash
mkdir -p ${HOME}/volumes/kafka
chmod -R ugo+rwx ${HOME}/volumes/kafka

KAFKA_HOME=${HOME}/volumes/kafka

# Docker
docker pull wurstmeister/kafka

docker container run -d \
-p 39092:9092 \
--name kafka-standalone \
--network sandbox \
-e KAFKA_ADVERTISED_HOST_NAME=LM0001680 \
-e KAFKA_ADVERTISED_PORT=39092 \
-e KAFKA_BROKER_ID=1 \
-e KAFKA_ZOOKEEPER_CONNECT=zookeeper-standalone:2181 \
-e ZK=zk \
-v kafka-volume:/kafka \
-v ${HOME}/volumes/kafka/samples:/opt/samples/ \
-t wurstmeister/kafka
```

## Housekeeping

```bash
docker container start kafka-standalone

docker container stop kafka-standalone

docker container rm kafka-standalone

docker container logs kafka-standalone -f 

docker container exec -it kafka-standalone bash
```