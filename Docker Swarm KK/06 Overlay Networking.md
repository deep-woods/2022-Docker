# <span id='top'>06 Overlay Networking</span>

<br>

[[Overlay/Ingress Network]](#networks)  
[[Embedded DNS]](#DNS)  
[[References]](#ref)

<br>

## <span id='networks'>Overlay/Ingress Network</span>

[[Top]](#top)

- Overlay networks in Docker Swarm
- Embedded DNS servers
- Routing mesh

`Network`
├──`Bridge` = `docker run ubuntu`
├──`None` = `docker run ubuntu --network=none`
└──`Host` = `docker run ubuntu --network=host`

- `None`: isolated network. Doesn't have any access to the external network or other containers.

- _IP address is address of the system in the Network. Port is address of the service within the System. So IP address + Port defines address of the particular service on the particular system. Think about how many application layer protocols exist (HTTP, FTP,DNS, SSH, etc)_ (Poroh, 2016).

- `Overlay network`: a new network of type overlay that spans across all the nodes participating in the Swarm cluster.
  - `docker netowrk create --driver overlay --subnet 10.0.0.0/24 my-overlay-network`
  - `docker service create --replica 5 --network my-overlay-network nginx`
- `Ingress network`: has a built-in load balancer that redirects traffic
  from the published port to all the mapped ports. The Ingress network
  is created automatically so no configuration is required.
  - The ingress network is a special overlay network that **facilitates _load balancing_ among a service’s nodes**. When any swarm node receives a request on a published port, it hands that request off to a module called IPVS. IPVS keeps track of all the IP addresses participating in that service, selects one of them, and routes the request to it, over the ingress network.
  - `docker run -p 80:5000 my-web-server`
  - port publishing or port mapping: for an external user to access to web service, the port must be mapped to a port on the Docker host.

<br>

## <span id='DNS'>Embedded DNS</span>

[[Top]](#top)

the built-in DNS server always runs
at address 127.0.0.11

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
- Dmitry Poroh (2016) What is Difference between IP address and Port Number in Networking? https://stackoverflow.com/questions/37496411/what-is-difference-between-ip-address-and-port-number-in-networking#:~:text=IP%20address%20is%20address%20of,service%20on%20the%20particular%20system.&text=Think%20about%20how%20many%20application,DNS%2C%20SSH%2C%20etc).
