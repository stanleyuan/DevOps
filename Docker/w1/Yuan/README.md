# Docker Week 1 Yuan

# Explain

## What is virtualization?

### Answer

- A hypervisor seperates the hardware resources and divides them into different virtual environments.

- Hypervisors can be on top of OS or be installed directly on hardware.

- Users run virtual machines within the virtual environments.

- VMs interact with the hardware resources through hypervisor.

- Virtualized components:
  - computing power, through hardware-assisted but limited access to the host machine's CPU and memory
  - one or more physical or virtual disk devices for storage
  - a virtual or real network inferface
  - any devices such as video cards, USB devices
  - other hardware that are shared with the virtual machine.

### References

- [What is virtualization?](https://opensource.com/resources/virtualization)
- [What is virtualization?](https://www.redhat.com/en/topics/virtualization/what-is-virtualization)

## What is containerization?

### Answer

- an isolated process sharing the same Kernel, libraries needed for the program running inside the container with the host machine.

- ARM Linux systems run ARM Linux containers, x86 Linux systems run x86 Linux containers, x86 Windows systems run x86 Windows containers.

- use kernel techs, like namespace and cgroup, to seperate the environments.

- Docker
  - namespace: seperates environments
    - pid
    - user id
    - network
- cgroup: resources management
  - CPU
  - memory
  - IO

### References

- [What are Linux containers?](https://opensource.com/resources/what-are-linux-containers)

## What is are the differences between VM and Container?

| Differences |       Container      |     VM     |
|:-----------:|:--------------------:|:----------:|
|    speed    |        faster        |   slower   |
|   whole OS  |          NO          |     YES    |
|     Tech    | namespace and cgroup | hypervisor |
|  Character  |        Process       |   System   |
|    Kernel   | Same as host machine |  different |

## What is a Docker Image?

### Answer

- Build up with differenct "Container Layer". Each layer reprecents an instructions in the image's Dockerfile.

- An Image is an ordered collection of root filesystem changes and the corresponding execution parameters for use within a container runtime.

- read-only

### References

- [About images, containers, and storage drivers](https://docs.docker.com/v17.09/engine/userguide/storagedriver/imagesandcontainers/)

## What is a Docker Container?

### Answer

- A process executed from a docker image.
- consist of
  - DockerImage
  - execution environment
  - standard set of instructions
- There is a additional writable layer on top of docker image.

### References

- [Docker overview](https://docs.docker.com/engine/docker-overview/)

## What is Docker Architecture?

### Docker Engine

- Docker Daemon
  - RESTful API

- Docker Client
  - Docker CLI

- Management
  - image
  - container
  - network
  - volume

## What is the lifecycle of a Docker Container?

### Answer

- Created
    - created but not started yet
- Running
- Paused
    - process paused
    - SIGSTOP
- Exited
    - finish and completed
- Dead
    - daemon tried and failed to stop

### References

- [Get Started with Docker Lifecycle](https://medium.com/future-vision/docker-lifecycle-tutorial-and-quickstart-guide-c5fd5b987e0d)

# Implement

## Set up the environment

- [Get Docker Engine - Community for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

```sh
$ docker --version
Docker version 19.03.5, build 633a0ea838

$ systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-01-09 10:53:06 CST; 1 weeks 3 days ago
     Docs: https://docs.docker.com
 Main PID: 31331 (dockerd)
```

## Pull Ubuntu image

```sh

# Search

$ docker search ubuntu

NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   10386               [OK]
dorowu/ubuntu-desktop-lxde-vnc                            Docker image to provide HTML5 VNC interface …   385                                     [OK]
rastasheep/ubuntu-sshd                                    Dockerized SSH service, built on top of offi…   239                                     [OK]
consol/ubuntu-xfce-vnc                                    Ubuntu container with "headless" VNC session…   207                                     [OK]
ubuntu-upstart                                            Upstart is an event-based replacement for th…   102                 [OK]

# Pull

$ docker pull ubuntu:18.04
18.04: Pulling from library/ubuntu
5c939e3a4d10: Pull complete
c63719cdbe7a: Pull complete
19a861ea6baf: Pull complete
651c9d2d6c4f: Pull complete
Digest: sha256:8d31dad0c58f552e890d68bbfb735588b6b820a46e459672d96e585871acc110
Status: Downloaded newer image for ubuntu:18.04
docker.io/library/ubuntu:18.04

# List

$ docker images | grep ubuntu

ubuntu 18.04   ccc6e87d482b        3 days ago          64.2MB
ubuntu <none>  7698f282e524        8 months ago        69.9MB
```

## Install Nginx in ubuntu container

```sh
$ docker run -d --name docker-w1 ubuntu:18.04
7cd1c3d3f8a9a12750ccae3e6f804e47d49c18e30e9728dccb33d849ea15aa23

$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
7cd1c3d3f8a9        ubuntu:18.04        "/bin/bash"         18 seconds ago      Up 2 seconds                            docker-w1
```

## See Hello World
