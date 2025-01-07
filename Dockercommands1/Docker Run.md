#####  Dockerfile RUN
https://docs.docker.com/reference/dockerfile/#run

- The RUN instruction will execute its given commands and create the resulting output in a new layer on top of the current image.
- RUN is used to create probably the largest part of an image. It runs most of the commands you are used to use at the shell when setting up a server / vps.
- Using -it flag for docker run command

Typical example.
``````sh
RUN mkdir ...
RUN useradd ..
RUN apt install ...
RUN copy ...
RUN unzip ...

``````
#####  Dockerfile Run Example

``````sh
vi Dockerfile

FROM alpine:3.8
RUN echo $HOME
RUN ["echo", "$HOME"]

docker buildx build -t tutorial:demo --file Dockerfile .

``````
#####  How to run the Docker image
- The -it flag tells docker that it should open an interactive container instance.
- -it is short for --interactive + --tty. When you docker run with this command it takes you straight inside the container.
- -d is short for --detach, which means you just run the container and then detach from it. Essentially, you run container in the background.
- The --rm flag tells docker that the container should automatically be removed after we close docker.
- The -p flag specifies which port we want to make available for docker.
- The -v flag tells docker which folders should be mount to make them accessible inside the container. Here: /path/to/nipype_tutorial is your local directory where you downloaded Nipype Tutorial repository. /path/to/data/ is a directory where you have dataset ds000114, and /path/to/output can be an empty directory that will be used for output. The second part of the -v flag (here: /home/neuro/nipype_tutorial, /data or /output) specifies under which path the mounted folders can be found inside the container.
``````sh
docker pull miykael/nipype_tutorial:latest
docker images

docker run -it -d --rm -p 8888:8888 miykael/nipype_tutorial jupyter notebook
docker run -it --rm -v /path/to/nipype_tutorial/:/home/neuro/nipype_tutorial -v /path/to/data/:/data -v /path/to/output/:/output -p 8888:8888 miykael/nipype_tutorial jupyter notebook

``````

##### Running Container on shell form to keep session alive.
The below command starts the container in the interactive mode (hence -it flag) that allows you to interact with /bin/bash of the container. That means now you will have bash session inside the container, so you can ls, mkdir, or do any bash command inside the container.

The key here is the word "interactive". If you omit the flag, the container still executes /bin/bash but exits immediately. With the flag, the container executes /bin/bash then patiently waits for your input.
``````sh
docker run -it ubuntu:xenial /bin/bash

``````
