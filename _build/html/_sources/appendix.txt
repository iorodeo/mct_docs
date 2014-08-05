.. _appendix:

*****************************
Appendix
*****************************

.. _appendix_finding_usb_attrs:

Finding ATTRS{product} and ATTRS{serial} for your USB/Serial device
====================================================================

In this section we discuss how to determine the "product" and "serial"
attributes for a USB/Serial device. We use an Arduino Nano as an examples.
Depending on the version of the Arduino Nano used on your random
synchronization device the product attribute will either be "ARDUINO NANO" or
"FT232 USB UART". The serial attribute will be specific to the exact Arduino
Nano - it is basically a unique serial number identifying the  device. 

By default, when the Arduino Nanos plugged into the  computer via USB it will
show up as a device in the "/dev" directory with a name of the form
"/dev/ttyUSBn"  where "n" depends on the what other devices are connected to
the computer. By running  the following command before and after connecting the
USB cable to the Arduino Nano you can determine the name assigned to your
device. 


.. code-block:: bash 

    ls /dev/ttyUSB*

When the device is connected a new entry of the form "/dev/ttyUSBn" should
appear  - this is temporary name automatically assigned to the device. You can
use this name to determine the product and serial attributes for the devicej by
running the following command.

.. code-block:: bash 

    udevadm info -a -p $(udevadm info -q path -n /dev/ttyUSBn)


Note, in the above command the "n" should be replace by the number assigned to
your device. The output of this command will return something like the following
:download:`udevadm_info_example <_static/udevadm_info_example.txt>`. Look for 
product and serial attributes for the Arduino Nano  - something line either this


.. code-block:: none 

    ATTRS{manufacturer}=="FTDI"
    ATTRS{product}=="FT232R USB UART"
    ATTRS{serial}=="11CP0195"


or this

.. code-block:: none 

    ATTRS{manufacturer}=="FTDI"
    ATTRS{product}=="ARDUINO NANO"
    ATTRS{serial}=="11CP0195"
