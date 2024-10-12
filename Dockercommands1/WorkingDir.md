##### WORKDIR command in Docker
The WORKDIR command is used to define the working directory of a Docker container at any given time.
The command is specified in the Dockerfile. Any RUN, CMD, ADD, COPY, or ENTRYPOINT command will be executed in the specified working directory.

If the WORKDIR command is not written in the Dockerfile, it will automatically be created by the Docker compiler

``````sh
FROM ubuntu:16.04
WORKDIR /project   <--- If the project directory does not exist, it will be created.
RUN npm install   <--- The RUN command will be executed inside project.


``````
##### Reusing WORKDIR
WORKDIR can be reused to set a new working directory at any stage of the Dockerfile.

``````sh
FROM ubuntu:16.04
WORKDIR /project
RUN npm install

WORKDIR ../project2
RUN touch file1.cpp
``````
