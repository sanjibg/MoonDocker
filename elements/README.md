These elements are designed to be used with diskImage-Builder 
at https://github.com/openstack/diskimage-builder.

To use these elements, follow instructions mentioned below. 
Although, the instructions are specific to ubuntu, they can
be altered for different distros too.
<<<<<<< HEAD

Change to root:
	sudo –i

Install git:
	apt-get install git qemu-utils kpartx

Checkout DIB and helper tools:
=======
----------------------------------------------------------

Change to root:
----------------
	sudo –i

Install git:
----------------
	apt-get install git qemu-utils kpartx

Checkout DIB and helper tools:
----------------
>>>>>>> 6e362883e69261a7f8cb3cb8faa153f110c7d77c
	git clone https://github.com/openstack/diskimage-builder.git
	git clone https://github.com/sanjibg/MoonDocker.git
	
Set Path:
<<<<<<< HEAD
=======
----------------
>>>>>>> 6e362883e69261a7f8cb3cb8faa153f110c7d77c
	export PATH="$HOME/bin:$PATH:/root/diskimage-builder/bin"
	export ELEMENTS_PATH="$ELEMENTS_PATH:/root/MoonDocker/elements"

Start creating images:
<<<<<<< HEAD
=======
----------------
>>>>>>> 6e362883e69261a7f8cb3cb8faa153f110c7d77c
	#Ubuntu Trusty images with docker
	git clone https://github.com/openstack/diskimage-builder.git
	disk-image-create –o ubuntu_docker –a amd64 –u base ubuntu baremetal docker
