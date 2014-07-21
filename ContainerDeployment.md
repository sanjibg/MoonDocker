PHP Container Deployment With Docker Tutorial by Shovik
=============================================

1. Go to https://docs.docker.com/installation/ubuntulinux/#ubuntu-trusty-1404-lts-64-bit and install Docker

        $ sudo apt-get update
        $ sudo apt-get install docker.io
        $ sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker
        $ sudo sed -i '$acomplete -F _docker docker' /etc/bash_completion.d/docker.io

2. Start a Docker daemon
            
        $ docker -d &

3. Clone the Github project tutum-docker-php

4. Build the Docker image

        $ docker build -t sanjibg/tutum-docker-php .
        
5. Push the newly built image to the Docker registry

        $ sudo docker push yourUsername/nameOfImage
        
6. Pull a Docker image from a registry

        $ sudo docker pull sanjibg/apache-php
        
7. Build and the run the docker image

        $ docker run -d -p 80:80 apache-php

8. Verfiy that the container is running correcty by going to a web browser and going to https://localhost:80
        
