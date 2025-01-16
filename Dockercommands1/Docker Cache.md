##### Docker Build Cache
- Building images should be fast, efficient, and reliable. The concept of Docker images comes with immutable layers.
- Every command you execute results in a new layer that contains the changes compared to the previous layer.
- All previously built layers are cached and can be reused. But, if your installation depends on external resources, the Docker cache can cause issues.

##### How to Leverage the Docker Build Cache
- If you build the image the first time, you see that it takes quite some time, in my case 11.3s.
- One long executing step is apt-get update && apt-get -y upgrade depending on how many dependencies are updated and how fast your internet speed is
- The caching behavior is intelligent. Once 1 step needs to rebuild, every subsequent step is built again.
``````sh
# create a Dockerfile that updates libraries and adds a custom startpage:
FROM nginx:1.21.6
# Update all packages
RUN apt-get update && apt-get -y upgrade
# Use a custom startpage
RUN echo '<html><bod>My Custom Startpage</body></html>' > /usr/share/nginx/html/index.html

# Build Image
docker build -t my-custom-nginx .
#output
[2/3] RUN apt-get update && apt-get -y upgrade           3.6s
=> [3/3] RUN echo '<html><bod>My Custom Startpage...        0.2s

# Now, you execute it again, and you benefit from the Docker build cache:
docker build -t my-custom-nginx .
#output
=> CACHED [2/3] RUN apt-get update && apt-get -y upgrade        0.0s
=> CACHED [3/3] RUN echo '<html><bod>My Custom Startpage...     0.0s

``````
##### How to Use the Docker Build --no-cache Option
- You can rebuild the image from the base image without using cached layers by using the --no-cache option.
- To ensure that you're getting the latest versions of dependencies in your build, you can use the --no-cache option to avoid cache hits.
``````sh
docker build -t my-custom-nginx .
docker build --no-cache -t my-image:my-tag .
``````
##### How to Use Docker Arguments for Cache-Busting
- You add a CACHEBUST argument to your Dockerfile at the location you want to enforce a rebuild
``````sh
FROM nginx:1.21.6
# Update all packages
RUN apt-get update && apt-get -y upgrade
# Custom cache invalidation
ARG CACHEBUST=1
# Use a custom startpage
RUN echo '<html><bod>New Startpage</body></html>' > /usr/share/nginx/html/index.html

``````
