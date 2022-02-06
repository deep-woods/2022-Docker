# <span id='top'>05 🐳Docker Storage</span>

<br>

[[🐳Docker Storage]](#Storage)  
[[🐳Docker Volume]](#Volume)  
[[🤲Practice Makes Perfect]](#Perfect)  
[[References]](#ref)

<br>

## <span id='Storage'>🐳Docker Storage</span>

[[Top]](#top)

Upon installation, `🐳Docker` creates the following directory in the local system.

`/var/lib/docker/`
├──`aufs`
├──`containers`
├──`images`
├──`volumes`
├── ...

<br>

### Layered Architecture

Compare the following two `dockerfiles`.

`Dockerfile1`

    FROM Ubuntu

    RUN apt update && apt install -y python && apt install -y pip
    RUN pip install flask flask-mysql

    COPY . /opt/source-code

    ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run

<br>

`Dockerfile2`

    FROM Ubuntu

    RUN apt update && apt install -y python && apt install -y pip
    RUN pip install flask flask-mysql

    COPY server.py /opt/source-code

    ENTRYPOINT FLASK_APP=/opt/source-code/server.py flask run

| Dockerfile1                        | size   | Dockerfile2                  | size   |
| ---------------------------------- | ------ | ---------------------------- | ------ |
| Layer 1. Base Ubuntu Layer         | 120 MB | same                         | 0 MB   |
| Layer 2. Changes in `apt` packages | 306 MB | same                         | 0 MB   |
| Layer 3. Changes in `pip` packages | 5.4 MB | same                         | 0 MB   |
| Layer 4. Source code               | 243 MB | Layer 4. Source code         | 243 MB |
| Layer 5. Update `ENTRYPOINT`       | 0 B    | Layer 5. Update `ENTRYPOINT` | 0 B    |

<br>

### COPY-ON-WRITE

<br>

## <span id='Volume'>🐳Docker Volume</span>

[[Top]](#top)

`docker volume create data_volume`

- this creates the below directory.

`/var/lib/docker`
├──`volumes`
│ㅤㅤ├──`data_volume`
├── ...

<br>

**Syntax**

`docker run -v data_volume:/var/lib/mysql mysql`  
`docker run -v data_volume2:/var/lib/mysql mysql`  
`docker run -v /data/mysql:/var/lib/mysql mysql`  
`docker run --mount type=bind, source=/data/mysql,target=/var/lib/mysql mysql`: newer syntax

- Volume-mount: mounts the volume from the `volumes` directory.
- Bind-mount: mounts the volume from any location in the docker host.

<br>

### Storage Drivers

- `AUFS`
- `ZFS`
- `BTRFS`
- `Device Mapper`
- `Overlay`
- `Overlay2`

<br>

## <span id='Perfect'>🤲Practice Makes Perfect</span>

1.  Docker container directory: `/var/lib/docker/`

        docker run -d forest/simple-app
        ad3a5d8369c

        $ pwd; ls
        /var/lib/docker/containers
        ad3a5d8369c

2.  Identify specific container.

        $ docker ps -a
        CONTAINER ID       IMAGE        COMMAND      NAME
        705ff82001e3       alpine       "/bin/sh"    alpine-3
        1dc6d7aafb73       alpine       "/bin/sh"    alpine-2
        36a9c7bd9750       alpine       "/bin/sh"    alpine-1

        $ cd /var/lib/docker; ls
        builder  buildkit  containerd  containers  image  network  overlay2  plugins  runtimes  swarm  tmp  trust  volumes

        $ cd containers;ls
        1dc6d7aafb7
        705ff82001e         <---- alpine-3
        36a9c7bd975         <---- alpine-1
        ad3a5d8369c         <---- ??

3.  Create a db with specific requirements.

        docker run -d --name=mysql-db -e MYSQL_ROOT_PASSWORD=mysqlpass1937 mysql
        57cbf7c7951b

4.  Run `bash` file.

        $ bash get-data.sh
        mysql: [Warning] Using a password on the command line interface can be insecure.
        id      Name    Phone   Email
        ...     ...     ...     ...

5.  A scenario: Database has been lost.

        $ bash get-data.sh
        Error: No such container: mysql-db

Let's mount the volume so we don't have to lose valuable data in disaster situations next time.

        $ docker run -v /opt/data:/var/lib/mysql -d --name=mysql-db -e MYSQL_ROOT_PASSWORD=mysqlpass1937 mysql
        745b1dbe

6. Disaster Recovery Action!

Disaster can strike anytime, like this time.

        $ bash get-data.sh
        Error: No such container: mysql-db

So run yet another db container but this time mounting the container to the previously configured path `/opt/data`. Since data is stored there, the lost data is easily restored.

        $ docker run -v /opt/data:/var/lib/mysql -d --name=mysql-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql
        178d9bf108d

Restored as below:

        $ bash get-data.sh
        mysql: [Warning] Using a password on the command line interface can be insecure.
        id      Name    Phone   Email
        ...     ...     ...     ...

        $ cat get-data.sh
        docker exec mysql-db mysql -pdb_pass123 -e 'use foo; select * from bar'

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
