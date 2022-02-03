# <span id='top'>02 Docker Run </span>

<br>

[[`Run` tag]](#run)  
[[`Run` port mapping]](#portmapping)  
[[`Run` volume mapping]](#volumemapping)  
[[Practice Makes Perfect]](#perfect) 

<br>

## <span id='run'>`Run` tag</span>

[[Top]](#top)

- `docker run [container]`
  - `-i`: interactive
  - `-it`: interactive terminaldoc
<br>

## <span id='portmapping'>`Run` Port mapping</span>

[[Top]](#top)

Port mapping allows to run multiple instances of an application using different ports. 

<br>

- Create multiple instances of the same application using different ports.   
  - `[host port]:[container port]`
  - external ports: `80`, `8000`, `8001`

        $ docker run -d -p 80:5000 kodekloud/simple-webapp      
        c4671ee5
        $ docker run -d -p 8000:5000 kodekloud/simple-webapp      
        6ee918b8
        d$ docker run -d -p 8001:5000 kodekloud/simple-webapp
        b50e5a72

- Create mulitple instances of `postgres` dbs. 
- Note that the third instance will not be created because it uses the same port as the second instance. 

        $ docker run -d -p 3306:3306 postgres
        9065c38847
        $ docker run -d -p 8306:3306 postgres    <---- Same port number 8306
        1897cc6b64
        $ docker run -d -p 8306:3306 postgres    <---- Same port number 8306
        00260ba9b6

Now check the running instances. 

        $ docker ps -a
        CONTAINER ID    IMAGE                    COMMAND                  STATUS
        00260ba9b62f    postgres                 "docker-entrypoint.s…"   20 
        1897cc6b64ed    postgres                 "docker-entrypoint.s…"   23 
        9065c3884711    postgres                 "docker-entrypoint.s…"   37 

        CONTAINER ID    IMAGE                    PORT
        b50e5a72568d    kodekloud/simple-webapp  8080/tcp, 0.0.0.0:8001->5000/tcp   
        6ee918b81b44    kodekloud/simple-webapp  8080/tcp, 0.0.0.0:8000->5000/tcp 
        c4671ee5479c    kodekloud/simple-webapp  8080/tcp, 0.0.0.0:80->5000/tcp     

<br>


## <span id='volumemapping'>`Run` volume mapping</span>

[[Top]](#top)

In the following scenario, you will lose all the data in the `container`. If you want to persist the data in a `container`, you have to map its data to an external `host directory`. 

    docker run mysql
    docker stop mysql
    docker rm mysql

- SYNTAX: `docker run -v [host dir]:[container dir]`
  - `docker run -v /opt/datadir:/var/lib/mysql mysql`
  - `-v`: host followed by a colon and the directory inside the docker container. When a `container` runs it will implicitly mount the external directory to a folder inside the docker container.

<br>

### Inspect container information

- `docker inspect [container_name]`
- `docker log [container_name]`

<br>

## <span id='perfect'>Practice Makes Perfect</span>

[[Top]](#top)

<br>

1. Note that multiple ports can be assigned to a single container. 

    CONTAINER ID      IMAGE             PORTS
    041ae7a7f441      nginx:alpine      0.0.0.0:3456->3456/tcp, 0.0.0.0:38080->80/tcp
 
<br>

2. Run an instance with the given configurations. 

- tag: `blue` 
- port: host 38282 + container 8080

      $ docker run -d -p 38282:8080 kodekloud/simple-webapp:blue
      Unable to find image 'kodekloud/simple-webapp:blue' locally
      blue: Pulling from kodekloud/simple-webapp
      4fe2ade4980c: Already exists 
      7cf6a1d62200: Already exists 
      f0d690b9e495: Already exists 
      fac5d45ad062: Already exists 
      a6fc8a0deb7d: Pull complete 
      f43c8e496f88: Pull complete 
      58ca939f7651: Pull complete 
      095a1a007cdb: Pull complete 
      Digest: sha256:9caf15476dc60b77c7460791bea8ea5f6ca02b90199aabe088beea83bc943fe5
      Status: Downloaded newer image for kodekloud/simple-webapp:blue
      e93d5a956a8f6a4fa69ee25e0c50b79a83a0c67b76e0882f835a588b0fd5e32f



