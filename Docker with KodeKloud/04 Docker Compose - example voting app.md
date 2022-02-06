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

`Clone` the `example-voting-app repo`.

    git clone https://github.com/dockersamples/example-voting-app.git

    cd example-voting-app

    ls 
    ... vote    worker    result ...

Build images inside each directory: `vote`, `worker`, and `result`

    cd vote
    docker build . -t voting-app

    cd ../worker
    docker build . -t worker-app

    cd ../result
    docker build . -t result-app

Now write a [`docker-compose.yml`](https://github.com/deep-woods/2022-Docker/blob/main/Docker%20with%20KodeKloud/docker-compose.yml).

    cd ~
    nano docker-compose.yml

Now run the `docker-compose.yml` file. 

    docker-compose up


<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/