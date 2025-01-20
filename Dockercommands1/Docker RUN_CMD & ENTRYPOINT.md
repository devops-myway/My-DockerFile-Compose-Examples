##### NOTE: 
By the end, you'll be a Dockerfile ninja, wielding these instructions with confidence and precision.

##### 
- ENTRYPOINT — Allows to configure the container as an executable, it provides the file to run when the container is started from a docker image.
- ENTRYPOINT doesn’t allow you to override the command.
- Note: it is also possible to overwrite the ENTRYPOINT instruction with the “ — entrypoint” flag on the “docker run” command

- CMD — provides the defaults (argument/executable) for running a container.
- the CMD instruction is overwritten. So, it is literally the default configuration.
#### Example 1
- I chose a python-slim image as my base image because obviously I run python and we want our image to be as lightweight as possible.

``````sh
moving on to entrypoint.sh:
#!/bin/bash
args_array=("$@")
for i in "${args_array[@]}"
do
  :
  echo "### Got variable $i ###"
done
echo "args_count = $#"
eval "$@"

# Dockerfile
from python:3.8-slim
COPY --chown=user1 . /app/        #chown of the file entrypoint.sh to non-root user1
WORKDIR /app
RUN useradd -m user1 && \
    chown -R user1:user1 /app
    chmod u+x ./entrypoint.sh
USER user1
ENTRYPOINT [ "/app/entrypoint1.sh" ]
CMD [ "echo", "Default argument from CMD instruction" ]

# Build
docker build . -t args_example

``````
##### Example 2
- 
``````sh

# with this script in file.sh
#!/bin/bash
echo Your container args are: "$@"

# Dockerfile
FROM ubuntu:14.04
COPY ./file.sh /
ENTRYPOINT ["/file.sh"]

# Build
docker build -t test .
docker run test hello world
Your container args are: hello world

``````

#####  The CMD Directive
To define an executable with its arguments that the container should run. In this case, we can omit the ENTRYPOINT directive.
It's like a pre-set option for what your container should do when it "boots up."

##### The Exec Form of the CMD Directive
The exec form invokes only the defined executable, without a shell
The exec form is parsed as a JSON array, which means that you must use double-quotes (") around words not single-quotes (').
``````sh
CMD "executable","param1","param2"
CMD "param1","param2"
CMD ["executable", "param1", "param2"]

``````
##### The Shell Form of the CMD Directive
Unless we set a shell, like /bin/sh or /bin/bash, to be the executable, the container doesn’t start a shell. 
``````sh
CMD command param1 param2 (shell form)
/bin/sh -c executable param1 param2

``````
#####  Run Multiple Commands With Dockerfile
The sh -c command accepts a list of commands and executes them. Moreover, the commands can be separated with the known shell operators:

- the semicolon (;) operator
- the ambersand (&) operator
- the AND (&&) operator
- the OR (||) operator
- the pipe (|)

###### Run Multiple Commands With the Shell Form

``````sh
FROM ubuntu:latest
CMD echo FIRST COMMAND;echo SECOND COMMAND
---
sudo docker build -t example1 .
sudo docker run -it example1
FIRST COMMAND
SECOND COMMAND
``````
#####  Run Multiple Commands With the Exec Form
The exec form doesn’t start a shell on its own, we can set the /bin/sh -c or the /bin/bash -c commands in the CMD executable position, and invoke the shell

``````sh
FROM ubuntu:latest
CMD ["/bin/bash", "-c", "echo FIRST COMMAND;echo SECOND COMMAND"]
---
sudo docker build -t example2 .
sudo docker run -it example2
FIRST COMMAND
SECOND COMMAND
``````

#####  Run Multiple Commands With a Shell Script
we can create a shell script that contains all the necessary logic, and copy it to the container’s filesystem
We can use the Dockerfile COPY directive to copy the shell script file to our container’s filesystem.
``````sh
vi startup.sh

#! /bin/bash
echo "Current Date: $(date)"
echo "Home Directory: $HOME"
--- 
vi Dockerfile

FROM ubuntu:latest
WORKDIR /app
COPY --chown=user2 startup.sh .
RUN useradd -m user2 && \
    chown -R user2:user2 /app && \
    chmod u+x ./startup.sh
USER user2
CMD ["bash"]
----
docker buildx build -t test2:demo --file Dockerfile2 .
docker image ls
docker run -itd --name test2 4a83114fe0eb
docker ps

``````
#####  Example 3- Now CMD using the Exec form:

``````sh
vi Dockerfile

FROM alpine:3.8
CMD ["/bin/echo","hello from CMD - using exec form"]
--

docker build --tag tutorial:demo --file Dockerfile  .
docker run --name tutorial tutorial:demo

# expected Output
hello from CMD - using exec form
``````
#####  Now CMD using the shell form:

``````sh
FROM alpine:3.8
CMD /bin/echo "hello from CMD using shell form"

---
docker build --tag tutorial:demo --file Dockerfile  .
docker stop -t 0 tutorial ; docker container prune -f;docker ps -a
docker run --name tutorial tutorial:demo

---
# Expected Output
hello from CMD using shell form
``````
#####  

``````sh
FROM alpine:3.8
CMD /bin/echo $HOME

docker build --tag tutorial:demo --file Dockerfile  .
docker stop -t 0 tutorial
docker container prune -f
docker ps -a
docker run --name tutorial tutorial:demo

--
Expected Output
/root
``````
#####  CMD or ENTRYPOINT
These two come into play when you actually run a container from that image. when a container starts, Docker needs to know what it should do – 
what program to run, and how to run it. That's where CMD and ENTRYPOINT step in. They tell Docker what the main process inside the container is and how to start it.

- ENTRYPOINT: This is where you define the "command part" of the expression. It's the core thing you want your container to do when it starts.
- ENTRYPOINT doesn’t allow you to override the command.
- CMD: This is where you define the "parameters" for that command. They're the additional instructions you want to give it.

##### Example 1 
Dockerfile that uses Alpine Linux as the base image and wants to run the ping command could look like this:

``````sh
FROM alpine:latest

ENTRYPOINT ["ping"]
CMD ["-c", "10", "127.0.0.1"]

docker image build -t pinger .
docker container run --rm -it pinger

docker container run --rm -it pinger -w 5 127.0.0.1
``````


