# Docker

## What is Docker?

#### Docker 
Docker is a platform designed to simplify the process of creating, deploying, and running applications using containers. **Containers** are lightweight, portable, and consistent environments that package an application and its dependencies together. This means that a Docker container can run on any system that supports Docker, without worrying about compatibility issues between the application and the host system.

#### Difference between Docker and VM 
The main difference between Docker and Virtual Machines (VMs) lies in how they virtualize resources and manage isolation for applications. 

Docker containers virtualize at the operating system level. They share the host OS kernel, meaning all containers run on the same operating system but in isolated environments. Containers package only the application and its dependencies, making them lightweight and fast.

VMs virtualize at the hardware level. Each VM runs its own operating system (guest OS) on top of a hypervisor (software that manages multiple VMs on a host machine). VMs include an entire OS, libraries, and the application, making them larger and slower to boot than containers.

## The Docker Architecture
Docker uses a client-server architecture. The server is a **Docker Daemon**. And the client is a **Docker-CLI**. The client and server communicate between them using **REST API** over a UNIX socket or network interface. The client manages objects such as- Images, Containers, Networks, Data Volumes etc.

#### Namespaces
In Docker, namespaces are a fundamental Linux kernel feature that provide the isolation necessary to run containers. There are several different namespaces in Docker:
- pid namespace: Process Isolation
- net namespace: managing network interfaces
- ipc namespace: managing interprocess communication
- mnt namespace: managing filesystem mountpoints
- uts namespace: isolating kernel and version identifiers

#### Control Groups (CGroups)
- Resource Limiting: Limits resources for docker containers to a certain threshold.
- Prioritization: Some groups may get a larger shrare of CPU utilization or disk I/O throughput.
- Accounting: Measures a groups resource usage.
- Control: Freezing groups of process.

#### Union File System (UnionFS)
- Merging: Initially docker containers have a base image. UnionFS merges any new layer of changes with the existing image.
- Read/Write: Branches can be read-only or read-write.

## Installing Docker
The below installation instructions are written for Ubuntu24.04 LTS.

- Step 1: Add user to docker group if not already a member
```bash
sudo usermod -aG docker $USER
newgrp docker
```
- Step 2: Docker Installation
```bash
sudo apt update -y
sudo apt install docker.io -y
sudo systemctl enable docker
```

- Step 3: Check Docker Installation
```bash
docker --version
```

## Creating and Executing Docker Containers
- To create a Docker Container with ubuntu OS - ```docker run -it ubuntu /bin/bash``` (the ```-it``` flag runs the command iteratively, keeping the stdin in the terminal enabled)

## Images vs Containers
#### Docker Images
A Docker image is a read-only template that contains all the instructions and dependencies needed to run an application. It includes the application code, libraries, environment variables, configuration files, and other necessary components.

Images are static and immutable. If you need to update an image, you must rebuild it by modifying the Dockerfile or creating a new version.

Images are created using a **Dockerfile**, which is a script containing instructions to assemble an image step by step (e.g., install dependencies, set environment variables). Images can also be pulled from registries like Docker Hub or other repositories.

- You can take a look at existing docker images using - ```docker images```

- You can inspect a docker image using - ```docker inspect <image-name>```

- To remove a docker image - ```docker rmi <docker-image>```

#### Docker Containers
A Docker container is a runtime instance of a Docker image. It represents the execution of an image in a live, running environment. 

Containers are mutable. A container can change during its lifetime. For example, you can install additional software inside a running container or modify files. However, changes made to a container do not persist in the original image

Containers are created from images using the docker run command. You can start multiple containers from a single image, each running in isolation.

Containers are **ephemeral** by default. They exist only while running, and any changes made are typically lost when the container is stopped or deleted. However, containers can use **volumes** to persist data outside the container’s lifecycle.

- You can check the running containers using - ```docker ps```

- You can also check previously launched containers using - ```docker ps -a```

- You can also start a stopped contrainer using - ```docker start <container-name>```

- After starting the docker container, to interact with it you can - ```docker attach <container-name>```

- If you want to remove all of your existing containers - ```docker container prune```

- If you want to remove a specific docker container - ```docker rm <container-name>```

