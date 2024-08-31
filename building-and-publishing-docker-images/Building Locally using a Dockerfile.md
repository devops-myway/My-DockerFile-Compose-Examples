
##### Building Locally using a Dockerfile
using the "Whale say " image which is a small Docker Image (based on an Ubuntu Image)

``````sh
mkdir mywhale
cd mywhale
vi Dockerfile

FROM docker/whalesay:latest
RUN apt-get -y update && apt-get install -y fortunes
CMD /usr/games/fortune -a | cowsay

docker build -t my-docker-whale .
docker image ls
docker run my-docker-whale
``````
##### Publishing your Custom Docker Image on Docker Hub
you will need to create an account on the Docker Hub signup webpage where you will provide a name, password, and email address for your account

you will need the ID and the TAG of your “my-docker-whale” image. 
Run again the "docker images" command and note the ID and the TAG of your Docker image e.g. a69f3f5e1a31.

``````sh
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
docker push <DOCKER_HUB_USERNAME/IMAGE_NAME[:tag]>

docker login --username=yourhubusername --email=youremail@provider.com
``````

``````sh
docker tag a69f3f5e1a31 accountname/my-docker-whale:latest
docker push accountname/my-docker-whale

``````

##### Downloading your Custom Image
If you want to pull your image from your Docker Hub repository you will need to first delete the original image 
from your local machine because Docker would refuse to pull from the hub as the local and the remote images are identical.

``````sh
docker rmi -f a69f3f5e1a31

docker run accountname/my-docker-whale
``````
