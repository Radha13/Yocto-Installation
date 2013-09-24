Yocto-Installation
==================


######################## Yocto ###################### 

Welcome!
This documentation deals with building the yocto system on ubuntu 12.04.


1. Introduction

The Yocto Project is an open-source collaboration project focused on embedded Linux developers.

BitBake is a make-alike build tool with the special focus of distributions 
and packages for embedded Linux cross compilation. It is derived from Portage,
which is the package management system used by the Gentoo Linux distribution.


BitBake recipes tell bitbake how to build a particular package.
It includes all the package dependencies, sources to fetch the 
source code from, configuration, compilation, build, install
and remove instructions.

The Crown Bay platform consists of the Intel Atom Z6xx processor,
plus the Intel EG20T Platform Controller Hub (Tunnel Creek + Topcliff).

It also supports the E6xx embedded on-chip graphics via the Intel
Embedded Media and Graphics Driver (EMGD) 1.8 Driver.

2. Downloads
	
	git://git.yoctoproject.org/meta-intel	
	wget http://downloads.yoctoproject.org/releases/yocto/yocto-1.1/poky-danny-8.0.tar.bz2
	
	The following packages are needed to be installed :  

	$ sudo apt-get install sed wget cvs subversion git-core coreutils \
          unzip texi2html texinfo libsdl1.2-dev docbook-utils gawk \
          python-pysqlite2 diffstat help2man make gcc build-essential \
          g++ desktop-file-utils chrpath libgl1-mesa-dev libglu1-mesa-dev \
          mercurial autoconf automake groff libtool xterm

3.Building the meta-crownbay BSP layer

	you can build a crownbay image by adding the location of the meta-crownbay 
	layer to bblayers.conf, along with the meta-intel layer itself (to access 
	common metadata shared between BSPs) e.g.:

	$ source oe-init-build-env

	#in bblayers.conf

	yocto/meta-intel \
	yocto/meta-intel/meta-crownbay \

	#in local.conf

	MACHINE ?= "crownbay"

	The 'crownbay' machine includes the emgd-driver-bin package, which has a 
	proprietary license that must be whitelisted by adding the string 
	"license_emgd-driver-bin_1.14" to the LICENSE_FLAGS_WHITELIST variable in your local.conf.
	Also before whitelisting it, check the version of poky.

	LICENSE_FLAGS_WHITELIST = "license_emgd-driver-bin_1.14"

	Now go back to the build directory and run bitbake

	$ source oe-init-build-env 
	$ bitbake core-image-sato  #fasht internet connection is required!


4.Booting the images in /binary

	After the build is succesful and image is generated in the build directory
	/tmp/deploy/images which is like core-image-sato-*.hddimg.
	dd the image to a usb key and boot.

	$ dd if=core-image-sato-crownbay-20101207053738.hddimg of=/dev/sdb
	$ sync
	$ sync  #Running it twice just buys some time for the first sync to flush disk buffer


##########################
Errors and Fixes
############################


1.Environment variables 

	AttributeError: 'module' object has no attribute 'contains'
	ERROR: Error evaluating '${TARGET_OS}:${TRANSLATED_TARGET_ARCH}:build-${BUILD_OS}:pn-
	${PN}:${MACHINEOVERRIDES}:${DISTROOVERRIDES}:${CLASSOVERRIDE}:forcevariable'

	ERROR: Error evaluating 'linux${LIBCEXTENSION}${ABIEXTENSION}'

	ERROR: Error evaluating '${@bb.utils.contains("TUNE_FEATURES", "mx32", "x32", "" ,d)}'

	REMARK: DONT RUN BITBAKE AS ROOT!!!!!!!!!

	That way will only lead to your host machine having bits of its filesystem
	replaced/deleted. OE relies on sandboxing the build as a non priviledged user.
	Check for permissions in the directory and make sure it doesn't have root permissions (ls -l)
	if it has then,
			chown -R user:user /folder

2.Pseudo is not found

	make sure you have internet connection and you are ready to go.

3.unable to parse $BBPATH

	Exception: variable BBPATH references itself! 

	Basically when bitbake is called, it ckecks the BBPATH variable, expands it and parses the file
	bblayers.conf and the layers mentioned within the quotes. If there are any new line , it stops.
	So make sure there are no new/empty line, if there is then back slash it.


##########################
Further Reading
#########################

	https://www.yoctoproject.org/docs/current/yocto-project-qs/yocto-project-qs.html
	https://www.yoctoproject.org
