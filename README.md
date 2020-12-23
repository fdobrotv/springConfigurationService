# Getting Started

## Requirements
Java 15

Gradle 6.6+

### Host name aliases in hosts file
cat `127.0.0.1 configuration-service-1.com` >> /etc/hosts

## Base URL
http://localhost:8888

## Build and run

### By docker-compose with discovery
1 - Run `docker-compose up` of https://github.com/fdobrotv/springDiscoveryService

2 - `docker-compose up`

### By gradle
`gradlew bootRun`

### Build and publish docker image into registry
`gradlew jib`

### TODO
Add Spring Cloud Bus
Add Hashicorp Vault

### Additional Links
https://medium.com/@kirill.sereda/spring-cloud-bus-wtf-f904079cca84
https://www.javainuse.com/spring/cloud-vault

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

