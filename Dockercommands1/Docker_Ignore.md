##### What is a .dockerignore file?
- A .dockerignore is a configuration file that describes files and directories that you want to exclude when building a Docker image.
- you put the Dockerfile in the root directory of your project, but there may be many files in the root directory that are not related to the Docker image or that you do not want to include.
- .dockerignore is used to specify such unwanted files and not include them in the Docker image.
- The .dockerignore file is helpful to avoid inadvertently sending files or directories that are large or contain sensitive files to the daemon
- avoid adding them to the image using the ADD or COPY commands.
- Reduces the image size: By specifying files not to be included in the context in .dockerignore, the size of the image can be reduced
- Security Issues: If a Docker image file contains sensitive information such as credentials, it becomes a security issue

##### How .dockerignorefile works
- Before sending the Docker build context (Dockerfile, files you want to send inside the Docker image,
- and other things needed when building the Docker image) to the Docker daemon,
- in the root directory of the build context look for a file named .dockerignore in the root directory of the build context
- The .dockerignore file works by creating a file named .dockerignore in the build context root.
- However, in .dockerignore, all paths must be listed relative to the path. Let's take a concrete example.
- In .gitignore, the file or directory name is ignored in any hierarchy below the .gitignore file
``````sh
docker build -f path/to/Dockerfile dir

# Suppose you have the following files in the current directory
target
src/target
path/to/target

#For example, suppose you write the following in ".gitignore"
target

#In that case, files like the following will be "ignored"
target
src/target
path/to/targetà

#you can write the following in ".dockerignore"
target

#only the following files will be ignored. In other words, you need to specify the directory in .dockerignore.
target

#If you want to exclude files in other directories as in the .gitignore example, you can put the following in .dockerignore:
**/target

``````
##### Example of .dockerignore file (Best Practice)
- In the case of Node.js projects, it is effective to exclude files such as node_modules and npm-debug.
``````sh
**/node_modules/
**/dist
.git
npm-debug.log
.coverage
.coverage.*
.env
.aws
``````
- Python: In the case of Python, you can efficiently build Docker images by excluding cache files such as pycache and files such as pip-log.
``````sh
__pycache__
*.pyc
*.pyo
*.pyd
.Python
env
pip-log.txt
pip-delete-this-directory.txt
.tox
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
*.log
.git
.mypy_cache
.pytest_cache
.hypothesis
``````
