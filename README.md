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