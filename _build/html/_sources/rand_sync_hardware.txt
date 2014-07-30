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
toggles every time the synchronization signal changes, is output on pin 4. IN

In addition there is an input the camera hardware trigger signal which triggers
an interrupt service routine in the firmware.  During the interrupt the
firmware counts camera trigger and records the state of the random
synchronization signal. The values are stored on a ring buffer which can be
queried from the host PC over USB. 

For each camera frame the MCT software will query the random synchronization
device in order to get the associated 3-bit signal so that it can be recorded
along with the tracking data. 

Additional information:

* :download:`Schematic <_static/rand_sync_device_schem.pdf>` and :download:`layout <_static/rand_sync_device_layout.png>`

* Design files and BOM (KiCad) https://bitbucket.org/iorodeo/mct_rand_sync_pcb/src

* Device firmware https://bitbucket.org/iorodeo/mct/src/  (subdirectory: mct_rand_sync/firmware/rand_sync)


Installation/Upgrade
====================

The initial MCT system did not include the random synchronization hardware. The
following set of notes describes how to install/upgrade the MCT system so that
it can make use of this hardware. 

Overview of installation steps:

* Update the mct software and the mct_config on both the master and slave computers.
* Add the random synchronization device to the udev rules file. 
* Install the hardware.
* Test the installation.

USB Communications
==================

Description of the USB communication used between the device and the host computer.
