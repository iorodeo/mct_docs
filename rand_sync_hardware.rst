*****************************
Random Synchronization Signal
*****************************

The purpose of the random synchronization hardware is to provide a random 3-bit
signal which be used to verify the synchronization between the camera tracking
and Neuralynx data. If a misalignment is discovered the random synchronization
signal can be used to help determine where the misalignment has occurred and to
help re-align the data.  

Hardware Description
=====================

.. figure:: _static/rand_sync_device_figure.png
   :align:  center

The random synchronization hardware is designed as shield for the  `Arduino
Nano. <http://arduino.cc/en/Main/ArduinoBoardNano>`_  

The firmware on the Arduino generates a random 3-bit synchronization signal
which changes value once a second. The random synchronization bits are
output on pins 5,7 and 9 of the Digital IO header. A change signal, which
toggles every time the synchronization signal changes, is output on pin 4. 

A BNC input is provided for the camera trigger signal. A camera trigger on this
input will initiate an interrupt service routine in the firmware.  The
interrupt routine counts the camera trigger, records the state of the random
synchronization signal and save the values in a ring buffer. The MCT master can
then query the random synchronization device, over USB, in order to get the random
synchronization signal associate with a given camera trigger. 

For each camera frame the MCT software will query the random synchronization
device in order to get the associated 3-bit signal so that it can be recorded
along with the tracking data. 

Additional information:

* :download:`Schematic <_static/rand_sync_device_schem.pdf>` and :download:`layout <_static/rand_sync_device_layout.png>`

* Design files and BOM (KiCad) https://bitbucket.org/iorodeo/mct_rand_sync_pcb/src

* Device firmware https://bitbucket.org/iorodeo/mct/src/  (subdirectory: mct_rand_sync/firmware/rand_sync)


Installation/Upgrade
====================

Note, the initial MCT system did not include the random synchronization
hardware. In order to make use of this this system the MCT software and
configuration needs to be upgraded and the random synchronization hardware
needs to be installed. 

Overview of installation steps:

#. Update MCT software and configuration. 
#. Install the hardware.
#. Test the installation.

Updating the MCT software and configuration files.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using the random synchronization devices requires upgrades to both the MCT
software and the MCT configuration files.  

The MCT software needs to be upgraded as new ROS package is required for
communicating with the random synchronization hardware. In addition modified
versions of serveral existing MCT ROS packages - such as mct_logging and
mct_web_apps - are required in order to properly add the synchronization
signals to the MCT tracking data.

The MCT configuration files need to be modified as an new rand_sync section
(directory) has been added to the configuration in order to store the required
parameters for communicating with this new hardware.

In what follows it is assumed that the master and slave computers are all
running.  

The first step is to pull the latest versions of the MCT software and MCT
configuration repositories. Two cases are considered: first where the MCT
master has internet access second where it does not.

Pulling latest version with internet access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

On the MCT master computer the following command should be run twice - once
from within the local copy of the  MCT source repository (e.g.  ~/ros/mct) and
a second time from within the local copy of the MCT configuration repository
(e.g. ~/ros/mct_config).

.. code-block:: bash

   # Pull and update mercurial repository  
   hg pull -u

This will update the local copes of the MCT software and configuration to the 
latest version. 

Pulling latest version without internet access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the MCT master does not have internet access copies of the latest versions
of the MCT software and configuration repositories will need to be downloaded
onto a thumb drive (or similar) on a computer with does.

Copies (.zip archives) of the latest versions of the repositories can be
downloaded from here

* MCT software https://bitbucket.org/iorodeo/mct/downloads 

* MCT configuration https://bitbucket.org/iorodeo/mct_config/downloads

Unzip the downloaded archives and place them on a thumbdrive. Then copy them 
to a temporary directory on the MCT master computer (e.g. ~/temp). 

On the MCT master computer the following command should be run twice - once
from within MCT source repository (e.g.  ~/ros/mct) and a second time from
within the local copy of the MCT configuration repository (e.g.
~/ros/mct_config).

