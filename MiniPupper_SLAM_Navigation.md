# MiniPupper SLAM Navigation

## Testing the LD06 on the laptop

05.05 followed instructions in the [LD06 Readme](https://github.com/ldrobotSensorTeam/ldlidar_stl_ros) to setup and test LiDAR on the laptop

1. Clone the repo:

```
$ cd ~
$ mkdir -p ldlidar_ros_ws/src
$ cd ldlidar_ros_ws/src
$ git clone  https://github.com/ldrobotSensorTeam/ldlidar_stl_ros.git
```

2. Connect to USB through the control board provided

3. Identify the serial port device mounted by the radar in the system (using e.g. `$ ls -l /dev` ) and give it writing permission, e.g.:
```
$ sudo chmod 777 /dev/ttyUSB0
```

4. if needed edit the corresponding line in the launch file `~/ldlidar_ros_ws/src/ldlidar_stl_ros/launch/ld06.launch` to set the proper `port_name`

```yaml
...
<param name="port_name" value ="/dev/ttyUSB0"/>
...
```

5. build

```
$ cd ~/ldlidar_ros_ws
$ catkin_make
```

6. set environment variables. To run only once: 

```
$ cd ~/ldlidar_ros_ws
$ source devel/setup.bash
```

or add the line below to `~/.bashrc`  for convenience to set them every time when a terminal is open:

```
$ echo "source ~/ldlidar_ros_ws/devel/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

6. run the node

To just launch the ld06 lidar node:

```
$ roslaunch ldlidar_stl_ros ld06.launch
```

Or, to launch the node and visualize it in RVIZ (in noetic):

```
$ roslaunch ldlidar_stl_ros viewer_ld06_noetic.launch
```

I do not understand the instructions for testing, get an error message.

### To do

- [ ] Ask about test
- [ ] compare with ROS notes, investigate a bit nodes and topics etc
- [ ] Take screenshots
- [ ] How to identify the serial port device mounted by the radar in the system?