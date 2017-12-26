# Docker-BootCamp

Docker BootCamp Documentation

# 1. Installation

 Note: This installation downloads + installs Docker from https://www.docker.com not Linux Repo
 
 Prerequisite: curl
 Run this script to Check if curl has been installed: whereis curl
 
 If not installed: apt-get install -y curl
 
  
 Run this script to get Docker installed: curl -sSL https://get.docker.com | sudo sh
 
 Run this script to kick off a Docker container: sudo docker run echo 'Hi'
 


 Dont want to use sudo to run docker everytime?
 
 Run the following scripts line by line: 
 
 sudo groupadd docker
 
 sudo gpasswd -a ${user} docker
 
 #MAKE SURE TO LOG OUT THIS USER AND LOG BACK IN
 
 Now you can run Docker without sudo!
 
 
 Run this script: docker run echo 'Hi'
 
 # 2. Docker CMDS to Start off with
 - docker run -i -t --name YOUR_NAME -d ubuntu /bin/bash
  - e.g. docker run
 - docker start -i ${CONTAINER NAME}
 - docker ps -a  and -l
 - docker rm ${CONTAINER ID or NAME}
 - docker inspect ${CONTAINER ID}
 - Ctrl + P Ctrl + Q
 - docker attach ${CONTAINER ID or NAME}
 - docker restart %{CONTAINER NAME}
 - docker logs -t -f --tail ${0 or NUM} ${CONTAINER ID or NAME}
 - docker top ${CONTAINER ID or NAME}
 - docker exec -i -t ${CONTAINER NAME}  CMDs
 - docker stop ${CONTAINER ID or NAME}
 - docker kill ${CONTAINER ID or NAME}  
 
 Install nsenter (util-linux):  
 User can now attach to a Docker Container via nsenter CMD, here is a .sh script to make it even easiler.  
 File: in-docker.sh  
 CNAME=$1  
 CPID=$(docker inspect --format="{{.State.Pid}}" $CNAME)  
 nsenter --target "$CPID" --mount --uts --ipc --net --pid  
 
 
 # 3. Docker Nginx
 - docker run -p 80 --name web01 -t -i ubuntu /bin/bash
 - apt-get install -y nginx (in container)
 - apt-get install -y vim (in container optional)
 - mkdir -p /var/www/html (in container optional this can be any dir)
 - vim index.html (simple HTML page)
 - whereis nginx (find out where is nginx config file)
 - vim  /etc/nginx/sites-enabled/default (find root key-value and change the path, in my case, to /var/www/html)
 - nginx (in container)
 - Ctrl + P Ctrl + Q (still in container)
 - docker inspect web01 | grep IPAddress
 - curl http://${IPAddress}
 - docker port ${CONTAINER NAME}
 
 # 4. Docker Repo + Image
 - docker info
 This shows: Lots of Info including Storage Driver location and Docker Root Dir ......
 - docker images
 - docker rmi  ${REPO+':'TAG or IMAGE ID} -f (For multi-images use space e.g. ubuntu:12.04 ubuntu:12.10)
   To delete all docker rmi $(docker images -q)
 - docker search ubuntu -s 3
 - docker pull $(REPO + ':' TAG)
 Change Docker Registry URL
 Edit /etc/default/docker
 Create Docker Images
 1. NON-AUTO
 - docker commit -a 'chenghongyu628' -m 'nginx' ${CONTAINER ID or CONTAINER NAME} ${YOUR IMAGE NAME}
 - docker run -d --name nginx_web02 -p 80 chenghongyu628/nginx nginx -g "daemon off;"
 - docker ps -l
 - curl http://0.0.0.0:${PORT}
 
 2. AUTO(Dockerfile + Suggested)  
 Example:  
 FROM ubuntu:14.04  
 MAINTAINER chenghongyu628 "email@email.com"  
 RUN apt-get update  
 RUN apt-get install nginx -y  
 EXPOSE 80  
 
  - mkdir -p dockerfile/df_01
  - cd dockerfile/df_01
  - vim Dockerfile
  - docker build -t='chenghongyu628/df_01' . (-no-cache or in Dockerfile add: ENV REFRESH_DATE 2017-12-08)
  - docker run -d --name nginx_02 -p 80 chenghongyu628/df_01 nginx -g "daemon off;" (Test if this works)
  - docker ps -l
  - curl http://0.0.0.0:${PORT Num returned from last cmd}
  - docker login
  ENTER USERNAME AND PASSWD  
  - docker push ${IMAGE NAME}  
  
  Another Example:  
  FROM centos  
  RUN yum install -y  wget gcc gcc-c++ make openssl-devel  
  RUN useradd -s /sbin/nologin -M www  
  ADD ./nginx*.*.gz /usr/local/src  
  ADD ./pcre*.*.gz /usr/local/src  
  WORKDIR /usr/local/src/nginx-1.13.7  
  RUN ./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_ssl_module  --with-pcre=/usr/local/src/pcre-8.41 && make && make install  
  ENV PATH /usr/local/nginx/sbin:$PATH  
  RUN echo "daemon off;" >> /usr/local/nginx/conf/nginx.conf  
  EXPOSE 80  
  CMD ["nginx"]  
  
  Note: nginx and pcre gz files have been downloaded to host pwd.

  
  
  # 5. Docker CS Arch (Client and Server)
  - nc -U /var/run/docker.sock
  - GET /info HTTP/1.0
  This is just like - docker info but via Remote API by default docker client connects to server via unix socket.  
    
  Check Status:  
  - service docker stop
  - service docker status
  - service docker restart  
  
  Docker Server Config File: /etc/default/docker
  By default in Docker  17.11.0-ce this file is not loaded.
  Here is what you need to do: 
  1. - vim /lib/systemd/system/docker.service  
  Update ExecStart to /usr/bin/dockerd -H fd:// $DOCKER_OPTS  
  Add EnvironmentFile=-/etc/default/docker  
  2. - vim /etc/default/docker (Add: DOCKER_OPTS=" --label name=docker_server " as an example)
  3. - systemctl daemon-reload  
  4. - service docker restart  
  5. - docker info  
  You should see this label under Labels section
  
  Client Connects to Server Over TCP:  
   
  1. - docker -H tcp://${IP}:${PORT} info (CMDs)  
  2. - curl http://${IP}:${PORT}/info (Via Remote API)  
  or set Env Var for Docker Client:  
  3. - export DOCKER_HOST="tcp://${IP}:${PORT}"  
  If this Env Var set, docker command will trigger remote Docker Server.  
  To unset this, export DOCKER_HOST=""  
  
  For Docker Host, it allows "mult- -H" options so it will resposne to both local and remote Docker Clients requests.  
  
  
  # 6. Dockerfile Instructions
