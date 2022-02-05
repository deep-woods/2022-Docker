# <span id='top'>02 🐳Docker Run Hands-on</span>

<br>

[[Versioning]](#versioning)  
[[Detach mode]](#detach)  
[[Attach mode]](#attach)  
[[Practice Makes Perfect]](#perfect)  
[[References]](#ref)

<br>

## <span id='versioning'>Versioning</span>

[[Top]](#top)

    docker run ubuntu cat /etc/*release*

    NAME="Ubuntu"
    VERSION="20.04.2 LTS (Focal Fossa)"   <------ Your current container version
    ID=ubuntu
    ID_LIKE=debian
    PRETTY_NAME="Ubuntu 20.04.2 LTS"
    VERSION_ID="20.04"
    HOME_URL="https://www.ubuntu.com/"
    SUPPORT_URL="https://help.ubuntu.com/"
    BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
    PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
    VERSION_CODENAME=focal
    UBUNTU_CODENAME=focal

## Supported tags and respective 🐳`Dockerfile` links

Explore the tags below.

- `18.04`, `bionic-20220128`, `bionic`
- `20.04`, `focal-20220113`, `focal`, `latest`
- `21.04`, `hirsute-20220113`, `hirsute`
- `21.10`, `impish-20220128`, `impish`, `rolling`
- `22.04`, `jammy-20220130`, `jammy`, `devel`
- `14.04`, `trusty-20191217`, `trusty`
- `16.04`, `xenial-20210804`, `xenial`

<br>

Provide a tag this time.

$ docker run ubuntu:18.04 cat /etc/_release_ <--- Specify tag upon running

    Unable to find image 'ubuntu:18.04' locally
    18.04: Pulling from library/ubuntu
    68e7bb398b9f: Pull complete
    Digest: sha256:c2aa13782650aa7ade424b12008128b60034c795f25456e8eb552d0a0f447cad
    Status: Downloaded newer image for ubuntu:18.04

    NAME="Ubuntu"
    VERSION="18.04.6 LTS (Bionic Beaver)"
    ID=ubuntu
    ID_LIKE=debian
    PRETTY_NAME="Ubuntu 18.04.6 LTS"
    VERSION_ID="18.04"
    HOME_URL="https://www.ubuntu.com/"
    SUPPORT_URL="https://help.ubuntu.com/"
    PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
    BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
    VERSION_CODENAME=bionic
    UBUNTU_CODENAME=bionic

<br>

## <span id='detach'>Detach mode</span>

[[Top]](#top)

Without `-d` tag, your terminal is stuck with the process running in the container.

    $ docker run ubuntu sleep 1500

    ^C

    ^C
    ^Z^X^C^X^C^X^C^X
    dslfkj                  <---- can't get out.

Now run a container in `-d` (detach) mode. You can freely do other things (such as checking the running processes) as the sleep process is running in the background.

    $ docker run -d --name coolest-ubuntu ubuntu:22.04 sleep 1500
    c7cd964

    $ docker ps
    CONTAINER ID    IMAGE          COMMAND         CREATED          STATUS         NAMES
    c7cd9642f5d7    ubuntu:22.04   "sleep 1500"    4 seconds ago    Up 3 seconds   coolest-ubuntu

<br>

## <span id='attach'>Attach mode</span>

[[Top]](#top)

    $ docker run -d timer
    2r2r23d3

    $ docker ps
    CONTAINER ID    IMAGE   STATUS
    2r2r23d3        timer   7 seconds ago

But you cannot see the output. You can see it once you switch back on to the `attach` mode.

    $ docker attach 2r2r23d3

    Fri Feb 04 03:22:12 UTC 2022    <------ Output again
    Fri Feb 04 03:22:13 UTC 2022
    Fri Feb 04 03:22:14 UTC 2022
    Fri Feb 04 03:22:15 UTC 2022
    Fri Feb 04 03:22:16 UTC 2022
    Fri Feb 04 03:22:17 UTC 2022

<br>

## <span id='perfect'>Practice Makes Perfect</span>

[[Top]](#top)

`Jenkins`: Jenkins Continuous Integration and Delivery web server.

Let's run a `jenkins` server.

    $ docker ps
    CONTAINER ID        IMAGE               PORT                  NAME
    35473fe72480        jenkins/jenkins     8080/tcp, 50000/tcp   admiring_bell

Now we want to access the container from our host. How do we do that?

1. Use the `internal IP`: See the IP indicated by an arrow in the snippet below. We should use this if we were to access this docker container from inside our host.
2. Map the port to the docker host and access it using the external IP.

   "Networks": {
   "bridge": {
   "IPAMConfig": null,
   "Links": null,
   "Aliases": null,
   "NetworkID": "93c257ff94a0ee2404e982050a06044ee306b5ed1293c96acaa247730f577700",
   "EndpointID": "2de88913649663fe9de73e81bd391911826fc5f6334115f0461623074223bcee",
   "Gateway": "172.12.0.1",
   "IPAddress": "172.12.0.3", <------ Jenkins container IP
   "IPPrefixLen": 24,
   "IPv6Gateway": "",
   "GlobalIPv6Address": "",
   "GlobalIPv6PrefixLen": 0,
   "MacAddress": "02:42:ac:0c:00:03",
   "DriverOpts": null
   }
   }

Configure the port when running.

    $ docker run -d -p 9000:9000 --name accessible-jenkins jenkins/jenkins
    e124fb36bec959b1e18774dc54379c28bcc61c7c84dbd66c7da50642501ce3b6

<br>

### Volume mapping on `Jenkins`

🐳`Docker run -p 8080:8080 -p 50000:50000 -v /root/jenkins-data-host:/var/jenkins_home jenkins/jenkins`

    mkdir jenkins-data-host
    docker run -p 8080:8080 -v /root/jenkins-data-host:/var/jenkins_home jenkins/jenkins
    Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
    ----> Permission issue

🐳`Docker run -p 8080:8080 -p 50000:50000 -v /root/jenkins-data-host:/var/jenkins_home -u root jenkins/jenkins`  
Then create a `test job`.

<br>

`Volume mapping` allows all the custom data generated by the docker container to be stored locally on our docker host.

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- https://kodekloud.com/courses/docker-for-the-absolute-beginner/
- https://www.jenkins.io/blog/2018/12/10/the-official-Docker-image/
- https://hub.docker.com/r/jenkins/jenkins/
