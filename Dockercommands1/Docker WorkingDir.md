##### WORKDIR command in Docker
- WORKDIR is a command that serves as a compass, directing subsequent instructions to operate within a specified working directory
- Setting the working directory with WORKDIR establishes a solid foundation for running other commands like RUN, CMD, ENTRYPOINT, COPY, and ADD

``````sh
# The WORKDIR /app command sets the working directory inside the container to /app
# This means that any subsequent commands or file operations will be performed within this directory

FROM ubuntu:alpine
WORKDIR /app
RUN pwd

``````
##### Example 2
``````sh
# There is a relative path in the first WORKDIR command and therefore the output of the final RUN pwd will be /a/b/c directory.

WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd

#  using a value in the environmental variable file to set the WORKDIR. Therefore the final RUN pwd command will output /path/$DIRNAME.
ENV DIRPATH=/path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
``````
##### Choose meaningful directory name

``````sh
# The below directory name indicates that myapp is a specific application within the /usr/src directory.
WORKDIR /usr/src/myapp

# Consider using absolute paths (the complete name of a directory or file from the root
WORKDIR /var/www/html

``````
##### Create the directory structure early
``````sh
FROM ubuntu:alpine
# Create the necessary directories at the beginning
RUN mkdir /app
RUN mkdir /app/src
RUN mkdir /app/config
# Set the working directory to /app
WORKDIR /app
# Copy package.json and package-lock.json to the working directory
COPY package*.json ./
# Install dependencies
RUN npm install
# Copy the application source code to the src directory
COPY src ./src
# Copy the configuration files to the config directory
COPY config ./config
# Set the command to run the application
CMD ["node", "src/app.js"]

``````
##### You dont have to RUN mkdir -p /usr/src/app
- This will be created automatically when you specifiy your WORKDIR
- So it acts like a cd and sets the tone for the upcoming statements
``````sh
FROM node:latest
WORKDIR /usr/src/app
COPY package.json .
RUN npm install
COPY . ./
EXPOSE 3000
CMD [ “npm”, “start” ]

# Example1
FROM microsoft/aspnetcore:2
COPY --from=build-env /publish /publish
WORKDIR /publish
ENTRYPOINT ["dotnet", "/publish/api.dll"]

# Example 2 - which is better way of using WORKDIR
FROM microsoft/aspnetcore:2
WORKDIR /publish
COPY --from=build-env /publish .
ENTRYPOINT ["dotnet", "api.dll"]

``````

##### Leverage variables for flexibility

``````sh
# Define environment variables
ENV APP_PATH=/app
ENV CONFIG_PATH=/config
# Set the working directory using variables
WORKDIR $APP_PATH or WORKDIR ${APP_PATH}

``````
##### Group related commands together

``````sh
# Copy custom configuration files
COPY nginx.conf /etc/nginx/nginx.conf
COPY default.conf /etc/nginx/conf.d/default.conf
# Set the working directory to /usr/share/nginx/html
WORKDIR /usr/share/nginx/html
# Copy the web application files
COPY index.html .
COPY styles.css .
COPY scripts.js .

``````
##### Avoid nesting multiple levels of working directories

``````sh
// AVOID

WORKDIR /app 
WORKDIR backend 
WORKDIR subdirectory

// USE
WORKDIR /app/backend/subdirectory

``````
##### 

``````sh


``````
