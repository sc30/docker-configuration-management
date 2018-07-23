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