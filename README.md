# 2022-Docker

<br>

### Resources

Docker Certified Associate Exam Preparation Guide (v1.0.1) https://dockerlabs.collabnix.com/docker/dca.html

<br>

[[Mini project 1 - Example Voting App]](#votingapp)  
[[Mini project 2 -]](#)  
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
- Notes: the inputs from the 1`voting-app` is cached on 2`redis`, and is sent to the 3`postgres` db by the 4`worker`. The result is then displayed on 5`result-app`.

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

## <span id=''></span>

[[Top]](#top)

<br>

<br>
<br>

### <span id='ref'>References</span>

[[Top]](#top)

- Docker's official samples https://github.com/dockersamples
- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
