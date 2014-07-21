PHP Container Deployment With Docker Tutorial
=============================================

1. Go to https://docs.docker.com/installation/ubuntulinux/#ubuntu-trusty-1404-lts-64-bit and install docker

        $ sudo apt-get update
        $ sudo apt-get install docker.io
        $ sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker
        $ sudo sed -i '$acomplete -F _docker docker' /etc/bash_completion.d/docker.io

2. Install apache and php

    For apache:
    
        sudo apt-get update
        sudo apt-get install apache2
        
    For PHP:
        
        sudo apt-get install php5 libapache2-mod-php5

2. Start a docker dameon
            
        docker -d &

3. Download the Github project tutum-docker-php under username sanjibg

4. Follow the instructions on the README.md file there.
