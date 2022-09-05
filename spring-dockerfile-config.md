# Spring Docker file config

1. Layered Approach

```Dockerfile
# syntax=docker/dockerfile:1
FROM openjdk:11-jre-slim as builder
WORKDIR application
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} application.jar
RUN java -Djarmode=layertools -jar application.jar extract


FROM openjdk:11-jre-slim
WORKDIR application

COPY --from=builder application/dependencies/ ./
COPY --from=builder application/spring-boot-loader/ ./
COPY --from=builder application/snapshot-dependencies/ ./
COPY --from=builder application/application/ ./

ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
# Extermely generic yet good way
# copy paste and you should be good to go
```

2. Yet Another way to build everything inside the container and still keep it layered

```Dockerfile
# syntax=docker/dockerfile:1
FROM maven:3-jdk-11 as builder

ENV HOME=/usr/app

RUN mkdir -p $HOME

WORKDIR $HOME

ADD pom.xml $HOME
# get dependencies
RUN mvn verify --fail-never

ADD . $HOME
# create the build, no tests
RUN mvn package -Dmaven.test.skip=true


COPY target/your-app-jar-name.jar application.jar
RUN java -Djarmode=layertools -jar application.jar extract


FROM adoptopenjdk:11-jre-hotspot

ENV HOME=/usr/app

COPY --from=builder $HOME/dependencies/ ./
COPY --from=builder $HOME/snapshot-dependencies/ ./
COPY --from=builder $HOME/spring-boot-loader/ ./
COPY --from=builder $HOME/application/ ./

ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

3. Non Layered Approach

```shell
mvn clean install
```
then
```Dockerfile
FROM openjdk:11

ENV HOME=/usr/app

COPY --from=build $HOME/target/your-app-jar-name.jar application.jar

ENTRYPOINT ["sh","-c","java -jar -Dspring.profiles.active=prod application.jar"]
```
