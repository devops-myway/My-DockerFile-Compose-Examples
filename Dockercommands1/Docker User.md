##### USER Directive
- By default, a Docker Container runs as a Root user. This poses a great security threat if you deploy your applications on a large scale inside Docker Containers.
- You can change or switch to a non-root user inside a Docker Container using the USER Instruction or directive.
- you first need to create a non-root user and a group inside the Container.

``````sh
# Create the Dockerfile

FROM ubuntu:latest
RUN apt-get -y update
RUN groupadd -g 1500 dev_users && useradd -g dev_users -u 1500 user1
USER user1

docker buildx build -t user-demo --file .
docker run -it user-demo bash
id

``````
##### Example of User Directive


``````sh
# Dockerfile
FROM opensuse/tumbleweed:latest
RUN zypper in -y elinks libmozjs-102-0
RUN useradd elinks
USER elinks
WORKDIR /home/elinks
RUN mkdir ./.elinks
COPY --chown=elinks elinks.conf ./.elinks/
ENTRYPOINT ["/usr/bin/elinks"]
CMD elinks


``````
