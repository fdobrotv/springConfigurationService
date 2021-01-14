# Getting Started

## Requirements
Java 15

Gradle 6.6+

### Host name aliases in hosts file
cat `127.0.0.1 configuration-service-1.com` >> /etc/hosts

## Base URL
http://localhost:8888

## Build and run

If you start without `kubernetes` spring profile, eureka discovery client will be used, otherwise kubernetes.
Obviously you can not use both discovery client implementation together

### By docker-compose with discovery
1 - Run `docker-compose up` of https://github.com/fdobrotv/springDiscoveryService

2 - `docker-compose up`

### By gradle
`gradlew bootRun`

### Build and publish docker image into registry
`gradlew jib`

### Spring cloud bus

#### Setup
Add a webhook in configuration of your CSM

#### Readiness test

##### Config server side

###### Via config monitor
WebHook examples:
With GitHub
`curl -v "http://configuration-service-1.com:8888/monitor" -H "X-Github-Event: push" -H "Content-Type: application/json" -d @example\webHookBodyGitHub.json`

With any other, using path parameters
`curl -v "http://configuration-service-1.com:8888/monitor?path=client-service.yml" -d {}`

Successful response with HTTP code 200
`["client:service","client-service"]`

Look at Kafka topics
`docker exec -ti message-broker-service bash`

`/opt/kafka/bin/kafka-topics.sh --list --zookeeper zookeeper-service:2181`

Must show the topic
`springCloudBus`

Read the broker messages
`/opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server message-broker-service:9092 --topic springCloudBus --from-beginning`

Must show in springCloudBus topic
From configuration service
`{
"type":"RefreshRemoteApplicationEvent",
"timestamp":1610437785284,
"originService":"configuration-service:git,vault:b7b8d9d3-0431-45aa-a79c-5813438944a6",
"destinationService":"client-service:**",
"id":"a68ddbce-b81d-41d0-a329-7d2a287a456e"
}`

From client-service microservice
`{
"type":"AckRemoteApplicationEvent",
"timestamp":1610523146538,
"originService":"client-service:${spring.profiles.active}:6f21f748-5310-431a-a29f-d43600ee6b6f",
"destinationService":"**",
"id":"2a045f64-1a0c-4f76-a96a-6c4e627b544f",
"ackId":"d673e8e9-1229-486a-ad52-fbec422d37df",
"ackDestinationService":"client-service:**",
"event":"org.springframework.cloud.bus.event.RefreshRemoteApplicationEvent"
}`

###### Config client side, will propagate to other services.
This should be used by hands, only if a webHook endpoint is not accessible by event publisher
`curl -v "http://client-service-1.com:8080/actuator/bus-refresh" -H "Content-Type: application/json" -d {}`

###### Via Actuator without cloud bus! Will refresh configuration without propagation.
`curl -v "http://client-service-1.com:8080/actuator/refresh" -H "Content-Type: application/json" -d {}`

### TODO
Add Hashicorp Vault

### Additional Links
https://medium.com/@kirill.sereda/spring-cloud-bus-wtf-f904079cca84
https://www.javainuse.com/spring/cloud-vault
https://pamesh12.github.io/spring-cloud/spring-cloud-bus.html
https://soshace.com/spring-cloud-config-refresh-strategies

### Spring Initializr recipe
https://start.spring.io/#!type=gradle-project&language=java&platformVersion=2.4.0.RELEASE&packaging=jar&jvmVersion=15&groupId=com.fdobrotv&artifactId=configuration_service&name=configuration_service&description=Base%20configuration%20service&packageName=com.fdobrotv.configurationService&dependencies=cloud-config-server

###Vault instructions
docker pull vault
docker run --cap-add=IPC_LOCK \
-e 'VAULT_DEV_ROOT_TOKEN_ID=ffa6f30b-041f-445a-9beb-3b42927fab1a' \
-p 8200:8200 \
-v /tmp/vault/logs \
--name my-vault vault
docker exec -it my-vault /bin/sh
export VAULT_TOKEN="ffa6f30b-041f-445a-9beb-3b42927fab1a"
export VAULT_ADDR="http://127.0.0.1:8200"
vault kv put secret/client-service db.password=qwerty