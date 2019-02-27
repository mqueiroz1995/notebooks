# Docker <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Introduction](#introduction)
- [How does it works?](#how-does-it-works)
- [Docker Registry](#docker-registry)
- [Docker vs Virtual Machines](#docker-vs-virtual-machines)
- [Cheatsheet](#cheatsheet)
    - [General](#general)
    - [Container Lifecycle](#container-lifecycle)
    - [Dockerfile Instructions](#dockerfile-instructions)
- [References](#references)

## Introduction

Docker is an open-source tool intended to make the process of creating, deploying and running applications easier. It is used for running applications in an isolated environment, called container. 

## How does it works?

Docker works in a really simple way. In docker each isolated environment is called container.

The containers are obtained by running images. An image is a lightweight, stand-alone, executable package of a piece of software that includes everything needed to run it: code, runtime, system tools, system libraries, settings.

And the images are built from Dockerfiles. That are plain text files with instructions on how to build a image. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

TODO: Add image: https://www.pinterest.com/pin/326511041717099391/

## Docker Registry

One of the instructions you can use in a Dockerfile is the `FROM` instruction. This instruction will receive as argument one docker image name and tag and include all the instructions of this image in the new one. This is one of the most powerful features of Docker because it avoids a lot of code rewriting. 

Docker Registries are store houses that provides those `images` to other developers. They work with Docker as npm works with Javascript.

## Docker vs Virtual Machines

Why we just don't use Virtual Machines?

As everything in computer science, the usage of container over virtual machines is a trade-off. The separation in sandboxes is not quite as extreme as in VM but is enough and as a result containers consume much less server resources.

You can read more about the trade-offs in the following post: [Why Docker? Pros and Cons - Aram Koukia](https://koukia.ca/why-docker-pros-and-cons-949d104478c5)

## Cheatsheet

[Docker Reference](https://docs.docker.com/engine/reference)

### General

* `docker build [options] <build-directory>` 
    * `-t`  allows you to specify a friendly name for the image and a tag, commonly used as a version number.
    * `--no-cache=<true|false>`  
* `docker ps [options]` is used to display all executing docker containers.
* `docker container ls [options]` list containers.
* `docker inspect [options] <id|short-id|name>` Docker inspect provides detailed information on constructs controlled by Docker.
* `docker logs [options] <id|short-id|name>` Fetch the logs of a container

### Container Lifecycle

TODO: ![alt text](imgs/docker-container-lifecycle.png)
[Lifecycle of Docker Container - FoxuTech](https://foxutech.com/lifecycle-of-docker-container/)

* `docker run [options] <image-name>` is used for running a docker image. Some of the options are:
    * `--name <name>` - Handy way to add meaning to a container
    * `-p [<host-port>]:<container-port>` - If a service needs to be accessible by a process not running in a container, then the port needs to be exposed via the Host. This is the command to do so. By default, the port on the host is mapped to 0.0.0.0, which means all IP addresses. You can specify a particular IP address when you define the port mapping, for example, -p 127.0.0.1:6379:6379
    * `-v <host-dir>:<container-dir>` - Binds a host directory on the host to a directory in the container. When a directory is mounted, the files which exist in that directory on the host can be accessed by the container and any data changed/written to the directory inside the container will be stored on the host. This allows you to upgrade or change containers without losing your data. Docker allows you to use $PWD as a placeholder for the current directory.
    * `-d` - With this option the container runs detached from foreground, or rephrasing it, in background.
    * `-it` - Allows interaction with container
    * `-e <environment_variables>` - Sets environment variables in the container.
* `docker start [options] <id|short-id|name>` - Start the container, if present in stopped state.
* `docker stop [options] <id|short-id|name>` - Stop a running container (send SIGTERM, and then SIGKILL after grace period). The main process inside the container will receive SIGTERM, and after a grace period, SIGKILL.
* `docker kill [options] <id|short-id|name>` - Kill a running container (send SIGKILL, or specified signal). The main process inside the container will be sent SIGKILL, or any signal specified with option --signal.
* `docker rm [options] <id|short-id|name>` is used to destroy a container.

### Dockerfile Instructions

* `FROM <image-name>:<tag>` will define a base image.
* `RUN <command>` allows you to execute any command as you would at a command prompt.
* `COPY <src> <dest>` allows you to copy files from the directory containing the Dockerfile to the container's image.
* `EXPOSE <port>` will tell Docker which ports should be open and can be bound too.
* `CMD <command>` command in a Dockerfile defines the default command to run when a container is launched. If the command requires arguments then it's recommended to use an array, for example ["cmd", "-a", "arga value", "-b", "argb-value"], which will be combined together and the `command cmd -a arga value -b argb-value` would be run.
* `ENTRYPOINT` defines a command which can have arguments passed to it when the container launches.
* `WORKDIR <directory>` future commands will be executed from the directory.

## References

* https://www.wikipedia.org/
* https://www.youtube.com/watch?v=YFl2mCHdv24
* https://koukia.ca/why-docker-pros-and-cons-949d104478c5
* https://www.pinterest.com/pin/326511041717099391/
* https://www.katacoda.com/courses/docker/
* https://docs.docker.com/engine/reference/commandline/
* https://foxutech.com/lifecycle-of-docker-container/
