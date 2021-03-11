# Liberty-Way AMLS Landing Controller


![Logo](https://github.com/XxOinvizioNxX/Liberty-Way/blob/main/git_images/logo_book.png "Logo")

[![Liberty-Way_beta_1.1.0](https://img.shields.io/badge/Latest_version-beta_1.1.0-informational?logo=Github&style=flat-square "Liberty-Way_beta_1.1.0")](https://github.com/XxOinvizioNxX/Liberty-Way/releases/tag/beta_1.1.0 "Liberty-Way_beta_1.1.0")
[![Our_channel](https://img.shields.io/badge/Our_channel-YouTube-red?logo=Youtube&style=flat-square "Our_channel")](https://www.youtube.com/channel/UCqN12Jzy-1eJLkcA32R0jdg "Our_channel")
![GitHub All Releases](https://img.shields.io/github/downloads/XxOinvizioNxX/Liberty-Way/total?style=flat-square "GitHub All Releases")



This project was created as a part of project activities subject of Moscow Polytech University by the 181-311 group.
It also participates in the CopterHack 2021 hackathon from Copter Express.

Liberty-Way AMLS Landing Controller © 2021 Pavel Neshumov (Frey Hertz)

All the code excluding the dependencies block, was written by Pavel Neshumov (Frey Hertz)

------------

### Dependencies
- **Flak** (Web framework): https://github.com/pcdv/flak
- **MiniPID** (PID Controller): https://github.com/tekdemo/MiniPID-Java
- **Log4j** (Logback): https://logging.apache.org/log4j/1.2/
- **OpenCV** (Computer vision): https://github.com/opencv/opencv
- **OpenCV-contib** (Extra OpenCV modules): https://github.com/opencv/opencv_contrib
- **Commons-CLI** (Arguments parser): https://commons.apache.org/proper/commons-cli/
- **jSerialComm** (Serial communication): https://github.com/Fazecast/jSerialComm
- **GSon** (JSON implementation): https://github.com/google/gson

------------

### Description
This application is a part of the AMLS project (Autonomous Multi-rotor Landing System).
The task of the project is to automatically land a drone on a platform in motion.

![Screenshot](https://github.com/XxOinvizioNxX/Liberty-Way/blob/main/git_images/Screenshot_1.png "Screenshot")

This application processes the frame received from the camera located on the platform which is looking up. On the bottom of the drone, there is an ARUco tag. The application detects the marker, estimates its position, then passes through the PID controller and sends the correction values to the drone.

------------

### License
Liberty-Way is licensed under AGPLv3, you can find it’s contents in the main branch.

------------

### Feedback
Our E-Mails:
- xxoinvizionxx@gmail.com (Pavel Neshumov)
- astik452@gmail.com (Andrey Kabalin)
- vlad.yasn@gmail.com (Vladislav Yasnetsky)
 
------------

### Logotype
AMLS and Liberty-X logo was designed by Pavel Neshumov

------------

### Building and running
Liberty-Way is a cross-platform application and has been tested on Linux and Windows. 
You can try running the application on any other operating system. But you first need to build the OpenCV-contrib library (**The releases include libraries for Windows and Linux**).
Builded binary JAR-file can be found in releases.


All of the controls is performed only from the browser.
When starting the application, you can specify the arguments:
```
 -c,--color                write colored logs.
 -i,--ip <arg>             server ip
 -sp,--server_port <arg>   web server port (0 - 65535)
 -vp,--video_port <arg>    video stream port (0 - 65535)
```
Also, the server address and ports can be specified in the configuration (settings.json)

-------

###  Configuration
These are the parameters presented in settings.json file that are used by the program and can be changed depending of its application:
```
"marker_size",             length of one side of the tracking marker (cm)
"default_exposure",        default exposure value of the camera
"landing_alt",             altitude of sending a packet to turn off the motors (service info = 3)
"pid_file",              file of PID configuration
"camera_matrix_file",              camera calibration (matrix)
"camera_distortions_file",     camera calibration (distortions)
"watermark_file",              image of a watermark (top-right corner)
"web_resources_folder",              folder of web resources
"web_templates_folder",              folder of templates for a web-page
"blackbox_folder",           folder which stores blackboxes' .csv files
"frame_width",              resolution that is set to web-camera (width)
"frame_height",              resolution that is set to web-camera (height)
"disable_auto_exposure",              disabling/enabling auto exposure feature
"disable_auto_wb",              disabling/enabling auto white balance feature
"disable_auto_focus",              disabling/enabling auto focus feature
"default_server_host",              server host which can be overridden by cmd argument
"default_server_port",              server port which can be overridden by cmd argument
"default_video_port",              video port which can be overridden by cmd argument
"video_stream_enabled_by_default",              should the video stream be enabled from the start
"video_on_page_enabled_by_default",              should the video be enabled on the page from the start 
"blackbox_enabled_by_default",                   should the blackbox feature be enabled by default
"platform_light_enable_threshold",              lower threshold of the surrounding light to enable additional lighting
"platform_light_disable_threshold",             upper threshold of the surrounding light to disable additional lighting
"platform_reply_timeout",         amount of seconds in which a drone is declared lost from vision
"platform_loop_timer",            
"fps_measure_period",              period of measurements of fps (milliseconds)
"adaptive_thresh_constant",              detector of parameters (ARUCO)
"aruco_dictionary",
"allowed_ids",              array of allowed tracking markers ARUCO ids
"input_filter",              Kalman filter coefficient
"setpoint_alignment_factor",
"allowed_lost_frames",              frames where the marker is not in the input frame
"landing_decrement",              constant latitude decrement (cm)
"allowed_landing_range_xy",              range of the landing allowance (cm)
"allowed_landing_range_yaw",              range of the landing allowance (degrees)
"setpoint_x",              setpoint of PID controller (absolute estimated x)
"setpoint_y",              setpoint of PID controller (absolute estimated y)
"setpoint_yaw",            angle setpoint of PID controller (absolute estimated degrees)
"data_suffix_1",          first of the unique ASCII pair symbols that show the end of the packet
"data_suffix_2",          second of the unique ASCII pair symbols that show the end of the packet
"push_osd_after_frames",           after how many frames the image is being sent to the web-page
```

--------

### Data packet structure
This is the structure of a packet that is being sent to the drone for execution of a PID controller arguments:

![Packet](https://github.com/XxOinvizioNxX/Liberty-Way/blob/main/git_images/Data_structure.png "Data packet")

Bytes description:
- **Roll bytes** - Roll correction values 
- **Pitch bytes** - Pitch correction values 
- **Yaw bytes** - Yaw correction values
- **Altitude bytes** - Altitude correction values
- **Service info** - sets the drone state (0 - Nothing to do, 1 - Stabilization, 2 - Landing (command not implemented and will be removed in the future. This is not a real landing, just to tell the drone to start decreasing altitude), 3 - Disable motors)
- **Check byte** - XOR sum of all previous bytes that is compared via transmittion in order to verify the data
- **Data suffix** - unique pair of ASCII symbols that is not represented in the packet in any form and that shows the end of the packet 

--------

### AMLS Article
In this Article we will describe AMLS project, namely, AMLS Optical stabilization, GPS holding, GPS following, Grapling system and Weather protection system. Plus we will make clear how it works and how it's done!

-------

### How AMLS started
AMLS or Autonomous Multirotor Landing System is COEX initiative on creating a system of drone and landing platform implemented on a car with automized landing while in move.
So why we decided to take this assignment. A year before we assigned on this project we had a self made drone developed by Pavel Neshumov and as we were interested in developing a new system with lack of solutions and actually working systems we started our project.

![Screenshot](https://github.com/XxOinvizioNxX/Liberty-Way/blob/main/git_images/drone.png "Screenshot")
                                               
Picture of Liberty_X

-------

### Optical stabilization 
Lets pass to systems of AMLS. First and may be the most important part of our project besides from landing is Optical stabilization.

As we mentioned earlier our drone and controller are self made in this regard we were able to implement new features and systems in short amount of time 

And as we couldn't predict the possibility of accomplishing of our task, first of all, we started to think about means for stabilization system 

Afterwards, we stopped on stabilazing using augmented reality tags. Firstly, it won't take much finances as GPS or RTK systems and will be accurate enough to accomplish it's purpose

Our first idea was to attach Raspberry Pi with Liberty_X and give all the stabilization math to Raspberry Pi 

[![Watch the video](https://github.com/XxOinvizioNxX/Liberty-Way/tree/main/git_images/2021-03-11 (1).png)](https://youtu.be/TrrxXOHAqbQ)

but tests showed that it's 

-------

### GPS holding

-------

### GPS following

-------

### Grabbing system

-------

### Weather protection system

-------
