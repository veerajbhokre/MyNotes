# Docker

## Introduction

- Physical servers
    - Many challenges while using physical servers.
    - We need to configure different physical servers.
    - Need to install Os on each server.
    - Difficult to manage.

- Virtual Machines
    - This will create a VM on top of the physical server.
    - These VMs will have their own OS.
    - Not very lightweight
    - OS itself will consume 1 CPU and some memory

- Containers
    - It shares the same kernel.
    - Very lightweight
    - If the Application is built on cent OS and DB is built on Ubuntu OS, then you can use the base image and create
      your image from that base image.
    - Similarly, you can use a base image of python and Java etc.
    - Base image downloads only required dependencies needed.
    - All containers are fully isolated from each other.

Example:

- Build an image from alpine Linux
- It should print Hello from Alpine on the run
- It should use the latest image of alpine
- the image you build should have named as myalpineimg and the tag as 2020

```dockerfile
FROM aline:latest

CMD echo "Hello from Alpine"
```

To build the image,

`docker build . -t myalpineimg:2020`

To run the image,

`docker run myalpineimage:2020`

---

## Images

**1. Some example to understand,**

**Example 1:**

- Create a website and copy index.html file in it.
- The page should be available on localhost:8080

```dockerfile
FROM httpd:2.4

COPY index.html /usr/local/apache2/htdocs/
```

Build:

`docker build . -t mywebsite:1.0`
Run:

`docker run -p 8080:80 mywebsite:1.0`

Here, While running the container we have provided -p 8080:80

- This means port 8080 from local will map to port 80 in container
- And hence you will be able to access index.html as `localhost:8080`

**2. COPY vs ADD**

- Both works the same way.
- It just copies the content into image.
- But ADD also supports adding from URLs (not recommended from docker it connection drops are not supported)
- Another important difference is, while ADD the tar files it will extract the file and copy its content.

**3. RUN vs CMD vs ENTRYPOINT**

- RUN: It executes when building the image
    - Ex: Use curl to download something which will be used in image.
- CMD & ENTRYPOINT are used while container is running.
    - ENTRYPOINT
        - It is for the main thing that we want to run.
        - Like spring boot jar file
        - Usually for process id 1
    - CMD
        - It provides additional parameters you want to pass into.

### Challenge:

- Create a image and run which will download python and run ping commands 5 times.
- It also takes IP as an argument while running container

```dockerfile
FROM alpine
# Runs on apline linux inside image not on your mac
RUN apk add python3

CMD ["2.2.2.2"]

ENTRYPOINT ["ping","-t","5"]
```

```shell
docker run mywebsite:2.0

> PING 2.2.2.2 (2.2.2.2): 56 data bytes

docker run mywebsite:2.0 9.9.9.9

> PING 2.2.2.2 (2.2.2.2): 56 data bytes
```

**4. Expose PORT:**

- Expose instruction informs docker that container listens on the specified port.
- It is used for container to container communication
- EXPOSE does not actually exposes publish the port but just tells that container is ready to listen on port specified.
- You can use publish with expose to externally expose the port.
- For publish you can use -p 8080:80
- Another option is -P which will exposes the random port on machine and maps it to exposed port.

```dockerfile
FROM httpd:2.4
EXPOSE 90
COPY . /usr/local/apache2/htdocs/
```

```shell
docker run -d -P mywebsite:2.0
> 0.0.0.0:55001->80/tcp, 0.0.0.0:55000->90/tcp
```

**5. Dockerfile name**

To build the image with different docker file name and different location

`docker build -f myfile.prod . -t mywebsite 2.0`

**Challenge for image:**

- Use CentOS base image
- Install web server
- yum -y install httpd
- copy the tar file and extract it into path,
    - /var/www/html
- Start web server using /usr/sbin/httpd
- Pass parameters
    - D and FOREGROUND
- Call the image challenge:2
- image port should be 80

```dockerfile
FROM centos

RUN yum -y install httpd

COPY website.tar.gz /var/www/html

CMD ["-D", "FOREGROUND"]

ENTRYPOINT ["/usr/sbin/httpd"]

EXPOSE 80
```

