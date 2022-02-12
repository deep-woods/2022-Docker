# <span id='top'>04 Docker Swarm</span>

[[Swarm]](#Swarm)  
[[RAFT - Distributed Consensus]](#raft)  
[[🤲Hands-on]](#)  
[[References]](#ref)

<br>

## <span id='Swarm'>Swarm</span>

[[Top]](#top)

`manager node` (docker host)  
├──`worker node` (docker host)  
├── ...  
└──`worker node` (docker host)

- `docker swarm init`: initailise the manager
  - `--advertise-addr <ip addr>`: _To put it simply, the `--advertise-addr` is the address other nodes in the Docker swarm use to connect into your node. You need to provide an IP address of your host, or a network interface which Docker will use to lookup your IP address, and a port number which defaults to 2377:_ (Mitchell, 2016)
- `docker swarm join-token manager`
- `docker node update --availability drain <node>`: Docker recommends dedicating
  manager nodes for management tasks only in the production environment.

<br>

## <span id='raft'>RAFT - Distributed Consensus</span>

[[Top]](#top)

- The `leader` sends out notifications informing them that it is continuing
  to assume the role of the leader.
- If, for some reason, the `leader` becomes unavailable, a re-election process initiates to select another `leader`.
- Every manager has its own copy of Raft database that stores the information
  about the entire cluster. These raft db should be all in sync.

## <span id=''>🤲Hands-on </span>

[[Top]](#top)

Install docker

[Install using the repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

1.  Update the apt package index and install packages to allow apt to use a repository over HTTPS:

        sudo apt-get update
        sudo apt-get install ca-certificates curl gnupg lsb-release

- `ca-certificates`: _A CA certificate is a digital certificate issued by a certificate authority (CA), so SSL clients (such as web browsers) can use it to verify the SSL certificates sign by this CA. For example, stackoverflow.com uses Let's Encrypt to sign its servers, and SSL certificates send by stackoverflow.com mention they are signed by Let's Encrypt. Your browser contains the CA certificate from Let's Encrypt and so the browser can use that CA certificate to verify the stackoverflow's SSL certificate and make sure you are indeed talking to real server, not man-in-the-middle. https://security.stackexchange.com/a/20833/233126 provides a more detail explanation about how TLS/SSL certificates work._ (Chen, 2020)

2.  Add Docker’s official GPG key:

        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

        gpg: WARNING: unsafe ownership on homedir '/home/forest/.gnupg'

3.  Use the following command to set up the stable repository. To add the nightly or test repository, add the word nightly or test (or both) after the word stable in the commands below. Learn about nightly and test channels.

        echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

4.  Install.

        sudo apt-get update
        sudo apt-get install docker-ce docker-ce-cli containerd.io

5.  Start the service and configure permission.

        sudo systemctl start docker
        sudo usermod -aG docker "${USER}"
        sudo newgrp docker

6.  Test run.

        docker run docker/whalesay cowsay 'install successful'
        ____________________
        < install successful >
        --------------------
            \
             \
              \
                            ##        .
                      ## ## ##       ==
                  ## ## ## ##      ===
              /""""""""""""""""___/ ===
          ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
              \______ o          __/
                \    \        __/
                  \____\______/

<br>

### 🐳Swarming starts here.

- Create a `swarm cluster`.
- Add `nodes` to the `swarm cluster` as `workers` and as `masters`.

<br>

### `Swarm Manager`

- `docker swarm init`
- `docker swarm init --advertise-addr <IP addr>`
- `docker swarm join --token <TOKEN>`
- `docker swarm leave --force`
- `docker swarm join-token manager`

      $ docker swarm init
      Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on different interfaces (10.42.106.6 on eth0 and 172.25.0.93 on eth1) - specify one with --advertise-addr

1.  The above happens because the current host has multiple interfaces and multiple IP addresses - it has two interfaces connected to two separate networks.

    $ docker swarm init --advertise-addr 172.25.0.93
    Swarm initialized: current node (6039ej4zlaajd6rlc3w77l2cs) is now a manager.

    To add a worker to this swarm, run the following command:

          docker swarm join --token SWMTKN-1-06vz9ck0q0irqk48ztpn6k5l0p5r0ljo3zrxmlxt3unf3yfmv4-99x4msreqr1298icz6tlecd4h 172.25.0.93:2377

    To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

In the above, `docker` has successfully initialized a `swarm cluster` with this node as the master node.

<br>

2. Join some workers, then check out the `swarm nodes`.

### `Worker node`

Join the node to the `swarm`.

        docker swarm join --token SWMTKN-1-5uwo54exfrheiu2muli8exlk6yuud3uev3o11quaa6gh4977rj-7wzb3ohunkuoigutziu7wznzr 172.16.239.2:2377
        This node joined a swarm as a worker.

### `Master node`

Now check the `nodes` on the `master`.

        docker node ls
        ID                            HOSTNAME        STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
        p5hz0a0twkroz0b4f5fpgg3ik     willow   Ready     Active                          20.10.12
        glp7924bmhvoquj4iwtk80wvd *   forest        Ready     Active         Leader           20.10.12

<br>

3. Let's add the other two nodes as master nodes.

- A single swarm cluster can have more than one master nodes.
- As good practice, `docker` recommends appointing more than one `manager node`.

### `Master node`

        docker swarm join-token manager

        To add a manager to this swarm, run the following command:

        docker swarm join --token SWMTKN-1-3403rk8fzj... 172.16.239.187:2377

### `Worker node`

        docker swarm join --token SWMTKN-1-3403rk8fzj...

        This node joined a swarm as a manager.

### `Master node`

Check the node list. Look at the `MANAGER STATUS`. Note that the origianl leader is labelled as `Leader`, and additionally appointed manager as `Reachable`.

        docker node ls
        ID                            HOSTNAME        STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
        ftvhqd4z99l9y8xq84rs89q0z *   forest          Ready     Active         Leader           20.10.12
        yay20tf97gnx2gjqx6kngk2ux     willow          Ready     Active         Reachable        20.10.12
        q60yl44bqsdfcidd07jg8y2q8     willow          Down      Active                          20.10.12

4. Promote a `worker node` to a `manager mode`.

### `Master node`

        docker node promote <worker node name>
        Node <worker node name> promoted to a manager in the swarm.

5. Update the `hostname` of a node.

### `Worker node`

        hostname <new name>

6. Simulate node failure.

If a `cluster` fails, you cannot perform any more managerial actions, such as maybe adding a new service or adding a new worker. However, the current running services
will continue to run as it should.

<br>

| Swarm Manager                                    | Worker                           |
| ------------------------------------------------ | -------------------------------- |
| `docker swarm init --advertise-addr 172.25.0.93` | `docker swarm join`              |
| `docker node ls`                                 | Cannot view nodes from worker.   |
| `docker swarm join-token manger`                 | paste the instruction code here. |
| `docker node promote <node name>`                |                                  |

<br>

### Workernode troubleshoots

1.  From on the `worker node`, it's not possible to view the `cluster`.

        docker node ls
        Error response from daemon: This node is not a swarm manager. Worker nodes can't be used to view or modify cluster state. Please run this command on a manager node or promote the current node to a manager.

2.  Cannot connect to the `swarm cluster`.

        Error response from daemon: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing dial tcp 172.16.239.187:2377: connect: connection refused"

3.  Cannot join the `cluster`.

        docker swarm join --token SWMTKN-1-3403   172.16.239.187:2377
        Error response from daemon: rpc error: code = Unknown desc = The swarm does not have a leader. It's possible that too few managers are online. Make sure more than half of the managers are online.

<br>
<br>

### Quiz

1.  How many nodes are there?

        $ docker node ls
        ID HOSTNAME STATUS AVAILABILITY MANAGER ENGINE VERSION
          60rlc3ws \* forest-plane Ready Active Leader 19.03.15

<br>

2. Initialise a `single-node swarm`.

You run into this error when you initiailise `swarm` again on a host where `swarm` is already running.

    $ docker swarm init
    Error response from daemon: This node is already part of a swarm. Use "docker swarm leave" to leave this swarm and join another one.

Check out the status.

    $ docker node ls
    ID                            HOSTNAME            STATUS                  ID           HOSTNAME       STATUS    AVAILABILITY   MANAGER  ENGINE VERSION
    60rlc3ws *   forest-plane   Ready     Active         Leader   19.03.15

Leave the current `swarm`.

- `docker swarm leave`
- `docker swarm leave --force`

      $ docker swarm leave
      Error response from daemon: You are attempting to leave the swarm on a node that is participating as a manager. Removing the last manager erases all current state of the swarm. Use `--force` to ignore this message.

      $ docker swarm leave --force
      Node left the swarm.

      $ docker node ls
      Error response from daemon: This node is not a swarm manager. Use "docker swarm init" or "docker swarm join" to connect this node to swarm and try again.

3.  Create a service with 3 `replicas`.

        $ docker service create --name simple-forest-app --replicas 3 -p 8083:8080 -e APP_COLOR=pink forest/forest-app
        m6kdgxgz8jdk1kdphux5z03ju
        overall progress: 3 out of 3 tasks
        1/3: running
        2/3: running
        3/3: running
        verify: Service converged

4.  Scale the `replicas`.

        $ docker service update simple-forest-app --replicas 4
        simple-forest-app
        overall progress: 4 out of 4 tasks
        1/4: running
        2/4: running
        3/4: running
        4/4: running
        verify: Service converged

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
- Ding-Yi Chen (2020) What is CA certificate, and why do we need it?
  https://stackoverflow.com/questions/40061263/what-is-ca-certificate-and-why-do-we-need-it
- Brandon Mitchell (2016) What’s the Docker Swarm “–advertise-addr”? https://boxboat.com/2016/08/17/whats-docker-swarm-advertise-addr/
