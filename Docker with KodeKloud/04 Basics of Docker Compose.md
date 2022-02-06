# <span id='top'>04 🐳Docker Compose</span>

<br>

[[🐳Docker Compose]](#Compose)  
[[Mini Demo - Example Voting App]](#appdemo1)  
[[Practice Makes Perfect]](#Perfect)  
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

## <span id='appdemo1'>Mini Demo - Example Voting App</span>

[[Top]](#top)

<br>

Connect to: `[server IP]:[port num]`

<br>

<img src="https://github.com/deep-woods/2022-Docker/blob/main/images/Voting-App-01-votingapp-backend.png" width=500 />

<img src="https://github.com/deep-woods/2022-Docker/blob/main/images/Voting-App-01-votingapp.png" width=500 />

<img src="https://github.com/deep-woods/2022-Docker/blob/main/images/Voting-App-02-votingapp-redis-connected.png" width=500 />

<img src="https://github.com/deep-woods/2022-Docker/blob/main/images/Voting-App-05-votingapp-result.png" width=1000 />

`Redis`: _Redis is an in-memory data structure store, used as a distributed, in-memory key–value database, cache and message broker, with optional durability. Redis supports different kinds of abstract data structures, such as strings, lists, maps, sets, sorted sets, HyperLogLogs, bitmaps, streams, and spatial indices._ ([Wikipeida](https://en.wikipedia.org/wiki/Redis))

<br>

## <span id='Perfect'>Practice Makes Perfect</span>

1.  Create a cache db.

`docker run -d --name=redis redis:alpine`

    Unable to find image 'redis:alpine' locally
    alpine: Pulling from library/redis
    59bf1c3509f3: Pull complete
    719adce26c52: Pull complete
    b8f35e378c31: Pull complete
    d034517f789c: Extracting 5.112MB/7.707MB
    3772d4d76753: Download complete
    211a7f52febb: Download complete

    Digest: sha256:4bed291a
    Status: Downloaded newer image for redis:alpine

2.  run a container and link it to the `redis` cache db.

`docker run -d --name=grocery -p 8085:5000 --link redis:redis forest/grocery-shopping`

    Unable to find image 'forest/grocery-shopping:latest' locally

        Digest: sha256:530e4532a
        Status: Downloaded newer image for forest/grocery-shopping:latest

<br>

3.  Now compose a `docker-compose.yml`

    services:

        redis:
          image: redis:alpine

        grocery-shopping:
          container_name: grocery-shopping
          image: forest/grocery-shopping
          ports:
            - 8085:5000

        version: '3.0'

Then run it as an app.

    docker-compose run

      Attaching to grocery-shopping_redis_1, grocery-shopping
      redis_1         | 1:C 06 Feb 2022 14:56:29.629 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
      redis_1         | 1:C 06 Feb 2022 14:56:29.629 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=1, just started
      redis_1         | 1:C 06 Feb 2022 14:56:29.629 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
      redis_1         | 1:M 06 Feb 2022 14:56:29.630 * monotonic clock: POSIX clock_gettime
      redis_1         | 1:M 06 Feb 2022 14:56:29.633 * Running mode=standalone, port=6379.
      redis_1         | 1:M 06 Feb 2022 14:56:29.634 # Server initialized
      redis_1         | 1:M 06 Feb 2022 14:56:29.635 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
      redis_1         | 1:M 06 Feb 2022 14:56:29.636 * Ready to accept connections
      grocery-shopping    |  * Serving Flask app 'app.py' (lazy loading)
      grocery-shopping    |  * Environment: production
      grocery-shopping    |    WARNING: This is a development server. Do not use it in a production deployment.
      grocery-shopping    |    Use a production WSGI server instead.
      grocery-shopping    |  * Debug mode: off
      grocery-shopping    |  * Running on all addresses.
      grocery-shopping    |    WARNING: This is a development server. Do not use it in a production deployment.
      grocery-shopping    |  * Running on http://172.12.0.2:5000/ (Press CTRL+C to quit)

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
