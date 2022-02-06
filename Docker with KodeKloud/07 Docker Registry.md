# <span id='top'>07 Docker Registry</span>

<br>

[[Registry]](#Registry)  
[[🤲Practice Makes Perfect]](#Perfect)  
[[References]](#ref)

<br>

## <span id='Registry'>Registry</span>

[[Top]](#top)

- `Registry`: a central repository of all Docker images; a storage and distribution system for named Docker images
  - By default, the images are stored on the `Docker Hub`.
  - `[User/account]/[Image/Repo]`
  - `nginx/nginx`
- Examples
  - Google's Registry is at gcr.io (https://cloud.google.com/container-registry/) stores a lot of `Kubernetes` related images
  - Amazon Elastic Container Registry
  - On-premises Registry
  - DockerHub
  - Azure Container Registry

<br>

### Private Registry

- `docker login private-registry.io`
- `docker run private-registry.io/apps/internal-app`
- The docker registry itself is another application.
  - Available as a docker image;
  - Name: `registry` expose on `5000`
- Working with the `registry`
  - `docker run -d -p 5000:5000 --name registry registry:2`
  - `docker image tag my-image localhost:5000/my-image`
  - `docker push localhost:5000/my-image`
  - `docker pull localhost:5000/my-image`
  - `docker pull 193.452.00.xxx:5000/my-image`: accessing from another host in my environment.

<br>

## <span id='Perfect'>🤲Practice Makes Perfect</span>

[[Top]](#top)

1.  Log in to a self-hosted registry

        docker login [SERVER]

2.  Host our own registry using the open source Docker Registry.

        $ docker run -d -p 5000:5000 --restart=always --name=tree-registry registry:2

3.  Push some images to our `tree-registry`.  
    Before pushing the images, it is important to `re-tag` them with docker image tag command.

- `docker pull [image]:[version]`
- `docker image tag [image]:[version] [server-addr]:[port]/[image]:[version]`
- `docker push [server-addr]:[port]/[image]:[version]`

4.  Check if the images are successfully pushed.

        $ curl -X GET localhost:5000/v2/_catalog
        {"repositories":["httpd","nginx"]}

5.  Prune your local image repository.

        $ docker image prune -a
        WARNING! This will remove all images without at least one container associated to them.

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
