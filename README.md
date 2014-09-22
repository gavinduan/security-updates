security-updates
================

Create a roll with security updates.
Has been tested on rocks 6.0, 6.1 and 6.1.1

Introduction
--------------
The roll leverages updateinfo.xml generation process described in
http://blog.vmfarms.com/2013/12/inject-little-security-in-to-your.html
The 3rd party components used in the roll:
 
+ ``generate_info.py`` from https://github.com/hany55/generate_updateinfo

+ the ``generate_info.py`` uses ``errata.latest.xml`` file that is maintained and updated 
  by CEFS project http://cefs.steve-meier.de/errata.latest.xml

When the **make roll** is run for the first time, the **yum-plugin-security** RPM is installed
and the plugins are enabled in **/etc/yum.conf**.

The security information from downloaded ``errata.latest.xml`` is parsed and injected into
the repomd.xml file.  

The **yum check-update** command is run with ``--security`` flag that is now understood by yum
and listed security updates rpms (and their dependencies) are downloaded and added to the roll iso.

The subsequent roll builds will add any new security-related rpm updates to the **security-updates/RPMS/*/** 
and the latest iso will have all the rpms accummulated since the first **make roll**.

Prerequisites
--------------

Binary yumdownloader must be present. If not, install with :

     # yum --enablerepo=base install yum-utils

Building the roll
-----------------

Checkout roll distribution from git repo :

     # git clone https://github.com/rocksclusters/security-updates.git  
     # cd security-updates/  
     # make roll

The roll iso image name is of the form: ``hostname-security-updates-version-0.arch.disk1.iso``. 
For example, on the x86_64-based frontend with the FQDN of ``rocks-45.sdsc.edu``, the roll 
will be named ``rocks-45-security-updates-6.1-0.x86_64.disk1.iso``.


Installing the roll
---------------------

To install the above example roll :  

     # rocks add roll rocks-45-security-updates-6.1-0.x86_64.disk1.iso
     # rocks enable roll rocks-45-security-updates   
     # (cd /export/rocks/install; rocks create distro)  
     # yum clean all  
     # yum check-update  

The output of the last command should list all the RPMs that are now available from the Rocks repo.
For example:   

     # yum check-update
     Loaded plugins: fastestmirror, security
     Loading mirror speeds from cached hostfile

     epel-release.noarch      6-8                                     Rocks-6.0
     firefox.x86_64           31.1.0-5.el6.centos                     Rocks-6.0
     glibc.i686               2.12-1.132.el6_5.4                      Rocks-6.0
     glibc.x86_64             2.12-1.132.el6_5.4                      Rocks-6.0
     glibc-common.x86_64      2.12-1.132.el6_5.4                      Rocks-6.0
     glibc-devel.i686         2.12-1.132.el6_5.4                      Rocks-6.0
     glibc-devel.x86_64       2.12-1.132.el6_5.4                      Rocks-6.0
     glibc-headers.x86_64     2.12-1.132.el6_5.4                      Rocks-6.0
     glibc-static.x86_64      2.12-1.132.el6_5.4                      Rocks-6.0
     kernel.x86_64            2.6.32-431.29.2.el6.centos.plus         Rocks-6.0
     kernel-devel.x86_64      2.6.32-431.29.2.el6.centos.plus         Rocks-6.0
     kernel-doc.noarch        2.6.32-431.29.2.el6.centos.plus         Rocks-6.0
     kernel-firmware.noarch   2.6.32-431.29.2.el6.centos.plus         Rocks-6.0
     kernel-headers.x86_64    2.6.32-431.29.2.el6.centos.plus         Rocks-6.0
     libvirt.x86_64           0.10.2-29.el6_5.12                      Rocks-6.0
     libvirt-client.x86_64    0.10.2-29.el6_5.12                      Rocks-6.0
     libvirt-python.x86_64    0.10.2-29.el6_5.12                      Rocks-6.0
     procmail.x86_64          3.22-25.1.el6_5.1                       Rocks-6.0
     
To install RPMS :

     # yum update  

Or install specific updates one by one per your security requirements.

