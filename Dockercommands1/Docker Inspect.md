##### Docker Inspect
- docker inspect is a command that low-level information on Docker objects like docker images, containers, networks, volumes, etc.
- By default, docker inspect will return results in a JSON array  
- What are those? If we simply run the Docker command, we can see the available types under “Management commands” in the output.
- For example, config, container, context, image, manifest, network, node, plugin, secret, service, stack, and volume are all types
- We can get the IDs of these objects in the ls listing and pass them to the Docker inspect command to get more information
``````sh
docker docker_object inspect [OPTIONS] NAME|ID [NAME|ID...]
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
docker <type> ls

# inspect mysql image
docker inspect mysql
#Let’s say we wanted the base image, the volume mounts, and the command run on the container initialization
docker inspect mysql -f "{{.RepoTags}},  {{.Config.Volumes}}, {{.Config.Cmd}}"

#let’s inspect the ports under NetworkSettings, which are available only for a running container.
docker inspect --format='{{.NetworkSettings.Ports}}' <container-id>

#the other form of the inspect as an action of the container command
docker inspect <container-id>
docker container inspect <container-id>

docker inspect --format='{{ if and (index . "Image") (index . "State")}} Its a container {{else}} Its not a container{{end}}' <object-id>
``````
##### Example 2
``````sh
docker container ls
# To inspect container
docker container inspect ad

#To get only specific info about the container, you can run command from CLI passing the --format argument
docker inspect --format='{{.LogPath}}' \$INSTANCE_ID

``````
##### End
``````sh
#The -q option tells the command to print only the container IDs
docker ps -q

``````
