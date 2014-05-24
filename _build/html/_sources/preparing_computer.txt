**************************
Preparing a computer 
**************************




Download and install Ubuntu Natty 
==================================

MCT currently been tested and is known to work on ubuntu Natty (11.04). A
USB-key/CD installation image for Natty can be downloaded from ubuntu's
'old-releases' http://old-releases.ubuntu.com/releases/11.04/.  Download the
appropriate 'Desktop CD' image for your machine

 * 32bit `PC (Intel x86) desktop CD <http://old-releases.ubuntu.com/releases/11.04/ubuntu-11.04-desktop-i386.iso>`_ 
 * 64bit `64-bit PC (AMD64) desktop CD <http://old-releases.ubuntu.com/releases/11.04/ubuntu-11.04-desktop-amd64.iso>`_

Next burn the install image to either a USB-key or CD. Links to instruction for doing this are
given below.

 * `Create a bootable USB stick on Windows <http://www.ubuntu.com/download/desktop/create-a-usb-stick-on-windows>`_
 * `Create a bootable CD on Windows <http://www.ubuntu.com/download/desktop/burn-a-dvd-on-windows>`_
 * `Create a bootable USB stick on Ubuntu <http://www.ubuntu.com/download/desktop/create-a-usb-stick-on-ubuntu>`_
 * `Create a bootable CD on Ubuntu <http://www.ubuntu.com/download/desktop/burn-a-dvd-on-ubuntu>`_

Install the operating system on the desired PC. Note, you may need to change
the boot order in the BIOS so that the target computer will boot from the USB
key or CD. 

Update sytem software to latest available 
=========================================

