# <span id='top'>01 Docker in Depth - PID Namespaces</span>

<br>

[[🤲Hands-on PID namespaces]](#)  
[[References]](#ref)

<br>

## <span id=''>🤲Hands-on PID namespaces</span>

[[Top]](#top)

`docker run -d -it --rm -p 8888:8080 tomcat:8.0`

    Status: Downloaded newer image for tomcat:8.0
    Using CATALINA_BASE:   /usr/local/tomcat
    Using CATALINA_HOME:   /usr/local/tomcat
    Using CATALINA_TMPDIR: /usr/local/tomcat/temp
    Using JRE_HOME:        /usr/local/openjdk-11
    Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
    Using CATALINA_OPTS:
    NOTE: Picked up JDK_JAVA_OPTIONS:  --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED

    docker ps
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
    1ff2781162de        tomcat:8.0          "catalina.sh run"   6 seconds ago       Up 5 seconds        0.0.0.0:8888->8080/tcp   confident_almeida

<br>

### Namespace in action

- `ps -eaf | grep <ps ID>`
- `ps -eaf | grep <ps name>`

- ps -eaf lists all the processes running on that container.
- Look at the PID (process ID). This is the `container ID`.

      docker exec 1ff2781162de ps -eaf
      UID        PID  PPID  C STIME TTY          TIME CMD
      root         1     0  0 04:16 pts/0    00:00:05 /docker-java-home/jre/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dignore.endorsed.dirs= -classpath /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat -Dcatalina.home=/usr/local/tomcat -Djava.io.tmpdir=/usr/local/tomcat/temp org.apache.catalina.startup.Bootstrap start
      root       133     0  0 04:19 ?        00:00:00 ps -eaf

- Now compare the PID again. It's the same process, but the `PID` is different. This is because the `namespace` makes the container think that `docker-java-home` is a root process of this particular container (`1ff2781162de` = `tomcat:8.0`).
- From the underlying `host`'s perspective, however, `docker-java-home` is just another process running on it.

      ps -eaf | grep 1ff2781162de
      ps -eaf | grep docker-java-home

      UID        PID  PPID  C STIME TTY          TIME CMD
      root      3481  3464  0 04:16 pts/0    00:00:05 /docker-java-home/jre/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dignore.endorsed.dirs= -classpath /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat -Dcatalina.home=/usr/local/tomcat -Djava.io.tmpdir=/usr/local/tomcat/temp org.apache.catalina.startup.Bootstrap start
      root      3782   656  0 04:23 pts/0    00:00:00 grep docker-java-home

<br>

### <span id='ref'>References</span>

[[Top]](#top)

- kodekloud https://kodekloud.com/courses/docker-swarm-services-stacks-hands-on/
- https://hub.docker.com/_/tomcat
