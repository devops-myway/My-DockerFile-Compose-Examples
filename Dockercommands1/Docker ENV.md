#####  Dockerfile ENV Variables
- You can use environment variables to send values to running containers. They are part of the environment in which a container runs: you will see how this works below.
- The ENV instruction sets the environment variable <key> to the value <value>.
- The environment variables set using ENV will persist when a container is run from the resulting image
- Both ARG and ENV seem very similar. Both can be accessed from within our Dockerfile commands in the same manner.
- 
``````sh
Syntax:

ENV
ENV =

ENV myVar1=1
ENV myVar42=42
ENV myAlfaVar=alfa value abc

ARG VAR_A 5
ENV VAR_B 6
RUN echo $VAR_A
RUN echo $VAR_B

ARG var
ENV var=${var}

docker build --build-arg var=xxx
docker run -e var=yyy

``````
##### How not to use ARG and ENV
One additional tripwire: ARG default values need duplicated definitions if you use them before and below the FROM clause. So instead of

``````sh
ARG var1=default1
FROM your_base:${var1}
ENV var1=${var1}  # <- this will fail!; the ARG var1 default is empty

## Soluton

ARG var1=default1
FROM your_base:${var1}
ARG var1=default1
ENV var1=${var1}  # <- this works

``````

##### Why to use ARG or ENV 
Let's say we have a jar file and we want to make a docker image of it. So, we can ship it to any docker engine.

``````sh
### Dockerfile
FROM eclipse-temurin:17-jdk-alpine
VOLUME /tmp
ARG JAR_FILE
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]

# Now, if we want to build the docker image using Maven, we can pass the JAR_FILE using the --build-arg as target/*.jar

docker build --build-arg JAR_FILE=target/*.jar -t myorg/myapp

``````
``````sh
#### However, if we are using Gradle; the above command doesn't work and we've to pass a different path: build/libs/

docker build --build-arg JAR_FILE=build/libs/*.jar -t myorg/myapp .


``````

#####  Example 2

``````sh
vi Dockerfile
FROM alpine:3.8
ENV myVar1 1
ENV my42 42
ENV myVar42=42
ENV myAlfaVar='alfa abc'

# Run the Build
docker build --tag tutorial:demo --file Dockerfile  .

# Run the container
docker stop -t 0 tutorial ;   docker container prune -f  
docker run -ti -d --name tutorial tutorial:demo /bin/sh -c 'while true; do sleep 60; done'

docker exec -it tutorial /bin/sh

/ # printenv

# Run
docker stop -t 0 tutorial ;   docker container prune -f  

docker run -e  'my42=44000' -ti -d --name tutorial tutorial:demo /bin/sh -c 'while true; do sleep 60; done'

# If you now enter the container you will see my42 is 44000.
docker exec -it tutorial /bin/sh

/ # printenv
``````
