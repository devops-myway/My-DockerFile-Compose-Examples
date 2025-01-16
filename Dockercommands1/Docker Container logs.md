#####   Docker Container logs
- If are running your container in -d (detached mode) or by docker remote API, you will not see any logs in the console.
- To access the logs of the running container you need to use the docker logs command.
``````sh
Syntax:
docker container logs ContainerName/ContainerID

docker container ls

docker container logs bc
``````
#####  -f --follow Follow log output ( if you want to see logs continuously)

``````sh
docker container logs -f bc
``````
