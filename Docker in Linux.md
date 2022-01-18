# 🐳 <span id='top'>2022-Docker in Ubuntu</span>

Hello. Welcome to the Docker in Ubuntu handbook. 

Unlike virtual machines, containers are not meant to host an operating system. Containers are designed to host specific tasks such as a web server, an application, database, etc ([FCC](https://youtu.be/fqMOX6JJhGo?t=1476)). 

The container lives as long as the process inside it is alive. Once the process is over, the status is exited. For instance, the container is exited once the application inside it is stopped or crashes. 

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
[[]](#)   
[[]](#)   
[[]](#)   
[[]](#)   
[[]](#)   
[[]](#)   
[[]](#)   
[[References]](#ref)   


<br>

### <span id='setup'>Setup</span>

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

**Data persistance in a container**

    1 docker run mysql
    2 docker stop mysql
    3 docker rm mysql

    4 docker run -v /opt/datadir:/var/lib/mysql mysql

- Docker container has its own isolated file system. 
- All changes to data will be stored in `/var/lib/mysql`. Any changes to data will take place within the container.
- On line 3, all data will be lost with the removal of `mysql` container. 
- On line 4, however, `-v` command implicitly mounts an external path in docker host to the container's internal file system. Once the volume is mounted, the data will be stored in the external volume. Now the data will persist even after the container is destroyed. 


****

-

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

Modify part of your application using the command `export`. 

    1 export APP_COLOR=blue; python app.py
    2 docker run app-image

In line 1, `export` command will find and edit only the line exported by the command. Once the image has been packaged and the container is run, the modification will apply. 

    1 docker run -e APP_COLOR=blue app-image

Alternatively, You can simply use the environment variable comment `-e` when executing `run`, which will do the job too.


<br>

## <span id="image">Image</span>

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

Upon installation, Docker automatically craetes three networkes.

  - `Bridge`
  - `none`
  - `host`

<br>

**Specify network**

    docker run ubuntu
    docker run Ubuntu --network=none
    docker run Ubuntu --network=host


****

-

<br>

## <span id=""></span>

****

-

****

-

<br>

### <span id="ref">References</span>

[[☝️top]](#top)

  - freeCodeCamp (2019) Docker Tutorial for Beginners - A Full DevOps Course on How to Run Applications in Containers https://www.youtube.com/watch?v=fqMOX6JJhGo&t=180s