Since Natty is an older distribution, which is past end of life, it is no
longer getting updates. For this reason it is necessary to modify the location
from which the package manager "apt" searches for software.  This is done by
editing the /etc/apt/sources.list file. Note this must be as root or w/ sudo. Make
the following changes to the sources.list file:


 * replace all instances of *us.archive.ubuntu.com* and *security.ubuntu.com*
   with *old-releases.ubuntu.com*.

 * comment out the following two lines (by placing a # in front) 

.. code-block:: none

   deb http://extras.ubuntu.com/ubuntu natty main   
   deb-src htt://extras.ubuntu/ubuntu natty main

After making the changes run the following to commands to upgrade the software.

.. code-block:: none

   sudo apt-get update 
   sudo apt-get upgrade


Install the base software 
=========================

The following script can be run to install the base software required for the
system. .

.. code-block:: bash

   # Install ROS
   sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu natty main" > /etc/apt/sources.list.d/ros-latest.list'
   wget http://packages.ros.org/ros.key -O - | sudo apt-key add -
   sudo apt-get update
   sudo apt-get install ros-electric-desktop-full
   
   # Install base software requirements
   sudo apt-get install vim mercurial git bzr openssh-server ipython
   
   # Create ROS_LOCAL directory and download mct and mct_config
   mkdir ~/ros
   
   # Download and install mct and mct_config
   cd ros
   hg clone http://bitbucket.org/iorodeo/mct
   hg clone http://bitbucket.org/iorodeo/mct_config
   cd ~/ 
   
   # Setup bash for mct
   python ~/ros/mct_config/bash_setup.py

Download this script from :download:`here <_static/mct_base_setup>`.  Make it executable using

.. code-block:: none

   chmod u+x mct_base_setup

and run the script as shown below

.. code-block:: none

   ./mct_base_setup

Note, during the installation there maybe several prompts - answer yes to all question.

This will install ROS and seveal other packages required to install the
remaining dependencies using *rosdep*. It will also make some required changes to the
system's environment variables.   When the script is finished close the current
shell and open a new before proceeding with the install. 

Note, you can test ROS by running  


.. code-block:: none

   roscore

Should work without errors, use ctl-c to exit. 


Issues: 
-------
    
 * I had to edit mct_config/bash/mct_setup.bash to remove references to
   "albert" and replace with "wbd". We will probably want this to be
   automatic. May want to modify bash_setup.py so that this replacement
   occurs automatically - or replace with $HOME ... not sure which is best.

 * had to edit mct_config/bash/mct_setup.bash to change ROS_MASTER_URI definition. 

 * pydc1394 didn't install correctly. It downloaded, but python setup.py stuff didn't run.
   Needed to do this manually. Happened both machines.


Install MCT's dependencies using rosdep
=======================================


Update the list of ROS packages with the following command

.. code-block:: none

   rospack profile

This will display a list of the packages that are visible to ROS.  Make sure that 

.. code-block:: none

   /home/<username/ros 
   /home/<username>/ros/mct 
   
are on the list.


Install MCT's dependencies using rosdep with the following command

.. code-block:: none 

   rosdep install mct
   
Again durning the installation process there will be occasional prompts - answer yes to all. 


Build the MCT ROS package
=========================

First, cd into mct directory ~/ros/mct.   Then run 

.. code-block:: none

   rosmake  
   
Build will take awhile. After the build has finished run the install_scripts.py python program found in the ~/ros/mct 
directory.

.. code-block:: none

   python ~/ros/mct/install_scripts.py


Set device permissions
=========================

In order to run MCT the user must have permissions to access the cameras and various serial devices. 


Setup permisions for 1394 cameras 
---------------------------------

      
 * Add user to video group.  Edit (root or sudo) /etc/group and put <username> after "video" item - e.g. video:x:44:<username>.
 
 * Copy udev rules to appropriate location.  "sudo cp ~/ros/mct/mct_computer_admin/99-camera1394.rules /etc/udev/rules.d"
 
 * logout and back in again for group changes to take effect.
 
 * force system to reload udev rules. "sudo udevadm control --reload-rules"

Note, at this point it is possible to test the 1396 cameras are working using a program called coriander.  First, install coriander via

.. code-block:: none

   sudo apt-get -s install coriander 
   
You can then start coriander from the command line or GUI menu. At this point
using coriander you should be able to start and view images from any camera
attached to the system.

Setup permissions for serial devices
------------------------------------

Serial deviced include the - camera_trigger, active_target, pulse-skipper, mightex-serial devices, etc. 

 * A template udev file "99-mct-usb-serial.rules" can be found in the ~/ros/mct/mct_computer_admin/misc directory.  
 * This file needs to be edited so that the serial numbers of the devices match those for the specific system. (Add notes for figuring out the serial numbers
  of the devices).
 * After finishing force a reload of the udev rules "sudo udevadm control --reload-rules"


  
Generate ssh keys  
------------------
       
  * cd into ~/.ssh directory. 
  * Create keys with "ssh-keygen -t rsa -C <your@email.com".
  * Add to authorized_keys file w/ "cat id_rsa.pub >> authorized_keys"
  * Add master's public key (id_rsa.pub) to each slave's "authorized_keys" file.
  * Add slaves's public keys to master's authorized_keys file.

Note, need to ssh (just once) from the master to each slave. Need to do this
with the following command to ensure that the ecdsa algorithm is not used.

.. code-block:: none

   ssh -oHostKeyAlgorithms='ssh-rsa' <hostname>

Note, master need to knows slaves by name - so need some kind of DNS or add
slaves to /etc/hosts file.  Also, network setup should be such that for each
slave you can 'ping' the master from the slave and 'ping' the slave from the
master. See the following: http://wiki.ros.org/ROS/NetworkSetup
  


Other Notes 
-----------

In order to run "camera_assignment" and "help" I needed to:  

 * Edit machine_def.yaml file to reflect current computer setup. Run "mct update_machine_def".

 * At this point should be able to run "mct help", "mct camera_assignment".

In order to run "tracking_2d" I need to:

 * Edit tracking_2d regions.yaml and camera_pairs.yaml files. Make sure that they
   only contain the cameras that exist.  Cameras are assigned to the correct
   regions etc.

 * cameras/calibrations  Remove all calibrations except that for current camera
   (required otherwise hangs).

 * logging extra_video.yaml - commented this out as I don't have a fisheye camera.
  

