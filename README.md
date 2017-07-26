# optitrack_ros_bridge
This package is a modified [Optitrack](https://github.com/crigroup/optitrack) package, that was created to suit the needs of Robotican use cases.

# Getting started

## prerequisite

### On server PC

#### Install Motive sofware version 1.10.2

1. Plug in OptiTrack dongle
2. Open Motive software
3. Insert liscense file if necessary
4. Inside Motive calibrate OptiTrack cameras
http://wiki.optitrack.com/index.php?title=Calibration

You are now ready to set Motive connection to publish OptiTrack data to client PC:
1. Inside motive 
	open Streaming panel: click View -> Data Streaming
	On Streaming panel:
		Select your local IP address under "Local Interface"
		Make sure "Stream rigid bodies" is set to true.
		Make sure "Type" is set to "Multicast"
		Make sure "Data port" is set to 1511
		Insert client machine IP inside "Multicast interface"
		Check "Broadcast Frame Data" on top of the panel, and make sure "NET" label is light up (bottom-right corner of screen)
		
<img src="/images/instructions/motive.gif" width="500" height="300" />

### On client PC

#### Install Ubuntu 14.04
http://howtoubuntu.org/how-to-install-ubuntu-14-04-trusty-tahr

#### Install ROS Indigo
http://wiki.ros.org/indigo/Installation/Ubuntu

OptiTrack repository depends on optirx 1.9 repository.
Install the python-optrix library:
```
$ pip install optirx --user
```

Clone the this repository:
```
git clone https://github.com/robotican-organization/optitrack_ros_bridge.git
```

Go to optitrack_ros_bridge/src folder, and copy the repositories (hrl_kdl and interactive_markers) inside it to your catkin workspace. 

Install any missing dependencies using rosdep:
```
$ rosdep update
$ rosdep install --from-paths . --ignore-src --rosdistro indigo -y
```

Now compile your catkin workspace. e.g.
```
$ cd ~/catkin_ws && catkin_make
```

#### preparation 

Optitrack transfer information using NetNat protocol. Make sure the protocol version
is correct (if not, the repository will not be able to decode incoming packages from Motive software).
The correct version depends on what version Motive software is using. The optirx repository documentation defines the latest supported version.
For optirx documentation go to: https://pypi.python.org/pypi/optirx

Open rigid_bodies_publisher.py :
```
$ gedit ~/catkin_ws/src/optitrac_ros_bridge/scripts/rigid_bodies_publisher.py
```
make sure that NatNet version located in line 43 is correct. The most up to date version at the time of writing these lines is 2.9.0.0 :
```
version = (2, 9, 0, 0)  # the latest SDK version
```

In case you made some changes, don't forget to compile catkin workspace again

# Usage

Launch optitrack:
```
$ roslaunch optitrack optitrack_pipeline.launch iface:=wlan0
```
iface argument is a must. It specify the interface you are using in order to listen to NetNat packages.
After launching optitrack, your local IP will automatically be identified and used
to listen to packages over the selected interface. 
Launching optitrack_pipeline.launch load rigid_bodies.yaml config file and Rviz will appear on screen. 

<img src="/images/instructions/launching_optitrack.gif" width="500" height="300" />

You can also open RQT to get the ability to watch topics, tf tree etc.
To do so, just close RViz, and open rqt while rigid_bodies_publisher node is still running:
```
$ rqt
```
Inside rqt:
1. Select "world" for "Fixed frame" property
2. Add tf/axis and select rigid_body_[id]. repeat this step for every rigid body you want to present

<img src="/images/instructions/launching_tf_in_rqt.gif" width="500" height="300" />

In rqt you can monitor published topics. i.e: in order to view rigid body markers, open topic monitor and select to appropriate topic.

<img src="/images/instructions/markers_in_rqt.gif " width="500" height="300" />

