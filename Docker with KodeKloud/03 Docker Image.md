# <span id='top'>03 🐳Docker Image</span>

<br>

[[Dockerfile]](#Dockerfile)  
[[Layered Architecure]](#Layered)  
[[Practice Makes Perfect]](#Perfect)  
[[Mini Project]](#miniproject)

<br>
 
## <span id='Dockerfile'>Dockerfile</span>

[[Top]](#top)

Setting up an environment on a new machine is a tedious task. 🐳`Docker Image` can save us from having to repeat ourselves on tedious jobs.

**Process**

1. OS `Ubuntu`
2. Update `apt` repo
3. Install dependencies using `apt`
4. Install `python` dependencies using `pip`
5. Copy source code to `/opt` folder
6. Run the web server using `flask` command

<br>

**Dockerise the process into an image**

- Syntax: `[INSTRUCTION] arcument`

  - `FROM`: every 🐳`Dockerfile` starts with this.
  - `RUN`
  - `ENTRYPOINT`: specify a command that will be run when the image is run as a container.

        Dockerfile                <-------------------- Define base OS

        FROM Ubuntu

        RUN sudo apt Update               <------------ Execute something
        RUN sudo apt install -y python

        RUN sudo pip install -y flask
        RUN sudo pip install -y flask-mysql

        COPY . /opt/source-code

        ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run

<br>

## <span id='Layered'>Layered Architecure</span>

[[Top]](#top)

<br>

- 🐳`Docker build Dockerfile -t forest/custom-app`
- All the layers build are cached so the layered architecture helps you restart docker build from that particular step in case it fails. Or, if you were to add new steps in the build process you wouldn't have to start all over again.
  - This makes `image building` faster.
  - This is helpful especially when you update source code of your application as it may change more frequently
  - only the layers above the updated layers needs to be rebuild.
- Layered architecture overview by layer size

<br>

## <span id='Perfect'>Practice Makes Perfect</span>

[[Top]](#top)

<br>

    $ cat Dockerfile
    FROM python:3.6

    RUN pip install flask

    COPY . /opt/

    EXPOSE 8080                      <----- Container port

    WORKDIR /opt

    ENTRYPOINT ["python", "app.py"]  <----- Run command to execute app

<br>
<br>

## <span id='miniproject'>Mini Project</span>

[[Top]](#top)

    docker run -it --name flask-server ubuntu:20.04 bash

    apt update
    apt-get install -y python3
    apt-get install -y python-pip

    Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-Z6EKjW/PyMySQL/

    pip install --upgrade setuptools
    pip install --upgrade pip

    WARNING: pip is being invoked by an old script wrapper. This will fail in a future version of pip.
    Please see https://github.com/pypa/pip/issues/5599 for advice on fixing the underlying issue.
    To avoid this problem you can invoke Python with '-m pip' instead of running pip directly.

    pip install flask
    pip install flask-mysql

<br>

    cat > /opt/app.py

    import os
    from flask import Flask
    app = Flask(__name__)

    @app.route("/")
    def main():
        return "Welcome!"

    @app.route('/how are you')
    def hello():
        return 'I am good, how about you?'

    if __name__ == "__main__":
        app.run(host="0.0.0.0", port=8080)

<br>

    $ FLASK_APP=app.py flask run --host=0.0.0.0

    * Serving Flask app "app.py"
    * Environment: production
      WARNING: This is a development server. Do not use it in a production deployment.
      Use a production WSGI server instead.
    * Debug mode: off
    * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
    10.2.165.10 - - [04/Feb/2022 03:40:02] "GET / HTTP/1.1" 200 -
    10.2.165.10 - - [04/Feb/2022 03:40:27] "GET /how%20are%20you HTTP/1.1" 200 -

<br>

### History!

`history`: build 🐳`Dockerfile` from the history of your hands-on building process.

    VM Ubuntu 20.04

    16  apt update
    17  apt install -y python
    19  apt install -y pip
    21  pip install flask
    22  pip install flask-mysql
    23  apt update
    ...
    29  cat > /opt/app.py (write your app)
    ...
    35  apt install -y nano
    36  nano app.py
    ...
    40  FLASK_APP=app.py flask run --host=0.0.0.0
    ...
    45  history

<br>

### Write a 🐳`Dockerfile`

Now exit from the container. Then create a 🐳`Dockerfile` to build it.

🐳`Dockerfile`

    FROM ubuntu

    RUN apt update
    RUN apt install -y python pip
    RUN pip install flask
    RUN pip install flask-mysql

    COPY app.py /opt/app.py

    ENTRYPOINT FLASK_APP=/opt/app.py flask run --host=0.0.0.0

Now `build .` it!
Syntax

- 🐳`Docker build . -t [account]/[image name]`

🐳`Docker build .`

    Step 1/6 : FROM python:3.6
    3.6: Pulling from library/python
    Status: Downloaded newer image for python:3.6
    ---> 54260638d07c

    Step 2/6 : RUN pip install flask
    Installing collected packages: zipp, typing-extensions, MarkupSafe, importlib-metadata, dataclasses, Werkzeug, Jinja2, itsdangerous, click, flask
    Successfully installed Jinja2-3.0.3 MarkupSafe-2.0.1 Werkzeug-2.0.2 click-8.0.3 dataclasses-0.8 flask-2.0.2 importlib-metadata-4.8.3 itsdangerous-2.0.1 typing-extensions-4.0.1 zipp-3.6.0

    Step 3/6 : COPY . /opt/
    ---> 7b8ddbf1367e
    Step 4/6 : EXPOSE 8080
    ---> Running in 4cfd977b4355
    Removing intermediate container 4cfd977b4355
    ---> 852c5b0d5db7
    Step 5/6 : WORKDIR /opt
    ---> Running in 2a974cc1b68c
    Removing intermediate container 2a974cc1b68c
    ---> 853b616380c9
    Step 6/6 : ENTRYPOINT ["python", "app.py"]
    ---> Running in 8f0d51979ebf
    Removing intermediate container 8f0d51979ebf
    ---> 0284627c06dc
    Successfully built 0284627c06dc
    Successfully tagged simple-flask-server:latest

<br>

`$ docker images`

    $ docker images
    REPOSITORY              TAG         IMAGE ID         CREATED           SIZE
    simple-flask-server     latest      0284627c06dc     2 minutes ago     913MB
    python                  3.6         54260638d07c     6 weeks ago       902MB
    redis                   latest      ccee4cdf984f     9 months ago      105MB

<br>

### Push to the dockr hub

- 🐳`Docker login`
  - `Uername:`
  - `Password:`
- 🐳`Docker push forest/simple-flask-server`

<br>

### Know your built `image`

Check the version of your `image`: 🐳`Docker run python:3.6 cat /etc/*release*`

    PRETTY_NAME="Debian GNU/Linux 11 (bullseye)"
    NAME="Debian GNU/Linux"
    VERSION_ID="11"
    VERSION="11 (bullseye)"
    VERSION_CODENAME=bullseye
    ID=debian
    HOME_URL="https://www.debian.org/"
    SUPPORT_URL="https://www.debian.org/support"
    BUG_REPORT_URL="https://bugs.debian.org/"
