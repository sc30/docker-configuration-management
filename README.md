# Managing Images

````bash
docker images

# force deleting image id
docker rmi -f <image_id>

# When docker runs completely, it will shutdown automatically
docker ps

# Docker is going to look for ubuntu 14.04 images first, if it is not in local, it will look this image at docker hub and download it
docker run ubuntu:14.04 /bin/echo 'Hello World'
docker images

# Pull an image to the registry
# sc30ucc is docker hub username, get-started is docker image, part1 is the tag
# docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
# docker pull --help
docker pull sc30ucc/get-started:part1
# Running command line of this image
docker run -t -i sc30ucc/get-started /bin/bash
# exit
exit

# Dockerfile
FROM ubuntu:14.04
RUN apt-get update && apt-get install -y ruby ruby-dev
RUN gem install sinatra

# build docker from the current directory, it will take all files from this folder
# -t for creating a tag
docker build -t sc30ucc/get-started .

# Push to docker hub
docker push schen/get-started

# check docker history with image_id
# all the layers make up the complete docker image
docker history <image_id>

# change docker tag
docker tag <image_id> schen/get-started:prod

# Importing, Deleting, and Exporting Images
docker pull
docker import

docker export --output="mylatest.tar" <image_id>

# delete all containers
docker rm $(docker ps -a -q)

# delete all images
docker rmi $(docker images -q)

# remove all images which have none as the repository name
docker rmi $(docker images | grep "^<none" | aek '{print $3}')
````

# Managing Image Layers

````bash
# Containers, Layers and Images
docker ps -a
docker ps

# history is made up of layers
docker history <image_id>

# Methododologies of image layering
docker build -t sc30ucc/get-started:v2

# Building a container interactively
docker search centos
docker run -t -i centos /bin/bash

# DockerFile

````

# Working with the dockerfile

````bash
# build dockerfile
docker build -t <tag> .
docker run -d --name myguest2 -P centos

# Building an Apache Image
FROM httpd:2.4
docker build -t my-apache2 .
docker run -i -t my-apache2

# Accessing Firefox from a Container
# Container DISPLAY equals to $DISPLAY which is our shell display
# Mounting volumes by doing -v /tmp/.X11-unix/tmp/.X11-unix
docker run -ti --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/tmp/.X11-unix firefox
# Exercise, run FUSIONWORKS

# Building a WordPress Image
md wordpress
# delete docker image with a specific tag
docker rmi debian:jessie
````

# Advanced Docker Volumes

````bash
# Building from Basic Volumes
docker run -v /www --name="bobexample1" ubuntu:14.04 echo yo
# check volumes status
docker ps -a
# insepect
docker inspect bobexample1

"Mounts": [
{
"Type": "volume",
"Name": "13cda9177b13cadf2a9e901bfb7dc785d188f4ed68b933770293959a5d22c28d",
"Source": "/var/lib/docker/volumes/13cda9177b13cadf2a9e901bfb7dc785d188f4ed68b933770293959a5d22c28d/_data",
"Destination": "/www",
"Driver": "local",
"Mode": "",
"RW": true,
"Propagation": ""
}
],

# create another volume
docker run -v /host/logs:/container/logs --name="bobmamaexample" ubuntu:14.04 echo mama

# Dockerfile Volume keyword
docker network inspect bridge
docker-machine ls

# rmove container and its associated volume
docker rm -v <image>

# Make volumes shared between multiple docker containers
docker run -v <directory> --name=bobservice -d busybox
# Use the volume that bobservice is using, bobservice doesn't even have to be running
# because as bobservice stops running, they leave the volumes there until we actually delete them
docker run -d -it -p 9144:9144 --volumes-from bobservice <image_name>
# check Mount information
docker inspect <docker_image>
````

# Networking in Docker

````bash
docker network ls

NETWORK ID          NAME                DRIVER              SCOPE
04574865bb08        bridge              bridge              local
3df75784e23f        host                host                local
1b3d6679c229        none                null                local
aa6ef014c7da        simple-network      bridge              local

# by default it will create a bridge network
docker network create simple-network
# inspect simple-network
docker network inspect simple-network
# remove simple-network
docker network rm simple-network
# see all docker network commands
docker network COMMAND
# connect docker container to a network
docker network connect <network> <docker image name>

# Configure DNS
docker run -itd --name=bobcontainer1 busybox
# attach a container
docker attach bobcontainer1
# dettach a container
CTRL+Q
# see DNS configuration
mount

# Communication between two containers
docker-machine ls
docker run -itd --name=bobcontainer1 busybox
docker run -itd --name=bobcontainer2 busybox
docker network inspect bridge

# when you start up a docker container, and you don't specifiy which machine or which network to go to, i will always go to the bridged network
docker network create bob_network
docker run -itd --name=newbobcontainer10 --net=bob_network busybox
docker network inspect bob_network

# Communication between containers and outside world
docker ps
[sc47@openet-2fd3d3b619-prov-vm grok_exporter]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
5f9ed2d0b2ff        busybox             "sh"                3 minutes ago       Up 3 minutes                            newbobcontainer10
d2744bff0a71        busybox             "sh"                4 minutes ago       Up 4 minutes                            bobcontainer2
dd8e35b769a1        busybox             "sh"                5 minutes ago       Up 5 minutes                            bobcontainer1
# look at PORTS column, it's empty, therefore they would not be able to communicate outside of the machine, but nothing from the outside of the machine or the container can talk to busybox unless container is running in the same network
# boot2docker

# Binding Docker Container Ports to the Host
docker run -d -P # map any port from the machine to the container
docker run -d -p 80:5000 training/webapp python app.py
docker ps
docker port <running instance name>
docker-machine ip my-default-hendry

# Using Pipework
docker run -itd --name=newbobcontainer1 busybox
docker run -itd --name=newbobcontainer2 --net=bob_network busybox
# check github Pipework. to communicate between different containers
````

