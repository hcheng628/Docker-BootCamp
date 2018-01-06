#
# MAINTAINER        Carson,C.J.Zeong <zcy@nicescale.com>
# DOCKER-VERSION    1.6.2
#
# Dockerizing HelloWorld: Dockerfile for building HelloWorld images
#
FROM       centos7/base/tomcat
MAINTAINER Carson,C.J.Zeong <zcy@nicescale.com>

RUN yum -y install mysql
ADD init /init

ENTRYPOINT ["/init", "/scripts/run"]
