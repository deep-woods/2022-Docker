# <span id='top'>03 Docker Image - Variables, Commands vs Entrypoint</span>

<br>

[[Environment Variables]](#Variables)  
[[Practice - Variables]](#Variablesp)  
[[Commands vs Entrypoint]](#vs)  
[[Practice - Entrypoint]](#Practicee)  
[[References]](#ref)

<br>

## <span id='Variables'>Environment Variables</span>

[[Top]](#top)

<br>

Example variable: `APP_COLOR`  
Code: [here](https://github.com/deep-woods/2022-Docker/tree/main/Docker%20with%20KodeKloud/app.py)

<br>

## <span id='Practicep'>Practice - Variables</span>

[[Top]](#top)

Search for Variables: `grep -A <line number> <search keyword>`  
`-A n` : Prints searched line and nlines after the result.  
`-B n` : Prints searched line and n line before the result.  
`-C n` : Prints searched line and n lines after before the result.

    $ docker inspect bf6cbcc96130 | grep -A 10 Env
                "Env": [
                    "APP_COLOR=pink",
                    "PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                    "LANG=C.UTF-8",
                    "GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D",
                    "PYTHON_VERSION=3.6.6",
                    "PYTHON_PIP_VERSION=18.1"
                ],
                "Cmd": null,
                "Image": "kodekloud/simple-webapp",
                "Volumes": null,

<br>

`grep [options] pattern [files]`

    grep -n COLOR app.py

    19:SUPPORTED_COLORS = ",".join(color_codes.keys())
    22:COLOR_FROM_ENV = os.environ.get('APP_COLOR')
    24:COLOR = random.choice(["red", "green", "blue", "blue2", "darkblue", "pink"])
    30:    return render_template('hello.html', name=socket.gethostname(), color=color_codes[COLOR])
    38:          " 1. As a command line argument with --color as the argument. Accepts one of " + SUPPORTED_COLORS + " \n"
    39:          " 2. As an Environment variable APP_COLOR. Accepts one of " + SUPPORTED_COLORS + " \n"
    52:        COLOR = args.color
    53:        if COLOR_FROM_ENV:
    54:            print("A color was set through environment variable -" + COLOR_FROM_ENV + ". However, color from command line argument takes precendence.")
    55:    elif COLOR_FROM_ENV:
    56:        print("No Command line argument. Color from environment variable =" + COLOR_FROM_ENV)
    57:        COLOR = COLOR_FROM_ENV
    59:        print("No command line argument or environment variable. Picking a Random Color =" + COLOR)
    62:    if COLOR not in color_codes:
    63:        print("Color not supported. Received '" + COLOR + "' expected one of " + SUPPORTED_COLORS)

<br>

Search for variable data: `docker exec -it blue-app env`

    $ docker exec -it blue-app env
    PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=0244fe860185
    TERM=xterm
    APP_COLOR=blue
    LANG=C.UTF-8
    GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
    PYTHON_VERSION=3.6.6
    PYTHON_PIP_VERSION=18.1
    HOME=/root

<br>

    $ docker exec -it mysql-db env

    PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME=29309e82e4ae
    TERM=xterm
    LANG=C.UTF-8
    GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
    PYTHON_VERSION=3.7.0
    PYTHON_PIP_VERSION=18.0
    HOME=/root

<br>

## <span id='vs'>Commands vs Entrypoint</span>

[[Top]](#top)

<br>

    # Pull base image.
    FROM ubuntu:14.04

    # Install.
    RUN \
    sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \
    apt-get update && \

    ...

    # Define default command.
    CMD ["bash"]

### Syntax: `CMD [ "command", "param1" ]`

- bash is not really a process like your web server or database server.
- a shell listens for inputs from a terminal. If it cannot find a terminal, it exits.
- by default Docker does not attach a terminal to a container when it is run.
  - how do you specify a different command to start the container.
    - Solution 1. append a command to the `docker run` command and it overrides the default command
- Tag: `docker run --entrypoint [command]`: overrides the pre-defined `ENTRYPOINT`.

<br>

## <span id='Practicee'>Practice - Entrypoint</span>

[[Top]](#top)

<br>

The final command run at startup when the `image` is run?

    $ grep -C 5 ENTRYPOINT Dockerfile-wordpress
            rm wordpress.tar.gz; \
            chown -R www-data:www-data /usr/src/wordpress

    COPY docker-entrypoint.sh /usr/local/bin/

    ENTRYPOINT ["docker-entrypoint.sh"]
    CMD ["apache2-foreground"]

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
- GeeksforGeeks (2021) grep command in Unix/Linux https://www.geeksforgeeks.org/grep-command-in-unixlinux/
