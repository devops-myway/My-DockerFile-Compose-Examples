##### Dockerfile VOLUME
https://docs.docker.com/reference/dockerfile/#volume
- Docker volumes are a way to persist and share data between Docker containers and the host machine
- Volumes are not a good choice if you need to access the files from the host, as the volume is completely managed by Docker.
- Use bind mounts if you need to access files or directories from both containers and the host
- Volumes are especially useful when you need to preserve data even if the container is removed or replaced.
- Docker volume is simply a directory that shares data among container to container, container to host, host to container.
-  volumes are stored in a single location (most likely /var/lib/docker/volumes/ on unix systems) and greatly facilitates managing data (backup, restore, and migration)
-  Docker enables you to manage volume with the command line docker volume, making their management simple

##### Bind mounts
- When you use a bind mount, a file or directory on the host machine is mounted from the host into a container
- when you use a volume, a new directory is created within Docker's storage directory on the host machine, and Docker manages that directory's contents.
- Volumes are not a good choice if you need to access the files from the host, as the volume is completely managed by Docker.
- Use bind mounts if you need to access files or directories from both containers and the host
- Option	                                            Description for mount
- source, src	:                 The location of the file or directory on the host. This can be an absolute or relative path.
- destination, dst, target	:   The path where the file or directory is mounted in the container. Must be an absolute path.
- readonly, ro	:               If present, causes the bind mount to be mounted into the container as read-only.
- bind-propagation: 	          If present, changes the bind propagation

##### When to use bind mounts
- Sharing source code or build artifacts between a development environment on the Docker host and a container.
- When you want to create or generate files in a container and persist the files onto the host's filesystem.
- This is how Docker provides DNS resolution to containers by default, by mounting /etc/resolv.conf from the host machine into each container.
- If you bind mount file or directory into a directory in the container in which files or directories exist, the pre-existing files are obscured by the mount.
- Bind mounts have write access to files on the host by default.
- One side effect of using bind mounts is that you can change the host filesystem via processes running in a container, including creating, modifying, or deleting important system files or directories

``````sh
docker run --mount type=bind,src=<host-path>,dst=<container-path>
docker run --volume <host-path>:<container-path>
``````
- --mount: does not automatically create a directory if the specified mount path does not exist on the host. Instead, it produces an error:
- --volume: to bind-mount a file or directory that does not yet exist on the Docker host, Docker automatically creates the directory on the host for you. It's always created as a directory
- In general, --mount is preferred
``````sh
docker run --mount type=bind,src=/dev/noexist,dst=/mnt/foo alpine
docker: Error response from daemon: invalid mount config for type "bind": bind source path does not exist: /dev/noexist.
``````
##### Start a container with a bind mount
- a directory source and that when you build the source code, the artifacts are saved into another directory, source/target/
- Use the following command to bind-mount the target/ directory into your container at /app/
``````sh
docker run -d \
  -it \
  --name devtest \
  --mount type=bind,source="$(pwd)"/target,target=/app \
  nginx:latest

docker inspect devtest
docker container rm -fv devtest
``````
##### Use a read-only bind mount
- For some development applications, the container needs to write into the bind mount, so changes are propagated back to the Docker host.
-  but mounts the directory as a read-only bind mount, by adding ro to the (empty by default) list of options, after the mount point within the container.
``````sh
docker run -d \
  -it \
  --name devtest \
  --mount type=bind,source="$(pwd)"/target,target=/app,readonly \
  nginx:latest

docker inspect devtest
docker container rm -fv devtest
``````
##### Use a bind mount with Docker Compose
- A single Docker Compose service with a bind mount looks like this
``````sh
services:
  frontend:
    image: node:lts
    volumes:
      - type: bind
        source: ./static
        target: /opt/app/static
volumes:
  myapp:
``````

##### Creating and Managing Docker Volumes
- Create a Docker Volume Implicitly: The easiest way to create and use a volume is with "docker run" and the -v or --volume flag.
-  If the “source” is a name, then Docker tries to find this volume or creates one if one cannot be found
``````sh
docker run -v <host-path>:<container-path>[:opts]

docker run -it --rm --name nginx -p 8080:80 -v demo-earthly:/usr/share/nginx/html nginx
docker volume ls
docker volume inspect demo-earthly

