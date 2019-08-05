## Run Jenkins in container on AWS EC2 instance with HTTP

$ docker run -d --rm -u root -p 8080:8080 -v /var/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean

Note: The webserver built into Jenkins (Winstone) uses HTTP by default

## Run Jenkins in container on AWS EC2 instance with HTTPS

#### Possible options

1. To configure the built-in Jenkins Winstone webserver to use HTTPS (not recommended)
2. Using a reverse proxy which handles all the HTTPS stuff and allows you to run Jenkins in its default configuration (using HTTP) behind the reverse proxy

#### Following steps are required for option 2:

1. Create a TLS server certificate for a domain
2. Run the reverse proxy and Jenkins containers with the right configurations
