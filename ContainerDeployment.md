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
        
5. Run the docker container

        $ docker run -d -p 80:80 tutum/tutum-docker-php
        
6. Push the mewly built image to a docker registry

        
