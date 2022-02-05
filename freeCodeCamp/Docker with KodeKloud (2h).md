# 🐳 <span id='top'>2022-Docker in Ubuntu</span>

Hello. Welcome to the Docker in Ubuntu handbook. 

Unlike virtual machines, containers are not meant to host an operating system. Containers are designed to host specific tasks such as a web server, an application, database, etc ([FCC](https://youtu.be/fqMOX6JJhGo?t=1476)). 

The container lives as long as the process inside it is alive. Once the process is over, the status is exited. For instance, the container is exited once the application inside it is stopped or crashes. 

<br>

### Notes

All PPT slides and example questions are from KodeKloud on freeCodeCamp tutorial (see reference below).

<br>

### Content

[[Setup]](#setup)   
[[Basics]](#basics)   
[[Port]](#port)   

- Expose ports
- [Map ports](#mapports)

[[Data]](#data)   
[[Inspection]](#inspect)

- `log`
- `inspect`   

[[Export]](#export)   
[[Image]](#image)   

- [Create image][#create]
- [Layer](#layer)
- [Run an instance](#runinstance)

[[CMD vs ENTRYPOINT]](#cmd)   
[[Networking]](#networking)   
[[Storage]](#storage)   
[[Compose]](#compose)   
[[Registry]](#registry)   
[[Engine]](#engine)   
[[Swarm]](#swarm)   
[[Kubernetes]](#kubernetes)   
[[Other]](#other)   
[[References]](#ref)   


<br>

### <span id='setup'>Setup</span>

[[☝️top]](#top)

**Install Ubuntu**

- Donwload Ubuntu `ISO`   
- Install Ubuntu `ISO` on VMWare   

https://ubuntu.com/download/desktop

<br>

## 🐳Docker 

**Install 🐳Docker**

    sudo apt-get update 
    sudo apt-get install ca-certificates curl gnupg lsb-release

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

<br>

**Check result**

    sudo docker version

<br>

## <span id="basics">Basics</span>

[[☝️top]](#top)

**Test run: pull image from hub**

    sudo docker run docker/whalesay cowsay hello world!

**Permission error**

Add user to the docker permission group.

    sudo usermod -a -G docker $USER

<br>

**Commands**

- `docker ps`: lists running containers
- `docker ps -a`: lists history of ALL containers

<br>

  **Image**

  - `docker images`

  Pull image: just pulls and store the image in the host machine; does not run it. 

  - `docker pull nginx`

  Run iamge: running an image causes the container to immediately exit because there is no running application in it ([FCC](https://youtu.be/fqMOX6JJhGo?t=1476)). 

  - `docker run ubuntu`
  - `Attached mode` (foreground mode)
    - `docker run [account name]/[container name]`: using this command, the container runs in the foreground (in the attached mode). Once it starts running, the app is attached to the console and the user can do nothing but viewing the output until this docker container stops. The container won't respond to any user input. 
  - `Detached mode` (background mode)
    - `docker run -d [account name]/[container name]`: you can come back to the prompt and do other things.
  - `docker attach [container ID]`


  Delete image: in order to delete an image, make sure to delete all dependent containers. 

<br>

  **`exec`**

  - `docker exec [container ID] cat /etc/hosts`

<br>
 
  **Tag**

  - Run specific versions
    - `docker run [contaner name]:[version]`
    - Example: `docker run redis:4.0`
  - Latest version
    - `docker run [contaner name]:latest`

  **Clean up**

  - `docker ps -a`
  - `docker ps -aq`: get all container IDs
  - `docker image ls`
  - `docker image ls -aq`: get all image IDs

<br>

## <span id="port">Port</span>

[[☝️top]](#top)

Key concepts
  
      Running on http://0.0.0.0:3000/
      Container IP: 172.17.0.2 (internal IP)

  - 🐳 Docker host (docker engine): the machine on which the docker is installed. aka your computer. 
  - The application in the container is listening on `port 5000`. So it will catch any access coming through the `port 5000`.
  - What `IP` should I use to access the containersied application from a web browser?
    - Option 1. use the `IP` of the docker container (by default, every container gets an `IP` assigned). However, this is an internal `IP` and users outside the container cannot access the app using this `IP`. You should map the container port to the docker host port. 
      - `docker run -p [HOST]:[CONTAINER] [image]`
      - `docker run -p 80:5000 kodekloud/simple-webapp`
      - All traffic to `port 80` will be routed to container's `port 5000`.
      - In this way, you can run multiple instanaces of your application within a single container using multiple internal ports (container's) mapped to different external ports (host machine's). 

- Expose ports 
- <span id='mapports'>Map ports</span>

    PORTS
    0.0.0.0:3456->3456/tcp, 0.0.0.0:38080->80/tcp

Q1. How many ports are published on this container?

    2

Q2. Which ports are the exposed on the CONTAINER? (askling abpit container ports)

    3456 & 80 

Q3. Which ports are **published on Host**? (asking about local machine ports)
  
    38080 & 3456

Q4. Run an instance of `kodekloud/simple-webapp` with a tag `blue` and map port `8080` on the container to `38282` on the host.

- Container Port: 8080
- Host Port: 38282

      docker run --name testrun -d -p 38282:80 kodekloud/simple-webapp:blue 

<br>

## <span id="data">Data</span>

[[☝️top]](#top)

**Data persistance in a container**

    1 docker run mysql
    2 docker stop mysql
    3 docker rm mysql

    4 docker run -v /opt/datadir:/var/lib/mysql mysql

- Docker container has its own isolated file system. 
- All changes to data will be stored in `/var/lib/mysql`. Any changes to data will take place within the container.
- On line 3, all data will be lost with the removal of `mysql` container. 
- On line 4, however, `-v` command implicitly mounts an external path in docker host to the container's internal file system. Once the volume is mounted, the data will be stored in the external volume. Now the data will persist even after the container is destroyed. 

<br>


**interactive mode**

`-i` Interactive mode

    docker run -i [account]/[image]

`it` sudo terminal 

    docker run -it [account]/[image]

<br>

**Dockerfile**

Inspect Dockerfile

- `vim /root/path/Dockerfile`
- `grep -i FROM /root/path/Dockerfile`

      FROM python:3.6
      RUN pip install flask
      COPY . /opt/
      EXPOSE 8080
      WORKDIR /opt
      ENTRYPOINT ["python", "app.py"]

**Explanation**

- The application code is copied to `/opt/` (location) within the container during a Docker build.
- Container's port `8080` is exposed. 
- When a container is created using the image built with the above Dockerfile, `ENTRYPOINT` runs the application. Hence, `python app.py`.

<br>

**Downsize image**

Build a new smaller docker image by modifying the same Dockerfile and name it webapp-color and tag it lite.

    FROM python:3.6
    RUN pip install flask
    ...

Modify as follows:

    FROM python:3.6-alpine

    docker build -t [image]:lite

      ex) docker build -t webapp-color:lite

<br>

## <span id="export">Export</span>

[[☝️top]](#top)

Modify part of your application using the command `export`. 

    1 export APP_COLOR=blue; python app.py
    2 docker run app-image

In line 1, `export` command will find and edit only the line exported by the command. Once the image has been packaged and the container is run, the modification will apply. 

    1 docker run -e APP_COLOR=blue app-image

Alternatively, You can simply use the environment variable comment `-e` when executing `run`, which will do the job too.

<br>

Example: 

    docker run --name blue-app -e APP_COLOR=blue -d -p 38282:8080 kodekloud/simple-webapp

Then check if the variable has been modified successfully.

    $ docker exec -it blue-app env

    PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=007352bca401
    TERM=xterm
    APP_COLOR=blue    <<<<<<<<  HERE. YES!
    LANG=C.UTF-8
    GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
    PYTHON_VERSION=3.6.6
    PYTHON_PIP_VERSION=18.1
    HOME=/root

<br>

**Q. Deploy a mysql database using the mysql image and name it mysql-db.**

Set the database password to use db_pass123. Lookup the mysql image on Docker Hub and identify the correct environment variable to use for setting the root password.

    docker run -d -e MYSQL_ROOT_PASSWORD=db_pass123 --name mysql-db mysql

Check if the password has been set correctly.

    $ docker exec -it mysql-db env

    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=f8f45262aad6
    TERM=xterm
    MYSQL_ROOT_PASSWORD=db_pass123
    GOSU_VERSION=1.12
    MYSQL_MAJOR=8.0
    MYSQL_VERSION=8.0.24-1debian10
    HOME=/root
    failed to resize tty, using default size

<br>

## <span id="image">Image</span>

[[☝️top]](#top)

<span id='create'>**Create image**</span>

    1 FROM Ubuntu

    2 RUN apt-get update
    3 RUN apt-get install python

    4 RUN pip install flask
    5 RUN pip install flask-mysql

    6 COPY . /opt/source-code

    7 ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run


1. `OS` is `ubuntu`.
2. Update `apt` repo.
3. Install dependencies using `apt`.
4. Instal python dependencies using `pip`.
6. Copy source code (everything `.`) from your local system to `/opt/` folder.
7. Run the web server using `flask` command.

<br>

**Build image** 

    docker build Dockerfile -t [account]/[image name]

Creates an image locally on your system. 

<br>

**Push image**

Publish your image on docker hub. 

    docker push [account]/[image name]Image<
    1 FROM Ubuntu
    2 RUN apt-get update && apt-get -y install python
    3 RUN pip install flask flask-mysql
    4 COPY . /opt/source-code
    5 ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run

Docker containers are layered architecture. When Docker builds images, each line of the Dockerfile instruction creates a new layer with just the changes from the previous layer.

- Layer 1. Base `Ubuntu` layer
- Layer 2. + Changes in `apt` packages
- Layer 3. + Changes in `pip` packages
- Layer 4. + Source code
- Layer 5. + Update entry point with `"flask"` command.

This is reflected in the image size, which can be checked using the following command:

    docker history [user]/[image name]

All the layers built are cast - meaning that you can pick up and build again from where the particular step failed (You don't have to start building all over again). The same goes with changes to the image. `rebuild` will start from where the changes have been made. So for instance, rebuilding after updating source code will be faster as `docker build Dockerfile` will pick up layer 3 and start buliding only the latter layers (layers 4 and 5).

<br>

<span id='runinstance'>**Run an instance**</span>

Create an image using `Dockerfile`.

    docker build -t instance . 

Run an instance of it.

    docker run --name [CONTAINER] -d -p 8282:8080 [IMAGE]

    docker run --name color-inst1 -d -p 8282:8080 [IMAGE]

<br>

## <span id="cmd">`CMD` vs `ENTRYPOINT`</span>

    docker run [image] [COMMAND]

Syntax

    CMD command param1
    CMD ["command", "param1"]

Syntax Example

    CMD sleep 5
    CMD ["sleep", "5"] 
  
<br>

App

    ubuntu-sleeper.py

    import time 

    time.sleep(5)


App Example

    docker build -t ubuntu-sleeper .
    docker run ubuntu-sleeper

Edit app using Option 1. Export
The command line `param` passed will be replaced entirely.

    docker run ubuntu-sleep sleep 10

<br>

**Q. Run an instance of the ubuntu image to run the sleep 1000 command at startup.**

    docker run -d ubuntu sleep 1000


<br>

**ENTRYPOINT**

  FROM Ubuntu
  ENTRYPOINT ["sleep"]

  docker run ubuntu-sleeper 10

The command line `param` will get appended. 

<br>

**Set default param for ENTRYPOINT

    FROM Ubuntu
    ENTRYPOINT ["sleep"]
    CMD ["5"]

<br>

**Modify ENTRYPOINT during runtime**

Use `run --entrypoint` command.

    docker run --entrypoint sleep2.0 ubuntu-sleeper 10


<br>

## <span id="networking">Networking</span>

[[☝️top]](#top)

Upon installation, Docker automatically craetes three networkes.

  - `Bridge`: a private internal network on the host. All containers are attached to this network by default and are assigned an internal `IP` address.
    - Internal IP range: `172.17~`
  - `none`: an isolated network. No access to ...
    - the external network nor
    - other containers
  - `host`: used to access the container externally by associating the container to the host network
    - The container uses the host's port directly.
    - No port mapping is required.
    - Not possible to run the multiple containers on the same port \
    - as the ports are common to all the containers in the host network.
  - User-defined networks

<br>

**Specify network**

    docker run ubuntu
    docker run Ubuntu --network=none
    docker run Ubuntu --network=host

<br>

**Custom network**

    docker network create \
      --driver bridge \
      --subnet 182.17.0.0/16
      custom-isolated-network

<br>

Example:

    docker network create --driver bridge --subnet 182.18.0.1/24 --gateway 182.18.0.1 wp-mysql-network

<br>

Use `docker network create`, then specify the network type (`driver`) and subnet (`182.17.0.0/16`). 

    docker inspect [container]

The `inspect` command shows information about network too. Look for:

    "bridge": {
        "Gateway": ,
        "IPAddress": "172.17.0.6",
        "{MacAddress": "02:42:ac:11:00:06",}
    }

<br>

**Embedded DNS**

- All containers can resolve each other with the container name.
- Docker has a built-in DNS server for this. 
- The built-in DNS server always runs at address 127.0.0.11.

- How are the containers isolated within the host? 
  - Docker uses `netowrk namespaces` that creates a separtae namespace for each container. Then uses virtual Ethernet pairs to connect containers together. 

<br>

**Q. Check list of networks.**

    docker network ls

<br>

**Q. Inspect network setting**

    docker network inspect bridge


<br>

## <span id="storage">Docker storage</span>

[[☝️top]](#top)

**Local file system**

- Upon installation of docker on a local system, it creates the following file system:
- Data: docker-related files such as images and containers running on host

      :open_file_folder: /var/lib/docker
      │   ├── aufs
      │   ├── containers
      │   ├── images
      └── └──volumnes

- As docker uses a layered architecture, for an image building operation that shares certain layers, Docker will reuse the same layers it has built for previous applications `from the cache`.
- Once the image is created, it is `Read-Only` and you cannot override it. If you want to modify the image, you have to build another. Using `docker run` command, Docker creates a container based off of the existing layers and creates a new `writable layer` on top of the image layer. The `writable layer` is used to store data created by the container such as `log files` written by the applications, temporary files generated by the container. 
- `Layer lifecycle` is as long as the contaner is alive. Once the container is destroyed, all the changes stored stored in the layer also dies with it.

**Copy-on-write**

- Although `images` are read-only, you CAN make changes if you wish. Docker automatically creates a container layer where writable files live, and also creates a copy of any file you want to make change to in the created image. In other words, you will write on a copy of the original file from an existing built image. 
- But this will die as the container is destroyed. 
- Then how can we persist the data? 

<br>

**Volume**

- Preserve the data by adding a `persistent volume` to the data.  

      docker volume create data_volume

      /var/lib/docker
      │   ├── volumes
      └── └── data_volumne

Volume mounting: mounts the volume from the `/volumes/` directory

    docker run -v data_volume:/var/lib/mysql mysql
    docker run -v [volume]/var/liq/[mount dir] [data for mount]

Bind-mounting: mounts the directory from any location in the docker host. 

    docker run -v /data/mysql:/var/lib/mysql mysql
    docker run -v [user defined path]:[container path]      

Clearer verbose command

    docker run \
        --mount type=bind, \
        source=/data/mysql, \
        target=/var/lib/mysql mysql

Who manages mounting? - `Storage drivers`

- AUFS, ZFS, BTRFS,
- Device Mapper, Overlay, Overlay2

<br>

## <span id="compose">Docker Compose</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/Doker%20compose%2002.png" width=1000 />


    docker-compose.yaml

    services:
        web:
            image: "user/simple-webapp"
        database:
            image: "mongodb"
        messaging:
            image: "redis:alpine"
        orchestration:
            image: "ansible"

    docker-compose up

<br>

Let's compose. The following command will run the individual components of the service app, but they are not linked with each other:

    docker run -d --name=redis redis
    docker run -d --name=db postgres:9.4  result-app
    docker run -d --name=vote -p 5000:80  voting-app
    docker run -d --name=result -p 5001:80
    docker run -d --name=worker worker

<br>

The instances run as part of a service should be linked to each other because there can be multiple instances of the same image (e.g. db, redis, python, etc). Using the following:

    -- links

Link the right instances to each other as below:

    docker run -d --name=redis redis
    docker run -d --name=db postgres:9.4 --link db:db result-app
    docker run -d --name=vote -p 5000:80 --link redis:redis voting-app
    docker run -d --name=result -p 5001:80 --link db:db result-app
    docker run -d --name=worker --link db:db --link redis:redis worker


By creating a link, we can use that name in our code so our app components can actually talk to each other and do things together. Some examples are shown below.

`voting-app.py` will get `redis`:

    def get_redis():
        if not hasattr(g, 'redis'):
            g.redis = Redis(host="redis", db=0, socket_timeout=5)
        return g.redis


`result-app.js` can find the database by `db`:

    pg.connect('postgres://postgre@db/postgres', function(err, client, done) {
      if(err) {
        console.error("Waiting for db");
      }
      callback(err, client);
    })

`worker` should be connected to both `redis` and `postgres db`:

    try{
      Jedis redis = connectToRedis("redis");  << name here.
      Connection dbConn = connectToDB("db");  << name here.

      System.err.println("Watching vote queue");
    }

<br>

`compose` file is a dictionary consisting of `key:value` (`image`:`image_name`) pairs. 

    docker-compose.yaml

    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        image: voting-app
        ports:
          - 5000:80
        links:
          - redis
    result:
        image: result-app
        ports:
          - 5001:80
        links:
          - db
    worker:
        image: worker
        links:
          - db
          - redis

<br>

Out of the 5 images, two (`redis` & `postgres`) are official images published by the respective organisations, and the remaining 3 are our user-made images. In case your images are not ready yet, you can specify the `image_path` instead of their names in `compose.yaml`.

    docker-compose.yaml

    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        image: ./vote
        ports:
          - 5000:80
        links:
          - redis
    result:
        image: ./result
        ports:
          - 5001:80
        links:
          - db
    worker:
        image: ./worker
        links:
          - db
          - redis

<br>

**Q. Create a docker-compose.yml file under the directory /root/clickcounter. Once done, run docker-compose up.**

    vi docker-compose.yml

    services:
      redis:
        image: redis:alpine
      clickcounter:
        image: kodekloud/click-counter
        ports:
        - 8085:5000
    version: '3.0'

Then run the `compose.yml`.

    docker-compose up -d

<br>

**Q. Deploy a web application named webapp using the kodekloud/simple-webapp-mysql image. Expose the port to 38080 on the host.**

The application makes use of two environment variable:
1: DB_Host with the value mysql-db.
2: DB_Password with the value db_pass123.
Make sure to attach it to the newly created network called wp-mysql-network.

Also make sure to link the MySQL and the webapp container.


    docker run -d --name=webapp -p 38080:3000 -e DB_Host=mysql-db -e DB_Password=db_pass123 --network=wp-mysql-network --link mysql:mysql-db kodekloud/simple-webapp-mysql

<br>

**Docker compose versions**

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/Doker%20compose%2001.png" width=1000 />

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/Doker%20compose%2002.png" width=1000 />

<br>

## <span id="registry">Registry</span>

Central repositry of all docker images. 

- image: [registry]/[user]/[image_repositry]
  - example: `docker.io/nginx/nginx`

**Deploy private registry**

    docker run -d -p 5000:5000 --name registry registry:2

    docker image tag my-image localhost:5000/my-image

    docker push localhost:5000/my-image

    docker pull localhost:5000/my-image

    docker pull 192.168.56.100:5000/my-image

<br>

## <span id="engine">Engine</span>

<br>

## <span id="orchestration">Container Orchestration</span>


    docker service create --replicas=100 nodejs


<br>

## <span id="swarm">Swarm</span>

[[☝️top]](#top)

Docker's own container orchestration tool

    docker swarm init (swarm master)
    doker swarm join --token <token> (worker node)

<br>

Docker Service

    docker run [container]

    docker service create --replicas=3 [container]

<br>

Example

    docker run my-web-server
    docker service create --replicas=3 --network frontend my-web-server
    docker service create --replicas=3 -p 8080:80 my-web-server
    docker service create --replicas=3 my-web-server

<br>

## <span id="kubernetes">Kubernetes</span>

[[☝️top]](#top)

With K8s, you can run 1,000 instances of the same application with a single command.
Kubernetes uses `docker host` to host applications in the form of `docker container`. But it doesn't have to be docker, it can be other container system.

Example

    docker run my-web-server
    kubectl scale --replicas=1000 my-web-server
    kubectl scale --replicas=2000 my-web-server
    kubectl rolling-update my-web-server iimage=web-server:2
    kubectl rolling-update my-web-server --rollback

<br>

**`kubectl`**

    kubectl run hello-minikube
    kubectl cluster-inofo
    kubectl get nodes
    kubectl run my-web-app --image=my-web-app

<br>

## <span id="other">Other</span>

[[☝️top]](#top)

**Running pip as the root user**

WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv

<br>

**Check base OS of an image**

    docker run <image-name> cat /etc/*release*

<br>

**Check env variables**

From within the container: 

    docker exec -it [contaner] env

Example:

    $ docker exec -it blue-app env
    PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=007352bca401
    TERM=xterm
    APP_COLOR=blue
    LANG=C.UTF-8
    GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
    PYTHON_VERSION=3.6.6
    PYTHON_PIP_V

<br>

### <span id="ref">References</span>

[[☝️top]](#top)

  - freeCodeCamp (2019) Docker Tutorial for Beginners - A Full DevOps Course on How to Run Applications in Containers https://www.youtube.com/watch?v=fqMOX6JJhGo&t=180s
