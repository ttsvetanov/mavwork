SAFETY WARNING AND DISCLAIMER
=============================
You are using this software at your onw risk. The authors decline any responsibility for personal injuries and/or property damage.

Some drones supported by this framework ARE NOT TOYS. Even operation by expert users might cause SERIOUS INJURIES to people around. So, please consider flying in a properly screened or isolated flight area.

WHAT IS MAVWORK?
================
MAVwork is a framework for visual control of Micro Aerial Vehicles (MAV). It defines a C++ API from which your application can interact with multiple drones of different types in a common way. Currently, MAVwork runs on Linux and it supports the following drones:

- AR.Drone by [Parrot](http://ardrone2.parrot.com) (Not yet tested with AR.Drone 2.0). More information here [here](http://uavster.com/projects/Initial-MAVwork-release-for-the-Parrot-AR.Drone).
- Pelican by [Ascending Technologies](http://www.asctec.de). More information [here](http://uavster.com/projects/MAVwork-support-for-the-Pelican-quadcopter).
- LinkQuad by [UAS Technologies Sweden AB](http://www.uastech.com). More information [here](http://uavster.com/projects/MAVwork-support-for-the-LinkQuad-quadcopter).

Plainly speaking, MAVwork transforms any drone into an AR.Drone on steroids, featuring:

- Autonomous take-off, hover and land*
- Normal flight mode with yaw, pitch, roll and altitude rate commands
- Shared access to a single drone from multiple applications
- Access from every application to multiple drones
- Two access modes (COMMANDER and LISTENER) with priority system for multiple COMMANDERS.

With MAVwork, your control application is able to:

- Send commands: flight mode, yaw, pitch, roll and altitude rate
- Get notified every time new navigation feedback is available: yaw, pitch, roll, altitude, forward velocity, lateral velocity and yaw rate
- Get notified every time a new frame is received from a camera or depth sensor
- Automatically record logs from every information source
- Interface with a Vicon system
- Get notified on error conditions: the connection with the proxy is lost, the proxy cannot connect with the autopilot

Check the following book chapter for more information: http://link.springer.com/chapter/10.1007%2F978-3-642-35485-4_13 

(*) If a drone does not natively support the autonomous modes, some hardware modifications are needed.

HOW DOES IT WORK?
=================
In the MAVwork system, your application links to a library that contains the API. The API then communicates with every drone in the network in a transparent way. It means that your application does not need to care about communications, drone specifics or basic operations like: take-off, hover or land. From an application's point of view, all drones are equal. The only thing it needs to know is the drone's IP address.

WHAT IF MY DRONE IS NOT SUPPORTED?
==================================
At the drone side, there is a component called "proxy" that communicates with the applications and deals with the drone's hardware. If the drone does not offer the AR.Drone functionalities natively -like speed estimation or automatic take-off, land and hover-, they are implemented in the proxy on software. To support a new drone, an already existing proxy can be easily adapted by rewriting the module that communicates with the autopilot and configuring some parameters. If this is your case and need some help, please check the contact information below.

PROJECTS THAT USE MAVWORK
=========================
As far as I know, these are the projects that officially use MAVwork:

- Flexible Multirotor Controller by Jesus Pestana: https://github.com/jespestana/MultirotorController4mavwork

INSTALLATION
============
First, build the API libraries
-----------------------------
1. Go to '{Your_MAVwork_directory}/lib/atlante/atlante' and run 'make'
2. Go to '{Your_MAVwork_directory}/api' and run 'make'

Second, build the proxy for your drone
-------------------------------------
### Parrot AR.Drone
This is how to build the AR.Drone proxy (Only tested with AR.Drone, not with AR.Drone 2.0):

3. Make sure you have the AR.Drone SDK installed. Otherwise, go to https://projects.ardrone.org and install it.
4. Go to '{Your_MAVwork_directory}/proxies/ARDrone/Build'
5. Edit 'Makefile' and change the SDK_PATH variable to point to your AR.Drone SDK installation directory
6. Run 'make'
Note: If you get an ffmpeg-related error like "make[5]: *** [../../Soft/Build/targets_versions/ardrone_lib_PROD_MODE_ffmpeg_Intel_Linux_3.2.0-33-generic_GNU_Linux_gcc_/ardrone_tool/Video/video_stage_ffmpeg_recorder.o] Error 1", edit {Your_ARDroneSDK_directory}/ARDroneLib/Soft/Build/custom.makefile and change "FFMPEG_RECORDING_SUPPORT = yes" for "FFMPEG_RECORDING_SUPPORT = no", then recompile the proxy by running 'make clean all' at 'proxies/ARDrone/Build'.

### AscTec Pelican
This is how to build the AscTec Pelican proxy:

3. Copy the project to the onboard Pelican Atom board (running Ubuntu)
4. Go to '{Your_MAVwork_directory}/proxies/Pelican/3rdparty/RS-232' and run 'make'
5. Go to '{Your_MAVwork_directory}/proxies/Pelican/bin' and run 'make'
6. Update the Pelican high-level board firmware by flashing '{Your_MAVwork_directory}/proxies/Pelican/AutoPilot_HL_SDK/main.hex' with the Flash Magic application provided by AscTec

### UAS Technologies LinkQuad
This is how to build the LinkQuad proxy:

3. Copy the project to the onboard LinkQuad Gumstix board (running Ubuntu)
4. Go to '{Your_MAVwork_directory}/proxies/LinkQuad/bin' and run 'make'
5. In LinkGS, load the file '{Your_MAVwork_directory}/proxies/LinkQuad/setup/mavwork_on_linkquad.xml' and write the configuration to the onboard flash memory

Third, build your application
-----------------------------
### Using the sample makefile as template
You can use the sample application makefile as a template for your project makefile. Please, check the customizable parameters section of the sample makefile to see what to modify.  
This is how to build the sample application:

3. Set the drone proxy IP in '{Your_MAVwork_directory}/example/sources/main.cpp' defined as DRONE_HOST in line 8 
4. Go to '{Your_MAVwork_directory}/example/bin' and run 'make'

### Setting your build from scratch
If you prefer to set your makefile from scratch, please take into account the following tips:

**a) Your application must include the following headers:**

*MAVwork headers*  
Add "-I{Your_MAVwork_directory}/api -I{Your_MAVwork_directory}/lib/atlante/atlante" to your compiler flags

**b) Your application must link to the following libraries:**

*MAVwork libraries*  
Add "-L{Your_MAVwork_directory}/api/lib/gcc -lcvgdroneclient" to your linker flags  
Add "-L{Your_MAVwork_directory}/lib/atlante/atlante/lib/gcc -latlante" to your linker flags

*Third-party libraries*  
Add "-L{Your_Vicon_library_location} -lViconDataStreamSDK_CPP" to your linker flags

*System libraries*  
Add "-L/usr/lib -lc -lpthread" to your linker flags

HOW TO RUN
==========
First, run a proxy for your MAV
------------------------------
### AscTec Pelican
1. Connect your wifi to the Pelican adhoc network and login with ssh
2. Run the proxy onboard the Pelican with './run.sh' from '{Your_MAVwork_directory}/proxies/Pelican/bin/' (change permissions with 'chmod +x run.sh' if needed)

### Parrot AR.Drone
1. Connect the computer where the proxy will run to the AR.Drone adhoc network
2. Run the AR.Drone proxy at 'proxies/ARDrone/Build/Release/cvgDroneProxy'

### UAS Technologies LinkQuad
1. Connect your wifi to the Pelican adhoc network and login with ssh
2. Run the proxy onboard the LinkQuad with './cvgLinkquadProxy' at '{Your_MAVwork_directory}/proxies/LinkQuad/bin/'

Then, run your application
-------------------------
After running the specific proxy, your app can run an interact with the drone from any node of the network, provided that it can reach the proxy IP.

To run an example app, go to '{Your_MAVwork_directory}/example/bin' and run './cvgDroneBrain'. Make sure that you compiled it with the correct proxy address, defined at 'example/sources/main.cpp' as DRONE_HOST.

CONTACT
=======
For any suggestions, please go to http://uavster.com/contact

CONTRIBUTORS
============
[Ignacio M. Bataller](https://github.com/uavster): Original project creator and maintainer  
[Jesús Pestana](https://github.com/jespestana): Bug reports, suggestions and backup pilot during test flights  
