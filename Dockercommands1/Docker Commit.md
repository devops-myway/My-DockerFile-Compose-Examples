#####  Create Docker Image From Running Container
- While working with containers, we keep doing installations and other changes to make our application work. And once we are done, we want to take an image.
- Options
- --author, -a: Author
- --change, -c: Apply Dockerfile instruction to the created image
- --message, -m: Commit message
- --pause, -p: Pause container during commit
``````sh
Syntax:
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
docker commit <container_id> <new_image_name>

``````
#####  Create container ex1

``````sh
learning-ocean:~ gaurav$ docker container run -it ubuntu:14.04 /bin/bash
root@1b2f7e840cf3:/# cd /tmp

# Create few files in the the containers filesystem
root@1b2f7e840cf3:/# touch 1 2 3 4 5 6 7 8 9
root@1b2f7e840cf3:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@1b2f7e840cf3:/# cd /tmp
root@1b2f7e840cf3:/tmp# ls
1  2  3  4  5  6  7  8  9

# come out from container using CTRL+pq
docker container ls

# Finally creating the image using the commit command-
docker container commit 1b2f7e840cf3 newubuntu

# verify new image
docker image ls

# running container from new image
learning-ocean:~ gaurav$ docker container run -it newubuntu bash
root@91d9dc9e566d:/# cd /tmp
root@91d9dc9e566d:/tmp# cd ..
root@91d9dc9e566d:/tmp# ls
1  2  3  4  5  6  7  8  9
root@91d9dc9e566d:/#
``````
