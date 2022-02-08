# <span id='top'>04 🐳Docker Compose</span>

<br>

[[🐳Docker Compose]](#Compose)  
[[Mini Demo - Example Voting App unComposed]](#appdemo1)  
[[References]](#ref)

<br>

## <span id='Compose'>🐳Docker Compose</span>

[[Top]](#top)

- A file that orchestrates multiple containers.
- Architecture of an application in 🐳`Docker`

  - `[container]:[host]`

        docker run -d --name=redis redis
        docker run -d --name=db postgres
        docker run -d --name=vote -p 5000:80 --link redis:redis voting-app
        docker run -d --name=result -p 5001:80 --link db:db result-app
        docker run -d --name=worker --link db:db --link redis:redis worker

<br>

Write a 🐳`Docker-compose` based on the above Architecture.

    docker-compose.yml

    redis:
      image: redis
    db:
      image: postgres:9.4
    vote:
      image: voting-app <----- alternatively, you can also (see following line)
      build: ./vote (this directory should contain both Dockerfile and the voting app file.)
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
        - redis
        - db

<br>

## <span id='appdemo1'>Mini Demo - Example Voting App unComposed</span>

[[Top]](#top)

Connect to: `[server IP]:[port num]`

<br>

`Redis`: _Redis is an in-memory data structure store, used as a distributed, in-memory key–value database, cache and message broker, with optional durability. Redis supports different kinds of abstract data structures, such as strings, lists, maps, sets, sorted sets, HyperLogLogs, bitmaps, streams, and spatial indices._ ([Wikipeida](https://en.wikipedia.org/wiki/Redis))

<br>

### unCompose Step by Step

Clone the repo.

    git clone https://github.com/dockersamples/example-voting-app.git

1.  Run `redis cache`.

        docker run -d --name=cache redis

2.  Build `voting-app`.

         example-voting-app $ cd vote
         Dockerfile  app.py  dotnet  requirements.txt  static  templates

         docker build . -t voting-app

         $ docker images
         REPOSITORY     TAG         IMAGE           ID
         voting-app     latest      ec5568d5dc68    141MB
         redis          latest      ccee4cdf984f    105MB
         postgres       latest      26c8bcd8b719    314MB

Run it.

        docker run -d --name=voting-app --link cache:redis voting-app

        $ docker ps
        CONTAINER ID   IMAGE        COMMAND                  PORT       NAME
        ebf99af8dbfc   voting-app   "gunicorn app:app -b…"   80/tcp     voting-app
        0036966eb892   redis        "docker-entrypoint.s…"   6379/tcp   cache

3.  Run `postgres db`.
4.  Build `worker-app`.

        $ cd ../worker;ls
        $ docker build . -t worker-app
        Successfully built 47913349af1c
        Successfully tagged worker-app:latest

        $ docker images
        CONTAINER ID    IMAGE          COMMAND                  CREATED    NAME
        59ce727ca835    postgres:9.4   "docker-entrypoint.s…"   5432/tcp   db
        ebf99af8dbfc    voting-app     "gunicorn app:app -b…"   80/tcp     voting-app
        0036966eb892    redis          "docker-entrypoint.s…"   6379/tcp   cache

Run it. (Try running the `postgres db` and `worker-app` in a single in a single line in case `postgres` dies before the `worker-app` container is created and becomes up and running.)

        docker run -d --name=db -e POSTGRES_HOST_AUTH_METHOD=trust postgres:9.4;
        docker run -d --link cache:redis --link db:postgres:9.4 --name=worker-app worker-app

        $ docker ps
        CONTAINER ID    IMAGE          COMMAND                   PORTS      NAMES
        2380ec9a6afe    worker-app     "dotnet Worker.dll"                  worker-app
        59ce727ca835    postgres:9.4   "docker-entrypoint.s…"    5432/tcp   db
        ebf99af8dbfc    voting-app     "gunicorn app:app -b…"    80/tcp     voting-app
        0036966eb892    redis          "docker-entrypoint.s…"    6379/tcp   cache

You can check the `env` variables you set here:

        $ docker exec -it 59ce727ca835 env
        PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/lib/postgresql/9.4/bin
        HOSTNAME=59ce727ca835
        TERM=xterm
        POSTGRES_HOST_AUTH_METHOD=trust   <------------------------------------- HERE
        GOSU_VERSION=1.11
        LANG=en_US.utf8
        PG_MAJOR=9.4
        PG_VERSION=9.4.26-1.pgdg90+1
        PGDATA=/var/lib/postgresql/data
        HOME=/root

5.  Build `worker-app`.

        $ cd ../result; docker build . -t result-app
        Successfully built 8519b6f90db2
        Successfully tagged result-app:latest

        $ docker run -d -p 5001:80 --link db:postgres result-app

        CONTAINER ID   IMAGE          COMMAND                   PORTS                  NAMES
        d713104d753f   result-app     "docker-entrypoint.s…"    0.0.0.0:5001->80/tcp   elastic_mccarthy
        2380ec9a6afe   worker-app     "dotnet Worker.dll"                              worker-app
        59ce727ca835   postgres:9.4   "docker-entrypoint.s…"    5432/tcp               db
        ebf99af8dbfc   voting-app     "gunicorn app:app -b…"    80/tcp                 voting-app
        0036966eb892   redis          "docker-entrypoint.s…"    6379/tcp               cache

It runs!

<img src="https://github.com/deep-woods/2022-Docker/blob/main/Docker%20with%20KodeKloud/demo01-example-voting-app.gif" />

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
