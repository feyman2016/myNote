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

