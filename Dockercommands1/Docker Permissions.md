##### Understanding Docker File Permissions
- Docker containers are designed to be self-contained, isolated environments, which means that the file permissions within a container may differ from those on the host system.
- When you create a Docker container, the files and directories within the container are owned by the user or group specified in the Dockerfile.
- By default, Docker uses the root user (UID 0) to run processes inside the container.
- This means that all files and directories created within the container are owned by the root user.
- By default, the root user has full access (read, write, and execute) to all files and directories within the container
- it's generally considered a best practice to avoid running applications as the root user inside a container.
- Instead, you should create a non-root user and run your application with that user's permissions.
-  Limited Permissions > Owned by the Root User> Files & Directoriues > Docker container

##### Understanding USER Directive in Dockerfiles
- The USER instruction in a Dockerfile allows you to specify the user or group that should be used to run the processes inside the container.
- By setting the USER instruction or directive, you can ensure that your application runs with the appropriate file permissions,
- improving the overall security posture of your container-based application.

``````sh
# This ensures that all subsequent commands and processes within the container will be executed with the permissions of the myuser user.
## Dockerfile
FROM ubuntu:22.04
RUN useradd -ms /bin/bash myuser
USER myuser

# example permission on Dockerfile
FROM ubuntu:latest
RUN apt-get -y update
RUN groupadd -g 1500 dev_users && useradd -g dev_users -u 1500 user1
USER user1

``````
##### Changing Permissions at Runtime using chmod
- You may need to change the permissions of files or directories within a running container.
- chmod command to modify the permissions as needed.

``````sh
# This command sets the permissions of the /app/data directory to rwxr-xr-x, allowing the owner or user to read, write, and execute, while others can read and execute.
## Inside a running container
$ chmod 755 /app/data

``````
##### Mounting Volumes with Specific Permissions
- When you mount a volume from the host system into a Docker container, the permissions of the mounted files and directories will be determined by the host system
- You can control the permissions of the mounted volume by setting the appropriate ownership and permissions on the host system before mounting the volume.

``````sh
# we create a directory on the host system, set the ownership to user ID 1000 and group ID 1000, and then set the permissions to rwxr-xr-x
# When we mount this volume into the container, the files and directories will have the appropriate permissions

## On the host system
$ sudo mkdir /host-data
$ sudo chown 1000:1000 /host-data
$ sudo chmod 755 /host-data

## In the Dockerfile or docker run command
$ docker run -v /host-data:/container-data ...

``````
##### Best Practices for Permissions Management
- - Use Non-Root Users:  generally recommended to avoid running applications as the root user inside a container.
  - Instead, create a non-root user and run your application with that user's permissions
- This helps reduce the risk of security vulnerabilities and limits the potential damage if the container is compromised.
``````sh
## Dockerfile
FROM ubuntu:22.04
RUN useradd -ms /bin/bash myuser
USER myuser

``````
##### Utilize Volume Mounts Carefully
- When mounting volumes from the host system into a container, be mindful of the permissions of the mounted files and directories
- Ensure that the permissions on the host system are set appropriately before mounting the volume.
- Implement Least Privilege Principle: Grant only the necessary permissions required for your application to function,
- and avoid granting excessive permissions that could lead to security vulnerabilities.
``````sh
## On the host system
$ sudo mkdir /host-data
$ sudo chown 1000:1000 /host-data
$ sudo chmod 755 /host-data

## In the Dockerfile or docker run command
$ docker run -v /host-data:/container-data ...

``````
##### Understanding the COPY Command
- The COPY command in a Dockerfile allows you to copy files and directories from your host machine into the image.
-  It's important to note that the source path is relative to the build context, which is typically the directory containing your Dockerfile.
``````sh
COPY <src: is the path to the file or directory on your host> <dest: path where it will be placed in the image>
COPY --chown=user:group <src: is the path to the file or directory on your host> <dest: path where it will be placed in the image>

``````
##### Setting Permissions with chmod
- After copying files, you may need to adjust their permissions to ensure they are executable or readable by the application
- This can be done using the RUN command in conjunction with chmod.
``````sh
# The -R flag applies the changes recursively to all files and directories within /code/app
COPY ./app /code/app
RUN chmod -R 755 /code/app

# You should set the owner directly when you copy the files:
FROM joomla:3.9-php7.2-apache
RUN apt-get update \
&& apt-get install -y apt-utils vim curl

COPY --chown=www-data:www-data ./joomla_html /var/www/html

RUN chmod -R 765 /var/www/html/
COPY ./docker/php.ini /usr/local/etc/php/conf.d/php-extras.ini
EXPOSE 80
``````

##### Another Example copy chown command- set the owner directly when you copy the files

``````sh
FROM opensuse/tumbleweed:latest
RUN zypper in -y elinks libmozjs-102-0
RUN useradd elinks
USER elinks       #create a non-root user and use the permissions to run our application.
WORKDIR /home/elinks
RUN mkdir ./.elinks

COPY --chown=elinks elinks.conf ./.elinks/    #set the owner directly when you copy the files

ENTRYPOINT ["/usr/bin/elinks"]
CMD elinks

``````
##### Best Practices for Using COPY
- Minimize Layers: Each command in a Dockerfile creates a new layer in the image. For instance, you can copy files and set permissions in a single RUN command:
- Leverage Caching: Docker caches layers to speed up builds. By copying files that change less frequently (like dependencies) earlier in the Dockerfile,
- you can take advantage of this caching mechanism.
``````sh
COPY ./requirements.txt /code/requirements.txt
RUN pip install --no-cache-dir -r /code/requirements.txt
COPY ./app /code/app
RUN chmod -R 755 /code/app

``````
