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

$ yum install docker nginx -y
$ mkdir jenkins-nginx-tls && cd jenkins-nginx-tls && mkdir jenkins-server && mkdir jenkins-data && mkdir jenkins-nginx
$ useradd -d /var/jenkins_home -u 1000 -m -s /bin/bash jenkins
$ chown -R jenkins:jenkins /var/jenkins_home

#### Create self-signed certificates

$ docker run -d --name nginx nginxi \
$ bash -c "clear && docker exec -it nginx sh" \
$ touch /var/sslNginx && cd /var/sslNginx \

$ openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout nginxkey.pem -out nginxCert.pem -subj "/CN=CA" -days 365

$ docker run --rm -d  -v /var/jenkins_home:/var/jenkins_home -v date2:/var/lib/jenkins -p 443:8443 -p 8080:8080 -p 50000:50000 --env JENKINS_ARGS="--httpPort=-1 --httpsPort=8443 --httpsCertificate=path/to/cert --httpsPrivateKey=path/to/privatekey" --env JAVA_OPTS="-Xmx8192m" Jenkins-march


