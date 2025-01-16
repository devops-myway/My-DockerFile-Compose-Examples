##### Docker: start container
- Downloading an image and run the container
- Start a stopped Docker container with docker start
``````sh
docker run hello-world
docker container start mywebserver
docker ps -a

``````
##### Docker: stop container
- The simplest way to stop a running container is to use the docker container stop command followed by the container name:
- Exiting containers immediately using the docker kill command
-  Stopping and removing a container with the docker rm command
-  Stopping all running containers, The -q (quiet) flag displays only the numeric ID, hiding all the other details
``````sh
docker container stop mywebserver
docker kill mywebserver
docker rm -f mywebserver

docker ps -q
docker stop $(docker ps -q)
``````
##### Docker: list containers with filters
-  Filters allow us to list containers matching the exact criteria we are looking for and give more options than using default docker flags.
``````sh
docker ps --filter "FILTER_KEY=FILTER_VALUE"

docker ps -a --filter "status=exited"
docker ps -a --filter "publish=8080/tcp"
docker ps -a --filter "expose=80/tcp"
docker ps -a --filter "name=dev"

# Combining filters with start/stop
docker start $(docker ps -aq --filter "FILTER_KEY=FILTER_VALUE")
docker stop $(docker ps -aq --filter "FILTER_KEY=FILTER_VALUE")
``````
