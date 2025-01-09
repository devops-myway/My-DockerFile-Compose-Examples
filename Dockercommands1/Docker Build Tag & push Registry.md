##### Docker Image Naming and Tagging
- There are two basic approaches for labeling and naming Docker images. When you build an image from a Dockerfile, you can either use the -t option, or the docker tag command.
- When you use the docker tag command, you specify the source repository name you are going to use as the base and the target name and tag you'll be creating
- When you use the docker build command, to name your image, you will use the Dockerfile as the source and then the -t option to name and tag your images.
- If you're uploading your images to Docker Hub, though, you'll definitely need to add your Docker Hub username to the beginning of the repository name, like this:

``````sh
docker tag <source_repository_name>:<tag> <target_repository_name>:tag
docker build -t <target_repository_name>:tag Dockerfile
docker built -t <dockerhub_user>/<target_repository_name>:tag Dockerfile

docker tag local-image:tagname new-repo:tagname
docker push new-repo:tagname

``````
##### Tagging Docker Images

``````sh
docker rmi -f $(docker images -a -q)
docker pull busybox
docker images

# we'll use the image ID. Keep in mind that your image ID will be different from the one I use in the example.
docker tag 65ad0d468eb1 mybusybox:v1
docker tag mybusybox:v1 kalkost/busybox:v1
docker images

``````
##### Let's put together a Dockerfile to create a basic image, using the mybusybox image

``````sh
echo "FROM mybusybox:v1" > Dockerfile
docker build it built_image:v1.1.1 .
docker images

``````
#### Docker Image Tag

https://docs.docker.com/reference/cli/docker/image/tag/
https://dev.to/zahraajawad/build-tag-and-push-docker-images-to-docker-hub-and-amazon-elastic-container-registry-4hbm

In Docker, a "Tag" is an identifying tag attached to a Docker image to distinguish it from different versions of the same image
or to specify other uses for it.
The tag is appended to the image name with a validated colon (:) to create a unique name for this version or interpretation of the image.

- SOURCE_IMAGE: is the name of the image you want to tag.
- TAG: is the tag associated with the source image (optional).
- TARGET_IMAGE: is the new name you want to give to the image.
- TAG: is the new tag you want to assign to the image (optional).

``````sh
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
docker tag [image] [username of docker hub or acr or ecr or gcr/image]:[tag name]

# Acr
docker tag mywebapp:v1 mywebappacrdemo.azurecr.io/mywebapp:v1
docker push mywebappacrdemo.azurecr.io/mywebapp:v1

# ECR
aws ecr create-repository --repository-name my-repo-name --region your- region
aws ecr get-login-password --region your-region | docker login --username AWS --password-stdin your-account-id.dkr.ecr.your-region.amazonaws.com

docker tag my-image:latest your-account-id.dkr.ecr.your-region.amazonaws.com/my-repo-name:latest
docker push your-account-id.dkr.ecr.your-region.amazonaws.com/my-repo-name:latest
``````

##### Tag an Image by Docker tag Command
Each image has a tag, so when the image is pulled without any tag, it pulls the latest.
when we run the docker Images command, we show that the image is tagged and takes the name that is specified to it

``````sh
# Tag without name, by the command
docker tag [image] [user name of docker hub/image]
docker tag nginx xyz/nginx

#Tag with name, by the command
docker tag [image] [user name of docker hub/image]:[tag name]

docker tag nginx xyz/nginx:web
docker image ls

``````
#### Docker Image Tagging Policies
- To popular ways to Tag Docker Images:
- Semantic versioning: is a popular and reliable system that can also be used for tagging your Docker images. If you haven't heard of it before, it's a simple three part number system (major.minor.patch). For example, a version like 2.1.0 would indicate a major release of version 2, a minor release of 1, and no patches.
- git commit hash: Another option is to use a hash value, such as the "git commit hash" for your code. This allows you to easily link the image tag back to the specific code changes in your repository, making it super easy for anyone to see what's been updated.

#### Tag Docker Images with Git Commit Information using git log or git rev-parse (recommended) commands:
- This shell script builds a Docker image and tags it with the short hash of the latest git commit.
- It passes the short commit hash and a build timestamp as build arguments to the Dockerfile, so that you can access these in the image/container.
``````sh
#!/bin/bash

VERSION=$(git log -1 --pretty=%h)
REPO="registry.example.com/my-project:"
TAG="$REPO$VERSION"
LATEST="${REPO}latest"
BUILD_TIMESTAMP=$( date '+%F_%H:%M:%S' )
docker build -t "$TAG" -t "$LATEST" --build-arg VERSION="$VERSION" --build-arg BUILD_TIMESTAMP="$BUILD_TIMESTAMP" . 
docker push "$TAG" 
docker push "$LATEST"

ARG VERSION
ENV VERSION $VERSION
ARG BUILD_TIMESTAMP
ENV BUILD_TIMESTAMP $BUILD_TIMESTAMP

``````
##### Git commit hash
- One of the option that you can try is to set commit hash in the environment variable, so you will able to get the ENV from the image as well.
- 
``````sh
# Dockerfile
FROM alpine
ARG GIT_COMMIT
ENV GIT_COMMIT=$GIT_COMMIT

#Now you can get GIT_COMMIT from env
echo "${GIT_COMMIT}"

# Build docker file
docker build --build-arg GIT_COMMIT=$(git rev-parse HEAD) -t my_image:$(git rev-parse HEAD) <path to Dockerfile>
``````
#### 

``````sh

``````
