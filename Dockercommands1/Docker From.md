#####  Dockerfile FROM Instruction
The Dockerfile FROM instruction specifies the base image / Linux distro we want to use to build a container.
Every Dockerfile must have a FROM instruction.

``````sh
docker pull alpine:3.8

FROM alpine:3.8
docker image remove alpine:3.8

#run
docker build --file Dockerfile .
``````
#####   Dokcerfile 2

``````sh
FROM ubuntu
RUN apt-get update
RUN apt-get install -y nginx
COPY index.nginx-debian.html /var/www/html
CMD nginx -g 'daemon off;'
``````

##### Simple Dockerfile 3
``````sh
FROM python:3.9.5-buster
ADD . /code                     <--- import the code from source local directory into container image destination
WORKDIR /code                   <---- destination directory inside the container image
RUN pip install flask
EXPOSE 5001
CMD python main.py
``````
