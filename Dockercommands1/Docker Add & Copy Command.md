##### ADD and COPY

https://docs.docker.com/reference/dockerfile/#add

##### COPY Directive
 it's common to include files from our local development environment into the image itself.
 - The COPY directive in a Dockerfile serves this purpose by allowing us to specify which files or directories
 - from our local filesystem should be copied into the image being built.
 - The <source> specifies the path to the file or directory
 - The <destination> specifies the path where the file or directory should be copied within the Docker image filesystem.
``````sh
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
COPY <local_directory_source> <destination_image_fsys>

COPY index.html /var/www/html/index.html
COPY *.html /var/www/html/

# When using the COPY directive in a Dockerfile to transfer files from the local filesystem into a Docker image, we can also specify the --chown flag
COPY --chown=myuser:mygroup *.html /var/www/html/
``````
##### ADD Directive
ADD directive in Dockerfiles functions similar to the COPY directive but with additional features
- The <source> specifies a path or URL to the file or directory on the local filesystem or a remote URL
- The <destination> again specifies the path where the file or directory should be copied within the Docker image filesystem.
- ADD directive allows specifying a URL (in this case http://example.com/test-data.csv) as the <source> parameter
- <source> is a compressed archive file (e.g., .tar, .tar.gz, .tgz, .bz2, .tbz2, .txz, .zip), 
- Docker will automatically extract its contents into <destination> within the Docker image filesystem.
##### Add Best Practice
- ADD has some features (like local-only tar extraction and remote URL support) that are not immediately obvious.
- Consequently, the best use for ADD is local tar file auto-extraction into the image, as in ADD rootfs.tar.xz /.


``````sh
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
ADD <source_local_directory> <destination_image_file_system>

ADD index.html /var/www/html/index.html,                  #use copy directive un such cases instead: COPY index.html /var/www/html/index.html
ADD http://example.com/test-data.csv /tmp/test-data.csv  #use Add directive for remote url as advise use Curl
ADD myapp.tar.gz /opt/myapp/                           #use Add directive for zip files

``````
``````sh
ADD http://example.com/big.tar.xz /usr/src/things/
RUN tar -xJf /usr/src/things/big.tar.xz -C /usr/src/things
RUN make -C /usr/src/things all

# And instead, do something like:

RUN mkdir -p /usr/src/things \
    && curl -SL http://example.com/big.tar.xz \
    | tar -xJC /usr/src/things \
    && make -C /usr/src/things all

# Another Example usage of Add and Curl

ENV TINI_VERSION=0.10.0
RUN curl -fsSL "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini" -o /tini \
 && curl -fsSL "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini.asc" -o /tini.asc \
 && export GNUPGHOME="$(mktemp -d)" \
 && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys 0527A9B7 \
 && gpg --batch --verify /tini.asc /tini \
 && rm -rf "$GNUPGHOME" /tini.asc \
 && chmod +x /tini
``````
##### COPY vs ADD in Dockerfiles
- Docker encourages building lightweight, efficient, and predictable containerized applications.
- COPY aligns well with this philosophy by promoting simplicity and reducing the risk of unintended side effects during image builds.

##### WORKDIR, COPY, and ADD Directives in a Dockerfile
-The WORKDIR /var/www/html/ directive sets the working directory to /var/www/html/, a common location for serving web content in Apache.
- Within this directory, COPY index.html . copies a local index.html file from the host machine into the container.
- ADD https://upload.wikimedia.org/wikipedia/commons/4/4e/Docker_%28container_engine%29_logo.svg ./logo.png retrieves an SVG image file from a URL and
- saves it locally as logo.png in the same directory.
``````sh
FROM ubuntu:latest
RUN apt-get update && apt-get upgrade
RUN apt-get install apache2 -y
WORKDIR /var/www/html/
COPY index.html .
ADD https://upload.wikimedia.org/wikipedia/commons/4/4e/Docker_%28container_engine%29_logo.svg ./logo.png
CMD ["ls"]
``````
##### Copy2
``````sh
# point 1: copy the directory testproject and its content to a directory named project inside the tmp directory in a container.
ls testproject/
index.html  test.html  test.php

# Dockerfile
FROM ubuntu:14.04
LABEL name="learning-ocean"
LABEL email="[email protected]"
ENV NAME Gaurav
ENV PASS password
RUN pwd > /tmp/1stpwd.txt
RUN cd /tmp
RUN pwd>/tmp/2ndpwd.txt
WORKDIR /tmp
RUN pwd>/tmp/3rdpwd.txt
RUN apt-get update && apt-get install -y openssh-server && apt-get install -y python
RUN useradd -d /home/gaurav -g root -G sudo -m -p $(echo "$PASS" | openssl passwd -1 -stdin) $NAME
RUN whoami>/tmp/1stwhoami.txt
USER $NAME
RUN whoami>/tmp/2ndwhoami.txt
RUN mkdir -p /tmp/project
COPY testproject /tmp/project

# Build and container instance
docker image build -t myubuntu:81 .
docker container run -it myubuntu:81
# output
Gaurav@f46db1563db6:/tmp$ ls
1stpwd.txt  1stwhoami.txt  2ndpwd.txt  2ndwhoami.txt  3rdpwd.txt  project
Gaurav@f46db1563db6:/tmp$ cd project/
Gaurav@f46db1563db6:/tmp/project$ ls
index.html  test.html  test.php
Gaurav@f46db1563db6:/tmp/project$


``````
##### Add2 Difference with COPY
- The main difference between the ADD and COPY instructions is that if the source file is in a recognized compression format such as identity, gzip, bzip2 or xz, then
- ADD instruction will unpack it as a directory and copy it to the destination while COPY instruction will copy the compressed file as it is without any unpacking.
``````sh
FROM ubuntu:14.04
LABEL name="learning-ocean"
LABEL email="[email protected]"
ENV NAME Gaurav
ENV PASS password
RUN pwd > /tmp/1stpwd.txt
RUN cd /tmp
RUN pwd>/tmp/2ndpwd.txt
WORKDIR /tmp
RUN pwd>/tmp/3rdpwd.txt
RUN apt-get update && apt-get install -y openssh-server && apt-get install -y python
RUN useradd -d /home/gaurav -g root -G sudo -m -p $(echo "$PASS" | openssl passwd -1 -stdin) $NAME
RUN whoami>/tmp/1stwhoami.txt
USER $NAME
RUN whoami>/tmp/2ndwhoami.txt
RUN mkdir -p /tmp/project
COPY testproject.tar.gz /tmp/project

# COPY used as TAR and copy will only copy the tar file without untar
docker image build -t myubuntu:81 .

gaurav@learning-ocean:~/dockerfiles$ docker container run -it myubuntu:81
Gaurav@5d417b034c06:/tmp$ ls
1stpwd.txt  1stwhoami.txt  2ndpwd.txt  2ndwhoami.txt  3rdpwd.txt  project
Gaurav@5d417b034c06:/tmp$ cd project/
Gaurav@5d417b034c06:/tmp/project$ ls
testproject.tar.gz
Gaurav@5d417b034c06:/tmp/project$

``````
``````sh
FROM ubuntu:14.04
LABEL name="learning-ocean"
LABEL email="[email protected]"
ENV NAME Gaurav
ENV PASS password
RUN pwd > /tmp/1stpwd.txt
RUN cd /tmp
RUN pwd>/tmp/2ndpwd.txt
WORKDIR /tmp
RUN pwd>/tmp/3rdpwd.txt
RUN apt-get update && apt-get install -y openssh-server && apt-get install -y python
RUN useradd -d /home/gaurav -g root -G sudo -m -p $(echo "$PASS" | openssl passwd -1 -stdin) $NAME
RUN whoami>/tmp/1stwhoami.txt
USER $NAME
RUN whoami>/tmp/2ndwhoami.txt
RUN mkdir -p /tmp/project
ADD testproject.tar.gz /tmp/project

# Build
 docker image build -t myubuntu:81 .
docker container run -it myubuntu:81
#output
Gaurav@55888cd83dd5:/tmp$ ls
1stpwd.txt  1stwhoami.txt  2ndpwd.txt  2ndwhoami.txt  3rdpwd.txt  project
Gaurav@55888cd83dd5:/tmp$ cd project/
Gaurav@55888cd83dd5:/tmp/project$ ls
testproject
Gaurav@55888cd83dd5:/tmp/project$ cd testproject/
Gaurav@55888cd83dd5:/tmp/project/testproject$ ls
index.html  test.html  test.php
Gaurav@55888cd83dd5:/tmp/project/testproject$
``````
