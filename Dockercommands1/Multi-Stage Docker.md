https://docs.docker.com/build/building/multi-stage/

#####  Using Multi-Stage Docker Builds
- The builder pattern (which is different from the builder design pattern) is a clever technique for creating Docker images that are as small as possible. It involves two Docker images working together to selectively copy only the essential parts of your application from one image to the other.
- The first image is called the "build image". It's like your workshop, where you have all the tools you need to build your application from the source code.
- This includes things like compilers, build tools, and any libraries or dependencies your application needs during the build process.
- The second image, aptly named the "runtime image", is where the magic happens. It's the environment where your compiled application actually runs.
- This image is a minimalist, containing only the essential components: your executable files, any necessary runtime dependencies.

###### Multi-Stage DockerFile
- In a multi-stage Dockerfile, you use the COPY --from=n instruction to selectively copy the necessary files (like your compiled application) from one stage to another.
- AS keyword after the FROM directive
``````sh
FROM golang:latest AS builder
WORKDIR /app
COPY hellodocker.go .
RUN go build -o hello hellodocker.go

FROM scratch
WORKDIR /app
COPY --from=builder /app/hello .
ENTRYPOINT ["./hello"]

docker build -t hello-multistage:v1
docker images ls
``````
##### End
``````sh


``````
