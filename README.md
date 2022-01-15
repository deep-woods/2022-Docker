# 🐳 2022-Docker

Docker forest

<br>

[Basics](#basiocs)  
[Port](#port)  
[Volume](#volume)  
[Dockerfile](#dockerfile)  
[Image](#image)  

<br>
<br>

## <span id="basics">Basics</span>

  **Check the list of running containers**

    - `docker container ls` 
    - `docker ps`: process status 

<br>

  **Run a container**

    - `docker run -d nginx:latest`
      - `-d` tag

  **Stop a container**

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

  **Format process status data**

    - format: `docker ps --format="ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"`
    - unformat: `docker FORMAT="ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"`

 <br>

  **Expose a port⚓**</span>

    - `localhost:8080`
    - `Port 8080 on host maps`
    - Map the local host port (8080) to container port (80)
        - ` -p 8080:80`
        - `docker run -d -p 8080:80 nginx:latest`
    - Map to a different port
        - `docker run -d -p 3000:80 nginx:latest`

  **Exposing multiple ports⚓⚓**

    - `docker run -d -p localhost1:PORT -p localhost2:PORT nginx:latest`
    - `docker run -d -p 8080:80 -p 3000:80 nginx:latest`

<br>

## <span id="volumn">🧳Volume</span>

  **Volumnes between host and container**

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

**Create docker file**

We don't have to mount a `volume` any longer if we build our own `image`. 

  - In your editor, create a `🐳Dockerfile` in the project root directory.
    - When building an `image`, you don't build one from scratch;
    - you will always build it upon an existing base image (based on the latest nginx, for instance).
      - `FROM nginx:latest`
      - `ADD . /usr/share/nginx/html` (everything in the root folder)

<br>

## <span id="image">🖼️Image</span>

**Build an image**
from the 🐳Dockerfile

  - `docker build --tag dir_name:latest .`
    - The dot(`.`) matters here.
    - `[1/2] FROM docker.io/library/nginx:latest`
    - `[2/2] ADD . /usr/share/nginx/html`

  - `docker run --name CONTAINER_NAME PORT_NUM:80 -d IMAGE_NAME:latest`

<br>

****

<br>

****

<br>