ARG:  
ARG CODE_VERSION=latest  
This variable can be used in FROM  



FROM:  
FROM ubuntu  
FROM ubuntu:14.04  



CMD:  
CMD echo 'This is a test.' | wc - (/bin/sh -c)  
CMD ["/usr/bin/wc","--help"]  



ENTRYPOINT:  
ENTRYPOINT top -b (/bin/sh -c)  
ENTRYPOINT ["top", "-b"]  
ENTRYPOINT ["/usr/sbin/apache2ctl","-D","FOREGROUND"]  
Use together with CMD(Options)  



RUN:  
RUN apt install nginx -y (/bin/sh -c)  
RUN ['/bin/bash', '-c', 'source $HOME/.bashrc']  



ADD:  
ADD <src FILE_SYSTEM_PATH or URL> <dest>  
Since <src> is a local tar archive, options like -x...... are available.  
See Doc for more info.  



COPY:  
COPY hom*.html /somedir/  
COPY ["home page.xml","/some dir/"]  


VOLUME:  
VOLUME /myVolume  



WORKDIR:  
WORKDIR /abc  
This set WORKDIR for RUN, CMD, ENTRYPOINT, COPY, and AND instructions.  
Abs-Path is recommended.  


USER:  
USER <user>[:<group>]  
USER <UID>[:<GID>]  



