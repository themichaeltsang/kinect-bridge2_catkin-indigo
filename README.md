# kinect-bridge2_catkin-indigo
A kinect2 bridge between windows and ubuntu for ros-indigo, catkin-buildsystem

The following steps illustrate how to set up the kinect_bridge2:

On the Windows side:

1.) Clone the repository.

2.) Download and install: Microsoft Visual Studio Community 2013 (w/ Update 5) from https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx

	For downloading, keep the "Web Installer Format" option ticked.
	For installing, keep the default "optional features to install" in the installation window.

3.) Download cmake (https://cmake.org/download/ --> cmake-3.3.2-win32-x86.exe)
	Add CMake to the system PATH for all users.
	iff C:/Program Files (x86)/Cmake 2.8/bin/cmake.exe??? then set PATH="C:/Program files (x86)/Cmake 2.8/bin/";%PATH%

4.) Untar the following deps folder and place it in the root of kinect_bridge2 (not necessarily the root of the entire project)
	http://robotics.usc.edu/~ekaszubski/files/kinect_bridge2-deps.tar.gz

5.) In the root of the kinect_bridge2 folder, exectue:
	mkdir build; cd build
