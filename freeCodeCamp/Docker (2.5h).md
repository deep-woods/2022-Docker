# 🐳 2022-Docker

  - Docker forest
  - All PPT slide images from: [freeCodeCamp docker tutorial by Nelson](#ref)

Docker `images` are read-only templates🖼️ used to build `containers`. Containers are _**deployed instances**_ created from those templates. Images and containers are closely related, and are essential in powering the Docker software platform.

<br>

<span id="top">Learn index</span>

[Basics](#basiocs)  
[⚓Port](#port)  
[Volume](#volume)  
[🐳Dockerfile](#dockerfile)  
[Image](#image)  
[Node JS and Express JS](#js)  
[🐳Dockerise API](#dockerapi)   
[🖼️Reduce image size](#imagesize)   
[Tags, Versioning, and Tagging](#versiontag)   
[🐳Docker Registraies](#registry)   
[🐳Docker inspect](#inspect)   
[🐳Docker log](#log)   
[🐳Docker exec](#exec)   


<br>

## <span id="basics">Basics</span>

[[☝️top]](#top)

  **Check the list of running containers**

    - `docker container ls` 
    - `docker ps`: process status 

<br>

  **Run a container**

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/00%20docker%20run%20nginx.png" width=550 />

    - `docker run -d nginx:latest`
      - `-d` tag

  **Stop a container**

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/01%20docker%20stop.png" width=550 />

    - `docker stop CONTAINER_ID`

  **Remove a container(s)**

    - `docker rm CONTAINER_ID`
    - `docker rm $(docker ps -aq)`

<br>

  **Name a container**
  
    - `docker run --name CONTAINER_NAME -d -p 3000:80 -p 8080:80 nginx:latest`
    - `docker run --name website -d -p 3000:80 -p 8080:80 nginx:latest`

  **Rename a container**

    - `docker rename my_container my_new_container`

<br>

**Interactive mode**

- `docker exec -it dir_name bash`

<br>

## <span id="port">⚓Port</span>

[[☝️top]](#top)

  **Format process status data**

    - format: `docker ps --format="ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"`
    - unformat: `docker FORMAT="ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"`

 <br>


**Expose a port⚓**</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2001.png" width=600 />


    - `localhost:8080`
    - `Port 8080 on host maps`
    - Map the local host port (8080) to container port (80)
        - ` -p 8080:80`
        - `docker run -d -p 8080:80 nginx:latest`
    - Map to a different port
        - `docker run -d -p 3000:80 nginx:latest`

  **Exposing multiple ports⚓⚓**

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2002.png" width=600 />

  <img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/02%20docker%20run%20on%20different%20port.png" width=550 />
<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/03%20docker%20run%20multiple%20ports.png" width=550 />

    - `docker run -d -p localhost1:PORT -p localhost2:PORT nginx:latest`
    - `docker run -d -p 8080:80 -p 3000:80 nginx:latest`

<br>

## <span id="volumn">🧳Volume</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2003.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2004.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2005.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2006.png" width=600 />

<br>

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/05%20mounts%20volume.png" width=550 />

  **Volumnes between host and container**

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/06%20inside%20container.png" width=550 />

    - `docker run --name dir_name -v $(cd) -d -p 8080:80 nginx`
  
  **Bind a mount**
    
    - `docker run --name dir_name -v "ABSOLUTE_PATH":/usr/share/nginx/html:ro -d -p 3000:80 nginx`

  **Share files between volumes**

    - `docker run --name DIR2 --volumes-from DIR1 -d -p 8081:80 nginx`

<br>

  **Hands-on prectice: the order**

    - 1. Run your directory in a docker container: `docker run --name dir_name -v "ABSOLUTE_PATH":/user/share/nginx/html:ro -d -p 8080:80 nginx`
    - 2. Enter the container: `docker exec -it website bash`
    - 3. Change directory: `cd /usr/share/nginx/html/`

<br> 


## <span id="dockerfile">🐳Dockerfile</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2007.png" width=600 />


**Create docker file**

We don't have to mount a `volume` any longer if we build our own `image`. 

  - In your editor, create a `🐳Dockerfile` in the project root directory.
    - When building an `image`, you don't build one from scratch;
    - you will always build it upon an existing base image (based on the latest nginx, for instance).
      - `FROM nginx:latest`
      - `ADD . /usr/share/nginx/html` (everything in the root folder)

<br>

## <span id="image">🖼️Image</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2008.png" width=600 />


**Build an image**
from the 🐳Dockerfile

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/07%20build%20image.png" width=700 />

  - `docker build --tag dir_name:latest .`
    - The dot(`.`) matters here.
    - `[1/2] FROM docker.io/library/nginx:latest`
    - `[2/2] ADD . /usr/share/nginx/html`

  - `docker run --name CONTAINER_NAME PORT_NUM:80 -d IMAGE_NAME:latest`

<br>

## <span id="js">**Node JS and Express JS**</span>

[[☝️top]](#top)

Express.js: a back end web application framework for Node.js; designed for building web applications and APIs.

  1. Check if Node is installed. `node -v`
  2. Create a directory. `mkdir dir_name`
  3. Enter the directory. `cd dir_name` 
  4. Start npm `npm init`
  5. Install express `npm install --save express`
  6. `node index.js`
    - `Example app listening at http://localhost:3000`

      const express = require('express')
      const app = express()
      const port = 3000

      app.get('/', (req, res) => {
        res.send('Hello World!')
      })

      app.listen(port, () => {
        console.log(`Example app listening at http://localhost:${port}`)
      })

<br>

## <span id="dockerapi">**🐳Dockerise API**</span>

[[☝️top]](#top)

  - Step 1. Create an API ([see above](#js))
  - Step 2. Create 🐳Dockerfile
    - `FROM node:latest`
    - `WORKDIR /app`: set working dicrectory inside a container. Use this dir(`app`), otherwise create a new one. 
    - `ADD . .`: add everything (`first .`) to the current directory (`second .`)
    - `RUN npm install`: install all the dependencies from the package.json.
    - `CMD node index.js`

imagehere

Now we have an image for the `user-service-api` in `node` and `express.js`.

  - Step 3. Run 🧳containers for API.
    - `docker run --name user-api -d -p 3000:3000 user-service-api:latest`

<br>

**🐳`.dockerignore`**

  A list of files that Docker doesn't need duplicating when creating container. 
  - 🐳`.dockerignore`
    - file `*.js`
    - folder `folder/**`
 

**Caching and layers**

  - Load only changes into the container
    - `ADD package*.json .`: caching. Docker will know if the `package.json` has changed. Use what's been cached, then: 
    - `ADD . .`: the dot(.) is the source code

  - build image then containerise: 
    - `docker build -t IMAGE_NAME:latest .`
    - `docker run --name CONTAINER_NAME -d -p HOST_PORT:CONTAINER_PORT IMAGE_NAME:latest`

<br>

## <span id="imagesize">**🐳Reduce image size**</span>

[[☝️top]](#top)

**Pull alpine image**

  - `docker pull ENGINE_NAME:lts-alpine`: look for supported tag.
    - ex: `docker pull node:lts-alpine`

<br>

## <span id="versiontag">**Tags, Versioning, and Tagging**</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2009.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2010.png" width=600 />

Allows you to control image version.  
Avoids breaking changes.  
Safe.

**Use tags for veresioning**

  - 🐳`Dockerfile` >> `FROM node:16.13.2-alpine`

  - `docker run --name TARGET_IMAGE-VERSION -p PORT:80 -d DEST_IMAGE:VERSION_NUM`

**Tag Override** 

<br>

## <span id="registry">🐳**Docker Registries**</span>

[[☝️top]](#top)

<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2011.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2012.png" width=600 />
<img src="https://github.com/Coding-Forest/2022-Docker/blob/main/images/FCC%20Docker%2013.png" width=600 />

Two types of registries

- Public: you can share your images publicly so others can use them!
- Private 

**Push images to 🐳Docker hub**

  - `docker login`
  - `docker push userID/IMAGE_NAME:VER`

<br>

**Pull images from 🐳Docker hub**

  - `docker pull USER/IMAGE:VER`
    - `Using default tag: latest`
    
<br>

## <span id="inspect">**🐳Docker inspect**</span>

[[☝️top]](#top)

  - `docker inspect CONTAINER_ID`

<br>

## <span id="log">**🐳Docker log**</span>

[[☝️top]](#top)

  - `docker logs CONTAINER_ID`
  - `docker logs --help`

<br>

## <span id="exec">**🐳Docker exec**</span>

[[☝️top]](#top)

  - `docker exec --help`

<br>

### <span id="ref">References</span>

[[☝️top]](#top)

  - freeCodeCamp (2020) Docker and Kubernetes - Full Course for Beginners https://www.youtube.com/watch?v=Wf2eSG3owoA&t=1615s