## Images from the Dockerfile
[Dockerfile Documentation](https://docs.docker.com/reference/dockerfile/)

You can create docker images using Dockerfile -

- If your Dockerfile is in the current directory - ```docker build .``` 

- Otherwise, you can mention the path - ```docker build <Dockerfile path>```

- If you have used anyother filename for the Dockerfile, except for the default "Dockerfile" name - ```docker build -f <Dockerfile name>```

If you build it this way you will see that the docker image has no repository name and TAG. You can assign it using - 
```bash
docker tag <ImageID> <dockerhub-username>/<repo-name>:<tag> .
```

If you want to create a docker image using Dockerfile with Repo and Tag name -
- ```docker build -t <repo-name>:<tag> .```

Now that you have a docker image, you can run it using - ```docker run -d <repo-name>``` (the ```-d``` flag runs the command in detached mode)

If you want to detach from a container without stopping the container - ```CTRL + p``` and then ```CTRL + q```

## Port Mapping
If you used ```EXPOSE 8080``` or ```EXPOSE 5000``` in your Dockerfile, it will expose that port for the container. By default, docker containers work using a network called the Bridge Network. The ports can be accessed using the containers' IP Address. You can get the containers IP address using - ```docker inspect <container-name>```. You can access an exposed port using - ```curl <IP-address>:<port>```

If you want to bind the exposed port to the host then you can do so with the publish ```-p``` or publish all ```-P```.

For example, I have exposed 5000 port in my dockerfile, and after building the docker image, I can run it on a specific port on the host using - 
```bash
docker run -d -p <host port>:<container port>
curl localhost:<host port>
```

## Docker Networks
To look at existing networks on the localhost - ```docker network ls```

If we want to get the IP address from a container - ```ip addr show```

If we run multiple containers on the same network, and then we want to see all of the containers - ```arp-scan --interface=eth0 --localnet```

There are three types of networking for Docker -

- Bridge Network

- Host Network - ```docker run -it --network=host <repo-name>```

- None Network - ```docker run -it --network=none <repo-name>```

## Docker Volumes
[Docker Volumes Tutorial](https://spacelift.io/blog/docker-volumes)

Different layers make up a docker image. The last layer is a writable layer. But whenever the container stops, all of the data is gone. To solve this docker provides three options - 

- Bind Mounts - Mounting a file or directory that is in the host and binding it to the container. The mounts are on the host, we just need to know the path of the mount. 

- Volumes - Volumes are managed by Dockers. You don't need to know the exact file paths. They can be stored on the host but also externally for example, AWS S3, Google Cloud Storage etc.

- In-memory Storage (tmpfs) - Temporary file system. Commonly used to store sensitive information such as Access Tokens etc.

#### Creating a Docker Volume
- First create a Dockerfile and build the docker image - ```docker build -t sample-docker-image .```

- Create a Docker volume - ```docker volume sample-docker-volume```

- Run the Docker container using the previously created Docker Image and Volume - 
```yml
docker run -d --name sample-docker-container -v sample-docker-volume:/<path> sample-docker-image
```

- Now you can put some data, for example - a .txt file in this path.

This will create a Docker container named ```sample-docker-container``` along with a docker volume named ```sample-docker-volume```.

Now to test that the Docker volume actually works - 

- Create another docker image and run the container.

- Access the path that was the volume path. 

- Now see if you can access the data.

To remove a docker volume - ```docker volume rm <volume-name>```

## Tagging
If we build an Image without specifying any tags, by default it will use the tag "latest" - ```docker build -t <image-name> .```
We can also change the tag of a built image using - ```docker tag <image-name>:<previous tag> <image name>:<new tag>```

If we want to tag a docker image using the GitHub commit ID, we can use - 
```yml
docker build -t <repo-name>:<service>-${{ github.sha }}
```

## DockerHub
Dockerhub is a docker image repository. To push your image into DockerHub -

- Create a DockerHub account.

- Create a Repository.

- Best practice is to build your image in this format - 
```yaml
docker build -t <Dockerhub username>/<repo-name>:<service>-${{ github.sha }}
```

- To push your image into docker - 
```yaml
docker push <dockerhub username>/<repo name>:<service>-${{ github.sha }}
```


