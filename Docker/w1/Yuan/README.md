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

The container will exit immediately. The reason is Docker requires command(s) to keep running in the foreground. Otherwise, it thinks that application is stopped and it shutdown the container.

It is required to allocate a tty and keep stdin opened (interactive).

- [Docker container stop automatically after running](https://webkul.com/blog/docker-container-will-automatically-stop-run/)
- [Why does container exit immediately?](https://www.reddit.com/r/docker/comments/8ip97p/why_does_container_exit_immediately/)

```sh
$ docker run -it ubuntu:18.04 /bin/bash
# $ docker run -dit ubuntu:18.04 /bin/bash
# $ docker run --rm -it ubuntu:18.04 /bin/bash

$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
cae27cdca0c1        ubuntu:18.04        "/bin/bash"         17 hours ago        Up 17 hours                             docker-w1
cf2b35f10e57        ubuntu:18.04        "/bin/bash"         18 hours ago        Up 18 hours                             distracted_feistel

# in container
$ apt update && apt install nginx

$ service nginx start

$ service nginx status
 * nginx is running

$ cat /etc/hosts
127.0.0.1   localhost
::1         localhost ip6-localhost ip6-loopback
fe00::0     ip6-localnet
ff00::0     ip6-mcastprefix
ff02::1     ip6-allnodes
ff02::2     ip6-allrouters
172.17.0.2  a52350de4ffa
```

## See Hello World from the container

In container:
```sh
$ cat "HelloWorld" > /var/www/html/helloworld.html
```

In local host:
```sh
$ ifconfig docker0
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:17ff:feb9:5d15  prefixlen 64  scopeid 0x20<link>
        ether 02:42:17:b9:5d:15  txqueuelen 0  (Ethernet)
        RX packets 9964  bytes 648340 (648.3 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 28162  bytes 36818739 (36.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

$ curl 172.17.0.2/helloworld.html
HelloWorld
```
