##### ADD and COPY

https://docs.docker.com/reference/dockerfile/#add

- The ADD: instruction is used to copy files, directories, or remote files from URL to your Docker images, from the 'src' to the absolute path 'dest'. Also, you can set up the default ownership of your file. ADD does Double Duty ... it adds and untars / unzips.

COPY does not automate untars tar files.

Best Practice: only use ADD when you need this double duty functionality.

- The ADD tarredfiles.tar /root in the Dockerfile automate untars tarredfiles.tar into /root/.

- The COPY more-tarredfiles.tar /root in the Dockerfile merely copies more-tarredfiles.tar into /root/.

``````sh
touch in-tar-file-1
touch in-tar-file-2
touch in-tar-file-3

tar -cvf tarredfiles.tar in-tar-*

touch more-in-tar-file-1 ; touch more-in-tar-file-2 ; touch more-in-tar-file-3 ;
tar -cvf more-tarredfiles.tar more-in-tar-*

vi Dockerfile

FROM alpine:3.8
ADD tarredfiles.tar /root
COPY more-tarredfiles.tar /root

docker build --tag tutorial:demo --file Dockerfile  .
docker stop -t 0 tutorial   ;   docker container prune -f   ;   docker ps -a

# Let's start up a container to see the result.
docker stop -t 0 tutorial ;   docker container prune -f  
docker run -ti -d --name tutorial tutorial:demo /bin/sh -c 'while true; do sleep 60; done'
docker exec -it tutorial /bin/sh

/ # ls /root

``````

##### Recursive Copy of dirs Using Dockerfile COPY
Purpose: demo recursive copy of dirs using Dockerfile COPY
 Since we used WORKDIR demo-work-dir the current dir is demo-work-dir

 Therefore the COPY will copy all files from demo-directories into demo-work-dir.
``````sh
yum install tree
mkdir -p demo-directories/dir-a/dir-a1/dir-a2

# Let's add some files into each of those dirs.
touch demo-directories/demo-file
touch demo-directories/dir-a/file-a
touch demo-directories/dir-a/dir-a1/file-a1
touch demo-directories/dir-a/dir-a1/dir-a2/file-a2

# Dockerfile
FROM alpine:3.8
WORKDIR demo-work-dir
COPY demo-directories/ .

# Run
docker build --tag tutorial:demo --file Dockerfile .

docker stop -t 0 tutorial ; docker container prune -f;docker ps -a
docker run -ti -d --name tutorial tutorial:demo /bin/sh -c 'while true; do sleep 60; done'

# Enter ls -R at the # shell prompt.
docker exec -it tutorial /bin/sh
/demo-work-dir # ls -R
``````
##### COPY Directive
 it's common to include files from our local development environment into the image itself.
 - The COPY directive in a Dockerfile serves this purpose by allowing us to specify which files or directories
 - from our local filesystem should be copied into the image being built.
 - The <source> specifies the path to the file or directory
 - The <destination> specifies the path where the file or directory should be copied within the Docker image filesystem.
``````sh
COPY <source> <destination>

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

``````sh
ADD <source> <destination>

ADD index.html /var/www/html/index.html
ADD http://example.com/test-data.csv /tmp/test-data.csv
ADD myapp.tar.gz /opt/myapp/

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
