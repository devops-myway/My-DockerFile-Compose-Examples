#####  Understanding Docker's Disk Usage
Docker's disk usage primarily comes from a few sources:
- Images: Docker images are the blueprints for containers. They can be quite large, especially if you're working with multiple projects or versions.
- Containers: Running and stopped containers take up space, as they maintain their state on disk.
- Volumes: Docker volumes are used for persistent data storage that can be mounted into containers. These can accumulate over time.
- Docker Network: Managing Docker networks is essential for maintaining system efficiency. Unused Docker networks contribute to network bridges and routing table entries, which can affect performance even though they don't take up disk space. It's important to list, remove, and prune these networks regularly to keep the system running smoothly.
- Build Cache: Docker's build cache is a double-edged sword. It speeds up builds by reusing layers from previous builds when Dockerfile instructions haven't changed. This is especially useful for time-consuming steps like dependency installation. However, over time, particularly with frequent builds and Dockerfile changes, the cache can accumulate significantly. Cached layers from various projects and image versions can quietly occupy gigabytes of storage, creating a trade-off between build speed and disk usage.

What you see below:
- The total size of all images
- The total size of all containers
- The local volumes size
- And the cache
``````sh
docker system df

#output
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          188       37        51.98GB   39.13GB (75%)
Containers      60        6         7.026GB   7.026GB (99%)
Local Volumes   21        17        2.985GB   115.1MB (3%)
Build Cache     955       0         2.68GB    2.68GB

#To show more detailed information on space usage
docker system df --verbose

docker system df -v

# List running container sizes
docker ps --size
``````
#####  Strategies for Reclaiming Disk Space

``````sh
docker image prune
docker image prune --all
``````
#####  Clean Up Stopped Containers

``````sh
docker container prune
docker stop $(docker ps -q) && docker container prune

``````
#####  Remove Unused Volumes

``````sh
docker volume prune

``````
##### Clear the Build Cache

``````sh
docker builder prune

``````
