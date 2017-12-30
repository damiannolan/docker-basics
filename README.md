# Getting Started with Docker

## Overview

This repository serves as some notes and references to Docker basics for myself and others who wish to use it. I originally made this repository a few months ago, however I have not worked on it since. I may add to this with some extra notes on Docker Compose and Docker Swarm in the future but for now this is it. Enjoy! 

## Images

- The filesystem and configuration of our application which are used to create containers.

To find out more about a Docker image, run docker image inspect alpine. In the demo above, you used the docker image
pull command to download the alpine image. When you executed the command docker container run hello-world, it also did a
docker image pull behind the scenes to download the hello-world image.

## Containers

- Containers are running instances of Docker images - containers run the actual applications.

A container includes an application and all of its dependencies. It shares the kernel with other containers, and runs as
an isolated process in user space on the host OS. You created a container using docker run which you did using the
alpine image that you downloaded. A list of running containers can be seen using the docker container ls command.

## Other Notes

- Docker daemon - The background service running on the host that manages building, running and distributing Docker
  containers.

- Docker client - The command line tool that allows the user to interact with the Docker daemon.

- Docker Store - Store is, among other things, a registry of Docker images. You can think of the registry as a directory
of all available Docker images.

## Useful Commands

Where *alpine* is the image name.

```
docker image pull alpine
```

```
docker image ls
```

```
docker container run alpine ls -l
```

```
docker container run -it alpine /bin/sh
```

```
docker container ls
```

```
docker container ls -a
```

## Part 2

```
docker container run -d seqvence/static-site
```

```
$ docker container stop a7a0e504ca3e
$ docker container rm   a7a0e504ca3e
```

```
docker container run --name static-site -e AUTHOR="Your Name" -d -P seqvence/static-site
```

In the above command:

- -d will create a container with the process detached from our terminal
- -P will publish all the exposed container ports to random ports on the Docker host
- -e is how you pass environment variables to the container
- --name allows you to specify a container name
- AUTHOR is the environment variable name and Your Name is the value that you can pass

See the ports

```
docker container port static-site
```

If you were running Docker for Mac, Docker for Windows, or Docker on Linux, you would see your app at
http://[YourDockerIP]:32773.

You can also run a second webserver at the same time, specifying a custom host port mapping to the container’s
webserver.

```
docker container run --name static-site-2 -e AUTHOR="Your Name" -d -p 8888:80 seqvence/static-site
```

```
docker container stop static-site
```

```
docker container rm static-site
```

```
docker container rm -f static-site-2
```

Pull a specific image version

```
docker image pull ubuntu:12.04
```

To get a new Docker image you can either get it from a registry (such as the Docker Cloud) or create your own. There are
hundreds of thousands of images available on Docker Store. You can also search for images directly from the command line
using docker search.

An important distinction with regard to images is between base images and child images.

Base images are images that have no parent images, usually images with an OS like ubuntu, alpine or debian.

Child images are images that build on base images and add additional functionality.

Another key concept is the idea of official images and user images. (Both of which can be base images or child images.)

Official images are Docker sanctioned images. Docker, Inc. sponsors a dedicated team that is responsible for reviewing
and publishing all Official Repositories content. This team works in collaboration with upstream software maintainers,
security experts, and the broader Docker community. These are not prefixed by an organization or user name. In the list
of images above, the python, node, alpine and nginx images are official (base) images. To find out more about them,
check out the Official Images Documentation.

User images are images created and shared by users like you. They build on base images and add additional functionality.
Typically these are formatted as user/image-name. The user value in the image name is your Docker Cloud user or
organization name.

### Creating a Dockerfile

We want to create a Docker image with this web app. As mentioned above, all user images are based on a base image. Since
our application is written in Python, we will build our own Python image based on Alpine. We’ll do that using a
Dockerfile.

A Dockerfile is a text file that contains a list of commands that the Docker daemon calls while creating an image. The
Dockerfile contains all the information that Docker needs to know to run the app — a base Docker image to run from,
location of your project code, any dependencies it has, and what commands to run at start-up. It is simple way to
automate the image creation process. The best part is that the commands you write in a Dockerfile are almost identical
to their equivalent Linux commands. This means you don’t really have to learn new syntax to create your own Dockerfiles.

### Build an image

Build an image using the Dockerfile created. The "." refers to the current directory - specifying the location of the
Dockerfile.

```
docker image build -t YOUR_USERNAME/app_name .
```

### Run your image

```
$ docker container run -p 8888:5000 --name myfirstapp YOUR_USERNAME/myfirstapp
```

### Dockerfile commands summary
Here’s a quick summary of the few basic commands we used in our Dockerfile.

- FROM starts the Dockerfile. It is a requirement that the Dockerfile must start with the FROM command. Images are created
in layers, which means you can use another image as the base image for your own. The FROM command defines your base
layer. As arguments, it takes the name of the image. Optionally, you can add the Docker Cloud username of the maintainer
and image version, in the format username/imagename:version.

- RUN is used to build up the Image you’re creating. For each RUN command, Docker will run the command then create a new
layer of the image. This way you can roll back your image to previous states easily. The syntax for a RUN instruction is
to place the full text of the shell command after the RUN (e.g., RUN mkdir /user/local/foo). This will automatically run
in a /bin/sh shell. You can define a different shell like this: RUN /bin/bash -c 'mkdir /user/local/foo'

- COPY copies local files into the container.

- CMD defines the commands that will run on the Image at start-up. Unlike a RUN, this does not create a new layer for the
Image, but simply runs the command. There can only be one CMD per a Dockerfile/Image. If you need to run multiple
commands, the best way to do that is to have the CMD run a script. CMD requires that you tell it where to run the
command, unlike RUN. So example CMD commands would be:

```
CMD ["python", "./app.py"]
```

- EXPOSE opens ports in your image to allow communication to the outside world when it runs in a container.
