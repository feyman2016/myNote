## Docker Util

#### 1. Use other source in ubuntu image 

change the mirrors by run command, and install iptables if you want

```
FROM ubuntu:latest
MAINTAINER xhr
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse" > /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb-src http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse" >> /etc/apt/sources.list
RUN apt-get update
RUN apt-get install  -y iptables
```



#### 2. Run as root within docker 

add `--privileged` when you execute `docker run`

```
docker run XXX --privileged
```



#### 3. Copy file from container to host

```
docker cp <CONTAINER_NAME>:<SRC_DIR_IN_CONTAINER> <DST_FILE_NAME>
```

**EXAMPLE**: sudo docker cp docker-nginx:/etc/nginx/conf.d/default.conf default.conf

copy `/etc/nginx/conf.d/default.conf ` in container `docker-nginx` and save it on the host as file `default.conf`



#### 4.Solve issues for local docker registry error

If you ever met this error:

```
docker pull registry.vmse.skydns.local:5000/infraspawner
FATA[0000] Error response from daemon: v1 ping attempt failed with error: Get https://registry.vmse.skydns.local:5000/v1/_ping: local error: record overflow. If this private registry supports only HTTP or HTTPS with an unknown CA certi
```

It is caused by untrusty local docker registry settings, one of the following might solve this:

- 1. set the `/etc/default/docker`:

     ```
     DOCKER_OPTS="--insecure-registry registry.vmse.skydns.local:5000"
     ```

     ​

- 2. set the `/etc/docker/daemon.json`:

     Normally, the file `daemon.json` might mot exist, just create it if needed.

     ```
     { "insecure-registries":    ["developer.vmse.skydns.local:5000", "registry.vmse.skydns.local:5000"] }

     ```

     This setting will trust a list of docker registries.