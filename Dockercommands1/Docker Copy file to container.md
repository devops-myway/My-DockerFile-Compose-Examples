##### Copy File To Docker Container
- The docker cp command allows you to copy files and directories from your local machine to a running container.
- src_path: is the path on your local machine of the file you want to copy.
- container: is the name or the ID of the container you want to copy files to.
- dest_path: is the path in the container of the directory you want to copy files to.
``````sh
Syntax:
docker cp src_path container:dest_path

``````
###### Copying individual files
- To copy a file to a container, you can either use its relative or absolute path, and specify the destination directory
- you can copy and rename at the same time, by providing the full destination path:
- Note that it is not possible to copy multiple files at once using the docker cp command, unless you either use a for loop
``````sh
docker cp ./file.txt container:/app/
docker cp ./file.txt container:/app/script.txt

for file in *.txt; do docker cp $file container:/app; done
``````
##### Copying entire directories recursively
- When copying a directory, the docker cp command will behave like the Unix cp -a command,
- which means that the directory will be copied recursively with permission preserved if possible.
``````sh
docker cp ./dir container:/app/

``````
##### Copying absolute and relative paths, such as a parent directory
- The docker cp assumes container paths are relative to the container's root directory (i.e. /)
``````sh
docker cp ./file.txt container:tmp

 docker cp ./file.txt container:/tmp

docker cp ./file.txt container:/tmp

docker cp /home/johndoe/Documents/file.txt container:/tmp
``````
##### Copying files at build-time using a Dockerfile
- To copy files and directory at build-time, you can use the COPY instruction:
- src is the path on your local machine of the file(s) you want to copy.
- dest is the path on the container you want to copy the file to.

``````sh
COPY file.txt dir /app

COPY *.txt /app   #These source paths may also contain wildcards

# You can therefore copy the entire directory of the build context using the dot notation:
COPY . /app

COPY dir /app    #the following command will copy the content of the dir directory into the /app directory

# Or a path relative to the one specified in the WORKDIR instruction:
WORKDIR /app
 COPY file .

mkdir dir && cd dir
``````
``````sh

``````
``````sh

``````
