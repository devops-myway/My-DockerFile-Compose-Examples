##### Understanding Docker Restart Policies: — restart=no and — restart=always

- Docker, a popular containerization platform, provides flexibility and control over how containers behave when they encounter issues or stop unexpectedly.
- Docker provides a restart policy option to let your containers restart automatically in case of certain events or failures.
- This is extremely helpful in scenarios where you have to restart the Docker host (your Linux server) or if the service running in the container fails.

##### Docker restart policies
- There are following restart policies for Docker containers:

- no: The default behavior is to not start containers automatically
- always: Always restart a stopped container unless the container was stopped explicitly
- unless-stopped: Restart the container unless the container was in stopped state before the Docker daemon was stopped (explained later)
- on-failure: Restart the container if it exited with a non-zero exit code or if the docker daemon restarts
``````sh
syntax:
docker container run --restart <policy>

``````
##### Always restart policy
- With this policy set, the container will always be restarted unless it was stopped explicitly.
``````sh
# The container has one task. It runs the bash sleep command for 10 seconds and then exits

docker container run --name always-policy --restart always alpine sleep 10
docker ps
#  use the docker inspect command to see how many times the container has been restarted so far.
docker inspect always-policy | grep -i restartcount
        "RestartCount": 4
#If you stop the container with the stop command, it will not restart automatically afterwards
docker stop always-policy 
``````
##### unless-stopped vs always restart policy
- the container with always restart policy will start the container automatically but the container with unless-stopped policy won't be restarted.

``````sh
docker container run --name unless-stopped-policy --restart always alpine sleep 10
docker stop unless-stopped-policy
docker ps -a

# Restart Docker daemon:
sudo systemctl restart docker
docker ps
``````
##### Setting restart policy in Docker Compose file
- If you are using something like Docker Compose for deploying containers, you can mention the restart policy for the service object in YAML file.
``````sh
version: "3.3"      
services:
    NginxProxy:
        image: "jwilder/nginx-proxy:latest"
        restart: "on-failure"
        networks: ["net"]
        ports:
            - "80:80"
            - "443:443"

``````
``````sh


``````
