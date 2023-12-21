# Docker Commands

### docker --version

Return the version of the docker

### docker --help

Return list of commands available in docker along with possible flags

### docker pull <imagename>

It will pull new image from docker hub

**Example:**

```
docker pull ubuntu

```

### docker images

It will list down all the images in your local repo

**Example:**

```
docker images

```

### docker run

It will execute docker image on your local repo and create container out of it
**Example:**

```
docker run ubuntu

```

### docker build

It is used to compile the dockerfile, for building custom docker image

**Example:**

```
docker build -t myUbuntuImage

```

### docker container

It is used to perform various operaions on container

**Example:**

```
docker container logs
docker container kill
docker container rm
docker container run
docker container start

```

### docker push

It is used to push docker image from your local to docker hub

**Example:**

```
docker push mayank/myImage

```

### docker ps

It is used to list out all running containers in the host
if -a flag is used then it will show all running and shutdown containers

**Example:**

```
docker ps
docker ps -a

```

### docker stop

This command will shutdown the container whose container id is mentioned in arguments.

**Example:**

```
docker stop <containerID>

```

### docker kill

This command will kills the container by stoping its execution immediately. It will force kill

**Example:**

```
docker kill <containerID>

```

### docker rm

This command will remove the container whose container id is mentioned in arguments.

**Example:**

```
docker rm <containerID>

```

### docker rmi

This command will remove the image whose name is mentioned in arguments.

**Example:**

```
docker rmi myImageName

```

### docker exec

This command is used to access an already running container and performs operations inside it.

**Example:**

```
docker exec -it <containerID> bash

```

### docker commit

This command will modify the existing image by our new commits.

**Example:**

```
dockercommit <containerID> mayank/myModifiedImage

```

### docker export

This command will export the image into local tar file

**Example:**

```
docker export --output="latest.tar" myContainer

```

### docker import

This command will import the content of tar file

**Example:**

```
docker import <path>

```

### docker compose

This command is used to power multi-container applications where various services hosted inside different container

**Example:**

```
docker-compose build
docker-compose up
Note: this command will execute where yaml file is present 

```

### docker restart

This command is used to restart the stopped container

**Example:**

```
docker restart <containerID>

```

### docker volume

This command creates a volume so that the docker container can use it to store data.

**Example:**

```
docker volume create

```