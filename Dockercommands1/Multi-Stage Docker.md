https://docs.docker.com/build/building/multi-stage/

#####  Using Multi-Stage Docker Builds
Multi-stage Docker builds can greatly reduce the size of final built images, and the savings can be extreme with Go.
It's important to keep the size of Docker images small as it reduces the size that needs to be published, hosted, and downloaded.

Publishing is usually infrequent, and hosting is free through a number of container registries, 
so those aren't a big deal - but the download size will affect everyone and every cluster that wants to use your image.

As a general rule of thumb, reducing the number of layers in a final image helps reduce the size.

###### Multi-Stage DockerFile
Multistage builds make use of one Dockerfile with multiple FROM instructions. Each of these FROM instructions is a new build stage that can COPY artifacts from the previous stages.
- The build stage is named by appending AS *name-of-build* to the FROM instruction.
- The name of the build stage can be used in a subsequent FROM and COPY command by providing a convenient way to identify the source layer for files brought into the image build.
- The final image is produced from the last stage executed in the Dockerfile.
``````sh
FROM node:12.13.0-alpine As build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx
EXPOSE 3000
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/build /usr/share/nginx/html

``````
#####  # Single-stage build
8 layers! Granted, 5 of them are coming from the base image - but that's a lot, especially for such a small application.
``````sh


``````


``````sh


``````
