##### Exporting and Importing Docker Images
Docker images offer a flexible and portable way to package applications for easier deployment and distribution.
While end-users download images from a container registry, developers and testers often need a quicker sharing method.

Identify the Image ID or Name: To export an image, you need its ID or name. List all images using:
``````sh
docker images
docker save -o <filename>.tar <image_name>     <----    Step-by-Step Process to Export a Docker Image 

docker load -i <filename>.tar                  <---- Step-by-Step Process to Import a Docker Image
docker images

``````
##### Exporting and Importing Docker Containers
Export a container's filesystem as a tar archive
``````sh
docker ps
docker export <container_id> -o <filename>.tar            <----  Step by Step Process to Export a Docker Container

docker import <filename>.tar <new_image_name>             <----  Import the contents from a tarball to create a filesystem image
``````

##### Exporting and Importing Docker Images and Containers

``````sh
# File: vi package.json
{
  "name": "basic-express-server",
  "version": "1.0.0",
  "description": "Demo for exporting and importing docker containers/images",
  "main": "./src/app.js",
  "scripts": {
    "start": "node ./src/app.js"
  },
  "author": "Your Name",
  "license": "ISC",
  "dependencies": {
    "express": "^4.19.2"
  }
}

Run npm install     <----- to install the dependencies.
``````

``````sh
# File: vi src/app.js

const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
    res.send('Hello World!');
});

app.listen(port, () => {
    console.log(`Server is running on http://localhost:${port}`);
});


``````
##### Let’s create a Dockerfile to containerize the Express server.
``````sh
#File: Dockerfile

FROM node
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD [ "npm", "start" ]
EXPOSE 3000

# File: .dockerignore
node_modules
npm-debug.log
``````

``````sh
docker build -t basic-express-server .
docker image ls

``````
##### build an image of our Express server by running this command:

``````sh
docker build -t basic-express-server .
docker image ls

``````
##### Save the image to a tarball
``````sh
docker save -o demo-image.tar basic-express-server     <--- -o, –output string Write to a file, instead of STDOUT

``````
``````sh
docker rmi <image_id>

docker load -i demo-image.tar    <---Importing Docker Image
docker run -p 3000:3000 -d basic-express-server

docker export cff6965045e1 -o container.tar    <---To export this container & A tarball file will be created named container.tar

docker import container.tar new-image     <---- Import the Docker container by running
``````
