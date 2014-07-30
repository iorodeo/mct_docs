============================
The mct command line utility 
============================

The **mct** utility is  a command line function which forms the basis for
administering and running for the MCT system. This function is typically run
from a shell on the master computer and includes commands for waking/shutting
down the slave computers,  starting and stopping the various applications which
make up the MCT system -  such as camera assignment, calibration, tracking_2d, etc.

The mct utility is called as follows:

.. code-block:: bash

  mct [comand]

where is command is one of the mct commands listed below.


Help
====

**help**      
    Prints and abbreviated list of the commands most commonly used commands along 
    with a short description of each. 

**list_all**  
    Prints a comprehensive list of the commands available along with the brier
    description of each.

Basic administration
====================

**wakeup** 
    Wakes up (powers up) the slave computers listed in the machine_def.yaml
    file in the current mct configuration.  Note, slave computers must support
    wake-on-lan.

**shutdown**
    Shuts down (powers down) the slave computers listed  in the
    machine_def.yaml file in the current mct configuration. 

**update_machine_def**
    Updates the mct machine definition baseed on the values in the
    machine_def.yaml file in the current mct configuration. Note, this function
    should be run anytime the machine_def.yaml file is changed.  Running this
    function will re generate ROS's xml machine definition file (mct.machine). 


Calibration (in order of application)
=====================================

**camera_assignment**          
    Starts the camera assignment application. This application is a used to
    assign unique numeric identifiers to each camera in the system. Further
    documentation for this application can be found here. *TO DO* 

**zoom_calibration**           
    Starts the camera zoom adjustment application. This application provides a
    preview window of each camera and enables the user to adjust the camera
    zoom setting prior preforming camera calibration. Further documentation 
    for this application can be found here. *TO DO*

**camera_calibration**         
    Starts the camera calibration application. The purpose of this application
    is to generate the basic calibration for each individual camera which is
    part of a tracking region in MCT system. As part of the procedure used by
    this application the user presents a calibration chessboard to each camera
    until sufficient data has been collected to calculate the calibration
    parameters for that camera. Further documentation 
    for this application can be found here. *TO DO*


**homography_calibration**     
    Starts the homography calibration application. The purpose of this
    application is generate the basic mapping (homography) from the camera
    image plane to the tracking plane. As part of the procedure for this
    application, for each camera, the user will place the active calibration
    target in the tracking plane and in view of the camera. The user will the
    initiate a calibration sequence which causes the IR LEDs on the active
    target to light in sequence. The software will attempt to find the LEDs and
    use this information to generate the homography between the camera image
    plane and the tracking plane. Further documentation 
    for this application can be found here. *TO DO*

    
**transform_2d_calibration**   
    Starts the 2d transform calibration application. The purpose of this
    application is to generate the transformations between all user specified
    camera pairs in each tracking region. Note, in order to run this
    application, is it assumed that for each tracking region the user has
    specified a list of camera pairs such that the resulting graph - where the
    cameras in the region are nodes and each pair in the list is an edge  -
    forms a spanning tree for the region.  As part of the procedure for this
    application, for each camera pair,  the user will place the active
    calibration target in the tracking plane and in the overlapping view of the
    current camera pair. The user then initiates a calibration procedure for
    that camera pair which causes the LEDs on the active target to light in
    sequence. The software will attempt to locate the LEDs in both camera views
    in order to find point correspondences. If sufficient data is collected
    during the procedure the 2d transformation for the camera pair will be
    calculated. This action must be performed  for all camera pairs.
    Further documentation for this application can be found here. *TO DO* 




Tracking 
========

**tracking_2d**  
    Starts the tracking_2d application - this is the main 2D tracking application. 
    Documentation for the this application can be found here.  *TO DO*



Advanced administration
=======================

**push_setup**
    Pushes the current mct setup to slave computers. 

**list_slaves**
    Prints a list of the  slaves computers. 

**rospack_profile**
    Runs 'rospack profile' on all slave computers. This is required by ROS
    whenever a new ROS package is added to the MCT system.  

**pull**
    Pulls the latest version of MCT from bitbucket to slave computers.

**pull_master**
    Pulls the lastest version of MCT from bitbucket to the mct master.

**pull_all**
    Pulls the latest version of MCT from bitbucket  to all computers in current
    machine - slaves + master.,

**pull_from_master**
    Pulls the latest version of the MCT from the local repository on the master to the slaves 

**clone**
    Clones a new version of MCT from the repository of slave computers.

**clean**
    Removes an old version of MCT.

**rosmake**
    Runs  rosmake in order to to build MCT on the slave computers. This is
    required when certain changes have been made to an existing ROS package or
    when a new package has been added.

**rosmake_preclean**
    Runs preclean followed by rosmake in order to build MCT on slave. This is
    required when certain changes have been made to an existing ROS package or
    when a new package has been added.  computers.

**list_machine_def**
    Prints the contents of the current MCT machine definition.

**list_cameras**
    Finds all cameras currently connected to the MCT master and slave computers
    and prints the camera information in a list.  
    
**list_camera_assignment**
    Prints a list of the current camera assignment.

**rsync_camera_calibrations**
   Uses rsync to synchronize the camera calibrations on the slave computers
   with that on the MCT master.

**clean_camera_calibrations**
   Cleans (deletes) the current camera calibrations on the slave computers. *TO
   DO: on the master too??*

**show_camera_info**
    Launches camera_info viewers. Basically this runs 'rostopic echo' for each
    camera_info topic.

**show_camera_info_header**
    Launches camera_info/header viewers. Basically this runs 'rostopic echo'
    for each camera_info/header topic.

**show_camera_info_header_seq** 
    Launches camera_info/header/seq viewers. Basically this runs 'rostopic echo'
    for eacht cmaera_info/header/seq_veiwer topic.
    
**show_corrector_seq** 
    Launches seq_and_image_corr/seq viewers. Basically this runs 'rostopic echo'
    for each seq_and_image_corr/seq  topic.


Testing
=======

**frame_drop_test**        
    Starts the  IR LED frame drop test application. *TO DO: add more details*.

**frames_dropped**         
    Prints a report of the frames dropped so far. *TO DO: add more details.*

**frames_dropped_no_seq**  
    Prints a report of the frames dropped no seq lists. *TO DO: add more details*
