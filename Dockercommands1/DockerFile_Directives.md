#####  Dockerfile
A Dockerfile is a text file that contains instructions on how to create a Docker image. 
These commands are known as directives. A Dockerfile is a way of creating a custom Docker image based on our requirements.

Instructions within a Dockerfile are case-insensitive. Even though the DIRECTIVE is case-insensitive, 
it is considered a best practice to write all directives in uppercase to distinguish them from arguments.

#### From Directives
This is used to specify the parent image of our custom Docker image. The parent image can be an image from Docker Hub, such as Ubuntu, or Nginx.

``````sh
FROM <image>:<tag>

#FROM directive, uses the ubuntu parent image, with the 20.04 tag:
FROM ubuntu:20.04

``````
##### LABEL Directive
LABEL is a key-value pair that can be used to add metadata to a Docker image

``````sh
LABEL <key>=<value>

#Dockerfile can have multiple labels:
LABEL maintainer=somerandomguy@somerandomdomain.com
LABEL version=1.0
LABEL environment=dev
``````
#####  RUN Directive
The RUN directive is used to execute commands during the image build time.
The RUN directive can be used to install the required packages, create users and groups, and so on
``````sh
RUN <command>

RUN apt-get update
RUN apt-get upgrade
RUN apt-get install nginx -y

#add multiple shell commands to a single RUN directive by separating them with the && symbol
RUN apt-get update && apt-get upgrade && apt-get install nginx -y
``````
##### ENV Directive
The ENV directive in a Dockerfile can be used to set environment variables. Environment variables are used to set environment variables.
``````sh
ENV <key> <value>
ENV <key>=<value> <key=value> ...

ENV PATH $PATH:/usr/local/app/bin/
ENV PATH=$PATH:/usr/local/app/bin/ VERSION=1.0.0
``````
##### ARG Directive
ARG directive in a Dockerfile is used to define variables that users can pass at build time to the builder with the docker build command.
hese variables behave similarly to environment variables and can be used throughout the Dockerfile but are not persisted, 
in the final image unless explicitly declared using the ENV directive.

``````sh
ARG <varname>

ARG USER
ARG VERSION
ARG USER=TestUser
ARG VERSION=1.0.0
``````
##### Using ENV and ARG Directives in a Dockerfile
- ENVIRONMENT with a value of dev and APP_DIR pointing to /usr/local/app/bin.
- These variables can be used by applications running inside the container to adjust behavior based on the environment and directory paths.
- CMD directive specifies the command to run when a container is started from this image,
- in this case, it executes env to display all environment variables set within the container.

``````sh
ARG TAG=latest
FROM ubuntu:$TAG
LABEL maintainer=ananalogguyinadigitalworld@example.com
ENV ENVIRONMENT=dev APP_DIR=/usr/local/app/bin
CMD ["env"]

docker image build -t env-arg --build-arg TAG=23.10 .
docker container run env-arg
``````
##### WORKDIR Directive
WORKDIR directive in a Dockerfile is used to set the current working directory for any subsequent instructions that follow in the Dockerfile.
This directive helps to define where the commands such as ADD, CMD, COPY, ENTRYPOINT, and RUN, will be executed within the container.

- WORKDIR /one will set /one as the initial working directory.
- WORKINGDIR two will then change the directory to /one/two
- WORKDIR three further changes it to one/two/three
- Finally WORKDIR drink will change it to its final form one/two/three/drink

``````sh
WORKDIR /path/to/workdir

WORKDIR /one
WORKDIR two
WORKDIR three
WORKDIR drink
``````

##### USER Directive
- by default, containers run with the root user, which has extensive privileges within the container environment
- To mitigate security risks, Docker allows us to specify a non-root user using the USER directive in the Dockerfile
- Implementing the USER directive is considered a best practice in Docker security, aligning with the principle of least privilege.
- It ensures that containers operate with minimal privileges necessary for their functionality,
- thereby enhancing overall system security and reducing the attack surface.
``````sh
USER <user>
USER <user>:<group>

#It enhances security by switching to the www-data user, commonly used for web servers, to minimize potential vulnerabilities
FROM ubuntu:latest
RUN apt-get update && apt-get upgrade
RUN apt-get install apache2 -y
USER www-data
CMD ["whoami"]

