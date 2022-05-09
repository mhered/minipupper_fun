# MiniPupper SLAM Navigation

## Testing the LD06 on the laptop

05.05 followed instructions in the [LD06 Readme](https://github.com/ldrobotSensorTeam/ldlidar_stl_ros) to setup and test LiDAR on the laptop. This is not necessary but I wanted to make sure LD06 was working standalone in a simple way.

1. Clone the repo (**Note: in hindsight it would've been better to create `ldlidar_ros_ws` under `catkin_ws` but never mind**):

```
$ cd ~
$ mkdir -p ldlidar_ros_ws/src
$ cd ldlidar_ros_ws/src
$ git clone  https://github.com/ldrobotSensorTeam/ldlidar_stl_ros.git
```

2. Connect to USB through the control board provided

3. Identify the serial port device mounted by the radar in the system. Original instructions suggested using `$ ls -l /dev` but this yields a very long list where I wasn't able to see anything. I found [here](https://askubuntu.com/questions/398941/find-which-tty-device-connected-over-usb) a better way. Plug the USB then issue:
```bash
$ dmesg|grep tty
[317997.130732] usb 2-2: cp210x converter now attached to ttyUSB0
```

This will output the tty devices detected by kernel, in the order that they were detected and with a time stamp. We are looking for the **cp210x converter**. We then give it writing permission:

```
$ sudo chmod 777 /dev/ttyUSB0
```

4. if needed (i.e. different from `ttyUSB0`) edit the corresponding line in the launch file `~/ldlidar_ros_ws/src/ldlidar_stl_ros/launch/ld06.launch` to set the proper `port_name`

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

6. set environment variables. If you're running it only once: 

```
$ cd ~/ldlidar_ros_ws
$ source devel/setup.bash
```

or add the line below to `~/.bashrc`  for convenience to set them every time a terminal is open:

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

- [ ] Ask about failed test
- [ ] compare with my ROS notes, investigate a bit nodes and topics etc
- [ ] Take screenshots
- [x] How to identify the serial port device mounted by the radar in the system?

## 07.05 Install LiDAR

The installation instructions for the LiDAR are somewhat lacking detail:

1. Which length screws should we use to fix the 3D-printed stand of the LiDAR to the robot frame? 
   - Do we need to replace the two screws at the rear with longer ones? (I used the same ones and it seems fine). 
   - Which length screw to use for the third hole at the front? Does the front screw require a locknut? (I used a M2x10mm + locknut. Access to install the locknut is not obvious, but I came up with a trick to hold the nut using the wrench provided and cello tape).  
2. Attaching the LiDAR to the 3D printed part is also not easy:
   - The fwd arrow embossed at the top of the LiDAR module is actually pointing left, is that normal? 
   - Also both the turrets in the 3D printed part broke on me when I was trying to install the self-tapping screws. I will need to print a replacement part, maybe using a different material.
3. The USB to microUSB cable is too long and rigid. I ordered a shorter flat cable from AliExpress: https://es.aliexpress.com/item/1005003635006049.html S1W1A 10cm 
4. There is no space to store the control board for the LD06. Maybe I will design a 3D-printed clip. (edited)

### To do

- [ ] Order replacement 3D-printed part for LD06 stand
- [ ] Design and send to print a 3D printed clip to hold the board
- [x] Order flat USB-microUSB cable AliExpress (21 Jun!)
- [ ] Bring flat cable from Don Juan
- [x] Install sfw PC side

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

Failed again when compiling `cartographer_ros`. I tried deleting `~/catkin_ws/carto_ws/build_isolated/cartographer_ros/CMakeCache.txt` but it didn't work. Instead I edited it to replace all occurences of `/usr/bin/python` by `/usr/bin/python3`

Success!

I suspect in the first attempt I left behind some `python` in `CMakeCache.txt`

Note: The issues seems related to the fact that I have python 2 and 3 installed and `python`refers to python 2. 

### Install Minipupper ROS packages

Done following instructions here: https://github.com/mangdangroboticsclub/minipupper_ros

```
cd ~/catkin_ws/src
git clone --recursive https://github.com/mangdangroboticsclub/minipupper_ros
cd ~/catkin_ws/
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

### Network and environment

Obtain the local IP address of the PC with: 

```
$ ip a
```

In my case it was `192.168.0.23`. Edit `~/.bashrc ` so the configuration is executed every time we launch a terminal. You will need super user permission: 

```bash
$ sudo gedit ~/.bashrc
```

Add the following lines to `~/.bashrc` (using the PC local IP address for  both `ROS_MASTER_URI` and `ROS_HOSTNAME`):

```bash
export ROS_MASTER_URI=http://192.168.0.23:11311
export ROS_HOSTNAME=192.168.0.23
source ~/catkin_ws/devel/setup.bash
source ~/catkin_ws/carto_ws/install_isolated/setup.bash
```

## 09.05 MiniPupper setup

According to this: https://minipupperdocs.readthedocs.io/en/latest/guide/SLAM.html#for-the-new-version-please-refer-to-ros-page-and-use-this-ros-image the base image from 2022.02.19 is valid to install on top of it MiniPupper ROS packages etc. Lets try.

Not done yet, just reviewed and wrote down all the instructions 

### Hardware dependencies

The repo for the BSP (board support package) for MiniPupper ROS is [here](https://github.com/mangdangroboticsclub/minipupper_ros_bsp)

Installation instructions are in the [ReadMe.txt]() but have typos, should be:

```bash
$ cd home/ubuntu
$ git clone https://github.com/mangdangroboticsclub/minipupper_ros_bsp.git
$ cd minipupper_ros_bsp/mangdang
$ sudo sh Legacy/pre_install.sh
$ sudo sh install.sh
$ reboot
```

Note: check also the cleaned-up repo by `hdumcke`

### Install the ROS interface for the PS4 joystick

PS4 Joystick interface in ROS is based on [ps4-ros](https://github.com/solbach/ps4-ros) project.

```bash
$ pip install ds4drv
$ sudo apt install ros-noetic-joy
$ sudo wget https://raw.githubusercontent.com/chrippa/ds4drv/master/udev/50-ds4drv.rules -O /etc/udev/rules.d/50-ds4drv.rules
$ sudo udevadm control --reload-rules
$ sudo udevadm trigger
$ sudo reboot
```

Then go into pairing mode with PS4: Playstation button + share button for ~5 sec. The issue the following command at terminal until PS4 Joystick is connected.

```
$ ds4drv
```

This will output something like `Created devices /dev/input/jsX`. Then give the permissions to the device

```bash
sudo chmod a+rw /dev/input/jsX
```

### Install the MiniPupper ROS packages on the MiniPupper

Remember they were also installed on the PC. We will install them here as well. The difference is that we will remove gazebo before compiling because it is too heavy for the Raspberry Pi:

```bash
$ cd ~/catkin_ws/src
$ git clone --recursive https://github.com/mangdangroboticsclub/minipupper_ros

$ cd minipupper_ros/champ
$ sudo rm -rf champ_gazebo

$ cd ~/catkin_ws/
$ rosdep install --from-paths src --ignore-src -r -y
$ catkin_make
$ source ~/catkin_ws/devel/setup.bash
```

### Network and environment

Connect your PC and Mini Pupper to the same WiFi and find the assigned local IP address:

```bash
$ ip a
```

In my case it was `192.168.0.23`. Edit `~/.bashrc` so the configuration is executed every time we launch a terminal. You will need super user permission: 

```bash
$ sudo gedit ~/.bashrc
```

Add the following lines (using the PC local IP address for `ROS_MASTER_URI` and the Raspberry Pi local IP address for `ROS_HOSTNAME`):

```bash
export ROS_MASTER_URI=http://192.168.0.23:11311
export ROS_HOSTNAME=192.168.0.XX
source ~/catkin_ws/devel/setup.bash
```

### To do

- [ ] connect MiniPupper to screen, keyboard & mouse
- [ ] recalibrate: yesterday I corrected the length of the adjustable rods. Need to rerun calibration!
- [ ] actually perform the MiniPupper setup steps (see above)

- [ ] Run SLAM