ls /var/lib/docker/volumes/target/_data/demo-earthly
docker run -it --rm -v demo-earthly:/opt/demo-earthly ubuntu ls /opt/demo-earthly
``````
##### Create a Docker Volume Explicitly
- you can use the docker volume create command to explicitly create a data volume.
- This command gives you the option to choose and configure the volume driver. The implicit creation of volumes always uses the local driver with default settings.
``````sh
docker volume --help
docker volume create my-vol
docker volume ls
docker volume inspect my_vol
docker volume rm my-vol
``````
##### Start a container with a volume
- If you start a container with a volume that doesn't yet exist, Docker creates the volume for you.
- The following example mounts the volume myvol2 into /app/ in the container.
- -v and --mount examples produce the same result.
``````sh
docker run -d \
  --name devtest \
  --mount source=myvol2,target=/app \
  nginx:latest
or
docker run -d \
  --name devtest \
  -v myvol2:/app \
  nginx:latest

docker inspect devtest
docker container stop devtest
docker container rm devtest
docker volume rm myvol2
``````

##### Declare a Docker Volume from Dockerfile
- Volumes can be declared in your Dockerfile using the VOLUME statement.
- This statement declares that a specific path of the container must be mounted to a Docker volume
- When you run the container, Docker will create an anonymous volume (volume with a unique id as the name) and mount it to the specified path.
``````sh
FROM nginx:latest
RUN echo "<h1>Hello from Volume</h1>" > /usr/share/nginx/html/index.html
VOLUME /usr/share/nginx/html

#You can now validate that nginx serves your message at http://localhost:8080/
docker build -t demo-earthly .
docker run -p 8080:80  demo-earthly
docker volume ls
docker volume inspect demo-earthly     #inspect command to view the data volume details
``````
##### Mount a Volume to a Container
- -v and --volume are the most common way to mount a volume to a container
- One notable option is ro which means that the volume will be mounted as read-only
- An alternative to -v is to add the —mount option to the docker run command. --mount is the more verbose counterpart of -v.
- Remember if the volume doesn’t exist on the source in host machine Docker will create it for you.
``````sh
Syntax:
-v <name_in the source host>:<destination in container>:<options>

docker run -it -v demo-volume:/data:ro ubuntu
echo "test" > /data/test       #write into the folder/data to validate that the volume is in read-only mode:

#mount
docker run --mount source=[volume_name],destination=[path_in_container] [docker_image]
docker run -it --name=example --mount source=demo-volume,destination=/data ubuntu

``````

##### Another Volume Ex2
- we’re using an Ubuntu base image and creating a volume named “logs_volume” at the path /opt/myapp_logs
- This command creates a container from the myapp image and mounts the "logs_volume" volume to the /opt/myapp_logs directory within the container.
``````sh
FROM ubuntu:latest
# Create a volume named "logs_volume"
VOLUME /logs_volume
# Copy the application files into the container
COPY . /app
# Set the working directory
WORKDIR /app
# Define the entry point and command to run the application
ENTRYPOINT ["python", "app.py"]
CMD ["--log-path", "/opt/myapp_logs/application.log"]

docker build -t myapp .
docker run -v logs_volume:/opt/myapp_logs myapp
docker volume inspect logs_volume

``````
##### Configure a Volume Using docker-compose
- it’s more convenient to use the docker-compose command to easily share data between multiple containers.
- to perform a bind mount (mount a directory from your local machine), you can use a relative path unlike -v with the command docker run that requires an absolute path.
- configuration use volumes in the services definition (web) to mount ./target from the host to /usr/share/nginx/html of the container


``````sh
version: "3.2"
services:
  web:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - ./target:/usr/share/nginx/html

``````
- With docker-compose, volumes must be declared at the same level as services
- you declared a volume named html_files and used it in both web and web1 service. Multiple containers can mount the same volume.
- docker-compose up will create a volume named <project_name>_html_files if it doesn’t already exist 
``````sh
version: "3.2"
services:
  web:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - html_files:/usr/share/nginx/html
  web1:
    image: nginx:latest
    ports:
      - 8081:80
    volumes:
      - html_files:/usr/share/nginx/html
 
volumes:
  html_files:

# Build compose file
docker-compose up
docker volume ls
``````

##### Docker Compose external Volume
- You can also manage container outside of you docker-compose file, but you still need to declare them under volumes and set the property external: true
``````sh
version: "3.2"
services:
  web:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - html_files:/usr/share/nginx/html
 
volumes:
  html_files:
    external: true

``````
