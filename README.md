## Run Jenkins in container on AWS EC2 instance with HTTP

$ docker run -d --rm -u root -p 8080:8080 -v /var/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean

Note: The webserver built into Jenkins (Winstone) uses HTTP by default

## Run Jenkins in container on AWS EC2 instance with HTTPS

#### Possible options

1. To configure the built-in Jenkins Winstone webserver to use HTTPS (not recommended)
2. Using a reverse proxy which handles all the HTTPS stuff and allows you to run Jenkins in its default configuration (using HTTP) behind the reverse proxy

#### Following steps are required for option 2

1. Create a TLS server certificate for a domain
2. Run the reverse proxy and Jenkins containers with the right configurations

#### Prerequite

$ yum update -y
$ yum install -y docker
$ systemctl start docker
$ usermod -a -G docker ec2-user

$ mkdir jenkins-nginx-tls && cd jenkins-nginx-tls && mkdir jenkins-server && mkdir jenkins-data && mkdir jenkins-nginx

$ useradd -d /var/jenkins_home -u 1000 -m -s /bin/bash jenkins
$ chown -R jenkins:jenkins /var/log/jenkins

## create Jenkins server Dockerfile

~~~
vi jenkins-server/Dockerfile
FROM jenkins:1.609.1
MAINTAINER Ahmet Atalay
USER root
RUN mkdir /var/log/jenkins
RUN mkdir /var/cache/jenkins
RUN chown -R jenkins:jenkins /var/log/jenkins
RUN chown -R jenkins:jenkins /var/cache/jenkins
USER jenkins
# Set Defaults
ENV JAVA_OPTS="-Xmx4096m"
ENV JENKINS_OPTS=" - handlerCountStartup=100 - handlerCountMax=300 - logfile=/var/log/jenkins/jenkins.log - webroot=/var/cache/jenkins/war"
~~~
