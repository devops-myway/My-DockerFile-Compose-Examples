##### SSH into a Docker Container – Secure Shell vs Docker Attach
##### Attach Command
you can gain access to the primary process running in the container. If this process is interactive, such as a Bash shell session, you will be able to execute commands directly through a docker attach session.

However, if the primary process in the container terminates, so will the entire container instance, since the Docker container lifecycle is dependent on the running state of the primary process.

By default, docker provides the Ctrl+P and Ctrl+Q key sequence to gracefully detach from an attach session.

``````sh
#By default, the primary process of this container is /bin/bash.

docker run -itd --name ubuntu-attach ubuntu:latest
docker ps            # Take not that the primary process of the container is a Bash shell (/bin/bash)

docker attach ubuntu-attach       #Run the docker attach command to attach to the primary process inside the container
root@cf16890cbe41:/#             # This should drop you into the primary Bash shell session of this container image. successfully accessed the container instance:

root@cf16890cbe41:/# read escape sequence     #Upon successful detachment of the container, the words read escape sequence
docker ps

docker attach cf16890cbe41
root@cf16890cbe41:/# exit

docker ps
docker ps -a
docker system prune -fa

``````
#####  Attach to a Running Container using Docker Exec Command
The most common and helpful command for getting a shell in a container is "docker exec -it".

You can exit the current shell by pressing control + d or typing exit.
``````sh
# start a container
$ docker run --name nginx --rm -p 8080:80 -d nginx

# create and connect to a bash shell in the container
$ docker exec -it nginx bash

root@a84ad71521b1:/#
``````
##### If Container is not bash installed by Default
It can happen that your container does not have bash installed. In case you can not start bash, you can try starting a sh-shell:

``````sh
docker exec -it nginx sh

``````
