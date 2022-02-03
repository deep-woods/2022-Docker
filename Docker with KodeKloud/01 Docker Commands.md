# <span id='top'>01 Docker Commands </span>

<br>

[[Commands]](#Commands)  
[[Docker Commands Demo]](#demo)   
[[Practice Makes Perfect]](#perfect)  

<br>

## <span id='Commands'>Commands</span>

[[Top]](#top)

<br>

- `docker run [image]`
  - `--detach` or `-d`: a Docker container runs in the background of your terminal
- `docker stop [image]`

- `docker ps`: list processes
  - although not in used, running dockers consume space. 
- `docker ps -a`: list all processes (including dead ones)
- `docker images`

- `docker rm [container]`
- `docker rmi [image]`: be sure to remove all dependencies (`containers`) prior to deleting `image`. 

- `docker pull [image]`

<br>

### `Container` vs `VM`

    CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                          PORTS               NAMES
    2e09b65b1b59        centos              "/bin/bash"         About a minute ago   Exited (0) About a minute ago                       festive_mirzakhani

- `docker run [image]` ----> `STATUS Exited (0)`
  - `Containers` are not meant to host an OS. 
  - `Containers` host specific instances (web application, database) and tasks (computing, analysis)
  - Once the task is complete the container exits a container only lives as long as the process inside it.
  - An image of Ubuntu, for instance, if run, will stop immediately because there is no process or application running in it by default.

<br>

### Examples

- `docker run kodekloud/simple-webapp`
- `docker run -d kodekloud/simple-webapp`
- `docker attach a323f`


## <span id='demo'>Docker Commands Demo</span>

[[Top]](#top)

<br>

### `run`

Pull image from docker hub. 

    $ docker run centos

    Unable to find image 'centos:latest' locally
    latest: Pulling from library/centos
    a1d0c7532777: Pull complete 
    Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
    Status: Downloaded newer image for centos:latest
    
Process is dead as there is no instance running in the image. 

    $ docker ps -a

    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    fbc3720029a9        centos              "sleep 100"         15 seconds ago      Up 13 seconds                           confident_ellis

Start the downloaded `CentOS` image and run something keep the instance alive. 

    $ docker run -it centos bash

    [root@db38dacce2d1 /]# cat /etc/*release*
    CentOS Linux release 8.4.2105
    Derived from Red Hat Enterprise Linux 8.4
    
    [root@db38dacce2d1 /]# exit

<br>

### `ps`

    $ docker run -d centos sleep 30
    ed333c03ff6e6f16826eea03ca11e7770d725bae95e03e112c5f11040488a4b3

    $ docker ps
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    ed333c03ff6e        centos              "sleep 30"          6 seconds ago       Up 3 seconds                            intelligent_lumiere

<br>

### `stop`

    docker stop e499ab98
    de499ab98

    CONTAINER ID   IMAGE    COMMAND        CREATED         STATUS   
    e499ab9826e3   centos   "sleep 2000"   2 minutes ago   Exited (137) 26 seconds ago


<br>  

### `rm`

Reclaim disk space by removing unwanted images.

    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS
    fbc3720029a9        centos              "sleep 100"         5 minutes ago       Exited 
    ed333c03ff6e        centos              "sleep 30"          6 minutes ago       Exited
    ed5b4755d6cc        centos              "sleep 30"          7 minutes ago       Exited 
    db38dacce2d1        centos              "bash"              13 minutes ago      Exited 
    2e09b65b1b59        centos              "/bin/bash"         29 minutes ago      Exited 

    $ docker rm fbc3720029a9 ed333c03ff6e ed5b4755d6cc db38dacce2d1 2e09b65b1b59
    fbc3720029a9
    ed333c03ff6e
    ed5b4755d6cc
    db38dacce2d1
    2e09b65b1b59
 
    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

<br>  

### `pull`

    $ docker pull ubuntu

    Using default tag: latest
    latest: Pulling from library/ubuntu
    08c01a0ec47e: Pull complete 
    Digest: sha256:669e010b58baf5beb2836b253c1fd5768333f0d1dbcb834f7c07a4dc93f474be
    Status: Downloaded newer image for ubuntu:latest
    docker.io/library/ubuntu:latest


<br>  

### `exec`

`exec` command runs or executes a command on a running container.

    $ docker exec 8c52d0438e5f cat /etc/*release*

    NAME="Ubuntu"
    VERSION="20.04.3 LTS (Focal Fossa)"
    ID=ubuntu
    ID_LIKE=debian
    PRETTY_NAME="Ubuntu 20.04.3 LTS"
    VERSION_ID="20.04"
    HOME_URL="https://www.ubuntu.com/"
    SUPPORT_URL="https://help.ubuntu.com/"
    BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
    PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
    VERSION_CODENAME=focal
    UBUNTU_CODENAME=focal

<br>

## <span id='perfect'>Practice Makes Perfect</span>

[[Top]](#top)

<br>

1. Check the version of Docker Server Engine running on the Host.

    $ docker --version
    Docker version 19.03.15, build 99e3ed8919

<br>

2. A good reminder: you cannot delete objects with associated dependencies.

    $ docker rm 58d1369bffa3 e7e536b7cc85 1617091ddeb2 e4e4925293f9 d319a838bd08 bbc60b0996e4
    58d1369bffa3
    bbc60b0996e4
    Error response from daemon: You cannot remove a running container e7e536. Stop the container before attempting removal or force remove
    Error response from daemon: You cannot remove a running container 161709. Stop the container before attempting removal or force remove
    Error response from daemon: You cannot remove a running container e4e492. Stop the container before attempting removal or force remove
    Error response from daemon: You cannot remove a running container d319a8. Stop the container before attempting removal or force remove

<br>

3. Pull an image. Only `pull`, do not _**create a container**_.

  $ docker pull nginx:1.14-alpine

  1.14-alpine: Pulling from library/nginx
  bdf0201b3a05: Pull complete 
  3d0a573c81ed: Pull complete 
  8129faeb2eb6: Pull complete 
  3dc99f571daf: Pull complete 
  Digest: sha256:485b610fefec7ff6c463ced9623314a04ed67e3945b9c08d7e53a47f6d108dc7
  Status: Downloaded newer image for nginx:1.14-alpine
  docker.io/library/nginx:1.14-alpine

<br>

4. Run a `container`. Look, syntax matters here 👀. 

`docker run -d --name [container name] [image]:[version]`

    $ docker run -d nginx:1.14-alpine --name webapp
    0363aecafa3739a41129d3810db06abd7b2d21c1afda483c999a9b32e2e6d4ca

Look, it's breaking because I mixed up the order. 

    docker: Error response from daemon: OCI runtime create failed: container_linux.go:370: starting container process caused: exec: "--name": executable file not found in $PATH: unknown.

The `image` name and `version` tag must always come at the end. 

    $ docker run -d --name webapp nginx:1.14-alpine
    d5225f3a2197b9168e3c710ff3dd7e216b5b0d70610ae8f9077e27832f6aa351

Image successfully created, up and running. 

    $ docker ps
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
    d5225f3a2197        nginx:1.14-alpine   "nginx -g 'daemon of…"   4 seconds ago       Up 2 seconds        80/tcp              webapp