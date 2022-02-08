# <span id='top'>03 Docker Compose</span>

<br>

[[🤲Hands-on Voting-app Troubleshooting]](#Voting-app)  
[[Demo Composed]](#Composed)  
[[References]](#ref)

<br>

## <span id='Voting-app'>🤲Hands-on Voting-app Troubleshooting</span>

[[Top]](#top)

### Troubleshooting 1 : when you haven't linked your `app` to the `db`

Run your app.

    $ docker run -p 5000:80 --name=voting-app voting-app

Now the app server is up and running.

    [2022-02-08 15:19:44 +0000] [1] [INFO] Starting gunicorn 20.1.0
    [2022-02-08 15:19:44 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
    [2022-02-08 15:19:44 +0000] [1] [INFO] Using worker: sync
    [2022-02-08 15:19:44 +0000] [66] [INFO] Booting worker with pid: 66
    [2022-02-08 15:19:44 +0000] [67] [INFO] Booting worker with pid: 67
    [2022-02-08 15:19:44 +0000] [68] [INFO] Booting worker with pid: 68
    [2022-02-08 15:19:44 +0000] [69] [INFO] Booting worker with pid: 69
    10.39.102.7 - - [08/Feb/2022:15:19:54 +0000] "GET / HTTP/1.1" 200 1285 "https://0edfd58dcf664975.labs.kodekloud.com/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.99 Safari/537.36"
    10.39.102.7 - - [08/Feb/2022:15:19:54 +0000] "GET /static/stylesheets/style.css HTTP/1.1" 304 0 "https://5000-port-0edfd58dcf664975.labs.kodekloud.com/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.99 Safari/537.36"

However, as soon as you interact with the app, it breaks.

    [2022-02-08 15:19:56,431] INFO in app: Received vote for a   <----------------- INTERACTION
    [2022-02-08 15:19:56 +0000] [69] [INFO] Received vote for a
    [2022-02-08 15:19:56,459] ERROR in app: Exception on / [POST]
    Traceback (most recent call last):
    File "/usr/local/lib/python3.9/site-packages/redis/connection.py", line 607, in connect
        sock = self.retry.call_with_retry(
    File "/usr/local/lib/python3.9/site-packages/redis/retry.py", line 45, in call_with_retry
        return do()
    File "/usr/local/lib/python3.9/site-packages/redis/connection.py", line 608, in <lambda>
        lambda: self._connect(), lambda error: self.disconnect(error)
    File "/usr/local/lib/python3.9/site-packages/redis/connection.py", line 641, in _connect
        for res in socket.getaddrinfo(
    File "/usr/local/lib/python3.9/socket.py", line 954, in getaddrinfo
        for res in _socket.getaddrinfo(host, port, family, type, proto, flags):
    socket.gaierror: [Errno -2] Name or service not known   <---------------------------- ERROR


    Traceback (most recent call last):
    File "/usr/local/lib/python3.9/site-packages/flask/app.py", line 2073, in wsgi_app
        response = self.full_dispatch_request()
    File "/usr/local/lib/python3.9/site-packages/flask/app.py", line 1518, in full_dispatch_request
        rv = self.handle_user_exception(e)
    File "/usr/local/lib/python3.9/site-packages/flask/app.py", line 1516, in full_dispatch_request
        rv = self.dispatch_request()
    File "/usr/local/lib/python3.9/site-packages/flask/app.py", line 1502, in dispatch_request
        return self.ensure_sync(self.view_functions[rule.endpoint])(**req.view_args)
    File "/app/app.py", line 37, in hello
        redis.rpush('votes', data)   <---------------------- CAUSE OF BREAK
    File "/usr/local/lib/python3.9/site-packages/redis/commands/core.py", line 2049, in rpush
        return self.execute_command("RPUSH", name, *values)
    File "/usr/local/lib/python3.9/site-packages/redis/client.py", line 1173, in execute_command
        conn = self.connection or pool.get_connection(command_name, **options)
    File "/usr/local/lib/python3.9/site-packages/redis/connection.py", line 1370, in get_connection
        connection.connect()
    File "/usr/local/lib/python3.9/site-packages/redis/connection.py", line 613, in connect
        raise ConnectionError(self._error_message(e))
    redis.exceptions.ConnectionError: Error -2 connecting to redis:6379. Name or service not known.

### Troubleshooting 2 - When `docker-compose.yml` doesn't get up and running.

There is a certain order in which the components should be created and run because some are dependent on some others: voting-app is dependent on redis cache, for instance.

        $ docker-compose up
        ERROR: Service 'vote' has a link to service 'redis-cache' which is undefined.

- solution: change the name to `redis` just. :P

<br>

        $ docker-compose up
        ERROR: The Compose file './docker-compose.yml' is invalid because:
        'db', 'redis', 'result', 'vote', 'worker' do not match any of the regexes: '^x-'

- solution: specify the `version` and enclose the service components with `services`.

<br>

        ERROR: The Compose file './docker-compose.yml' is invalid because:
        'service' does not match any of the regexes: '^x-'

- solution: it's plural. It's `services` LOL

<br>

        WARNING: Image for service worker was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
        Building result
        Sending build context to Docker daemon  507.4kB
        Step 1/13 : FROM node:10-slim
        ---> 6fbcbbb5c603
        Step 2/13 : RUN apt-get update     && apt-get install -y --no-install-recommends     curl     && rm -rf /var/lib/apt/lists/*
        ---> Using cache
        ---> dc9cf045b433
        Step 3/13 : ENV TINI_VERSION v0.19.0
        ---> Using cache
        ---> bc5de1b2ed8d
        Step 4/13 : ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
        Downloading  24.06kB/24.06kB

        ---> Using cache
        ---> 628c25c59b11
        Step 5/13 : RUN chmod +x /tini
        ---> Using cache
        ---> 2df1fc38e97e
        Step 6/13 : WORKDIR /app
        ---> Using cache
        ---> 9d4c49a5f3b9
        Step 7/13 : RUN npm install -g nodemon
        ---> Using cache
        ---> fce17a7bcb33
        Step 8/13 : COPY package*.json ./
        ---> Using cache
        ---> 9354e79cacd1
        Step 9/13 : RUN npm ci  && npm cache clean --force  && mv /app/node_modules /node_modules
        ---> Using cache
        ---> e53d1a35bc4e
        Step 10/13 : COPY . .
        ---> Using cache
        ---> d28924635f0c
        Step 11/13 : ENV PORT 80
        ---> Using cache
        ---> 37b4e128c924
        Step 12/13 : EXPOSE 80
        ---> Using cache
        ---> d080faba95fa
        Step 13/13 : CMD ["/tini", "--", "node", "server.js"]
        ---> Using cache
        ---> 9a195b715830
        Successfully built 9a195b715830
        Successfully tagged example-voting-app_result:latest

- solution: the problem solves itself. Although I find it weird that `docker-compose` claims that the images didn't exist because they are there...:

        $ docker images
        REPOSITORY    TAG        IMAGE ID         CREATED          SIZE
        result-app    latest     9a195b715830     25 seconds ago   `160MB
        worker-app    latest     e62a074aaf37     2 minutes ago    193MB
        <none>        <none>     1ed78561e46e     2 minutes ago    749MB
        voting-app    latest     625ed5f84303     5 minutes ago    141MB

<br>
Now it runs!

        Creating network "root_default" with the default driver
        Creating redis      ... done
        Creating db    ... done
        Creating result-app ... done
        Creating worker-app ... done
        Creating voting-app ... done
        Attaching to db, redis, result-app, worker-app, voting-app
        db        | ********************************************************************************
        db        | WARNING: POSTGRES_HOST_AUTH_METHOD has been set to "trust". This will allow
        db        |          anyone with access to the Postgres port to access your database without
        db        |          a password, even if POSTGRES_PASSWORD is set. See PostgreSQL
        db        |          documentation about "trust":
        db        |          https://www.postgresql.org/docs/current/auth-trust.html
        db        |          In Docker's default configuration, this is effectively any other
        db        |          container on the same system.
        db        |
        db        |          It is not recommended to use POSTGRES_HOST_AUTH_METHOD=trust. Replace
        db        |          it with "-e POSTGRES_PASSWORD=password" instead to set a password in
        db        |          "docker run".
        db        | ******************************************************************************

<br>

## <span id='Composed'>Demo Composed</span>

[[Top]](#top)

Install `docker-compose`. (from [here](https://docs.docker.com/compose/install/))

1.  Run this command to download the current stable release of Docker Compose:

        sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

          % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
        100   664  100   664    0     0   3590      0 --:--:-- --:--:-- --:--:--  3608
        100 12.1M  100 12.1M    0     0  26.5M      0 --:--:-- --:--:-- --:--:-- 26.5M

2.  Apply executable permissions to the binary:

        sudo chmod +x /usr/local/bin/docker-compose

3.  Test the installation.

        docker-compose --version
        docker-compose version 1.29.2, build 5becea4c

<br>

### Troubleshooting

        Creating example-voting-app_db_1    ... done
        Creating example-voting-app_redis_1 ... done
        Creating example-voting-app_result_1 ... done
        Creating example-voting-app_worker_1 ... done
        Creating example-voting-app_vote_1   ... done
        Attaching to example-voting-app_db_1, example-voting-app_redis_1, example-voting-app_result_1, example-voting-app_worker_1, example-voting-app_vote_1
        db_1      | The files belonging to this database system will be owned by user "postgres". <-------------- IMPORTANT
        db_1      | This user must also own the server process.                                   <-------------- IMPORTANT
        db_1      |
        db_1      | The database cluster will be initialized with locale "en_US.utf8".
        db_1      | The default database encoding has accordingly been set to "UTF8".
        db_1      | The default text search configuration will be set to "english".
        db_1      |
        db_1      | Data page checksums are disabled.
        db_1      |
        db_1      | fixing permissions on existing directory /var/lib/postgresql/data ... ok
        db_1      | creating subdirectories ... ok
        db_1      | selecting default max_connections ... 100
        db_1      | selecting default shared_buffers ... 128MB
        db_1      | selecting default timezone ... Etc/UTC
        db_1      | selecting dynamic shared memory implementation ... posix
        db_1      | creating configuration files ... ok
        db_1      | creating template1 database in /var/lib/postgresql/data/base/1 ... ok
        db_1      | initializing pg_authid ... ok
        db_1      | setting password ... ok
        db_1      | initializing dependencies ... ok
        db_1      | creating system views ... ok
        db_1      | loading system objects' descriptions ... ok
        db_1      | creating collations ... ok
        db_1      | creating conversions ... ok
        db_1      | creating dictionaries ... ok
        db_1      | setting privileges on built-in objects ... ok
        db_1      | creating information schema ... ok
        db_1      | loading PL/pgSQL server-side language ... ok
        db_1      | vacuuming database template1 ... ok
        db_1      | copying template1 to template0 ... ok
        db_1      | copying template1 to postgres ... ok
        db_1      | syncing data to disk ... ok
        db_1      |
        db_1      | Success. You can now start the database server using:
        db_1      |
        db_1      |     postgres -D /var/lib/postgresql/data
        db_1      | or
        db_1      |     pg_ctl -D /var/lib/postgresql/data -l logfile start

### Trouble

        ERROR: The Compose file './docker-compose.yml' is invalid because:
        services.db.environment contains {"POSTGRES_USER": "postgres"}, which is an invalid type, it should be a string

- solution: use the correct syntax.

        environment:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres

or

        environment:
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=postgres

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-for-the-absolute-beginner/
- Install Docker Compose https://docs.docker.com/compose/install/
- docker services.web.working_dir contains an invalid type, it should be a string https://stackoverflow.com/questions/56128696/docker-services-web-working-dir-contains-an-invalid-type-it-should-be-a-string/59127687
