# <span id='top'>04 🐳Docker Compose</span>

<br>

[[🐳Docker Compose]](#Compose)  
[[Mini Demo - Example Voting App]](#appdemo1)  
[[]](#)  
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

`Redis`: _Redis is an in-memory data structure store, used as a distributed, in-memory key–value database, cache and message broker, with optional durability. Redis supports different kinds of abstract data structures, such as strings, lists, maps, sets, sorted sets, HyperLogLogs, bitmaps, streams, and spatial indices._ ([Wikipeida](https://en.wikipedia.org/wiki/Redis))
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/

├──
│ ├──
│ └──
├──
│ ├──
│ └──
├──
├──
│  
├──
└──