```shell
docker build . -t challenge:2

docker run -d -p 8080:80 challenge:2 
```

---

# HouseKeeping

View Images:

`docker image ls`

Delete Image:

`docker image rm <id/name>`

Delete All images:

`docker image rm $(docker image ls -aq)`

View Containers:

`docker container ls or ps`

Stop Container:

```shell
docker container stop <id/name>
docker container stop $(docker container ls -aq)
```

Prune Containers:
Remove all stopped containers.

`docker container prune`

Give a name to container:

`docker container run -d -p 8080:80 --name="mywebsite" httpd:2.4`

Volume Mount:

- When we mount the file and make any changes to it, it automatically gets reflected to container
- Earlier we added an index.html in container at specific location
- Now when you want to change in that file you need to exec to container and make changes
- But when you mount the same file from your disk then any changes in that file will be automatically reflected.

`docker container -p 8080:80 -v /user/veerub/index.html:/usr/local/apache2/htdocs/ httpd:latest`

- v source:destination Three types of volumes:
    - v source<host>:destination<container path>
      -v container_path (This will mount to an anonymous directory inside /var/lib/docker/volume ). You will not have
      access to that directory. It is automatically created.
    - Named volume:
        - It is similar to anonymous volume but here you can give reference name to the automatically generated
          directory -v name:container_path

Tag Rename:

`docker tag myweb:1 mywebserver:1.0`

EXEC into container:

```shell
docker container exec -it <cid> /bin/bash

# -it -> interactive mode and input/output terminal
```

---

# Repository

Important commands:

```shell
docker login
docker logout
docker image push <image name>
docker image pull <image name>
```

---

# Advance Docker

### Layers:

Each instruction creates a new layer

To check the image layer below are some important commands:

`docker image history port:1`

`docker image inspect port:1`

Below is a sample Dockerfile, we will see how we can reduce the size of this docker file,

```dockerfile
FROM ubuntu:16.04
RUN apt-get update
RUN apt install curl -y
RUN apt install ruby -y
RUN apt install python -y
RUN apt install build-essential -y
RUN apt install apache2 -y
```

Here every instruction will creates a new layer So we can reduce the layers by running all commands in one RUN like
below,

```dockerfile
FROM ubuntu:16.04
RUN apt-get update &&/
 apt install curl -y &&/
 apt install ruby -y &&/
 apt install python -y &&/
 apt install build-essential -y &&/
 apt install apache2 -y
```

We can also reduce the size significantly by using alpine version of linux,

```dockerfile
FROM alpine
RUN apk update &&/
 apk add curl &&/
 apk add ruby &&/
 apk add python &&/
 apk add build-essential &&/
 apk add apache2
```

### Import/Export

- We can export the image from running container and again run the exported image
- This exported image will also reduce the layers and size of image

```shell
docker export <cid> > ./export.tar

docker export.tar | docker import layer:3
```

### Multistage Images

- Here image is build with multiple layers
- The stages above will create a temp container and it will remove the container once the image is build
- Image will contain the last stage only

```dockerfile
FROM openjdk:9-jdk-slim AS build
COPY certificates /usr/local/share/ca-certificates/certificates
RUN apt-get update && apt-get install --no-install-recommends -y -qq ca-certificates-java && \
  update-ca-certificates --verbose

FROM openjdk:9-jre-slim
COPY --from=build /etc/ssl/certs/java/cacerts /etc/ssl/certs/java/cacerts
RUN groupadd --gid 1000 java && \
  useradd --uid 1000 --gid java --shell /bin/bash --create-home java && \
  chmod -R a+w /home/java
WORKDIR /home/java
USER java
```

---

TODOs:

Layers

https://jessicagreben.medium.com/digging-into-docker-layers-c22f948ed612

Process container and VM

https://jessicagreben.medium.com/what-is-the-difference-between-a-process-a-container-and-a-vm-f36ba0f8a8f7

Docker Architecture

https://medium.com/geekculture/the-docker-architecture-7f423a6c3b5c