PHP Container Deployment With Docker Tutorial
=============================================

1. Go to https://docs.docker.com/installation/ and install docker according to the instructions for your OS

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
