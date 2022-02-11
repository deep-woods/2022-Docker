# <span id='top'>07 Docker Stacks</span>

[[Docker Stacks]](#stacks)  
[[Sample Application in Swarm]](#sample)  
[[🤲Hands-on Stack]](#handson)  
[[Docker Visualizer]](#Visualizer)  
[[References]](#ref)

<br>

## <span id='stacks'>Docker Stacks</span>

[[Top]](#top)

### Compose

- `docker run forest/webapp`
- `docker run mongodb`
- `docker run redis:alpine`
- `docker run ansible`

        -------------- docker-compose.yml --------------

        services:
            web:
                image: "forest/webapp"
            database:
                image: "mongodb"
            messaging:
                image: "redis:alpine"
            orchestration:
                image: "ansible"

        docker-compose up

<br>

### Stack

- `docker service create forest/webapp`
- `docker service create mongodb`
- `docker service create redis`
- `docker service create ansible`

Docker service commands written into a Docker compose file.

        -------------- docker compose.yml --------------

        version: 3
        services:
            web:
                image: "forest/webapp"
            database:
                image: "mongodb"
            messaging:
                image: "redis:alpine"
            orchestration:
                image: "ansible"

- `Container`: a package form of an application that has its own dependencies and runs
  in its own environment.
- `Service`: one or more instances of the same type of container that runs on a single node or across multiple nodes in a `Swarm cluster`.
  - Three instances of the same web application running across multiple
    nodes
- `Stack`: a group of interrelated services which together forms an entire application.

<br>

## <span id='sample'>Sample Application in Swarm</span>

[[Top]](#top)

| 🐘`Postgres` | 🟥`Ansible` |             |
| ------------ | ----------- | ----------- |
|              | 🐍`Python`  | 🐍`Python`  |
|              |             | 🟩`Js`      |
|              | 🎗️`Dotnet`  |             |
| Manager Node | Worker Node | Worker Node |

- `Multiple instances`
- `Placement preferences`
- `Resource constraints`

        -------------- docker compose.yml --------------

        version: 3
        services:
            redis:
                image: redis
                deploy:
                    replicas: 1
            db:
                image: prostgres:9.4
                deploy:
                    placement:
                        constraints:
                          - node.hostname == node1
                          - node.role == manager
                    resources:
                        limages:
                            cpus: 0.01
                            memory: 50M

<br>

## <span id='handson'>🤲Hands-on Stack</span>

[[Top]](#top)

    docker node ls
    ID          HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
    uset7yj *   forest     Ready     Active         Leader           20.10.12
    fop2bad     willow     Ready     Active                          20.10.12

<br>

1. Deploy.

The services below are automatically created by the `docker stack deploy` script.

    docker stack deploy voting-app-stack --compose-file docker-stack.yml

    Creating network voting-app-stack_default
    Creating service voting-app-stack_result
    Creating service voting-app-stack_redis
    Creating service voting-app-stack_db
    Creating service voting-app-stack_vote
    Creating service voting-app-stack_worker

Verify if the `process` is running.

    docker service ps voting-app-stack_worker

    ID      NAME                        IMAGE
    o6a43   voting-app-stack_worker.1   dockersamples/examplevotingapp_worker:latest

    NODE     DESIRED STATE   CURRENT    STATE             ERROR     PORTS
    forest   Running         Preparing  4 minutes ago

<br>

2.  Increase the number of `replicas` just for the `voting-app`.

        docker service ls

        NAME           MODE         REPLICAS   IMAGE    PORTS
        stack_db       replicated   0/1        postgres:9.4
        stack_redis    replicated   1/1        redis:latest
        stack_result   replicated   1/1        result:latest   *:5001->80/tcp
        stack_vote     replicated   1/1        vote:latest     *:5000->80/tcp
        stack_worker   replicated   0/1        worker:latest

Modify the `compose.yml` file.

        -------------- docker compose.yml --------------

        vote:
            image: dockersamples/examplevotingapp_vote
            ports:
              - 5000:80
            deploy:
              replicas: 2

Now update. Note that the `deploy` command is not deploying a new service but updating the changes on the existing `services`.

        docker stack deploy voting-app-stack --compose-file docker-stack.yml

        Updating service voting-app-stack_db (id: lvool5opc5ensk9m9u305h5xo)
        Updating service voting-app-stack_vote (id: p40f9mvsy4k6zuhvtmnre0qzi)
        Updating service voting-app-stack_worker (id: 9kmjpa7axdx2j7jo5cdw6lfa0)
        Updating service voting-app-stack_result (id: jerx3h6jxz2mh3nykjzad4rh8)
        Updating service voting-app-stack_redis (id: alp01xxbac7qb5k1jpq97oip2)

Check the status. Note that the `stack_vote` now has 2 `replicas`.

        docker service ls

        NAME           MODE         REPLICAS   IMAGE    PORTS
        stack_db       replicated   0/1        postgres:9.4
        stack_redis    replicated   1/1        redis:latest
        stack_result   replicated   1/1        result:latest   *:5001->80/tcp
        stack_vote     replicated   1/2        vote:latest     *:5000->80/tcp
        stack_worker   replicated   0/1        worker:latest

And there are two `replicas` of the `service` is running.

        NAME                      IMAGE                   NODE     DESIRED STATE   CURRENT STATE
        voting-app-stack_vote.1   votingapp_vote:latest   forest   Running         Running 30 minutes ago
        voting-app-stack_vote.2   votingapp_vote:latest   willow   Running         Running 53 seconds ago

<br>

## <span id='Visualizer'>Docker Visualizer</span>

[[Top]](#top)

- `docker run -it -d -p 8080:8080 -v /var/run/docker.sock:/var/run/docker.sock dockersamples/visualizer`

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
- dockersamples (n.d.) Docker Swarm Visualizer https://hub.docker.com/r/dockersamples/visualizer
