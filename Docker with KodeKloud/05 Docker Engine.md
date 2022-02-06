# <span id='top'>05 Docker Engine</span>

<br>

[[🐳Docker Engine]](#engine)  
[[References]](#ref)

<br>

## <span id='engine'>🐳Docker Engine</span>

[[Top]](#top)

`🐳Docker Engine`
├──`Docker CLI`
├──`REST API`: the API interface that programs can use to talk to the demon and provide instructions
└──`Docker Daemon`: a background process that manages `Docker objects` such as the images containers, volumes and networks.

`docker -H=remote-docker-engine:2012`
`docker -H=123.23.0.1:2012 run nginx`

<br>

### 🐳Docker `nameaspace`

- 🐳Docker uses `namespace` to isolate workspace. process ids, network inter-process communication, mounts, and Unix time sharing systems.
  - This provides isolation between containers.

`namespace`
├──`Process ID`
├──`Unix Timesharing`
├──`Mount`
├──`Inter-process`
└──`Network`

<br>

### `namespace - PID`

- Whenever a Linux system boots up, it starts with just one process with a `process id` of 1.

**Linux system**

`PID: 1`
├──`PID: 2`
├──`PID: 3`
├── ...
├── ...
└──`PID: n`

**Child system (container)**

`PID: 1`
├──`PID: 2`
├──`PID: 3`
└──`PID: 4`

Although there are same `process id`s in both parent host and the child container, the namespace technology ensures that each PID is unique across the entire Linux system.

- `docker ps`: the same service but with a different process ID. This indicates that all processes are in fact running on the same host but separated into their own.

<br>

### Docker Architecture and resource control

|                         | **Linux System**        |                         |
| ----------------------- | ----------------------- | ----------------------- |
| `Docker`<br>`Container` | `Docker`<br>`Container` | `Docker`<br>`Container` |
| CPU                     |                         | Memory                  |

- `Docker host` and `Docker containers` share the same system resources such as CPU and memory.
- By default, there is no limit in how much resources containers can use.
- However, `Docker` can restrict the amount of hardware resources allocated to each container.

<br>

`docker run --cpus=.5 ubuntu`

- This ensures that the container does not take up more than 50 percent of the host at any given time.

`docker run --memory=100m ubuntu`

- Limits the amount of memory a container can use up to 100 MB.

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
