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
When we start using docker, we will test many images. These images are not removed from the system and they can fill our harddisk. To **list all the images** in the system:
```
docker image ls
```
It is important to understand that some images are built using others. So if we download an image that has python installed in Ubuntu Server, this one will depend for sure on the Ubuntu image. Thus, we have to be careful with the images that we delete. In order to **remove an image**:
```
docker image rm image:tag //for instance, docker image rm ubuntu:18:04
```
## Using Docker to deploy an application
One use of docker is to execute an application. Doing so we get a controlled enviroment for the application which do not depend on the system configuration and that can be runned in any machine that runs docker. The application will be issolated there, running alone, so everything should be smooth and controlled. Lets see how to do this. Lets image that we have developed a very simple webservice using *Python Flask*. Here is the source code:
#### **`ws.py`**
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run()
```
Save this in ws.py. So as we can see, our web service depends on Flask. Lets create a file requirements.txt with this content:
#### **`requirements.txt`**
```
Flask==1.1.1
```
