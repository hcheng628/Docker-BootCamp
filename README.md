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
  - docker build -t='chenghongyu628/df_01' .
  - docker run -d --name nginx_02 -p 80 chenghongyu628/df_01 nginx -g "daemon off;" (Test if this works)
  - docker ps -l
  - curl http://0.0.0.0:${PORT Num returned from last cmd}
  - docker login
  ENTER USERNAME AND PASSWD
  - docker push ${IMAGE NAME}
  
  
 
 
 
  
  
