# 2022-Docker

<br>

<img src="https://github.com/deep-woods/2022-Docker/blob/main/images/Docker-for-Beginners.png" width=200 /> &nbsp; <img src="https://github.com/deep-woods/2022-Docker/blob/main/images/Docker-SWARM.png" width=200 />

<br>

### Revision Required

- Demo – Kubernetes on Google Cloud Platform https://kodekloud.com/topic/demo-kubernetes-on-google-cloud-platform/

### Key Concepts

- Docker architecture, servce, swarm, overlay networks, load balancing, CI/CD integration

### Resources

- Play with Docker https://labs.play-with-docker.com/
- Awesome-docker https://awesome-docker.netlify.app/
- Play with Kubernetes https://labs.play-with-k8s.com/

- Docker Certified Associate Exam Preparation Guide (v1.0.1) https://dockerlabs.collabnix.com/docker/dca.html

<br>

[[Mini project 1 - Example Voting App]](#votingapp)  
[[Mini project 2 - Docker Local Registry]](#Registry)  
[[Mini project 3 -]](#)  
[[Mini project 4 -]](#)  
[[Mini project 5 -]](#)  
[[Mini project 6 -]](#)  
[[]](#)  
[[References]](#ref)

<br>

## <span id='votingapp'>Mini project 1 - Example Voting App</span>

[[Top]](#top)

- App used: [Official Docker Samples - example-voting-app](https://github.com/dockersamples/example-voting-app)
- [App architecture](https://github.com/dockersamples/example-voting-app#architecture)
- [`Docker-compose.yml`](https://github.com/deep-woods/2022-Docker/blob/main/Docker%20with%20KodeKloud/docker-compose.yml)

<img src="https://github.com/deep-woods/2022-Docker/blob/main/Docker%20with%20KodeKloud/demo01-example-voting-app.gif" />

- Left: voting-app
- Right: result-app
- Notes: the inputs from the 1`voting-app` is cached on 2`redis`, and is sent to the `postgres` db by the 4`worker`. The result is then displayed on 5`result-app`.

<br>

## <span id='Registry'>Mini project 2 - Docker Local Registry</span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- Docker's official samples https://github.com/dockersamples
- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