.. code-block:: bash

   # Pull and update mercurial repository  
   hg pull -u <path to downloaded repository>


In the above command the <path to downloaded repository> should be replaced by
the path to repositories in the temporary directory downloaded in the previous
step. For example, if the repository for the MCT was downloaded to a directory
named iorodeo-mct-999b0e854397 in the ~/temp directory, the you would run the
command  

.. code-block:: bash

   # Pull and update mercurial repository  
   hg pull -u ~/temp/iorodeo-mct-999b0e854397

from within the ~/ros/mct directory.  


Building the ROS packages on master computer.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The next step is to build the ROS packages on the MCT master computer. 
On the MCT master computer, from within the mct source repository (~/mct/ros),
run the following command.

.. code-block:: bash

   # Build the ROS packages on the mct master
   rosmake


Updating the slave computers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

From the MCT master run the following commands (from any directory - doesn't matter). 

.. code-block:: bash

   # Update the setup on the slave computers
   mct push_setup

   # Update the mct repositories on each of the slaves. 
   mct pull_from_master

   # Build the ROS packages on the mct slaves
   mct rosmake

The MCT software and configuration files should now be up to date.


Updating udev rules
^^^^^^^^^^^^^^^^^^^

In order for the MCT software to find the random synchronization USB device it
is necessary to add some  udev rules to the system. A new template udev rules
file (99-mct-usb-serial.rules) can be found in the mct/mct_computer_admin/misc
directory. The contents of this file are as follows:

.. code-block:: none 

    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="FT232R USB UART", ATTRS{serial}=="A8007Ryg", SYMLINK+="camera-trigger"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="FT232R USB UART", ATTRS{serial}=="A7006RxL", SYMLINK+="active-target"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="ARDUINO NANO", ATTRS{serial}=="11IP1984", SYMLINK+="pulse-skipper"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", ATTRS{product}=="USB-Serial Controller D", SYMLINK+="mightex-serial-0"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", ATTRS{product}=="USB-Serial Controller", SYMLINK+="mightex-serial-1"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="FT232R USB UART", ATTRS{serial}=="A600feL8", SYMLINK+="mightex-serial-2"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="FT232R USB UART", ATTRS{serial}=="A600feL9", SYMLINK+="mightex-serial-3"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="FT232R USB UART", ATTRS{serial}=="A600feLa", SYMLINK+="mightex-serial-4"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="FT232R USB UART", ATTRS{serial}=="A600feLb", SYMLINK+="mightex-serial-5"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{product}=="ARDUINO NANO", ATTRS{serial}=="12DP0619", SYMLINK+="rand-sync"


The last line of this file creates a symbolic link (rand-sync) for the random
synchronization device. This symbolic link is required in order for the MCT
software to find the random synchronization hardware. Two attributes (ATTRS)
on this line may need to be modified in order to match your exact hardware -
"product" and "serial".

Depending on the version of the Arduino Nano used on your random
synchronization device the product attribute will either be "ARDUINO NANO" or
"FT232 USB UART". The serial attribute will be specific to the exact Arduino
Nano - it is basically a unique serial number identifying the  device. 

By default, when the Arduino Nano on the random synchronization device is
plugged into the MCT master computer via USB it will show up as a device in the
"/dev" directory with a name of the form "/dev/ttyUSBn"  where "n" depends on
the what other devices are connected to the computer. By running  the following
command before and after connecting the USB cable to the Arduino Nano you can
determine the name assigned to your device. 


.. code-block:: bash 

    ls /dev/ttyUSB*

When the device is connected a new entry of the form "/dev/ttyUSBn" should
appear  - this is temporary name automatically assigned to the device. You can
use this name to determine the product and serial attributes for the devicej by
running the following command.

.. code-block:: bash 

    udevadm info -a -p $(udevadm info -q path -n /dev/ttyUSBn)


Note, in the above command the "n" should be replace by the number assigned to
your device. The output of this command will return something like the following:


USB Communications
==================

Description of the USB communication used between the device and the host computer.
