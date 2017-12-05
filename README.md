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
 
 
  
  
