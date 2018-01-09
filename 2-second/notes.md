Jenkins:
In order to contorl host machine from Jenkins container, -v is needed:
docker run -d -p 8080:8080 --names jenkins -v /usr/bin/docker:usr/bin/docker -v /var/run/docker.sock:/var/run/docker.sock -u root jenkins-local-image

Sample Shell Build Script:
date
REGISTRY_URL=172.17.0.3:5000
docker build -t="centos7/base/maven" $WORKSPACE/2-second/3rd-build/maven
if docker ps -a | grep -i maven ; then
	docker rm -f maven-build-apps
fi
docker create --name maven-build-apps centos7/base/maven
docker cp maven-build-apps:/hello/target/hello.war  $WORKSPACE/2-second/4th-build/hello
docker build -t $REGISTRY_URL/centos7/base/tomcat/app $WORKSPACE/2-second/4th-build/hello
docker push $REGISTRY_URL/centos7/base/tomcat/app
if docker ps -a | grep -i tomcat-app; then
	docker rm -f tomcat-app
fi
docker run -d -p 80:8080 --name tomcat-app $REGISTRY_URL/centos7/base/tomcat/app  


GitHub:
To use Jenkins GitHub Hook Plugin, URL follows:
https://${USERNAME}:${API-TOKEN}:${DOMAIN OR IP}${PORT}/job/${PROJECT-NAME}/build?token-${PROJECT-TOKEN}


