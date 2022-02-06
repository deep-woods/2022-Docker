# <span id='top'>04 🐳Docker Compose</span>

<br>

[[Install 🐳Docker Compose]](#install)  
[[🐳Example Voting App ]](#app)  
[[🐳Dockerfile]](#Dockerfile)  
[[References]](#ref)

<br>

## <span id='install'>Install 🐳Docker Compose</span>

[[Top]](#top)

Install 🐳Docker Compose:https://docs.docker.com/compose/install/

<br>

    sudo mkdir /user/local/bin

    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

    sudo chmod +x /usr/local/bin/docker-compose

<br>

## <span id='app'>🐳Example Voting App</span>

[[Top]](#top)



<br>

## <span id='Dockerfile'>🐳Dockerfile</span>

[[Top]](#top)

<br>

    docker-compose.yml

    redis: 
      container_name: redis
      image: redis

    vote: 
      container_name: voting-app
      image: voting-app
      ports: 
        - 5000:80
      links:
        - redis

    db: 
      container_name: db
      image: postgres:9.4
      environment: 
        - POSTGRES_HOST_AUTH_METHOD=trust

    worker: 
      container_name: worker-app
      image: worker-app
      links:
        - redis
        - db

    result: 
      container_name: result-app
      image: result-app
      ports: 
        - 5001:80
      links:
        - db


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
