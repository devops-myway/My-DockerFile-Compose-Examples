https://docs.docker.com/build/building/context/

##### Build context

The "docker build && docker buildx build" commands build Docker images from a Dockerfile and a context.

The build context is the set of files that your build can access.

You can pass any of the following inputs as the context for a build:

- The relative or absolute path to a local directory
- A remote URL of a Git repository, tarball, or plain-text file
- A plain-text file or tarball piped to the docker build command through standard input

``````sh
docker build [OPTIONS] PATH | URL | -            

``````

###### Local context
specify a relative or absolute filepath to the docker build command. current directory (.) as a build context:

``````sh
docker build .                   

``````
###### Local directories
Dockerfile instructions can reference and include these files in the build if you pass this directory as a context.

``````sh
considering the below directory structure:

- index.ts 
- src/
- Dockerfile
- package.json
- package-lock.json
``````
``````sh
# syntax=docker/dockerfile:1
FROM node:latest
WORKDIR /src
COPY package.json package-lock.json .
RUN npm ci
COPY index.ts src .

docker build .
``````

###### Local context with Dockerfile from stdin

The syntax uses the -f (or --file) option to specify the Dockerfile to use, and it uses a hyphen (-) as filename to instruct Docker to read the Dockerfile from stdin

``````sh
# create a directory to work in
mkdir example
cd example

# create an example file
touch somefile.txt

# build an image using the current directory as context
# and a Dockerfile passed through stdin
docker build -t myimage:latest -f- . <<EOF
FROM busybox
COPY somefile.txt ./
RUN cat /somefile.txt
EOF    

``````
###### Local tarballs
When you pipe a tarball to the build command, the build uses the contents of the tarball as a filesystem context.

``````sh
tar czf foo.tar.gz *
docker build - < foo.tar.gz

docker build --file test.Dockerfile - < foo.tar.gz
``````

###### Git repositories
When you pass a URL pointing to the location of a Git repository as an argument to docker build, the builder uses the repository as the build context.

The builder performs a shallow clone of the repository, downloading only the HEAD commit, not the entire history.

The format of the URL fragment is #ref:dir, where:

- ref is the name of the branch, tag, or commit hash
- dir is a subdirectory inside the repository
``````sh
docker build https://github.com/user/myrepo.git

docker build https://github.com/user/myrepo.git#container:docker

``````
