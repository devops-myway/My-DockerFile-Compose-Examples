##### Reusing WORKDIR

- A Dockerfile consists of instructions to be followed to build a Docker image.
- Docker image is a self-contained package. It contains all the components, such as libraries, configurations, environment variables, required to run the code within. The image is built once, and if any file that is stored in the local machine needs to be used in the image, it needs to be copied into the image using commands in the Dockerfile.
- Container is a running instance of the image. Containers utilize the host machine’s resources, but operate within their own dependencies and environment

##### Example 1
- Let’s take the example of a C++ file with the following code, which outputs Hello World
- If we want to run this in Docker, we would need the Docker container to have this file inside it’s environment.
``````sh
### 
#include <iostream>  // Installing necessary libraries
using namespace std;

int main() {
  // your code goes here
  cout << "Hello World" << endl; // Output of Hello World and end of line.
  return 0;
}


``````
##### Copying C++ file into image

COPY <source-file-path-on-host> <destination-file-path-inside-image>

- source-file-path-on-host: we need to give the path, on the host machine, of the file we want to copy.

- destination-file-path-on-host: we need to give the path, inside Docker image, where we want to copy the file to
``````sh
FROM ubuntu:latest

RUN	apt-get update && apt-get install sudo && \  #Installing libraries for running c++ file
	sudo apt install build-essential -y && \
	sudo apt -y install g++-10

COPY test/ans.cpp ans.cpp ## File will be copied into the root directory of the image

RUN	g++ -o new ans.cpp ## Syntax for running a cpp file. 'new' is the compiled code. 

#CMD command will be executed when image container is run.
#Following command will prove that 'ans.cpp' file has been uploaded in image directory
#It will also run the compiled code.
CMD ls ans.cpp; ./new
``````
``````sh

``````
