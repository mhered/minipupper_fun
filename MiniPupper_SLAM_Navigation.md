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

## 07.05 Install LiDAR

The installation of the 3D printed stand is a bit unclear: which length screws? Do I need to change the length of the tow rear? Which length for the third hole? Needs a locknut? I developed a trick with the wrench and cello tape to hold the nut

The 3D printed part breaks when installing the self-tapping screws. Need to print again

The USB to microUSB cable is too long and rigid

There is no space to store the control board for the LD06. Maybe design and print a 3D printed clip? 

### To do

- [ ] I corrected the length of the adjustable rods. Need to rerun calibration!
- [ ] Order replacement 3D part for stand LD06
- [ ] Design and send to print a 3D printed clip to hold the board
- [ ] Order flat USB-microUSB cable AliExpress (21 Jun!)
- [ ] Bring flat cable from Don Juan
- [x] Install sfw PC side
- [ ] Install sfw pupper side
- [ ] Run SLAM

## 08.05 PC setup

Followed the instructions here: https://github.com/mangdangroboticsclub/minipupper_ros

### Cartographer

For the installation of cartographer I followed the official page: https://google-cartographer-ros.readthedocs.io/en/latest/compilation.html but it failed with:

```
CMake Error at /usr/share/cmake-3.16/Modules/FindPackageHandleStandardArgs.cmake:146 (message):
  Could NOT find PythonInterp: Found unsuitable version "2.7.18", but
  required is at least "3" (found /usr/bin/python)
```

Following suggestions found here: https://github.com/YosysHQ/nextpnr/issues/214 I edited `CMakeCache.txt` to replace `/usr/bin/python` by `/usr/bin/python3`

This worked for a bit then failed again because could not find some files of littledog_description package (from memory)

--

 I then deleted `devel_isolated`, `build_isolated` and `install_isolated` folders and tried again with the instructions in `minipupper_ros` repo but but created `/carto_ws` folder under  `~/catkin_ws/`  instead of directly under`~` 

Failed again when compiling `cartographer_ros`. I tried deleting `~/catkin_ws/carto_ws/build_isolated/cartographer_ros/CMakeCache.txt` but it didn't work. Instead I edited to replace  `/usr/bin/python` by `/usr/bin/python3`

Success!

I suspect in the first attempt I left behind some `python` in `CMakeCache.txt`

Note: The problem is that I have python 2 and 3 and `python`refers to python 2 

### Minipupper packages

Done following instructions here: https://github.com/mangdangroboticsclub/minipupper_ros

```
cd ~/catkin_ws/src
git clone --recursive https://github.com/mangdangroboticsclub/minipupper_ros
cd ..
rosdep install --from-paths src --ignore-src -r -y
catkin_make
source ~/catkin_ws/devel/setup.bash
```

I got several messages highlighting the new install under `minipupper_ros`, may conflict with the install I made months ago to play with the simulated MiniPupper, e.g.: 

```
Multiple packages found with the same name "champ":

- champ/champ
- minipupper_ros/champ/champ
```

Not sure if this will result in runtime errors.

Recommendation here: https://answers.ros.org/question/231750/runtimeerror-multiple-packages-found-with-the-same-name-tf2-tf2_msgs-tf2_py-tf2_ros/ is to update `.rosinstall`

I found `~/catkin_ws/devel/.rosinstall` but is refers to `~/catkin_ws/devel/setup.sh`and nothing relevant there apparently

Humm...

### Network config

Obtain `inet` with 

```
$ ifconfig
```

In my case it was `192.168.1.106`. Add the following lines to `~/.bashrc` so the configuration is executed every time we launch a terminal: 

```bash
export ROS_MASTER_URI=http://192.168.1.106:11311
export ROS_HOSTNAME=192.168.1.106
source ~/catkin_ws/devel/setup.bash
source ~/catkin_ws/carto_ws/install_isolated/setup.bash
```