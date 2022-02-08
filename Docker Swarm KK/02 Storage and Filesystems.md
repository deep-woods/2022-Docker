# <span id='top'>02 Storage and Filesystems</span>

<br>

[[Filesystems]](#Filesystems)  
[[A walk-through]](#walk-through)  
[[🤲Hands-on: Explore Storage and Filesystems]](#fs)  
[[References]](#ref)

<br>

## <span id='Filesystems'>Filesystems</span>

[[Top]](#top)

`/var/lib`
├──`apt`
├──`containerd`
├── ...
├──`docker` <----------------- HERE
├──`kublet`
├──`python`
└── ...

    $ pwd
    /var/lib/docker/image/overlay2/imagedb/content/sha256

    cd sha256;ls
    0627ec6901db4b2aed6ca7ab35e43e19838ba079fffe8fe1be66b6feaad694de 7e0aa2d69a153215c790488ed1fcec162015e973e49962d438e18249d16fa9bd
    129dd9f673673e9e8507ac837dcd9eaa3906469c10ef4aa63d0cac1f1dfa6b3a a64a6e03b0551e1cefa94db6cc6677fb1efed3c557d173f79584ff4ec474b5ae
    26c8bcd8b71919f393464e595670e633e4f90c6981df4322d8c4a39332b0bd1e c6e3cd9aae3645a98dd69c15b048614603efce6cda26c60f5f7e867ef68f729f
    62d49f9bab67f7c70ac3395855bf01389eb3175b374e621f6f191bf31b54cd5b ccee4cdf984f11952441cbab30146dae792c8d9fb668e762c78a49e7db858082
    6dbb9cc54074106d46d4ccb330f2a40a682d49dda5f4844962b7dce9fe44aaec

<br>

## <span id='walk-through'>A walk-through</span>

[[Top]](#top)

    ls -l /var/lib/docker
    total 60
    drwx------  2 root root  4096 May  8  2021 builder
    drwx--x--x  4 root root  4096 May  8  2021 buildkit
    drwx--x--x  3 root root  4096 May  8  2021 containerd
    drwx-----x  2 root root  4096 May  8  2021 containers
    drwx------  3 root root  4096 May  8  2021 image
    drwxr-x---  3 root root  4096 May  8  2021 network
    drwx-----x 62 root root 12288 Feb  8 08:54 overlay2
    drwx------  4 root root  4096 May  8  2021 plugins
    drwx------  2 root root  4096 Feb  8 08:54 runtimes
    drwx------  2 root root  4096 May  8  2021 swarm
    drwx------  2 root root  4096 Feb  8 08:54 tmp
    drwx------  2 root root  4096 May  8  2021 trust
    drwx-----x  2 root root  4096 May  8  2021 volumes

### Storage driver

`Storage driver`: creates layers and maintains the layers and the files related to each layer (see [official doc](https://docs.docker.com/storage/storagedriver/)).

### `AUFS` storage driver

AUFS is a union filesystem. The aufs storage driver was previously the default storage driver used for managing images and layers on Docker for Ubuntu, and for Debian versions prior to Stretch. If your Linux kernel is version 4.0 or higher, and you use Docker Engine - Community, consider using the newer overlay2, which has potential performance advantages over the aufs storage driver.

<br>

### [The image layers](https://docs.docker.com/storage/storagedriver/aufs-driver/#how-the-aufs-storage-driver-works)

- `diff/`: the contents of each layer, each stored in a separate subdirectory
- `layers/`: metadata about how image layers are stacked. This directory contains one file for each image or container layer on the Docker host. Each file contains the IDs of all the layers below it in the stack (its parents).
- `mnt/`: Mount points, one per image or container layer, which are used to assemble and mount the unified filesystem for a container. For images, which are read-only, these directories are always empty.

<br>

### `docker history [image ID]`

    docker history docker/whalesay
    IMAGE               CREATED             CREATED BY                                      SIZE
    6b362a9f73eb        6 years ago         /bin/sh -c #(nop) ENV PATH=/usr/local/bin:/u…   0B
    <missing>           6 years ago         /bin/sh -c sh install.sh                        30.4kB
    <missing>           6 years ago         /bin/sh -c git reset --hard origin/master       43.3kB
    <missing>           6 years ago         /bin/sh -c #(nop) WORKDIR /cowsay               0B
    <missing>           6 years ago         /bin/sh -c git clone https://github.com/moxi…   89.9kB
    <missing>           6 years ago         /bin/sh -c apt-get -y update && apt-get inst…   58.6MB
    <missing>           6 years ago         /bin/sh -c #(nop) CMD ["/bin/bash"]             0B
    <missing>           6 years ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\)$…   1.9kB
    <missing>           6 years ago         /bin/sh -c echo '#!/bin/sh' > /usr/sbin/poli…   195kB
    <missing>           6 years ago         /bin/sh -c #(nop) ADD file:f4d7b4b3402b5c53f…   188MB

### Build an image then run the history

Clone a repo.

    $ git clone https://github.com/mmumshad/simple-webapp-docker.git
    Cloning into 'simple-webapp-docker'...
    remote: Enumerating objects: 10, done.
    remote: Total 10 (delta 0), reused 0 (delta 0), pack-reused 10
    Unpacking objects: 100% (10/10), done.

Build the image.

    $ docker build . -t simple-docker
    Sending build context to Docker daemon  62.46kB
    Step 1/5 : FROM ubuntu:16.04
    ...
    Step 5/5 : ENTRYPOINT FLASK_APP=/opt/app.py flask run --host=0.0.0.0 --port=8080
    ---> Running in e4f8d5293cca
    Removing intermediate container e4f8d5293cca
    ---> d1e27491f27f
    Successfully built d1e27491f27f
    Successfully tagged simple-docker:latest

Run the history.

    $ docker history simple-docker
    IMAGE               CREATED              CREATED BY                                      SIZE                COMMENT
    d1e27491f27f        About a minute ago   /bin/sh -c #(nop)  ENTRYPOINT ["/bin/sh" "-c…   0B
    c4eec8db78f6        About a minute ago   /bin/sh -c #(nop) COPY file:29b92853d738987b…   229B
    8c5a50a7482d        About a minute ago   /bin/sh -c pip install flask                    2.62MB
    461fa0dfd369        About a minute ago   /bin/sh -c apt-get update && apt-get install…   297MB
    b6f507652425        5 months ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
    <missing>           5 months ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
    <missing>           5 months ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0B
    <missing>           5 months ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B
    <missing>           5 months ago         /bin/sh -c #(nop) ADD file:11b425d4c08e81a3e…   135MB

Compare against the `🐳Dockerfile`.

    $ cat Dockerfile
    FROM ubuntu:16.04

    RUN apt-get update && apt-get install -y python python-pip

    RUN pip install flask

    COPY app.py /opt/

    ENTRYPOINT FLASK_APP=/opt/app.py flask run --host=0.0.0.0 --port=8080

### Browse image file sizes.

    /var/lib/docker/overlay2 $ du -sh

    20K     05762be05c4b6fa0142ad88a0cbc72b5bc02895c52e4ad8b94bba214a2a9f75f
    9.9M    0ac7a48ed13d70cffb8f84279bb9edd6c7df3df35a8d09298878b16a91b0309f
    48K     0c8a646886ee4dbb119b21f60f8fc3a9ea79300ce7d8f2a910f412fe85b04493
    73M     159001248b1d05ac5c4a2a419663941e86a99b3d5c86526de577da5da85f8154
    136K    19ec7a6503504f8fba5216e40837e620414d2f29016d62491f13cdfd3ad0562c
    28K     22f0954932a0589ae820ced169c3690024ec2d190b84ba38ad6cec9641f9bc65
    1.6M    248fbc45ea697d5b108eb08573f0fef0b3f1f6fbc2591c622dbd731be54324ec
    32K     2c284e80ba2927c89e19b6e0f3c92598fa3c7e192991544f3dafc621074c7275
    24K     30cac1f1d4657c8fc0ffcb6cb160967cc5b1d220a0886fc02de9614138b886e4
    48K     373aa1bd176c2a9581eab51c87616cf025b7cb0a7ac0e3889f6a75c9d3822553
    396K    381d06a5f4772ddf98224fab020329128348ac1d316964ac33eb1b4341f655a8

### Example: `simple-webapp-docker`

This is a simple operation to see `docker`'s layered architecture in action. We create two identical copies of an application (`app.py` and `Dockerfile`) and modify the ENTRYPOINT to see if `docker` really builds on cached layers and creates a new one only when there are discrepancies in the layer pairs.

    $ git clone https://github.com/mmumshad/simple-webapp-docker.git

    $ cd simple-webapp-docker

    $ cp app.py app2.py   <---- then modify the ENTRYPOINT as in the following line.
    ENTRYPOINT FLASK_APP=/opt/app2.py flask run --host=0.0.0.0 --port=8080


    $ docker build . -f Dockerfile2 -t simple-docker2
    Sending build context to Docker daemon  64.51kB
    Step 1/5 : FROM ubuntu:16.04
    ---> b6f507652425
    Step 2/5 : RUN apt-get update && apt-get install -y python python-pip
    ---> Using cache            <------------------------- USING CACHE
    ---> 461fa0dfd369
    Step 3/5 : RUN pip install flask
    ---> Using cache            <------------------------- USING CACHE
    ---> 8c5a50a7482d
    Step 4/5 : COPY app.py /opt/
    ---> Using cache            <------------------------- USING CACHE
    ---> c4eec8db78f6
    Step 5/5 : ENTRYPOINT FLASK_APP=/opt/app2.py flask run --host=0.0.0.0 --port=8080
    ---> Running in 789d2c88ce42
    Removing intermediate container 789d2c88ce42
    ---> 49b5e767be0e
    Successfully built 49b5e767be0e
    Successfully tagged simple-docker2:latest


    $ docker images
    REPOSITORY        TAG         IMAGE ID         SIZE
    simple-docker2    latest      49b5e767be0e     435MB   <---- SAME SIZE
    simple-docker     latest      d1e27491f27f     435MB   <---- SAME SIZE

### Inspect disk usage

- `docker system df`
- `docker system df -v`

        $ docker system df
        TYPE                TOTAL       ACTIVE       SIZE           RECLAIMABLE
        Images              12          0            1.612GB        1.612GB (100%)
        Containers          0           0            0B             0B
        Local Volumes       0           0            0B             0B
        Build Cache         0           0            0B             0B

Pay attention to the `SHARED SIZE`. This is because of the shared layers that docker buildes upon for overlapping build layers.

        $ docker system df -v
        Images space usage:

        REPOSITORY      TAG      IMAGE ID        CREATED          SIZE       SHARED SIZE   UNIQUE SIZE  CONTAINERS
        simple-docker2  latest   49b5e767be0e    12 minutes ago   434.8MB    434.8MB       0B           0
        simple-docker   latest   d1e27491f27f    30 minutes ago   434.8MB    434.8MB       0B           0
        ubuntu          16.04    b6f507652425    5 months ago     134.8MB    134.8MB       0B           0
        redis           latest   ccee4cdf984f    9 months ago     105.4MB    69.25MB       36.13MB      0
        ubuntu          latest   7e0aa2d69a15    9 months ago     72.7MB     0B            72.7MB       0
        mysql           latest   0627ec6901db    9 months ago     555.7MB    69.25MB       486.4MB      0
        nginx           alpine   a64a6e03b055    9 months ago     22.6MB     5.613MB       16.98MB      0
        alpine          latest   6dbb9cc54074    9 months ago     5.613MB    5.613MB       0B           0
        nginx           latest   62d49f9bab67    10 months ago    133.1MB    69.25MB       63.87MB      0
        postgres        latest   26c8bcd8b719    10 months ago    314.3MB    69.25MB       245.1MB      0

        Containers space usage:

        CONTAINER ID        IMAGE               COMMAND             LOCAL VOLUMES       SIZE                CREATED             STATUS              NAMES

        Local Volumes space usage:

        VOLUME NAME         LINKS               SIZE

        Build cache usage: 0B

        CACHE ID            CACHE TYPE          SIZE                CREATED             LAST USED           USAGE               SHARED

<br>
<br>

## <span id='fs'>🤲Hands-on: Explore Storage and Filesystems</span>

[[Top]](#top)

1.  Where are the files related to `docker containers` and `images`?

        cd /var/lib/docker; ls
        builder containerd image overlay2 runtimes tmp volumes
        buildkit containers network plugins swarm trust

<br>

2.  Let's inspect one (`dolfin-2`) of the container files.

        docker ps -a
        CONTAINER ID        IMAGE           COMMAND             NAMES
        eff1543a5e22        alpine          "/bin/sh"           dolfin-3
        24736339a02d        alpine          "/bin/sh"           dolfin-2  <---- THIS
        ae6bb79d24f7        alpine          "/bin/sh"           dolfin-1
        1ff2781162de        tomcat:8.0      "catalina.sh run"   0.0.0:8888->8080/tcp

        cd containers;ls
        1ff27
        24736   <------------------------- THIS
        ae6bb
        eff15

        cd 24736;ls
        24736-json.log  config.v2.json   hostname  mounts       resolv.conf.hash
        checkpoints                                                                hostconfig.json  hosts     resolv.conf

3.  Run a `mysql` server.

        docker run --name=seal-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql
        2022-02-08 04:53:13+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.24-1debian10 started.
        2022-02-08 04:53:13+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
        2022-02-08 04:53:13+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.24-1debian10 started.
        2022-02-08 04:53:13+00:00 [Note] [Entrypoint]: Initializing database files
        2022-02-08T04:53:13.558056Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.24) initializing of server in progress as process 101
        2022-02-08T04:53:13.564445Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
        2022-02-08T04:53:14.046275Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
        2022-02-08T04:53:15.667287Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.

4.  `sh get-data.sh`: to check data in the db.

        mysql: [Warning] Using a password on the command line interface can be insecure.
        id Name Phone Email

5.  Create another db. Mount the volume this time.

        docker run -d -v /opt/data:/var/lib/mysql --name=sealed-mysql -e MYSQL_ROOT_PASSWORD=db_pass123 mysql

        e1d335c74726ec3f68e8ed6fba90660d28aa6476f1b9798fa0d0c1a38a1c1f9b


        $ docker ps
        CONTAINER ID     IMAGE         COMMAND                  CREATED
        e1d335c74726     mysql        "docker-entrypoint.s…"   3306/tcp, 33060/tcp
        1ff2781162de     tomcat:8.0   "catalina.sh run"        0.0.0.0:8888->8080/tcp

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
