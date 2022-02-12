# <span id='top'>07 Docker CI/CD</span>

<br>

[[🐳Docker CI/CD]](#cicd)  
[[🐳Docker Integration]](#Integration)  
[[🐳Docker Registry]](#Registry)  
[[🤲Hands-on 🐳Docker Registry]](#hands-on)  
[[Demo cheatsheet]](#cheatsheet)
[[References]](#ref)

<br>

## <span id='cicd'>🐳Docker CI/CD</span>

[[Top]](#top)

| Feature #1 | Code Repo          | Bulid System | Test<br>Framework   |
| ---------- | ------------------ | ------------ | ------------------- |
| Feature #2 |                    | ⚙️🧰         | ✔️Unit Test         |
| Bugfix #1  |                    |              | ✔️Web UI Test       |
|            |                    |              | ✔️Integration Test  |
|            | :octocat: `GitHub` | `👨Jenkins`  | `🤖Robot Framework` |

- Continuous Integration: the whole process of enabling multiple developers to work seamlessly on the same application without stepping on each other's toes. At the same time, CI ensures that these new changes integrate into the application without introducing any new issues.
- `Robot Framework`: a popular testing framework which tests your application APIs or web interfaces.

<br>

- Continuous Delivery
- Continuous Deployment: automatically updating the production environment with the changes in the application

Let's say we are releasing our new version of the application.

- Examples
  - Packaging the build application into an executable / rpm package / ISO and make it available online.

<br>

## <span id='Integration'>🐳Docker Integration</span>

[[Top]](#top)

| Code Repo          | Bulid System      | Test<br>Framework   | Release        |         |
| ------------------ | ----------------- | ------------------- | -------------- | ------- |
|                    |                   | ⚙️🧰                |                |         |
|                    |                   |                     | `🐳Docker hub` | `⛅AWS` |
| :octocat: `GitHub` | `👨Jenkins`       | `🤖Robot Framework` |                |         |
|                    | `🐳Docker plugin` |                     |                |         |

- Each project has a docker file checked into its Code Repository along with
- `👨Jenkins` then builds an image using the `🐳Dockerfile` template (`forest-app.1`).
- Then test it using a test framework such as `🤖Robot Framework`.
- Release the product on hubs like `🐳Docker hub`.
- Deploy the image in production completes a CI/CD pipeline.

<br>

###

| `Google Cloud Platform`      | `⛅AWS`                   | `⛅CF`<br>`Pivotal CF`                | `🐳Docker Cloud` |
| ---------------------------- | ------------------------- | ------------------------------------- | ---------------- |
| ` 🟪Google Container Engine` | `🧱EC2 Container Service` | `Pivotal Container Service `<br>(PKS) |                  |
| `☸️Kubernetes`               |                           |                                       |                  |

<br>

## <span id='Registry'>🐳Docker Registry</span>

[[Top]](#top)

| `Private Docker Registry`                      | Public Docker Registry<br>`🐳Docker Hub` |
| ---------------------------------------------- | ---------------------------------------- |
| `docker push localhost:5000/forest/forest-app` | `docker push forest/forest-app`          |
| `docker build . -t /forest/forest-app`         | `docker build . -t /forest/forest-app`   |
| `docker build . -d -p 5000:5000 registry:2`    |                                          |

- Note that configuring private address (`localhost`) pushes the images to a local registry.

<br>

## <span id='hands-on'>🤲Hands-on 🐳Docker Registry</span>

[[Top]](#top)

1. Pull some images from the public registry (`docker hub`) to your local machine.
2. Push one of them to your private local registry.
3. Remove the image which has been pushed to the private registry from the local machine.
4. Then pull back the image on the private registry to the local machine again.

https://labs.play-with-docker.com/

- `docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`: Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
- `docker push [PRIV REGISTRY]:[PORT]/[IMAGE]`

### Docker official walkthrough

_This image contains an implementation of the Docker Registry HTTP API V2 for use with Docker 1.6+. See github.com/distribution/distribution for more details about what it is._

_Run a local registry: Quick Version_

        $ docker run -d -p 5000:5000 --restart always --name registry registry:2

_Now, use it from within Docker:_

        $ docker pull ubuntu
        $ docker tag ubuntu localhost:5000/ubuntu
        $ docker push localhost:5000/ubuntu

### My hands-on

        $ docker run -d -p 5000:5000 --restart always --name registry registry:2
        $ docker pull hello-world
        $ docker pull docker/whalesay

Currently there are 3 images.

        $ docker images
        REPOSITORY                TAG        SIZE
        registry                  2          913MB
        hello-world               latest     902MB
        docker/whalesay           latest     105MB

Create a tag image.

        docker tag docker/whalesay localhost:5000/whalesay

Check the result. Now you've got 4 images - 3 from the public registry (`docker hub`), and 1 for private registry ready.

        $ docker images

        REPOSITORY                       TAG        SIZE
        registry                         2          913MB
        hello-world                      latest     902MB
        docker/whalesay                  latest     105MB   <----- FROM PUBLIC REGIT
        localhost:5000/docker/whalesay   latest     105MB   <----- FOR PRIVATE REGIT

`Push` an image to the `local registry`.

        docker push localhost:5000/whalesay

        Using default tag: latest
        The push refers to Repository [localhost:5000/hello-world]
        4353ss5c: Pushed
        latest: digest: sha256:23ewd3x322     size: 525

Check the image is successfully pushed. Check out your `local registry` server which is up and running.

        $ docker ps
        CONTAINER ID    IMAGE       STATUS         NAMES
        c7cd9642f5d7    registry:2  Up 3 seconds   coolest-whale

Let's find out WHERE your `local registry` stores your images. First, enter the registry server

        $ docker exec -it registry /bin/sh

### Inside the registry server:

https://docs.docker.com/registry/deploying/#storage-customization

- Default storage location: `/var/lib/registry/`
- Image paths: `/var/lib/registry/docker/registry/v2/Repositories/whalesay/_layers/sha256`

`local registry`
├──`var`
│ㅤ└──`lib`
│ㅤㅤㅤ└──`registry`...

<br>

### Back on the host

      $ docker images

      REPOSITORY                TAG        SIZE
      registry                  2          913MB
      hello-world               latest     902MB
      docker/whalesay           latest     105MB   <----- FROM PUBLIC REGIT
      localhost:5000/whalesay   latest     105MB   <----- FOR PRIVATE REGIT


      $ docker rmi localhost:5000/whalesay
      Untagged: localhost:5000/whalesay:latest
      Untagged: localhost:5000/whalesay:latest@sha256:2134ew...

<br>

### Pull an image from private registry to local machine.

      docker pull localhost:5000/whalesay-private

<br>

### Docker Registry Frontend Application

Command Template

      sudo docker run \
        -d \
        -e ENV_DOCKER_REGISTRY_HOST=ENTER-YOUR-REGISTRY-HOST-HERE \
        -e ENV_DOCKER_REGISTRY_PORT=ENTER-PORT-TO-YOUR-REGISTRY-HOST-HERE \
        -p 8080:80 \
        konradkleine/docker-registry-frontend:v2

Custom

      sudo docker run \
        -d \
        -e ENV_DOCKER_REGISTRY_HOST=registry \
        -e ENV_DOCKER_REGISTRY_PORT=5000 \
        -p 8080:80 \
        --link registry:registry
        konradkleine/docker-registry-frontend:v2

<br>

## <span id='cheatsheet'>Demo cheatsheet</span>

### Local host

1.  Get registry image

        docker pull registry:2

2.  Start a private registry server

        docker run -d -p 5000:5000 --restart always --name forest-registry registry:2

3.  Then pull some images to the local host.

        docker pull docker/whalesay

        docker run docker/whalesay cowsay "Let's push this whale to the private registry!"

4.  Create a tagged image for the private registry.

        docker run docker/whalesay cowsay "Now we create 2 tagged images to push to the private registry"

        docker tag docker/whalesay localhost:5000/whalesay-private
        docker push localhost:5000/whalesay-private

5.  Check, check!

        docker run docker/whalesay cowsay "We can check if the pushed whale is well located on the registry server."

        docker exec -it registry /bin/sh

        cd /var/lib/registry/docker/registry/v2/repositories;ls

        exit

6.  Pull the registry Frontend application.

        docker run docker/whalesay cowsay "Now it's frontend funtime!"

        docker pull konradkleine/docker-registry-frontend:v2

7.  Run it.

        sudo docker run \
            -d \
            -e ENV_DOCKER_REGISTRY_HOST=registry \
            -e ENV_DOCKER_REGISTRY_PORT=5000 \
            -p 8080:80 \
            --link registry:registry
            konradkleine/docker-registry-frontend:v2

8.  Open the frontend to see if images are being successfully lodged on the private registry.

9.  Push some more images to the registry and refresh the frontend page.

           docker run docker/whalesay cowsay "Let's push some more images to see the private registry in action!"

           docker pull hello-world
           docker pull alpine
           docker pull nginx

           docker tag hello-world localhost:5000/hello-world-private
           docker tag alpine localhost:5000/alpine-private
           docker tag nginx localhost:5000/nginx-private

           docker push localhost:5000/hello-world-private
           docker push localhost:5000/alpine-private
           docker push localhost:5000/nginx-private

10. FIN

            docker run docker/whalesay cowsay "We've successfully played around with docker registry! Have fun y'all!"

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
- Docker Storage Customisation https://docs.docker.com/registry/deploying/#storage-customization
- Docker `registry` image https://hub.docker.com/_/registry
- konradkleine https://hub.docker.com/r/konradkleine/docker-registry-frontend