docker build -t user .
docker container run user
``````
##### VOLUME Directive
- by default, any data generated or modified within a Docker container's filesystem is ephemeral, meaning it exists only for the duration of the container's runtime
- When a container is deleted or replaced, this data is lost, which poses challenges for applications that require persistent storage,
- such as databases or file storage systems
- To address this challenge, Docker introduced the concept of volumes.
- When you define a volume in a Dockerfile using the VOLUME directive, Docker creates a managed directory within the container’s filesystem
- This directory serves as the mount point for the volume
- Volumes in Docker support various types, including named volumes and host-mounted volumes
- Named volumes are created and managed by Docker, offering more control and flexibility over volume lifecycle and storage management
- Host-mounted volumes, on the other hand, allow you to directly mount a directory from the host filesystem into the container,
- providing straightforward access to host resources
- We can use the docker container inspect <container> command to view the volumes available in a container.
``````sh
VOLUME ["path/to/volume"]
VOLUME /path/to/volume1 /path/to/volume2

#The VOLUME ["/var/log/apache2"] directive defines a Docker volume at /var/log/apache2, which is where Apache typically stores its log files
FROM ubuntu:latest
RUN apt-get update && apt-get upgrade
RUN apt-get install apache2 -y
VOLUME ["/var/log/apache2"]

docker build -t volume .
docker container run --interactive --tty --name volume-container volume /bin/bash

root@8aa0f5fb8a6d:/# cd /var/log/apache2/
root@8aa0f5fb8a6d:/var/log/apache2# ls -l
root@8aa0f5fb8a6d:/var/log/apache2# exit

#Inspect the volume-container to view the mount information
#Under the Mounts key, you will be able to see the information relating to the mount
docker container inspect volume-container

docker volume inspect 50d3a5abf34535fbd3a347cbd6c74acf87a7aa533494360e661c73bbdf34b3e8


``````
##### EXPOSE Directive
EXPOSE directive in Docker serves to indicate to Docker that a container will be listening on specific ports during its runtime.
This declaration is primarily informative and does not actually publish the ports to the host system or make them accessible from outside the container by default.
- This directive is a precursor to the -p or -P options used during container runtime
- to actually map these exposed ports to ports on the host machine, enabling external access if required.

##### HEALTHCHECK Directive
- A health check is a crucial mechanism that designed to assess the operational health of containers
- Without a specified health check, Docker lacks the capability to autonomously determine the health status of a container
- The HEALTHCHECK directive in Docker allows developers to define custom health checks, typically in the form of commands or scripts,
- that periodically inspect the container's state and report back on its health.
- 
  HEALTHCHECK directive in Docker, it's possible to configure additional parameters :
  
- --interval: Specifies the frequency at which health checks are executed, with a default interval of 30 seconds.
- --timeout: Defines the maximum time allowed for a health check command to complete successfully.
- If no successful response is received within this duration, the health check is marked as failed. The default timeout is also set to 30 seconds.
- --start-period: Specifies the initial delay before Docker starts executing the first health check.
- This parameter allows the container some time to initialize before health checks begin, with a default start period of 0 seconds.
- --retries: Defines the number of consecutive failed health checks allowed before Docker considers the container as unhealthy.
- By default, Docker allows up to 3 retries.

``````sh
EXPOSE <port>

HEALTHCHECK CMD curl -f http://localhost/ || exit 1

HEALTHCHECK \
    --interval=1m \
    --timeout=2s \
    --start-period=2m \
    --retries=3 \
    CMD curl -f http://localhost/ || exit 1

``````
##### EXPOSE and HEALTHCHECK Directives in the Dockerfile
``````sh
FROM ubuntu:latest
RUN apt-get update && apt-get upgrade
RUN apt-get install apache2 curl -y
HEALTHCHECK CMD curl -f http://localhost/ || exit 1
EXPOSE 80
ENTRYPOINT ["apache2ctl", "-D", "FOREGROUND"]

docker image build -t expose-healthcheck-example .
docker container run -p 8080:80 --name expose-healthcheck-container -d expose-healthcheck-example
docker container list

# you should be able to view the Apache home page. Navigate to the http://127.0.0.1:8080 endpoint from your browser
``````
##### 
``````sh

``````
