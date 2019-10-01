# Docker Cheetsheet

---
#### Terminology

**Keyword:** develop, deploy, and run

**Containerization:** use of linux container to deploy applications

**Features of Docker:** lightweight, scalable, stackable(you can stack services vertically)

**Image:** an executable package that includes everything needed to run an application-the code, a runtime, libraries, environment variabless and configuration files

**Container:** a container is a runtime instance of an image(what image becomes in memory when executed). A container is launched by `running an image`.

**Container & VM:** 

* Container runs `natively` on linux and shares the kernel of host machine with other containers. It runs a discrete process, taking no more memory than any other executable, making it lightweight.

* VM runs a full blown "guest" operating system with virtual access to host resources by hypervisor. VM provides environment with more resources than most application needs.

**Arthitecture Difference:** docker eliminates the extra use of GuestOS. Everything works as docker containers

**Docker Compose** is used to run multiple containers as a single service. For example, suppose you had an application which required NGNIX and MySQL, you could create one file which would start both the containers as a service without the need to start each one separately.

#### Basic Commands

```
$ docker version
```
> Output various details of the Docker version installed on the system

```sh
$ docker info
```
>The output will provide the various details of the Docker installed on the system such as −
 >   * Number of containers
>    * Number of images
>    * The storage driver used by Docker
>    * The root directory used by Docker
>    * The execution driver used by Docker
```sh
$ docker pull <image name>
```
> You can fetch the built image on hub
```sh
$ docker run <-it> hello-world
```
> Create an instance of an image: container
> `-it` is used to mention that we want to run in `interactive mode`
```sh
$ docker run -it ubuntu bash
exit use ctrl+P+Q to ensure the container still exists 
Use nsenter to do this easily ...
```
```
sudo docker run -p 8080:8080 -p 50000:50000 <jenkens> 
```
> Run image "jenkens". Jenken will eventually run as a container on ubuntu machine
`-p` is used to map the port number of the internal docker image to our main ubuntu server so that we can access the container accordingly
```sh
$ docker images <-q>
```
> Display all the images currently installed on the system. `-q`means return the id only
> * TAG: used to logically tag images
> * IMAGE ID: uniquely identify the image
> * CREATED: number of days since the image is created
> * VIRTUAL SIZE: size of image
```sh
$ docker rmi <image id>
```
> remove docker image on system
```sh
$ docker inspect <image name>
```
> Show detailed information on the image
```
$ docker ps <-a>
```
> List all the running containers on the machine
> `-a`list all containers on the system
```
$ docker history <image id/name>
```
> list all commands run against that image via a container
```
$ docker top <container id>
```
> show the top level `processes` within a container. list such as PID
```
$ docker stop <container id>
```
> Stop a running container
```
$ docker rm <container id>
```
> Delete a container
```
$ docker stats <container id>
```
> Provides the statistics of a running container such as CPU & Mem usage
```
$ docker attach <container id>
```
> Use docker attach to attach your terminal’s standard input, output, and error (or any combination of the three) to a running container using the container’s ID or name. This allows you to view its ongoing output or to control it interactively, as though the commands were running directly in your terminal.
```
$ docker pause <container id>
$ docker unpause <container id>
```
> Pause all processes in a running docker
```
$ docker kill <contain id>
```
> Kill all processes in a running container. Note this does not remove container

---

## Create Image

**Dockerfile** defines what goes on in the environment inside your container. Container creates environment that isolates from the rest of your system, so you need to map port to the outside world and give path about what files you want to copy into that environment from outside. Those are all done in this file.

#### Create Dockerfile

```
$ sudo vim Dockerfile
```
Example 1:
```
# Use an official Python runtime as a parent image
FROM python:2.7-slim
MAINTAINER demousr@gmail.com 
# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```
`app.py` and `requirement.txt` are present becasue of `copy` command of Dockerfile and output of `app.py` is accessible over HTTP thanks to `Expose` command.

requirements.txt
```
Flask
Redis
```
app. py: prints environment variable NAME and output of a call to `socket.gethostname()`. Note accessing the name of host here inside a container will retrieve the container ID, which is like the process ID of a running exe
```
...
html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)
...
```
Example 2: 
```
# from which base image you want to base your image from
FROM ubuntu 
MAINTAINER demousr@gmail.com 

RUN apt-get update 
RUN apt-get install –y nginx 
CMD [“echo”,”Image created”] 
```
#### Build the APP
```
$ docker build -t <ImageName>:<TagName> dir
=> sudo docker build –t myimage:0.1. 
```
> -t: mention a tag to the image
> ImageName: the name to your image
> TarName: the tag you give
> Dir: the directory where the Docker file is present

#### Push to Docker Hub
A. Go to docker hub and creates a repository. Then login docker on command line
```
$ docker login
```
B. Tag your image to the corresponding Repo: `docker tag + imageID + RepoName`
```
$ sudo docker tag ab0c1d3744dd demousr/demorep:1.0
```
C. Docker push: push image to docker hub: `docker push Repositoryname `
```
$ sudo docker push demousr/demorep:1.0 
```








