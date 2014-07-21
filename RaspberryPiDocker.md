Deploying Docker on a Raspberry Pi
==================================

1. Download Arch Linux for Raspberry Pi here: http://archlinuxarm.org/platforms/armv6/raspberry-pi and flash onto the SD card for your Raspberry Pi

2. Run the command

        # wget https://raw.github.com/resin-io/docker-install-script/master/install.sh
        # chmod 755 install.sh
        # ./install.sh

3. Follow the intructions on http://resin.io/blog/docker-on-raspberry-pi-in-4-simple-steps/ to start the daemon and enable IPv4 forwarding

        # docker -d &
        # sysctl -w net.ipv4.ip_forward=1
        
4. Pull the image from the Docker registry
        
        # docker pull sanjibg/apache-php

5. Run the image

        # docker run -d -p 80:80 apache-php 
