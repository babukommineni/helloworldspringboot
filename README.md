# helloworldspringboot
Helloworld example using springboot rest api




Steps:
-------------

Execute without docker  

1)    ./gradlew build && java -jar build/libs/gs-spring-boot-docker-0.1.0.jar

Execute using Docker

1) create src/main/docker/Dockerfile

FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD gs-spring-boot-docker-0.1.0.jar app.jar
RUN sh -c 'touch /app.jar'
ENV JAVA_OPTS=""
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]

2)  Build a Docker Image with Gradle

build.gradle

buildscript {
    ...
    dependencies {
        ...
        classpath('se.transmode.gradle:gradle-docker:1.2')
    }
}

group = 'springio'

...
apply plugin: 'docker'

task buildDocker(type: Docker, dependsOn: build) {
  push = true
  applicationName = jar.baseName
  dockerfile = file('src/main/docker/Dockerfile')
  doFirst {
    copy {
      from jar
      into stageDir
    }
  }
}

3)  build tagged docker using gradle

$ ./gradlew build buildDocker

4) When it is running you can see in the list of containers, e.g:

$ docker ps
CONTAINER ID        IMAGE                             COMMAND                CREATED             STATUS              PORTS                    NAMES
81c723d22865        springio/gs-spring-boot-docker:latest   "java -jar /app.jar"   34 seconds ago      Up 33 seconds       0.0.0.0:8080->8080/tcp   goofy_brown


