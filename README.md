## Basic usage
Docker allows us to create containers. It has many usages, as we will see in this tutorial, but lets start with something basic. Lets create a **container** using an **image**. We have thousands of images with preinstalled software. In [Docker Hub](https://hub.docker.com) we can browse them. Each image usually will come in different flavours that are called **tags**. So, we can use for instance ubuntu:20.04, ubuntu:22.04 or ubuntu:latest for the last version of Ubuntu Server.
```
docker pull ubuntu:22.04
```
Now, we have the image, we can create as many containers as we want with it. Lets **create a container**:
```
docker run --name test -it  ubuntu:22.04
```  
Option *--name* allows us to give a name to the container and the option *-it* executes /bin/bash so we can work in the command line inside the container. This is not always necessary, for instance if we use the container for running a service (we'll do that later).

If we exit the container it will stop. To **start it again** and get a command line:
```
docker start -i test //test is the name of the container
```
Note that starting a container after stopped is kind of an anti-patern. We should use our containers in a way that can be deleted and created again. If the container needs to persist any data it should do it through [volumes](https://docs.docker.com/storage/volumes/) in the host filesystem.

To **list the containers** that we have in our system:
```
docker ps --all
```
To **stop a container** we have two options. Try to stop the container in a tidy way:
```
docker stop test //test is the name of the container
```
or just **kill the container**:
```
docker kill test //test is the name of the container
```
To **remove a container**:
```
docker rm test //test is the name of the container
```
When we start using docker, we will test many images. These images are not removed from the system and they can fill our hard disk. To **list all the images** in the system:
```
docker image ls
```
It is important to understand that some images are built using others. So if we download an image that has python installed in Ubuntu Server, this one will depend for sure on the Ubuntu image. Thus, we have to be careful with the images that we delete. In order to **remove an image**:
```
docker image rm image:tag //for instance, docker image rm ubuntu:22:04
```
After using docker of a while you will see that you will start accumulating containers and images. These two commands are useful to **maintain the system tidy**. 
```
docker rm $(docker ps -q -f status=exited) //Remove all exited containers
docker image prune -a //Removes all unused images in the system
``` 

## Using Docker to deploy an application
One use of docker is to execute an application. Doing so we get a controlled environment for the application which do not depend on the system configuration and that can be run in any machine that runs docker. The application will be isolated there, running alone, so everything should be smooth and controlled. Lets see how to do this. Lets image that we have developed a very simple webservice using *Python Flask*. Here is the source code:
#### **`ws.py`**
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(debug=True, host=’0.0.0.0')
```
Save this in ws.py. So as we can see, our web service depends on Flask. Lets create a file requirements.txt with this content:
#### **`requirements.txt`**
```
Flask>=2.2.2
```
If we want to execute the webservice, we only need to run:
```
pip install -r requirements.txt
python ws.py
```
But obviously, this is being executed in our machine. What happens if someone accidentally uninstall Flask, our if we want to develop another service with another Flask version. This is when Docker works very well. Lets see how we can deploy this hello world webservice using a docker container. Lets create a **Dockerfile**. A Dockerfile is a file that describes how to create a docker image. For our image we will need an Ubuntu Server with python. We could use the ubuntu image that we had and install python in it but can use an image that already has python:
```
FROM python:3.7
COPY ./src /app
WORKDIR /app
RUN pip install -r requirements.txt
ENTRYPOINT ["python"]
CMD ["ws.py"]
```
So this file indicates that we part **FROM** the python:3.7 image. Then, we **COPY** the contents of the ./src directory (where I have the ws.py and the requirements.txt files) to the /app directory in the image. We set the **WORKDIR** to the /app directory. Then we **RUN** pip install in order to install the dependencies (in this case, only flask). The **ENTRYPOINT** is the command that will be executed when we run a container from this image. With **CMD** we pass the parameters to the previous command (so it will run `python ws.py`).

Once we have the Dockerfile, we can create the image executing:
```
docker build -t hello_world_flask .
```
So we are telling docker that it should create a new image with the name *hello_world_flask* using the docker file that is in this directory (.).

In order to create and run a container using this image:
```
docker run -p 5000:5000 hello_world_flask
```
The *-p* option maps the port 5000 from the container to the port 5000 of our machine, so we can access the webservice. As we can see, the webservice will be running inside the container and accessible from our machine. Using this same configuration we can deploy the container to a cloud service as Google Cloud or Amazon AWS and have our application running in minutes.
