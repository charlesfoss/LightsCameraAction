#LightsCameraAction


Plugin for Micro-Manger to tightly integrate imaging through the Micro-Manager API with control of Heliospectra lights via Telnet.

##Description
This software is a plugin for Micro-Manger (https://www.micro-manager.org/) used to enable control of Heliospectra lights (http://www.heliospectra.com/) and an imaging platform with high time-precision. Using this plugin one can orchestrate timing of lights actions with imaging actions over some period and under an arbitrary schedule.

At its core this plugin conducts some number of image acquisition events, where each image acquisition event is structured as:
* Send "stopSchedule" command to lights;
* Send a wavelengths command to lights, by default "setAll 0", ie. turn lights off. The actual WL command is user modifiable;
* Wait some amount of time, specified by the user;
* Acquire an image using the Micro-Manager API - camera settings such as exposure time are specified in the Micro-Manager interface;
* Send a "startSchedule" command to the lights.

Any number of image acquisition events can be performed, as specified by the user. This allows for time-course imaging, while controlling with high precision the time between lights commands and camera commands.

##Assumptions
We built and tested this software using Micro-Manager versions 1.4.16 and 1.4.18. One should be careful to compile this software using the same Java version as the version of the Java VM used to run Micro-Manager. For example, if this software is built using version 1.7 of the Java SDK, be sure that your installation of Micro-Manager is executed using version 1.7 of the Java VM. This can be tricky because, for example, on OS X, one's Netbeans platform may be configured to build using version 1.7 of the Java SDK even though the system default Java version is 1.6.

This project was developed using Apache Maven (http://maven.apache.org/). The project's Micro-Manager and ImageJ Java libraries are imported into the classpath based on one's Micro-Manager base install directory. This base install directory is stored in the project's pom.xml file in the variable "Micro-ManagerInstallDirectory". Be sure to edit your pom.xml file to correctly point to your particular Micro-Manager base install directory. The project's only other outside dependency is Joda-Time which should automatically be downloaded from a public Maven repository. Joda-Time and your compiled plugin Jar file are copied to the appropriate locations, again based on the Micro-Manager base install directory as indicated in your pom.xml file.

To use this plugin, if your pom.xml file has been appropriately edited, simply compile using Maven within your IDE (Netbeans in our case). This will generate your plugin Jar file, and copy it to the appropriate mmplugins location. Then, launch Micro-Manager and select "LightsCameraAction" from the Micro-Manager plugins menu. (Note: If your menu is empty, you probably have a mismatch between the version of Java used to generate the plugin Jar file and the version of Java used to launch Micro-Manager.)

##Quick-Start Guide
To get this working ASAP:

1. Download and install Micro-Manager (https://www.micro-manager.org/)
2. Download and install Java SDK for your platform (http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html), preferentially Java version 1.6 but newer versions *should* work, as long as the caveats uner Assumptions, above, are heeded. Specifically, the versions of java used to compile the plugin, and run Micro-Manager, must match.
3. Download and install NetBeans (https://netbeans.org/)
4. Clone this repository with the command ```git clone https://github.com/bryantdo/LightsCameraAction.git```
5. Open NetBeans, then file menu -> open project -> open the cloned git repo
6. Verify Micro-Manager install directory (line 12 in pom.xml, included with repo)
7. Clean and build
8. Launch Micro-Manager, plugins menu -> select LightsCameraAction

##Plugin Functions
Here we discuss each component of the plugin.

###Configuration
Controls in the Configuration group are used to specify, load, and save plugin settings.

####Load Configuration
Used to load a previously saved .configuration file. Settings which are saved include lights IP address and port, time and units between acquired images, time and units between wavelengths command and image capture, and total number of image acquisition events to conduct.

####Save Configuration
Creates a .configuration file to save settings. Settings saved include ights IP address and port, time and units between acquired images, time and units between wavelengths command and image capture, and total number of image acquisition events to conduct.

####Specify Image Save Location
Chooses a directory in which to save acquired images.

####Lights IP Address : Port
Location on the network and port that should be used to communicate via Telnet with your Heliospectra lights.

####Time and Units Between Acquired Images
Specifies the period over which to conduct the "issue WL command -> delay -> capture image" routine.

####WL Command Sent to Image Delay
Specifies the time between sending the wavelengths command (default: setall0, editable by user) and acquiring an image.

#### Number of Images to Acquire
Specifies the number of image acquisition routines to run.

###Testing
####Test Lights ON/OFF
Sends over Telnet a "hello" command using the IP address and port specified in the Configuration group.

####Test Image Acquisition
Not yet implemented.

###Wavelengths
####Send getwl command
Sends over Telnet a "getwl" command using the IP address and port specified in the Configuration group and prints the lights' reply, allowing the user to specify an appropriate "setwlsrelpower" command.

####WL Command
By default the wavelengths command is "setall0", meaning that lights will be set as "off" before imaging. This can be changed by the user, most likely replaced with a "setwlsrelpower" command.

###Run
####Start Imaging Job
Begins the image acquisition routine using settings specified in the Configuration and Wavelengths groups.

####Require Lights Schedule Running
At the end of every imaging cycle this plugin executes a "startSchedule" command on the lights. To ensure that a schedule is already running before beginning each image acquisition event, check this box. Otherwise, uncheck.
