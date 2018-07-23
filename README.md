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
````



