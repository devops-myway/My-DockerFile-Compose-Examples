##### Bindmounting a Volume
- --name=nginx names the container so we can refer to it more easily.
- -d detaches the process and runs it in the background. wouldn’t be able to use this terminal until we killed Nginx.
- -v ~/nginxlogs:/var/log/nginx sets up a bindmount volume that links the /var/log/nginx directory from inside the Nginx container to the ~/nginxlogs directory on the host machine. Docker uses a : to split the host’s path from the container path, and the host path always comes first.
- -p 5000:80 sets up a port forward. The Nginx container is listening on port 80 by default. This flag maps the container’s port 80 to port 5000 on the host system.

- If the first argument begins with a / or ~/, you’re creating a bindmount

#####  Accessing Data on the Host
Load the address in a web browser, using the IP address or hostname of your server and the port number
``````sh
docker run --name=nginx -d -v ~/nginxlogs:/var/log/nginx -p 5000:80 nginx

http://your_server_ip:5000

``````
##### Accessing Data on the Host
if we look in the ~/nginxlogs directory on the host, we’ll see the access.log created by the container’s nginx which will show our request

``````sh
cat ~/nginxlogs/access.log
``````
``````sh

``````
``````sh

``````
