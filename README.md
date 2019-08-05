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
$ useradd -d /var/jenkins_home -u 1000 -m -s /bin/bash jenkins
$ chown -R jenkins:jenkins /var/jenkins_home

#### Create self-signed certificates

$ openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout nginxkey.pem -out nginxCert.pem -subj "/CN=CA" -days 365

#### Create Jenkins

$ docker run -d \
    -v your_local_config_file.conf:/etc/nginx/nginx.conf \
    -p 80:80 \
    --name nginx \
    blacklabelops/nginx

Note: Starting Jenkins without any port mapping

#### HTTPS Reverse Proxy

$ docker run -d \
    -p 443:443 \
    -v /var/nginx-cert:/var/nginx-cert \
    -e "SERVER1REVERSE_PROXY_LOCATION1=/" \
    -e "SERVER1REVERSE_PROXY_PASS1=http://localhost/" \
    -e "SERVER1HTTPS_ENABLED=true" \
    -e "SERVER1CERTIFICATE_FILE=/var/nginx-cert/nginxCert.pem" \
    -e "SERVER1CERTIFICATE_KEY=/var/nginx-cert/nginxkey.pem" \
    -e "SERVER1HTTP_ENABLED=false" \
    --name nginx \
    blacklabelops/nginx
