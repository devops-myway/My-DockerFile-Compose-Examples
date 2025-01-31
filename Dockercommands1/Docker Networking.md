##### Docker Network Types
- Docker networks configure communications between neighboring containers and external services
- Docker comes with five built-in network drivers that implement core networking functionality:

#####  bridge
- Bridge networks create a software-based bridge between your host and the container.
- Containers connected to the network can communicate with each other, but they’re isolated from those outside the network.
- Each container in the network is assigned its own IP address.
- Because the network’s bridged to your host, containers are also able to communicate on your LAN and the internet.

###### host
- Containers that use the host network mode share your host’s network stack without any isolation.
- They aren’t allocated their own IP addresses, and port binds will be published directly to your host’s network interface.
- This means a container process that listens on port 80 will bind to <your_host_ip>:80.
  
##### overlay
- Overlay networks are distributed networks that span multiple Docker hosts.
- The network allows all the containers running on any of the hosts to communicate with each other without requiring OS-level routing support.
- Overlay networks implement the networking for Docker Swarm clusters, but you can also use them when you’re running two separate instances of
- Docker Engine with containers that must directly contact each other

##### IPvLAN
- IPvLAN is an advanced driver that offers precise control over the IPv4 and IPv6 addresses assigned to your containers, as well as layer 2 and 3 VLAN tagging and routing.
- This driver is useful when you’re integrating containerized services with an existing physical network.

 ##### macvlan
- macvlan is another advanced option that allows containers to appear as physical devices on your network.
- It works by assigning each container in the network a unique MAC address.
- This network type requires you to dedicate one of your host’s physical interfaces to the virtual network

#####  Which Network Type Should I Use?
###### Bridge networks: 
- Bridge is a private default network driver. The bridge network driver allows containers connected to the
- same bridge network to communicate while providing isolation from containers not connected to that bridge network
###### Host networks
- are best when you want to bind ports directly to your host’s interfaces and aren’t concerned about network isolation.
###### Overlay networks
- are required when containers on different Docker hosts need to communicate directly with each other
###### Macvlan networks:
- are useful in situations where containers must appear as a physical device on your host’s network, such as when they run an application that monitors network traffic
###### None
- Finally, Docker networking is always optional at the container level: setting a container’s network to none will
- completely disable its networking stack. The container will be unable to reach its neighbors, your host’s services,
- or the internet. This helps improve security by sandboxing applications that aren’t expected to require connectivity.

##### How Docker Networking Works
- Docker containers are assigned their own network namespace, a Linux kernel feature that provides isolated virtual network environments.
- Containers also create virtual network interfaces on your host that allow them to communicate outside their namespace using your host’s network.
- Docker uses your host’s network stack to implement its networking system. It works by manipulating iptables rules to route traffic to your containers

##### Using Docker Networks

###### Creating Networks
- To create a new network, use the docker network create command. You can specify the driver to use, such as bridge or host, by setting the -d or --driver flag
- A bridge network will be created if you omit the flag.
- You can attach new containers to a network by setting the --network flag with your docker run command
- Next, open your third terminal window and start another Ubuntu container, this time without the --network flag
``````sh
docker network create –-help

# The new network’s useless at the moment because no containers have been connected.

$ docker network create demo-network -d bridge    # create a custom bridge network
50ed05634f6a3312e56700ef683ca39df44bfc826e2e4da9179c2593c79910f9

docker run -it --rm --name container1 --network demo-network busybox:latest    # attach a container1 to the custom bridge network
docker run -it --rm --name container2 busybox:latest        # this is on default bridge network.

# Now try communicating between the two containers, using their names:
#The containers aren’t in the same network yet, so they can’t directly communicate with each other.
# in container1
/ # ping container2
ping: bad address 'container2'

#Use your first terminal window to join container2 to the network:
docker network connect demo-network container2

# in container1
/ # ping container2
PING container2 (172.22.0.3): 56 data bytes
64 bytes from 172.22.0.3: seq=0 ttl=64 time=4.205 ms
``````
- Docker uses a software-based bridge network that allows containers connected
- to the same bridge network to communicate while isolating them from other containers not running in the same bridge network.
- containers on the same bridge can see each other using their IPs.
- BIG QUESTIONS!!!!!! What if I want to use the containers’ name instead of the IP.
- ANSWER!!!!! the default bridge network does not support automatic service discovery.

``````sh
# how containers running in the same bridge network can connect to each other

docker run -dit --name busybox1 busybox   # container1 on same default bridge network will connect with IP
docker run -dit --name busybox2 busybox     # container2 on same default bridge network will connect with IP

# These are the IP addresses of our containers:
docker inspect busybox1 |  jq -r  ' [0].NetworkSettings.IPAddress'
docker inspect busybox2 |  jq -r  '.[0].NetworkSettings.IPAddress'

docker exec -it busybox2 ping 172.17.0.3
docker exec -it busybox2 ping busybox1   # this will throw an error
``````
##### Custom or User-defined Bridge Networks
- You can create a second bridge network using:
- We can conclude that only user-defined bridge networks support automatic service discovery
``````sh
docker network create my_bridge --driver bridge

# Now, attach “busybox1” and “busybox2” to the same network
docker network connect my_bridge busybox1
docker network connect my_bridge busybox2

#Retry pinging “busybox1” using its name:
docker exec -it busybox2 ping busybox1
``````
##### Using Host Networking
- Bridge networks are what you’ll most commonly use to connect your containers. Let’s also explore the capabilities of host networks
- where containers attach directly to your host’s interfaces.
- Because the container’s using a host network, you can access your NGINX server on your host’s localhost:80 outside the container,
- even though no ports have been explicitly bound:
- 
``````sh
docker run -d --name nginx --network host nginx:latest

$ curl localhost:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>

``````
##### Disabling Networking
- When a container’s networking is disabled, it will have no connectivity available – either to other containers
- This lets you easily sandbox unknown services.
``````sh
$ docker run -it --rm --network none busybox:latest
/ # ping google.com
ping: bad address 'google.com'
``````
##### Removing Containers from Networks
``````sh
docker network disconnect demo-network container2

``````
##### Managing Networks

``````sh
docker network ls

# To delete a network, disconnect or stop all the Docker containers that use it, 
docker network rm demo-network
docker network prune
``````
##### Connect containers to a Docker network
- You connect containers to a Docker network to enable communication between them
- You can also use the --network host flag to run a container in host networking mode
- docker run -dit --network host nginx:latest
``````sh
docker network connect [network-name] [container-name]

docker network connect test-network nginx-container
docker network inspect test-network

#If you want to create a new container with your custom network, use the --network option, as shown below:
docker run --network test-macvlan-network nginx:latest

``````
##### Disconnect containers from a Docker network
- docker inspect nginx-container -f "{{json .NetworkSettings.Networks }}"
``````sh
docker network ls
docker network disconnect [container-name-or-id]

docker network disconnect test-network nginx-container
docker inspect nginx-container -f "{{json .NetworkSettings.Networks }}"
``````
##### Docker networks in Docker Compose
- Docker Compose for multicontainer applications, you can define networks in the docker-compose.yml file using the networks section
``````sh
vi docker-compose.yml

version: '3'
services:
  web:
    image: nginx:latest
    networks:
      - my-network
networks:
  my-network:
    driver: bridge

docker-compose up -d
``````
##### Remove Docker networks

``````sh
docker network rm [network-name]

docker network rm test-network
docker network prune
``````
