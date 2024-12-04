# Docker Tutorial 

Docker is a tool that helps you create, package, and run applications in small, lightweight environments called containers. Containers bundle everything your app needs to run, like code, libraries, and settings, so it works the same way on any computer.

Think of a container like a portable box that holds your app and all its parts. No matter where you run the box—on your laptop, a server, or in the cloud—it behaves the same, making it easier to develop, share, and deploy software.

## Basic Commands: 

- To pull an image/ ```docker pull <image_name>```
- To list the images ```docker images```
- To pull an image & create container for the image -  ```docker run <image_name>```
- To remove an image ```docker rmi <image_name>```
- To remove a container ```docker <container_id>``` or ```docker container_name```
- To stop a container ```docker stop <container_id>```
- To list running containers ```docker ps```
- To list all container(including stopped) and to show their properties like status, container_id, name etc ```docker ps -a```
- To remove all containers ```docker system prune -f```

## Docker Commands with options 

- To create container from an image with volume mapping ```docker run -v host/drive/path:container/folder/path  <image_name>```
- To create container from an image in interactive mode(useful when container interaction is required, e.g. linux containers like alpine, busybox) ```docker run -it <image_name>``` 
- To create container and run it in the background ```docker run -d <image_name>```
- To create container with port mapping ```docker run -p hostport:container_port <image_name>```
- To create a container with name ```docker run --name=user_defined_container_name <image_name>```
- To create container with a network ```docker run —network=user_defined_network_name <image_name>```

## Creating a docker image

Below is the template to be followed to create a docker image. And this code needs to be written inside a file with name **Dockerfile**

```
FROM base images required
RUN pre-requisites like installing few software
ENV for setting environment variables
WORKDIR setting your work directory in the container
ADD for copying files to the image e.g. sourcesfile/dir-name targetfile/dir-name
ENTRYPOINT default-command-to-run-when-container-created-fromthis-docker-image
```

Example of a Dockerfile which copies source files of a java project and runs the maven commands when a container is built 

```
FROM maven:3-openjdk-11
WORKDIR /app
COPY src src
COPY pom.xml pom.xml
ENTRYPOINT mvn test -f pom.xml -Dgroups=$TESTS -Dallure.results.directory=target/allure-results
```

- To create an image out of the Dockerfile, use the command: ```docker built -t <user_defined_image_name> <path where the docker file is present>``` E.g. to build an image from the above Dockerfile docker build -t junit_tests . (here . refers to the same folder where the command is executed)
- To create a container from the image and run the tests simply fire the command: ```docker run -e TESTS=consumer-tests -v /Users/santosh/docker-volumemapping:/app/target pact-tests```

## Docker Compose

- Docker Compose is a tool that helps you manage and run multiple Docker containers at once. Instead of running each container manually, you can use a simple text file (called a docker-compose.yml file) to define:
    - Which containers you want to run.
    - How they should interact with each other (like sharing networks or storage).
    - Their configuration, such as environment variables and ports.

```
services:
  [service-name]: 
    image: [image-name]
    container_name: [some-name]
    entrypoint: ["command to invoke"]
    depends_on:
    - [any-other-service]
    working_dir: /a/b/c
    environment:
    - KEY1=value1
    - KEY2=value2
    ports:
    - 80:80
    - 1234:1234
    - 8080:3344
    volumes:
    - ./relative-path/host-path1:/a/b/c
    - /abolute-path/host-path234:/c/d/e
```

Example of docker-compose.yaml file which can create containers of alpine and nginx in the same network. 

```
services:
  nginx_service:
    image: nginx
    ports:
    - 80:80
    container_name: nginx_alp
  alpine_service:
    image: alpine
    container_name: alp_ng
    entrypoint: ping nginx_service
    depends_on: 
    - nginx_service
```

- Once the docker-compose file is ready, you can invoke it using the command: ```docker compose up```
- To run docker compose in detached/background mode use -d option. E.g. ```docker compose up -d```
- To print the logs of the containers created by docker compose use the commands
    - ```docker compose logs``` - for printing logs of all container
    - ```docker compose logs <service_name>``` - for printing logs of a specific service