EXPOSE:  
EXPOSE 80 (by default TCP make sure using -p to run a container)  



ENV:  
ENV abc hello  
ENV abc=hello def=$abc  



LABEL:  
LABEL key1="val_1" key2="val_2"  



MAINTAINER(deprecated):  
Use LABEL instead!!!  



ONBUILD:  
ONBUILD [INSTRUCTION]  
ONBUILD [RUN apt-get curl -y]  



STOPSIGNAL:  
STOPSIGNAL signal  

- docker history ${IMAGE NAME}  

# 6. Docker Networks  
Note: Have brctl + ifconfig installed.  
- sudo apt install net-tools  
- sudo apt install bridge-utils  

Config Host Machine Network:  
- brctl show  
- brctl addbr docker_net  
- ifconfig docker_net 10.11.12.1 netmask 255.255.255.0  

Config Docker Network:  
- sudo vim /etc/default/docker (Add -b=docker_net in DOCKER_OPTS)  
- sudo service docker restart  
Now Docker containers will be running in this custom IP range.  
  
   
 Run Docker Container with Link Alias:  
 - docker run --link=${CONTAINER_NAME}:${YOUR_ALIAS}  
 - ping ${YOUR_ALIAS}  
   
 Connections Among Containers:  
 Edit /etc/default/docker + add --icc to DOCKER_OPTS  
 --icc=true (default) to enable.  
 --icc=false to disable.  
 Note: When updated this file, restart docker services.  
   
 iptables + Containers:  
 Edit /etc/default/docker + add --iptables=true  
     
 iptables CMD:  
 iptables -F
 iptables -L -n
 
 iptables + External Access:  
 Note: When docker service started, it will enable ip-forward.  
 To change this default value, in DOCKER_OPTS change --ip-forward=false  
 To check it:  
 - sysctl net.ipv4.conf.all.forwarding  
 - docker port ${CONTAINER_NAME}  
 
 To Drop a Request of a give IP:  
 - iptables -I DOCKER -s ${SOURCE_IP} -d ${DESTINATION_IP} -p TCP --dport 80 -j DROP  
 Google iptables and its options for more information. Very powerful!!!  
 
 # 7. Docker Backup/Restore:  
 - docker run -v ${HOST_MACHINE_PATH}:${DOCKER_CONTAINER_PATH} -it /bin/bash  
 Also, we can use VOLUME instruction in Dockerfile: VOLUME ["DOCKER_CONTAINER_PATH_1","DOCKER_CONTAINER_PATH_2"]   
 - docker inspect ${CONTAINER_NAME} --format="{{.Mounts}}"  
 Or Nav to Mounts Section, to see the mappings of its HOST_MOUNT_PATH and DOCKER_CONTAINER_PATH.  
 
 Docker Volume Container:  
 - docker run --volumes-from CONTAINER_NAME_A --volumes-from CONTAINER_NAME_B -it ubuntu  
 To backup + restore:  
 - docker run -rm --volumes-from CONTAINER_NAME_A -v /HOST_BACKUP:/VM_BACKUP ubuntu tar cvf /VM_BACKUP/backup.DATETIME.tar /CONTAINER_FILE_A CONTAINER_DIR_A
 
 # 8. Docker Resource Allocation/Isolation  
 Note: You will need to install stress in your docker container.  
 Here is the Dockerfile:  
 FROM centos  
 ADD ./epel-7.repo /etc/yum.repos.d/  
 RUM yum install -y stress && yum clean all  
 ENTRYPOINT ["stress"]  
 
 Need to add epel-7.repo file to get stress installed later in the build process.
 
 Now, the test:  
 Note: CPU info is here at: /proc/cpuinfo  
 
 - docker run -it --rm -c 512 stress-container --cpu 2  
 - docker run -it --rm --cpuset-cpus 0 stress-container --cpu 1
 - docker rum -it --rm -m 256m stress-container --vm 1 vm-bytes 222m --vm-hang 0  
 
 
 
 
 
 


  
  

  
  
 
 
 
  
  
