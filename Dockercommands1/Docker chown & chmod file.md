##### Using chown to Change File Ownership

I used COPY to copy one file on each line, not ADD.
``````sh
touch gamefile
touch anothergame

vi Dockerfile

FROM alpine:3.8
COPY --chown=games:games gamefile /root/
COPY --chown=35:35 anothergame /root/

#Build the image using
docker build --tag tutorial:demo --file Dockerfile  .

# start up a container to see the result.

docker stop -t 0 tutorial ;   docker container prune -f  
docker run -ti -d --name tutorial tutorial:demo /bin/sh -c 'while true; do sleep 60; done'
docker exec -it tutorial /bin/sh

/ # ls -l /root
``````
##### What happened : extract from the Dockerfile:
Notice the first copy command uses a username and a groupname to change ownerships.
Notice the second copy uses a UID and a GID to change ownerships. 35 is the UID for the games user id.

GID = group identifier (GID)

UID = user identifier (GID)

``````sh
COPY [--chown=<user>:<group>] [--chmod=<perms> ...] <src-host> ... <dest-container>
COPY --chown=55:mygroup files* /somedir/

COPY --chown=games:games gamefile /root
COPY --chown=35:35 anothergame /root

# Use the RUN command Example
FROM joomla:3.9-php7.2-apache
RUN apt-get update \
&& apt-get install -y apt-utils vim curl

COPY --chown=www-data:www-data ./joomla_html /var/www/html

RUN chmod -R 765 /var/www/html/
COPY ./docker/php.ini /usr/local/etc/php/conf.d/php-extras.ini
EXPOSE 80

``````
##### Dockerfile example
``````sh
RUN mkdir -p /run/php && \
    chown -R www-data:www-data /var/www/html && \
    chown -R www-data:www-data /run/php
    
``````
