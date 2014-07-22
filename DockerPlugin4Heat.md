How to setup an environment to deploy Docker containers using Heat?
==================================

Once you have Heat environment that is provisioning instances, follow these steps to use docker plugin for Heat:

1. Goto heat/contrib/docker and run the following command to install docker plugin dependencies:

        # pip install -r requirements.txt
        # (if you are running Heat in an venv then run it after activating the venv)

2. Run the following commands to complete docker plugin setup for Heat:

        # mkdir /usr/lib/heat;
        # cd /opt/stack/heat/contrib/docker;
        # ln -sf $(cd docker/resources; pwd) /usr/lib/heat/docker;
        
3. Make heat aware of the plugin by adding the following in heat.conf

        # plugin_dirs=/opt/stack/heat/contrib/docker/docker/resources

4. Restart Heat-engine

        # service heat-engine restart
        
Docker plugin can now be used with Heat.
