##### Creating a Docker Volume

``````sh
docker pull httpd:2.4
docker images


docker volume create website

docker volume ls

`````` 
##### Download the website image from https://github.com/howtoforge/docker-volume
Download this website code from GitHub to your current working directory.
Run the below command to copy the website code you just downloaded to the website volume.
Run the below command to list the files in the website volume.

``````sh
sudo cp -r /home/cloud_user/widget-factory-inc/web/* /var/lib/docker/volumes/website/_data/
sudo ls -l /var/lib/docker/volumes/website/_data/ 


docker run -d --name web1 -p 80:80 -v website:/usr/local/apache2/htdocs:ro httpd:2.4

``````
#####
You can open the IP address of the host machine on your web browser to see the website you just copied.

``````sh
docker run -d --name webTmp --rm -v website:/usr/local/apache2/htdocs:ro httpd:2.4
docker ps 
docker stop webTmp
docker volume ls
docker volume inspect website

``````
