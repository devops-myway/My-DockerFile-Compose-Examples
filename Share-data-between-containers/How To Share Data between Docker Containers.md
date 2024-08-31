
##### Docker Volumes
Docker containers are ephemeral, running just as long as it takes for the command issued in the container to complete.
Sometimes, however, applications need to share access to data or persist data after a container is deleted.

Databases, user-generated content for a web site, and log files are just a few examples of data that is impractical or impossible to include in a Docker image but which applications need to access.

Docker Volumes can be created and attached in the same command that creates a container, or they can be created independently of any containers and attached later.

##### Creating an Independent Volume
docker volume create command allows you to create a volume without relating it to any particular container.
-v: to mount the new volume. requires the name of the volume, a colon, then the absolute path to where the volume should appear inside the container.
--rm:  flag to automatically delete it when we exit

``````sh
docker volume create --name DataVolume1
docker volume ls

docker run -ti --rm -v DataVolume1:/datavolume1 ubuntu

# While in the container, let’s write some data to the volume
echo "Example1" > /datavolume1/Example1.txt
exit
# We can verify that the volume is present on our system with docker volume inspect
docker volume inspect DataVolume1

``````
##### Inspecting the Volume created
We can even look at the data on the host at the path listed as the Mountpoint
Next, let’s start a new container and attach DataVolume1:
``````sh
docker volume inspect DataVolume1


Output
[
    {
        "CreatedAt": "2018-07-11T16:57:54Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/DataVolume1/_data",
        "Name": "DataVolume1",
        "Options": {},
        "Scope": "local"
    }
]

``````
##### start a new container and attach DataVolume1

``````sh
docker run --rm -ti -v DataVolume1:/datavolume1 ubuntu

cat /datavolume1/Example1.txt
exit
``````
##### Creating a Volume that Persists when the Container is Removed
- --name: to identify the container.
- -v: flag will allow us to create a new volume, which we’ll call DataVolume2

- -v /path:/path/in/container mounts the host directory, /path at the /path/in/container
- -v path:/path/in/container creates a volume named path with no relationship to the host.
``````sh
docker run -it --name=Container2 -v DataVolume2:/datavolume2 ubuntu

# While in the container, we’ll write some data to the volume:
echo "Example2" > /datavolume2/Example2.txt
cat /datavolume2/Example2.txt
exit
``````
##### When we restart the container, the volume will mount automatically:

``````sh
docker start -ai Container2
cat /datavolume2/Example2.txt
exit

docker volume rm DataVolume2
docker rm d0d2233b668eddad4986313c7a4a1bc0d2edaf0c7e1c02a6a6256de27db17a63
docker volume ls
docker volume rm DataVolume2

``````
##### Creating a Volume from an Existing Directory with Data
we’ll create a container and add the data volume at /var, a directory which contains data in the base image
All the content from the base image’s /var directory is copied into the volume, and we can mount that volume in a new container

The directory datavolume3 now has a copy of the contents of the base image’s /var directory:
``````sh
docker run -ti --rm -v DataVolume3:/var ubuntu
exit

docker run --rm -v DataVolume3:/datavolume3 ubuntu ls datavolume3
``````
##### Sharing Data Between Multiple Docker Containers

``````sh
docker run -ti --name=Container4 -v DataVolume4:/datavolume4 ubuntu

echo "This file is shared between containers" > /datavolume4/Example4.txt
exit

``````
##### Create Container5 and Mount Volumes from Container4

``````sh
docker run -ti --name=Container5 --volumes-from Container4 ubuntu
cat /datavolume4/Example4.txt
echo "Both containers can write to DataVolume4" >> /datavolume4/Example4.txt

echo "Both containers can write to DataVolume4" >> /datavolume4/Example4.txt
exit

``````
##### View Changes Made in Container5

``````sh
docker start -ai Container4
cat /datavolume4/Example4.txt
exit

``````

``````sh

``````
