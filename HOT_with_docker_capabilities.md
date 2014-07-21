Heat orchestration template with docker capabilities
================================================

The below Template can be used to deploy an container onto an instance.
The template has been written and tested mainly for ubuntu OS.
This template has the following instructions:
1. setup proxy and install docker. 
2. Pull the docker image provided as a parameter at the point of creating the stack
3. Use the docker plugin to create an container using the Image that was pulled.

Ex: heat  stack-create dockerstack -f docker.yaml -P random_key_name=heat_key -P image_id=trusty-image -P docker_image=cirros -P baremetal_flavor=baremetal

Brings up an stack named dockerstack using the following parameters:
1. random_key_name = key pair to be used for the instance
2. baremetal_flavor = flavor to be used for provisioning the instance
3. image_id = Image to used for provisioning an instance on which docker will be installed
4. docker_image = Image to be pulled from docker repository to create the container

Hot Template (docker.yaml)
==========================================================
# heat_template_version: 2013-05-23
# description: Single compute instance running a Docker container.
# parameters:
#   image_id:
#     type: string
#   random_key_name:
#     type: string
#   docker_image:
#     type: string
#   baremetal_flavor:
#     type: string
# 
# resources:
#   my_instance:
#     type: OS::Nova::Server
#     properties:
#       key_name: {get_param: random_key_name}
#       image: {get_param: image_id}
#       flavor: {get_param: baremetal_flavor}
#       user_data:
#         str_replace:
#           template: |
#             #!/bin/sh
# 
#             set -e
#             touch /root/docker_script_started_marker
#             # set proxy details
#             # dhclient eth1
#             export http_proxy="http://10.1.192.43:8080"
#             export https_proxy="http://10.1.192.43:8080"
#             export no_proxy=localhost,127.0.0.1
# 
#             url='https://get.docker.io/'
# 
#             command_exists() {
#                 command -v "$@" > /dev/null 2>&1
#             }
# 
#             case "$(uname -m)" in
#                 *64)
#                     ;;
#                 *)
#                     echo >&2 'Error: you are not using a 64bit platform.'
#                     echo >&2 'Docker currently only supports 64bit platforms.'
#                     exit 1
#                     ;;
#             esac
# 
#             if command_exists docker || command_exists lxc-docker; then
#                 echo >&2 'Warning: "docker" or "lxc-docker" command appears to already exist.'
#                 echo >&2 'Please ensure that you do not already have docker installed.'
#                 echo >&2 'You may press Ctrl+C now to abort this process and rectify this situation.'
#                 ( set -x; )
#             fi
# 
#             user="$(id -un 2>/dev/null || true)"
# 
#             sh_c='sh -c'
#             if [ "$user" != 'root' ]; then
#                 if command_exists sudo; then
#                     sh_c='sudo sh -c'
#                 elif command_exists su; then
#                     sh_c='su -c'
#                 else
#                     echo >&2 'Error: this installer needs the ability to run commands as root.'
#                     echo >&2 'We are unable to find either "sudo" or "su" available to make this happen.'
#                     exit 1
#                 fi
#             fi
# 
#             curl=''
#             if command_exists curl; then
#                 curl='curl -sL'
#             elif command_exists wget; then
#                 curl='wget -qO-'
#             elif command_exists busybox && busybox --list-modules | grep -q wget; then
#                 curl='busybox wget -qO-'
#             fi
# 
#             # perform some very rudimentary platform detection
#             lsb_dist=''
#             if command_exists lsb_release; then
#                 lsb_dist="$(lsb_release -si)"
#             fi
#             if [ -z "$lsb_dist" ] && [ -r /etc/lsb-release ]; then
#                 lsb_dist="$(. /etc/lsb-release && echo "$DISTRIB_ID")"
#             fi
#             if [ -z "$lsb_dist" ] && [ -r /etc/debian_version ]; then
#                 lsb_dist='Debian'
#             fi
#             if [ -z "$lsb_dist" ] && [ -r /etc/fedora-release ]; then
#                 lsb_dist='Fedora'
#             fi
# 
#             case "$lsb_dist" in
#                 Fedora)
#                     (
#                         set -x
#                         $sh_c 'sleep 3; yum -y -q install docker-io'
#                     )
#                     if command_exists docker && [ -e /var/run/docker.sock ]; then
#                         (
#                             set -x
#                             $sh_c 'docker run busybox echo "Docker has been successfully installed!"'
#                             /usr/local/bin/cfn-signal -s true $wait_handle
#                         ) || true
#                     fi
#                     your_user=your-user
#                     [ "$user" != 'root' ] && your_user="$user"
#                     echo
#                     echo 'If you would like to use Docker as a non-root user, you should now consider'
#                     echo 'adding your user to the "docker" group with something like:'
#                     echo
#                     echo '  sudo usermod -aG docker' $your_user
#                     echo
#                     echo 'Remember that you will have to log out and back in for this to take effect!'
#                     echo
#                     exit 0
#                     ;;
# 
#                 Ubuntu|Debian)
#                     export DEBIAN_FRONTEND=noninteractive
# 
#                     did_apt_get_update=
#                     apt_get_update() {
#                         if [ -z "$did_apt_get_update" ]; then
#                             ( set -x; $sh_c 'sleep 3; apt-get update' )
#                             did_apt_get_update=1
#                         fi
#                     }
# 
#                     # aufs is preferred over devicemapper; try to ensure the driver is available.
#                     if ! grep -q aufs /proc/filesystems && ! $sh_c 'modprobe aufs'; then
#                         kern_extras="linux-image-extra-$(uname -r)"
# 
#                         apt_get_update
#                         ( set -x; $sh_c 'sleep 3; apt-get install -y -q '"$kern_extras" ) || true
# 
#                         if ! grep -q aufs /proc/filesystems && ! $sh_c 'modprobe aufs'; then
#                             echo >&2 'Warning: tried to install '"$kern_extras"' (for AUFS)'
#                             echo >&2 ' but we still have no AUFS.  Docker may not work. Proceeding anyways!'
#                             ( set -x; sleep 10 )
#                         fi
#                     fi
# 
#                     if [ ! -e /usr/lib/apt/methods/https ]; then
#                         apt_get_update
#                         ( set -x; $sh_c 'sleep 3; apt-get install -y -q apt-transport-https' )
#                     fi
#                     if [ -z "$curl" ]; then
#                         apt_get_update
#                         ( set -x; $sh_c 'sleep 3; apt-get install -y -q curl' )
#                         curl='curl -sL'
#                     fi
#                     (
#                         set -x
#                         if [ "https://get.docker.io/" = "$url" ]; then
#                             $sh_c "apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9"
#                         elif [ "https://test.docker.io/" = "$url" ]; then
#                             $sh_c "apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 740B314AE3941731B942C66ADF4FD13717AAD7D6"
#                         else
#                             $sh_c "$curl ${url}gpg | apt-key add -"
#                         fi
#                         $sh_c "echo deb ${url}ubuntu docker main > /etc/apt/sources.list.d/docker.list"
#                         $sh_c 'sleep 3; apt-get update; apt-get install -y -q lxc-docker'
#                     )
#                     if command_exists docker && [ -e /var/run/docker.sock ]; then
#                         (
#                             set -x
#                             sysctl net.ipv6.conf.all.disable_ipv6
#                             sysctl net.ipv6.conf.default.disable_ipv6
#                             sysctl net.ipv6.conf.lo.disable_ipv6
#                             # Run docker on port 80
#                             sed -i 's/exec "$DOCKER" -d $DOCKER_OPTS/exec "$DOCKER" -d $DOCKER_OPTS  -H=tcp:\/\/0.0.0.0:80/' /etc/init/docker.conf
#                             service docker restart
#                             sleep 3
#                             # pull the specified image
#                             docker -H=tcp://127.0.0.1:80 pull $dockerImg
#                             touch /root/docker_config_done_marker
#                             $sh_c 'docker run busybox echo "Docker has been successfully installed!"'
#                             sleep 1
#                             /usr/local/bin/cfn-signal -s true $wait_handle
#                         ) || true
#                     fi
#                     your_user=your-user
#                     [ "$user" != 'root' ] && your_user="$user"
#                     echo
#                     echo 'If you would like to use Docker as a non-root user, you should now consider'
#                     echo 'adding your user to the "docker" group with something like:'
#                     echo
#                     echo '  sudo usermod -aG docker' $your_user
#                     echo
#                     echo 'Remember that you will have to log out and back in for this to take effect!'
#                     echo
#                     exit 0
#                     ;;
# 
#                 Gentoo)
#                     if [ "$url" = "https://test.docker.io/" ]; then
#                         echo >&2
#                         echo >&2 '  You appear to be trying to install the latest nightly build in Gentoo.'
#                         echo >&2 '  The portage tree should contain the latest stable release of Docker, but'
#                         echo >&2 '  if you want something more recent, you can always use the live ebuild'
#                         echo >&2 '  provided in the "docker" overlay available via layman.  For more'
#                         echo >&2 '  instructions, please see the following URL:'
#                         echo >&2 '    https://github.com/tianon/docker-overlay#using-this-overlay'
#                         echo >&2 '  After adding the "docker" overlay, you should be able to:'
#                         echo >&2 '    emerge -av =app-emulation/docker-9999'
#                         echo >&2
#                         exit 1
#                     fi
# 
#                     (
#                         set -x
#                         $sh_c 'sleep 3; emerge app-emulation/docker'
#                     )
#                     exit 0
#                     ;;
#             esac
# 
#             echo >&2
#             echo >&2 '  Either your platform is not easily detectable, is not supported by this'
#             echo >&2 '  installer script (yet - PRs welcome!), or does not yet have a package for'
#             echo >&2 '  Docker.  Please visit the following URL for more detailed installation'
#             echo >&2 '  instructions:'
#             echo >&2
#             echo >&2 '    http://docs.docker.io/en/latest/installation/'
#             echo >&2
#             exit 1
#           params:
#             $wait_handle: {get_resource: first_wait_handle}
#             $dockerImg: {get_param: docker_image}
#             
#   first_wait_handle:
#     type: AWS::CloudFormation::WaitConditionHandle
# 
#   first_wait:
#     type: AWS::CloudFormation::WaitCondition
#     depends_on: my_instance
#     properties:
#       Handle: {get_resource: first_wait_handle}
#       Timeout: 1000
#             
#   my_docker_container:
#     type: DockerInc::Docker::Container
#     depends_on: first_wait
#     properties:
#       docker_endpoint: 
#         str_replace:
#             template: http://host
#             params:
#                 host: { get_attr: [my_instance, first_address] }
#       image: {get_param: docker_image}
===========================================================================