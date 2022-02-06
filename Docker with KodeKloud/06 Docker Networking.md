# <span id='top'>06 🐳Docker Networking</span>

[[Networks]](#Networks)  
[[🤲Practice Makes Perfect]](#Perfect)  
[[References]](#ref)

<br>

## <span id='Networks'>Networks</span>

[[Top]](#top)

| Bridge<br>(default)                                                     | None                                                                                                                                                               | Host                                                                                                                                                                                  |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A private internal network created by docker on the host.               | Containers are not attached to any network<br>- doesn't have any access to the external network or other containers.<br>- The container is on an isolated network. | Used to access the containers from externally                                                                                                                                         |
| `docker run ubuntu`                                                     | `docker run Ubuntu --network=none`                                                                                                                                 | `docker run Ubuntu --network=host`                                                                                                                                                    |
| Are given internal IPs<br>- Range usually between: `172.17.x.x` series. |                                                                                                                                                                    | - Uses the same port as the host<br>- Cannot run multiple containers on the `[same host]:[same port]`<br>- Because the `ports` are now commont to all containers in the host network. |

### User-defined network

- By default, 🐳Docker only creates one internal bridge network.
- We can create our own internal network using the command `Docker network create` and specify the `driver`.

  - `dodcker netowrk create --driver bridge --subnet 127.53.0.0/16 custom-isolated-network`

            $ docker run -d --name test-server nginx:latest
            9d56

            $ docker inspect 9d56

                "Networks": {
                    "bridge": {
                        "IPAMConfig": null,
                        "Links": null,
                        "Aliases": null,
                        "NetworkID": "4e80e3e112df49",
                        "EndpointID": "e76cc803b25f92",
                        "Gateway": "172.12.0.1",
                        "IPAddress": "172.12.0.2",
                        "IPPrefixLen": 24,
                        "IPv6Gateway": "",
                        "GlobalIPv6Address": "",
                        "GlobalIPv6PrefixLen": 0,
                        "MacAddress": "02:42:ac:0c:00:02",
                        "DriverOpts": null
                    }
                }

<br>

### Embedded DNS

🐳Docker comes with a built-in DNS server: `127.0.0.11`

- 🐳Docker uses network which creates a separate namespace for each container.
- then uses virtual Ethernet pairs to connect containers together.

<br>

## <span id='Perfect'>🤲Practice Makes Perfect</span>

1.  Know your network: `docker network ls`

        $ docker network ls
        NETWORK ID          NAME                DRIVER              SCOPE
        4e80e3e112df        bridge              bridge              local
        5de628a52135        host                host                local
        e32507f046e4        none                null                local

2.  Study each network.

**Isolated network**

        $ docker inspect e32507f046e4
        [
            {
                "Name": "none",
                "Id": "e32507f046e45eccdbb65010138f2f0c7bbbc80f5160e26ef97691db2faa098a",
                "Created": "2021-05-08T03:28:57.796704323Z",
                "Scope": "local",
                "Driver": "null",
                "EnableIPv6": false,
                "IPAM": {
                    "Driver": "default",
                    "Options": null,
                    "Config": []
                },
                "Internal": false,
                "Attachable": false,
                "Ingress": false,
                "ConfigFrom": {
                    "Network": ""
                },
                "ConfigOnly": false,
                "Containers": {},
                "Options": {},
                "Labels": {}
            }
        ]

**Host network**

        $ docker inspect 5de628a52135
        [
            {
                "Name": "host",
                "Id": "5de628a521355060cff1ddf0528bd34bc17bb837083cc7193f42507e26fe524a",
                "Created": "2021-05-08T03:28:57.829499691Z",
                "Scope": "local",
                "Driver": "host",
                "EnableIPv6": false,
                "IPAM": {
                    "Driver": "default",
                    "Options": null,
                    "Config": []
                },
                "Internal": false,
                "Attachable": false,
                "Ingress": false,
                "ConfigFrom": {
                    "Network": ""
                },
                "ConfigOnly": false,
                "Containers": {},
                "Options": {},
                "Labels": {}
            }
        ]

**Bridge network**

        $ docker inspect 4e80e3e112df
        [
            {
                "Name": "bridge",
                "Id": "4e80e3e112df49e033db0332dede99f5f9f903556ed48346087fb25a0e64400c",
                "Created": "2022-02-06T16:34:16.162198089Z",
                "Scope": "local",
                "Driver": "bridge",
                "EnableIPv6": false,
                "IPAM": {
                    "Driver": "default",
                    "Options": null,
                    "Config": [
                        {
                            "Subnet": "172.12.0.0/24",
                            "Gateway": "172.12.0.1"
                        }
                    ]
                },
                "Internal": false,
                "Attachable": false,
                "Ingress": false,
                "ConfigFrom": {
                    "Network": ""
                },
                "ConfigOnly": false,
                "Containers": {
                    "9d566ef2edad45ccabdcd996934ea41a0e3595b07387d931f167cb43f126bf9a": {
                        "Name": "test-server",  <------------- Server I created. because by default bridge
                        "EndpointID":          "e76cc803b25f92b3965a91b8589709917fdfef6de7614a147217fb3de5b577a8",
                        "MacAddress": "02:42:ac:0c:00:02",
                        "IPv4Address": "172.12.0.2/24",
                        "IPv6Address": ""
                    }
                },
                "Options": {
                    "com.docker.network.bridge.default_bridge": "true",
                    "com.docker.network.bridge.enable_icc": "true",
                    "com.docker.network.bridge.enable_ip_masquerade": "true",
                    "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
                    "com.docker.network.bridge.name": "docker0",
                    "com.docker.network.driver.mtu": "1500"
                },
                "Labels": {}
            }
        ]

3.  Inspect a container attached to a certain network.

        $ docker inspect f9d8775ce51a

        "NetworkSettings": {
                    "Bridge": "",
                    "SandboxID": "d428e7d120fd10ea4ea4de0774d8445ee43132aeafe353634765441602d61c60",
                    "HairpinMode": false,
                    "LinkLocalIPv6Address": "",
                    "LinkLocalIPv6PrefixLen": 0,
                    "Ports": {},
                    "SandboxKey": "/var/run/docker/netns/default",
                    "SecondaryIPAddresses": null,
                    "SecondaryIPv6Addresses": null,
                    "EndpointID": "",
                    "Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "MacAddress": "",
                    "Networks": {
                        "host": {                   <------------------------ On the host network.
                            "IPAMConfig": null,
                            "Links": null,
                            "Aliases": null,
                            "NetworkID": "5de628a521355060cff1ddf0528bd34bc17bb837083cc7193f42507e26fe524a",
                            "EndpointID": "3afb76fb04ada7381caa074396181d1c0c854406ee74d96cd9b31fa6545acad8",
                            "Gateway": "",
                            "IPAddress": "",
                            "IPPrefixLen": 0,
                            "IPv6Gateway": "",
                            "GlobalIPv6Address": "",
                            "GlobalIPv6PrefixLen": 0,
                            "MacAddress": "",
                            "DriverOpts": null
                        }
                    }
                }

4.  Now that a new container is running on the `host network`, you can check out the updated information through inspection.

        $ docker inspect 5de628a52135

        [
            {
                "Name": "host",
                "Id": "5de628a521355060cff1ddf0528bd34bc17bb837083cc7193f42507e26fe524a",
                "Created": "2021-05-08T03:28:57.829499691Z",
                "Scope": "local",
                "Driver": "host",
                "EnableIPv6": false,
                "IPAM": {
                    "Driver": "default",
                    "Options": null,
                    "Config": []
                },
                "Internal": false,
                "Attachable": false,
                "Ingress": false,
                "ConfigFrom": {
                    "Network": ""
                },
                "ConfigOnly": false,
                "Containers": {
                    "f9d8775ce51a2e62cc0f48eeda38d710710c51b78d391dccee0c1bbdbd348e34": {           <----- this is newly filled.
                        "Name": "alpine-1",
                        "EndpointID": "3afb76fb04ada7381caa074396181d1c0c854406ee74d96cd9b31fa6545acad8",
                        "MacAddress": "",
                        "IPv4Address": "",
                        "IPv6Address": ""
                    }
                },
                "Options": {},
                "Labels": {}
            }
        ]

5.  The `subnet` of a `bridge` network?

        $ docker inspect 4e80e3e112df
        [
            {
                "Name": "bridge",
                "Id": "4e80e3e112df49e033db0332dede99f5f9f903556ed48346087fb25a0e64400c",
                "Created": "2022-02-06T16:34:16.162198089Z",
                "Scope": "local",
                "Driver": "bridge",
                "EnableIPv6": false,
                "IPAM": {
                    "Driver": "default",
                    "Options": null,
                    "Config": [
                        {
                            "Subnet": "172.12.0.0/24",   <----------------------- Look here.
                            "Gateway": "172.12.0.1"
                        }
                    ]
                },
                ...

6.  Create a container with a network configuration.

        $ docker run -d --network=none --name=none-server1 alpine
        bbc246e

        $ docker inspect bbc246e

        ...

                    "Networks": {
                "none": {                   <------------------------ None-type network
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "e32507f046e45eccdbb65010138f2f0c7bbbc80f5160e26ef97691db2faa098a",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }
        ...

7.  Now create a network yourself.

`$ docker network create --driver bridge --subnet 182.18.0.1/24 --gateway 182.18.0.1 forest-network`

        69268e

        $ docker inspect 6926
        [
            {
                "Name": "forest-network",
                "Id": "69268e",
                "Created": "2022-02-06T17:16:37.58164896Z",
                "Scope": "local",
                "Driver": "bridge",
                "EnableIPv6": false,
                "IPAM": {                                   <------ IP Address Management
                    "Driver": "default",                    <------ "bridge"
                    "Options": {},
                    "Config": [
                        {
                            "Subnet": "182.18.0.1/24",      <------ Configuration applied.
                            "Gateway": "182.18.0.1"
                        }
                    ]
                },
                "Internal": false,
                "Attachable": false,
                "Ingress": false,
                "ConfigFrom": {
                    "Network": ""
                },
                "ConfigOnly": false,
                "Containers": {},
                "Options": {},
                "Labels": {}
            }
        ]
        $

8.  Run a container by attaching it to a specific network.

        $ docker run -d --name=forest-db -e MYSQL_ROOT_PASSWORD=dbpassword1379 --network=forest-db-network  mysql:5.6

        Unable to find image 'mysql:5.6' locally
        5.6: Pulling from library/mysql
        35b2232c987e: Pull complete
        fc55c00e48f2: Pull complete
        0030405130e3: Pull complete
        e1fef7f6a8d1: Pull complete
        1c76272398bb: Pull complete
        f57e698171b6: Pull complete
        f5b825b269c0: Pull complete
        dcb0af686073: Pull complete
        27bbfeb886d1: Extracting [===================================>               ]  45.12MB/64.27MB
        6f70cc868145: Download complete
        1f6637f4600d: Download complete

9.  Run an application using the network and db created through the previous steps.

        docker run --network=forest-network -e DB_Host=forest-db -e DB_Password=forest1379 -p 38080:8080 --name forest-webapp --link forest-db:forest-db -d forest/simple-forest-webapp

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
