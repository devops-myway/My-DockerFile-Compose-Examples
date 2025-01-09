#####  
https://github.com/docker-library/cassandra/blob/0e270a93b53119818f9c71fa273cda3a07d0bf5c/2.1/Dockerfile?spm=a2c65.11461447.0.0.47cd3b74HeBjnW

``````sh
# explicitly set user/group IDs
RUN groupadd -r cassandra --gid=999 && useradd -r -g cassandra --uid=999 cassandra

RUN groupadd -r sonarqube && useradd -r -g sonarqube sonarqube
``````
##### Copy command for uid:gid
``````sh
#with this dockerfile will throw an error 
FROM docker.io/bitnami/jasperreports:7-debian-10
COPY custom-entrypoint.sh /app-entrypoint.sh   # take note of the copy directive
RUN  chmod +x /app-entrypoint.sh

docker build -t registry/jasperserver:latest .

#error example for permission issue
 => ERROR [3/7] RUN  chmod +x /app-entrypoint.sh                                                                                                                                                                                                                                                                                            0.4s
------                                                                                                                                                                                                                                                                                                                                           
 > [3/7] RUN  chmod +x /app-entrypoint.sh:
#8 0.380 chmod: changing permissions of '/app-entrypoint.sh': Operation not permitted
------
executor failed running [/bin/sh -c chmod +x /app-entrypoint.sh]: exit code: 1
``````
##### Solution to above copy commmand 
- The COPY step will create the file with the uid/gid of 0:0 (root:root) within the / directory where normal users have no access
- And the selected base image is configured to run as uid 1001. Probably the easiest is to switch back to root temporarily to run that step
- Alternatively, you can copy the script to a directory where the user has access with the right ownership on the file
- Without pulling the image, I suspect /opt/bitnami/scripts may have different permissions:
``````sh
FROM docker.io/bitnami/jasperreports:7-debian-10
USER root
COPY custom-entrypoint.sh /app-entrypoint.sh
USER 1001
RUN  chmod +x /app-entrypoint.sh

# Alternatively
FROM docker.io/bitnami/jasperreports:7-debian-10
COPY --chown=1001 custom-entrypoint.sh /opt/bitnami/scripts/app-entrypoint.sh
RUN  chmod +x /opt/bitnami/scripts/app-entrypoint.sh
``````
##### You should set the owner directly when you copy the files:
``````sh
FROM joomla:3.9-php7.2-apache
RUN apt-get update \
&& apt-get install -y apt-utils vim curl

COPY --chown=www-data:www-data ./joomla_html /var/www/html
RUN chmod -R 765 /var/www/html/

COPY ./docker/php.ini /usr/local/etc/php/conf.d/php-extras.ini
EXPOSE 80
``````


