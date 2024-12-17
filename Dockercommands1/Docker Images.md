##### Building Docker image
- A Docker image is like a compiled file. It's built in layers, starting with the base layer that contains the fundamental setup.
- This image build process is initialized by the Docker CLI and executed by the Docker daemon. 
- To generate a Docker image, the Docker daemon needs access to the Dockerfile, any source code, and files referenced inside that Dockerfile.
- These files are stored in a directory known as the build context.
- This context directory needs to be specified while executing the "docker image build" command.

``````sh
docker image build <context>

#Let's create a simple Dockerfile
FROM ubuntu:latest
LABEL maintainer="ananalogguyinadigitalworld@example.com"
CMD ["echo", "Hello World"]

#To specify the current directory as the context, we can use the dot (.) as a directory:
docker image build .
docker image list

#tag our image with IMAGE ID f14484d3185f as my-tagged-image:v1.0:
docker image tag f14484d3185f my-tagged-image:v1.0
docker image build -t my-tagged-image:v2.0 .
``````
##### Docker Layers and Caching
- A registry is a way to store and distribute Docker images. When you pull a Docker image from a registry,
- you might notice that the image is pulled in pieces and not as a single image. The same thing happens when you build an image on your system.
- These layers are organized on top of a base image, capturing every change to the filesystem that occurs with each instruction in the Dockerfile.
- When you instantiate an image as a container, Docker adds a writable layer on top of the existing read-only layers
- This writable layer, often referred to as the container layer, allows the container to modify and persist changes during
- runtime without affecting the underlying image.
- It's important to note, that as you setup your build environment and progress in development,
- the number of layers in the Docker image grows. More layers mean larger image sizes, which can lead to longer build times
- In a Dockerfile, each instruction contributes to the creation of layers in the image.
- Layers are created explicitly when commands like RUN, ADD and COPY are executed
- commands like FROM, ENV, WORKDIR and CMD do not directly create filesystem changes.
- Instead, they modify the environment or configure settings within the image without altering the filesystem itself
- When building our Docker images, we can use the "docker history" command and the image name or ID to see the layers used to create the image.
- The "docker image inspect" command is useful in providing further details on where the layers of our images are located
``````sh
docker history <image_name|image_id>
docker image inspect <image_id>

``````
##### Working with Docker Image Layers
``````sh
FROM alpine

RUN apk update
RUN apk add wget
RUN apk add curl

docker build -t basic-example .
docker history basic-example
docker build -t basic-example .

docker images
docker image inspect 2fc965ee555a
docker images -a
docker image prune
``````
##### Increasing Build Speed and Reducing Layers
- We've been working with small projects up until now. As our apps get bigger and more complex, though, we'll want to start thinking 
- about the size and number of layers in our Docker images, along with how quickly we're building them
- In this example, we'll focus on speeding up build times, shrinking those image sizes, and using the "--cache-from" option to make things even faster.
``````sh
#let's clean up any existing images on your system, using "docker rmi -f $(docker images -a -q)" command
# Dockerfile
FROM alpine

RUN apk update
RUN apk add wget curl
RUN wget -O randomdata.txt https://github.com/Kalkwst/Docker-Workshop-Repository/blob/master/Dockerfiles/create-base-image2/random_data.txt

CMD mkdir /var/www/
CMD mkdir /var/www/html/
WORKDIR /var/www/html/
COPY Dockerfile.tar.gz /tmp/

RUN tar -zxvf /tmp/Dockerfile.tar.gz -C /var/www/html
RUN rm /tmp/Dockerfile.tar.gz
RUN cat Dockerfile

# Download the alipine base image
docker pull alpine
# Create a TAR file to be added to our image
tar zcvf Dockerfile.tar.gz Dockerfile

# use the time command at the start of the command to allow us to gauge the time it takes to build the image
time docker build -t basic-server .
# Run the docker history command over the new basic-app image
docker history basic-server


``````
##### We can slim down our image by merging some of the commands in the Dockerfile we created earlier.
- Combine the RUN commands from lines 3 and 4, and then merge the CMD commands from lines 8 and 9
- We can further optimize our Dockerfile by replacing lines 11, 12, and 13 with a single ADD command.
- You might have noticed that a significant portion of the build time is spent running apk update, installing wget and curl,
- and fetching content from websites (as seen in lines 3 and 5 of our Dockerfile)

``````sh
FROM alpine

RUN apk update && apk add wget curl
RUN wget -O randomdata.txt https://github.com/Kalkwst/Docker-Workshop-Repository/blob/master/Dockerfiles/create-base-image2/random_data.txt
CMD mkdir -p /var/www/html/
WORKDIR /var/www/html/
COPY Dockerfile.tar.gz /tmp/
RUN tar -zxvf /tmp/Dockerfile.tar.gz -C /var/www/hmtl/
RUN rm /tmp/Dockerfile.tar.gz
RUN cat Dockerfile

# Slim don version

FROM alpine
RUN apk update && apk add wget curl
RUN wget -O randomdata.txt https://github.com/Kalkwst/Docker-Workshop-Repository/blob/master/Dockerfiles/create-base-image2/random_data.txt
CMD mkdir -p /var/www/html/
WORKDIR /var/www/html/
ADD Dockerfile.tar.gz -C /var/www/hmtl/
RUN rm /tmp/Dockerfile.tar.gz

RUN cat Dockerfile

``````
##### Further optimization using --no-cache option
- While this isn't a major issue for a few builds, it can become a bottleneck when creating multiple images
- To address this, we can create a base image that already includes these tools and dependencies
- By using this base image as our starting point, we can eliminate these lines from our Dockerfile altogether, further improving build times and image efficiency.
- Runs the apk commands: It updates the package index and installs wget and curl using apk add --no-cache.
- The --no-cache option prevents apk from storing the downloaded packages in the local cache, keeping the image smaller.
- 
``````sh
mkdir base-image
cd base-image

FROM alpine
RUN apk update && apk add --no-cache wget curl
RUN wget -O randomdata.txt https://github.com/Kalkwst/Docker-Workshop-Repository/blob/master/Dockerfiles/create-base-image2/random_data.txt

docker build basic-base .
cd ../<original_project_directory>

# open your Dockerfile
FROM basic-base

CMD mkdir -p /var/www/html/
WORKDIR /var/www/html/
ADD Dockerfile.tar.gz /var/www/html/
RUN cat Dockerfile

time docker build -t basic-server .
``````
``````sh

``````
