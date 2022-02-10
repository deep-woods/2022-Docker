# <span id='top'>05 Docker Services</span>

[[Docker Service]](#Service)  
[[🤲Hands-on Demo]](#demo)  
[[References]](#ref)

<br>

## <span id='Service'>🐳Docker Service</span>

[[Top]](#top)

### Attention: `node`, `service`, and `ps` (`replica`) are 3 different things.

- `docker service --help`
- `docker service ls`
- `docker service ps <ps ID>`
- `docker service create --replicas=<num> <image>`
- `docker service create --mode global <image>`
- `docker service create --name=<name> <image>`: you can name your service.
- `docker service update <service ID> --publish-add <port>`: update `port`.
- `docker service update --replicas=<num> <images>`: update `🐳Docker Swarm` to add a new instance of the service.
- `docker node update --availability drain <NODE-ID>`: drain a node on swarm.
- Manager node can keep track of the workers and instances running on them.

`docker service`
├── `Orchestrator`
├── `Scheduler`
│ㅤㅤ└── `Worker node`
│ㅤㅤ└── `Worker node`
└───└── `Worker node`

## <span id='demo'>🤲Hands-on Demo</span>

[[Top]](#top)

        docker node ls
        ID        HOSTNAME   STATUS  AVAILABILITY   MANAGER STATUS   ENGINE VERSION
        2j9ena *  forest     Ready   Active         Leader           20.10.12
        k0mom8    willow     Ready   Active                          20.10.12

0.  Get help.

        docker service --help

        Usage:  docker service COMMAND

        Manage services

        Commands:
        create      Create a new service
        inspect     Display detailed information on one or more services
        logs        Fetch the logs of a service or task
        ls          List services
        ps          List the tasks of one or more services
        rm          Remove one or more services
        rollback    Revert changes to a service's configuration
        scale       Scale one or multiple replicated services
        update      Update a service

        Run 'docker service COMMAND --help' for more information on a command.

1.  Run a `service`.

        docker service create nginx

        2y9toh5su0wqinsrgu1elf78g
        overall progress: 1 out of 1 tasks
        1/1: running   [==================================================>]
        verify: Service converged

2.  Check the `service`.

        docker service ls

        ID             NAME           MODE         REPLICAS   IMAGE          PORTS
        2y9toh5su0wq   quite_lake   replicated   1/1        nginx:latest

3.  Check the `service`'s `process`.

        docker service ps 2y
        ID         NAME         IMAGE          NODE     DESIRED STATE   CURRENT STATE
        xc9w8qea   quite_lake   nginx:latest   forest   Running         Running 5 minutes ago

4.  Check the `process`.

        docker ps

        CONTAINER ID   IMAGE          COMMAND                  STATUS          PORTS     NAMES
        fd1a5ca632c4   nginx:latest   "/docker-entrypoint.…"   Up 10 minutes   80/tcp    strange_saha.1.xc9...

5.  Update a `port`.

        docker service update --help

        --publish-add port                   Add or update a published port
        --publish-rm port                    Remove a published port by its target port

Now update.

        docker service update 2y9toh5su0wq --publish-add 5000:80

        2y9toh5su0wq
        overall progress: 1 out of 1 tasks
        1/1: running   [==================================================>]
        verify: Service converged

Verify update.

        docker service ls
        ID        NAME        MODE         REPLICAS   IMAGE          PORTS
        2y9toh5   quite_lake  replicated   1/1        nginx:latest   *:5000->80/tcp

6.  Create `replicas`.

        docker service create --replicas 2 --name nginx-server nginx

        p4zjhj33nndgithbz033y36gu
        overall progress: 2 out of 2 tasks
        1/2: running   [==================================================>]
        2/2: running   [==================================================>]
        verify: Service converged

Verify if the `replicas` are successfully running.

        docker service ps nginx-server

        ID             NAME             IMAGE          NODE            DESIRED STATE   CURRENT STATE
        ptdpe2zyt1fg   nginx-server.1   nginx:latest   caleston-lp10   Running         Running about a minute ago
        te3s8mysdwjg   nginx-server.2   nginx:latest   devapp01        Running         Running about a minute ago

Don't get confused. Compare the above with below.

`docker node ls`

        ID                            HOSTNAME        STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
        k0mom809edbwc397go07eto35     caleston-lp10   Ready     Active                          20.10.12
        2j9enaienubhaxzgbbdukfwdj *   devapp01        Ready     Active         Leader           20.10.12

`docker service ls`

        ID             NAME           MODE         REPLICAS   IMAGE          PORTS
        p4zjhj33nndg   nginx-server   replicated   2/2        nginx:latest

### Meanwhile on the `Worker Node`...

        docker ps

        CONTAINER ID   IMAGE          COMMAND                  STATUS         PORTS     NAMES
        f0e1c1f0a19a   nginx:latest   "/docker-entrypoint.…"   Up 4 minutes   80/tcp    nginx-server.1.ptdpe...xh

7. Drain a node on the swarm.

`docker node update --availability drain <NODE-ID>`

        docker node update --availability drain willow
        willow

Note that the `AVAILABILITY` is `drain` now.

        docker node ls

        ID        HOSTNAME   STATUS  AVAILABILITY   MANAGER STATUS   ENGINE VERSION
        2j9ena *  forest     Ready   Active         Leader           20.10.12
        k0mom8    willow     Ready   drain                           20.10.12

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
