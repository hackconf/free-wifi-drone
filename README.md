# Free Wi-Fi Drone
An autonomous drone that delivers free Wi-Fi made from household parts.

## Installation

### Compiling the Drivers

Pre-requirements:

- Internet connection
- ROS Indigo or Jade (Only tested on Ubuntu)
- Ubuntu packages: build-esstential, python-rosdep, python-catkin-tools
- Basic familiarity with building Robot Operating System packages

```bash
$ sudo apt-get install build-essential python-rosdep python-catkin-tools
```

Clone the source code in a new workspace, install dependencies and finally compile the workspace.

```bash
# Create the workspace
$ mkdir -p ~/free-wifi-drone/src && cd ~/free-wifi-drone
$ catkin init
$ git clone https://github.com/hackconf/free-wifi-drone.git src/free-wifi-drone
# Update rosdep database and install dependencies
$ rosdep update
$ rosdep install --from-paths src -i
# Build the workspace
$ catkin build -DCMAKE_BUILD_TYPE=RelWithDebInfo
```

The first time build may take up to 15 minutes while the build script downloads and compiles packages.


## Quick Start

### Sending Commands

**Takeoff**

Publish a message of type `std_msgs/Empty` to `takeoff` topic.

```
$ rostopic pub --once std_msgs/Empty [namespace]/takeoff
```

**Land**

Publish a message of type `std_msgs/Empty` to `land` topic.

```
$ rostopic pub --once std_msgs/Empty [namespace]/land
```

**Emergency**

Publish a message of type `std_msgs/Empty` to reset `topic`.

```
$ rostopic pub --once std_msgs/Empty [namespace]/reset
```

**Manual Piloting**

To move Free-Wifi-Drone around, publish messages of type `geometry_msgs/Twist` to `cmd_vel` topic while the drone is flying. The effect of each field of the message on the drone’s movement is listed below:

```
linear.x  (+)      Translate forward
          (-)      Translate backward
linear.y  (+)      Translate to left
          (-)      Translate to right
linear.z  (+)      Ascend
          (-)      Descend
angular.z (+)      Rotate counter clockwise
          (-)      Rotate clockwise
```

Acceptable range for all fields are `[-1..1]`. The drone executes the last received command as long as the driver is running. This command is reset to when Takeoff, Land or Emergency command is received. To make the drone hover and maintain its current position, you need to publish a message with all fields set to zero to `cmd_vel`.


**Moving the Antenna**

To move the drone’s starboard or port antenna, publish a message of type `geometry_msgs/Twist` to `starboard_antenna_control` or `port_antenna_control` topic. `angular.y` and `angular.z` fields of this message set absolute tilt and pan of the antenna in degrees respectively.

:construction: Warning: _The API for this command is not stable. We plan to use JointState message in future versions._

```
angular.y (+)      tilt down
          (-)      tilt up
angular.z (+)      pan left
          (-)      pan right
```

**Navigate Home**

To ask the drone to autonomously fly to it’s home position, publish a message of type `std_msgs/Bool` to `autoflight/navigate_home` topic with the data field set to true. To stop the drone from navigating home, publish another message with data set to false.


**Router Reset**

To reset the router itself, publish a `std_msgs/Empty` message on `broadcast` topic. The reset process takes between 30 and 60 seconds to complete.

**Router Console Access**

```
Default IP: 192.168.1.42
Port: 8080
Path: admin/router
Username: admin
Password: <no password>
```

## Credits

Built for the annual Major League Hacking contest by @courtney, @morgan, and @alex.
