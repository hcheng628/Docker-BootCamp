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
 
 # 2. Docker CMDS
 - docker run -i -t --name YOUR_NAME -d ubuntu /bin/bash
  - e.g. docker run
 - docker start -i ${CONTAINER NAME}
 - docker ps -a  and -l
 - docker rm ${CONTAINER ID or NAME}
 - docker inspect ${CONTAINER ID}
 - Ctrl + P Ctrl + Q
 - docker attach ${CONTAINER ID or NAME}
 - docker logs -t -f --tail ${0 or NUM} ${CONTAINER ID or NAME}
 - docker top ${CONTAINER ID or NAME}
 - docker exec -i -t ${CONTAINER NAME}  CMDs
 - docker stop ${CONTAINER ID or NAME}
 - docker kill ${CONTAINER ID or NAME}
 
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
  
  

  
  
 
 
 
  
  
