# kinect-bridge2_catkin-indigo
A kinect2 bridge between windows and ubuntu for ros-indigo, catkin-buildsystem

The following steps illustrate how to set up the kinect_bridge2:

On the Windows side:

1.) Clone the repository.

2.) Download and install: Microsoft Visual Studio Community 2013 (w/ Update 5) from https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx. To find this version, click Visual Studio 2013 under the Visual Studio Downloads title. Then click the tab Community 2013 to bring you to the correct version.

	For downloading, keep the "Web Installer Format" option ticked.
	For installing, keep the default "optional features to install" in the installation window.

3.) Download cmake (https://cmake.org/files/v3.3/ --> cmake-3.3.2-win32-x86.exe)
	Add CMake to the system PATH for all users.
	iff C:/Program Files (x86)/Cmake 2.8/bin/cmake.exe??? then set PATH="C:/Program files (x86)/Cmake 2.8/bin/";%PATH%

4.) Remove the deps folder that currently exists in the root of the kinect_bridge2 folder. Untar the following deps folder (Winzip is a good tool to use for this) and place it in the root of kinect_bridge2
	http://robotics.usc.edu/~ekaszubski/files/kinect_bridge2-deps.tar.gz

5.) In the root of the kinect_bridge2 folder (not kinect-bridge2_catkin-indigo), execute:
	mkdir build

6.) Now we use the CMake GUI to manage the build process. Navigate to the CMake GUI downloaded in step 2. Up top where it says "Where is the source code," navigate to the root of kinect-bridge2 (not kinect-bridge2_catkin-indigo) and select this folder. Where it says "Where to build the binaries", navigate to the build folder created in step 5. This build folder should exist in kinect-bridge2. Press Configure, and when that is complete, press Generate.

7.) Now we set up the Kinect. Download Kinect for Windows SDK 2.0 from this website: https://www.microsoft.com/en-us/download/details.aspx?id=44561
(If using a Kinect v1 and not v2, download version 1.8 of this software from https://www.microsoft.com/en-us/download/details.aspx?id=40278)

8.) Open up kinect_bridge2.sln file from within the build folder you just created.

9.) Set the build type to "Release" in Visual Studio. 

10.) Now we tell Visual Studio where the path is to the Kinect files. Follow the instructions in the following website to set this up:
http://www.cs.princeton.edu/~edwardz/tutorials/kinect2/kinect0_sdl.html
Where it says Empty Project, replace this with the kinect_server project which has already been built. Thus, we look at the property manager

11.)Then right click on "kinect_server" and select "build".

12.) In the command line, type ipconfig. Copy the IPv4 Address.

13.) In the kinect_bridge2 folder, go to bin/Release, and run ./kinect_server.exe --listen-ip <IPv4 Address>. Run everything from the Ubuntu Side (see below) to set up the connection. You will likely see that no messages are being sent from Windows to Ubuntu, which looks like:
```(0 | 0 | 0 | 0 | 0 | 0)```
If there are messages, you can skip the following step.

14.) By default, the Windows Firewall prevents any messages from going through the web socket. This is why there were not any messages sent in the above step. In order to change this, we need to create a new rule. Navigate to the Windows Control Panel. Select System and Security. Then select Windows Firewall. From here, select Advanced Settings on the left hand side. Select Inbound Rules on the top left. Double click kinect_server.exe, and update the action to allow the connection.

Note: Running step 13 is necessary for step 14 or else the executable will not appear in Inbound Rules.

On the Ubuntu Side,

1.) Run catkin_make.

2.) Execute source devel/setup.bash.

3.) Execute roslaunch kinect_bridge2 kinect_client.launch server_ip:=<IPv4 Address>

***You may have to run sudo apt-get install libsndfile1-dev libpng12-dev
