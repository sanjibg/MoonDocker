How to setup an environment to deploy Docker containers using Heat?
==================================


Once you have Heat environment that is successfully provisioning instances, follow these steps to enable docker plugin in Heat to deploy containers as resources in a Heat template.

1. Goto heat/contrib/docker and run the following command to install docker plugin dependencies:

        # pip install -r requirements.txt
        # (if you are running Heat in an venv, activate the venv before running the above command)

2. Run the following commands to complete docker plugin setup for Heat:

        # mkdir /usr/lib/heat;
        # cd /opt/stack/heat/contrib/docker;
        # ln -sf $(cd docker/resources; pwd) /usr/lib/heat/docker;
        
3. Make heat aware of the plugin by adding the following in heat.conf

        # plugin_dirs=/opt/stack/heat/contrib/docker/docker/resources

4. Restart Heat-engine

        # service heat-engine restart
        
Docker plugin is now enabled with Heat.

This plugin now enables using Docker containers as resources in a Heat template