PHP Container Deployment With Docker Tutorial
=============================================

1. Go to https://docs.docker.com/installation/ubuntulinux/#ubuntu-trusty-1404-lts-64-bit and install docker

        $ sudo apt-get update
        $ sudo apt-get install docker.io
        $ sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker
        $ sudo sed -i '$acomplete -F _docker docker' /etc/bash_completion.d/docker.io

2. Start a docker dameon
            
        $ docker -d &

3. Download the Github project tutum-docker-php under username sanjibg

4. Build the doker image

        $ docker build -t sanjibg/tutum-docker-php .
        
5. Run the docker image

        $ docker run -d -p 80:80 sanjibg/tutum-docker-php
        
6. Push the newly built image to a docker registry

        $ sudo docker push username/nameOfImage
        
7. Pull a docker image from a registry

        $ sudo docker pull imageName
        
8. Build and the run the docker image as before

        $ docker build -t sanjibg/tutum-docker-php .
        $ docker run -d -p 80:80 sanjibg/tutum-docker-php

9. Verfiy that the container is running correcty by going to a web browser and going to https://localhost:80
        
