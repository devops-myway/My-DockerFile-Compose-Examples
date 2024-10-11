##### Container With Shell Access
start a container directly with shell access using the docker run command with the -it option:
``````sh
$ docker run -it alpine
/# ls -all

``````
# That’s not directly a Linux distribution
But what happens if we want to explore something that’s not directly a Linux distribution
The Cassandra docker container comes with a default startup command, which runs Cassandra. As a result, we’re no longer connected to a shell.
Instead, we just see the standard output populated with log messages of the application.

However, we can bypass the default startup command.
Let’s pass the /bin/bash additional argument to the docker run command:

When we use this approach, we assume that we can control the startup of the container. In a production environment, this might not be possible.
``````sh
docker run -it cassandra

docker run -it cassandra /bin/bash
root@a71f71e98598:/# ls -all
``````
##### Spawning a Shell in a Running Container
 the docker exec command, which allows us to connect to running containers.
``````sh
docker run cassandra
docker ps                                      <---- identify the container id with docker ps
$ docker exec -it 00622c0645fb /bin/bash        <---- pass COMMAND "/bin/bash" as the argument with -it option to docker exec:
root@00622c0645fb:/# ls -all

docker ps
``````
##### Exporting the Filesystem
We can export the filesystem of a container into a tar file by using the docker export command.
we dump the filesystem into the hello.tar file using the -o option of docker export:
Finally, we print the contents of the archive using the tar utility with the -tvf flags:
``````sh
docker run hello-world
docker ps -a

docker export -o hello.tar a0af60c72d93
tar -tvf hello.tar

``````

##### Copying the Filesystem
we copy the complete filesystem starting from the root (/) from our container to the test directory:

``````sh
$ docker cp a0af60c72d93:/ ./test
ls -all test/
``````
