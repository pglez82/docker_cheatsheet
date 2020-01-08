## Basic usage
Docker allows us to create containers. It has many usages, as we will see in this tutorial, but lets start with something basic. Lets create a **container** using an **image**. We have thousands of images with preisntalled software. In [Docker Hub](https://hub.docker.com) we can browse them. Each image usually will come in different flavours that are called **tags**. So, we can use for instance ubuntu:16.04, ubuntu:18.04 or ubuntu:latest for the last version of Ubuntu Server.
```
docker pull ubuntu:18.04
```
Now, we have the image, we can create as many containers as we want with it. Lets **create a container**:
```
docker run --name test -it  ubuntu:18.04
```  
Option *--name* allows us to give a name to the container and the option *-it* executes /bin/bash so we can work in the command line inside the container. This is not always neccessary, for instance if we use the container for running a service (we'll do that later).

If we exit the container it will stop. To **start it again** a get a command line:
```
docker start -i test
```
To **list the containers** that we have in our system:
```
docker ps --all
```
To **remove a container**:
```
docker rm test //test is the name of the container
```
