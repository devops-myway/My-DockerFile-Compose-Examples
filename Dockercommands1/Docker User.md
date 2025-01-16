##### USER Directive
- By default, a Docker Container runs as a Root user. This poses a great security threat if you deploy your applications on a large scale inside Docker Containers.
- You can change or switch to a non-root user inside a Docker Container using the USER Instruction or directive.
- you first need to create a non-root user and a group inside the Container.

##### USER options and parameters to note:
- -u uid: Set the user’s ID to be uid. Unless you know what you’re doing, omit this option and accept the default
-g group: --gid : Set the user’s initial (default) group to group
-G group1,group2,: Make the user a member of the additional, existing groups group1, group2, and so on.
-d dir: Set the user’s home directory to be dir,
-m: Copy all files from your system skeleton directory, /etc/skel, into the newly created home directory
-k: to get new users started If you prefer to copy from a different directory (-k your_preferred_directory).
-M: create a user without a home directory
-e: To set an expiry date for a user account
-c: To add a comment or description for a user
-s shell: Set the user’s login shell to be shell
-p: To set an unencrypted password for the user

``````sh
# Create the Dockerfile

FROM ubuntu:latest
RUN apt-get -y update
RUN groupadd -g 1500 dev_users && useradd -g dev_users -u 1500 user1
USER user1

docker buildx build -t user-demo --file .
docker run -it user-demo bash
id

``````
##### Example of User Directive


``````sh
# Dockerfile
FROM opensuse/tumbleweed:latest
RUN zypper in -y elinks libmozjs-102-0
RUN useradd elinks
USER elinks
WORKDIR /home/elinks
RUN mkdir ./.elinks
COPY --chown=elinks elinks.conf ./.elinks/
ENTRYPOINT ["/usr/bin/elinks"]
CMD elinks


``````
##### Example2
- After this we are creating a new user 'gaurav' using the 'useradd' command.
- In the useradd command we are defining the user's home directory, user's group, providing the password in an encrypted form, and specifying the username.

``````sh
FROM ubuntu:14.04
LABEL name="learning-ocean"
LABEL email="[email protected]"
ENV NAME Gaurav
ENV PASS password
RUN pwd > /tmp/1stpwd.txt
RUN cd /tmp
RUN pwd > /tmp/2ndpwd.txt
WORKDIR /tmp
RUN pwd > /tmp/3rdpwd.txt
RUN apt-get update && apt-get install -y openssh-server && apt-get install -y python
RUN useradd -d /home/gaurav -g root -G sudo -m -p $(echo "$PASS" | openssl passwd -1 -stdin) $NAME
RUN whoami > /tmp/1stwhoami.txt
USER $NAME
RUN whoami > /tmp/2ndwhoami.txt


``````
``````sh



``````
