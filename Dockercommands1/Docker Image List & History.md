##### IMAGE LIST
- docker image ls command is used to list the docker images.
- We can also use the --format option along with the ls command to format the output of the ls command as required.
``````sh
docker image ls
docker image ls --format '{{.ID}} , {{.Repository}}'

# --format option to print Tag along with the ID and Repository
docker image ls --format '{{.ID}} , {{.Repository}} - {{.Tag}}'

``````
##### Docker Image History
- docker image history command can be used to show the history of the specified image.
``````sh
docker image history ubuntu:14.04

``````
``````sh


``````
