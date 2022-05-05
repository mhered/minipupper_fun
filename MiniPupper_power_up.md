## Start raspberry pi

- Connect the Raspberry pi to the display with a microHDMI cable.
- Connect a USB keyboard and mouse to the Raspberry pi. 
- Press and hold the button on the bottom of the Mini Pupper to  start Raspberry pi for three seconds. 
- If you can see Ubuntu running on your screen, you have succeeded. You should also see the face on Mini Pupper display. If you don’t see  both screen, then you have followed the instructions incorrectly.

## Log in

- Press ubuntu on the purple screen to log in.
- Default username : `ubuntu`
- Default password : `mangdang`
- You should change your password.  Open a terminal with `Ctrl + Alt + t` and type:

```bash
(robot): $ sudo passwd ubuntu
[sudo] password for ubuntu: 
New password: 
Retype new password: 
passwd: password updated successfully

```

## Enable wifi

Raspberry Pi comes out of the box with Wifi disabled by default. We need to edit the following configuration file:

```bash
(robot): $ sudo nano /boot/firmware/syscfg.txt
```
- Adding `#` to comment out the following line in `syscfg.txt` :  
```
  # dtoverlay=disable-wifi
```
- Reboot Ubuntu for changes to take effect.
- When you start up again, open settings and set up your wifi settings. Choose your wifi access point SSID and enter your password.
- Type `ip a`. Take note of IP address. This will be necessary when you connect to your PC via ssh. The configuration within Ubuntu is now complete.

## Wifi problems

I experienced the following issue: the Raspberry Pi connects to Wifi but and after a few seconds the connection is interrupted with the message: `Connection failed.  Activation of network connection failed`. The connection can be recovered after rebooting the R-Pi, but the issue appears again after a few seconds.

All my other devices are working fine, while the R-Pi loses Wifi both in my home network (I have a 5G router) and when using my iPhone as a router.

I looked up possible solutions and I tried a few things unsuccessfully:

* From [the official documentation](https://www.raspberrypi.com/documentation/computers/configuration.html#configuring-networking):

> Note that on Raspberry Pi devices that support the 5GHz band (Pi3B+,  Pi4, CM4, Pi400), wireless networking is disabled for regulatory  reasons, until the country code has been set. 
>
> To set the country code,  open the `Raspberry Pi Configuration` application from the Preferences Menu, select **Localisation** and set the appropriate code.

Couldn't find this Menu (I guess this is for RaspbianOS), but tried instead setting Date&Time as per Solution 5 described [here](https://appuals.com/activation-of-network-connection-failed-in-linux/) , and it didn't work:

Settings -> Date & Time -> Toggle Automatic Date & Time and set Time Zone

* Reinstalling `network-manager` as suggested [here](https://askubuntu.com/questions/1062390/activation-of-network-connection-failed-for-ethernet-connection)  didn't work either:

```bash
(robot): $ sudo apt-get install --reinstall network-manager; sudo reboot now
```

Then I read [here](https://techenclave.com/threads/wifi-activation-of-network-connection-failed.199880/) that it could be linked to the WLAN power saving feature. To [turn off WLAN power saving](https://unix.stackexchange.com/questions/269661/how-to-turn-off-wireless-power-management-permanently/315400#315400), edit the following file:

```bash
(robot): $ sudo nano /etc/NetworkManager/conf.d/default-wifi-powersave-on.conf
```

To have:

```none
[connection]
wifi.powersave = 2
```

Problem solved, but I guess this will reduce the battery life of my MiniPupper. Did anyone encounter a similar issue and how did you solve it?

## Questions

Why fan does not work with battery? ->it does work, sometimes

Can I continue to work while plugged in?

Can I plug directly the RPi?

**NOTE: should not switch off robot by pushing the power button from battery, it could destroy the SD!! Do shutdown instead from SSH or press & hold the remote triangle button.** [ThousanDIY](https://note.com/tomorrow56/n/nf83edb723a08)


## Open ports for ssh

- Attempt to make an ssh connection from your PC to Ubuntu. 
```
(computer): $ ssh ubuntu@192.168.x.x -p 22
```

- If you are unable to make an ssh connection from your PC, you  will need to configure the following, maybe.
```
sudo ufw allow 22
sudo ufw reload
```
- If firewall is not running, do the following.
```
sudo ufw enable
```

## Run headless

### Proposal by [Eric Orso](https://www.facebook.com/groups/716473723088464/posts/771385057597330/?__cft__[0]=AZXUWx7FSpUGjLoFxnyfXComw0C5jJWM_oozGYioTrHZZ_RP9yoMPcCuhF5PlcDu3KCsyuLNGBHXvzNMhxvE_FRlhmKzA01HA3vNGTzfw3PdbLNZoLkB9xZhxwt0hflHeTZIWIAuKd-gkKpZBdqXyBse2vtQLAp1Nlei573sNG-Ucjm-MNaV4h0mBD-EliwYpto&__tn__=%2CO%2CP-R) (not tried)

1. Install xRDP to use remote desktop
   This is needed for the remote desktop to be able to log into into MiniPupper with the ubuntu user credentials (otherwise ubuntu logs in locally when RPI4 is powered and the remote desktop gets stuck on a blank screen).

2. Deactivate auto login.  For this, edit the following config file using ssh:

```bash
(computer): $ ssh ubuntu@192.168.x.x -p 22
(robot): $ sudo nano /etc/gdm3/custom.conf
```

comment out with `#` the two autologin lines and save the file. 

```
# AutomaticLoginEnable=true
# AutomaticLogin=ubuntu
```

Reboot the robot for the changes to take effect:

```bash
(robot): $ sudo reboot now
```

Login manually with user:`ubuntu` pwd: `mangdang`

3. We don't have desktop icons on the remote desktop but we can run calibration from terminal.

Note that `xhost +` command is required before running the calibration script otherwise `tkinter` gets confused and cannot create the GUI because sudo is in another virtual desktop. `xhost +` disables all securities of xwindow

```bash
(robot): $ xhost +
(robot): $ sudo python3 /home/ubuntu/Robotics/QuadrupedRobot/StanfordQuadruped/calibrate_tool.py
```

With this the calibration application runs and you can move all twelve servos using sliders.

### Connecting the robot headless via Ethernet (tried-works)

To identify the IP network of the MiniPupper I followed loosely [this tutorial to scan the network (https://itsfoss.com/how-to-find-what-devices-are-connected-to-network-in-ubuntu/)

Find out the IP address range of your network. From desktop:  

```bash
(computer): $ ifconfig

...

wlp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.23  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::404f:129:49be:1e08  prefixlen 64  scopeid 0x20<link>
        ether 10:02:b5:81:25:a9  txqueuelen 1000  (Ethernet)
        RX packets 14732  bytes 15498585 (15.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 7186  bytes 1569656 (1.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


```

Look for the `inet` **192.168.0.23** and `netmask` **255.255.255.0** meaning the IP range is 192.168.0.0 - 255

Connect your robot to the router with an ethernet cable.

Scan devices connected to the network. It is better to do it using root privileges:

```bash
(computer): $ sudo nmap -sn 192.168.0.0/24
Starting Nmap 7.80 ( https://nmap.org ) at 2022-04-08 23:39 CEST
Nmap scan report for _gateway (192.168.0.1)
Host is up (0.0047s latency).
MAC Address: A4:08:F5:E5:A3:34 (Sagemcom Broadband SAS)
Nmap scan report for 192.168.0.10
Host is up (0.0076s latency).
MAC Address: A4:08:F5:E5:A3:35 (Sagemcom Broadband SAS)
Nmap scan report for 192.168.0.25
Host is up (0.070s latency).
MAC Address: B2:EA:48:66:8E:38 (Unknown)
Nmap scan report for 192.168.0.28
Host is up (0.085s latency).
MAC Address: EC:C4:0D:3C:23:9A (Nintendo)
Nmap scan report for 192.168.0.29
Host is up (0.0044s latency).
MAC Address: E4:5F:01:7F:DC:B7 (Unknown)
Nmap scan report for mhered-laptop (192.168.0.23)
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 20.10 seconds

```

You may need a little bit of detective work if you have a lot of unknown devices in your network. Compare scans with the robot connected and disconnected. Note the MAC and IP addresses for the future:

```bash
Nmap scan report for 192.168.0.29
Host is up (0.0044s latency).
MAC Address: E4:5F:01:7F:DC:B7 (Unknown)
```

If necessary try to ssh to the different candidates using `ubuntu`/`mangdang` until you find MiniPupper.

```bash
(computer): $ ssh ubuntu@192.168.0.29 -p 22
```

### Install tightVNC (tried-doesn't work)

Followed [this tutorial](https://www.youtube.com/watch?v=BIbJnnebWuY) 

Through ssh install tightVNC server and launch the server. Define a password (8 characters). Note the desktop: **ubuntu-desktop:1**

```bash
(robot): $ sudo apt-get install tightvncserver
(robot): $ tightvncserver
You will require a password to access your desktops.

Password: 
Warning: password truncated to the length of 8.
Verify:   
Would you like to enter a view-only password (y/n)? n
xauth:  file /home/ubuntu/.Xauthority does not exist

New 'X' desktop is ubuntu-desktop:1

Creating default startup script /home/ubuntu/.vnc/xstartup
Starting applications specified in /home/ubuntu/.vnc/xstartup
Log file is /home/ubuntu/.vnc/ubuntu-desktop:1.log

```

Note: use `$ vncpasswd`if you need to change this password in the future. I used `minipupp`

In the computer install the viewer and remmina:

```bash
(computer): $ sudo apt-get install xtightvncviewer
(computer): $ sudo apt-get install remmina
```

Launch remmina and create a VNC connection as described in [this video](https://www.youtube.com/watch?v=BIbJnnebWuY): **does not work** I get a grey screen

### Install and configure VNC on Ubuntu 20.04 (tried-works partially)

Followed [this tutorial](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-20-04) and it works!

Install software and execute VNC to perform the initial setup. Enter a 6-8 character VNC password. Then stop the VNC server instance in order to modify the configuration.

```
(robot): $ sudo apt install xfce4 xfce4-goodies
(robot): $ sudo apt install tightvncserver
(robot): $ vncserver
You will require a password to access your desktops.

Password:
Verify:

(robot): $ vncserver -kill :1
```

Backup `~/.vnc/xstartup` and make a new file, save it and make it executable

```bash
(robot): $ mv ~/.vnc/xstartup ~/.vnc/xstartup.bak
(robot): $ nano ~/.vnc/xstartup
```

```bash
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &
```

```
(robot): $ chmod +x ~/.vnc/xstartup
```

4 steps to connect in the future: 

1) ssh into the robot 
2) launch VNC server in the robot
3) ssh into the robot with port forwarding
4) launch Remmina from computer

```
(computer-Terminal1): $ ssh ubuntu@192.168.0.29 -p 22
(robot): $ vncserver -localhost

New 'X' desktop is ubuntu-desktop:1

Starting applications specified in /home/ubuntu/.vnc/xstartup
Log file is /home/ubuntu/.vnc/ubuntu-desktop:1.log

(computer-Terminal2): $ ssh -L 59000:localhost:5901 -C -N -l ubuntu 192.168.0.29
```

In Remmina open `localhost:59000` and authenticate with the pwd created in 

The tutorial includes steps to create a service and avoid steps 1 and 2.

Next step: activate Wifi. cfr this Q&A: https://askubuntu.com/questions/461825/how-to-connect-to-wifi-from-the-command-line/461831#461831

Stuck at error: A 'wireless' setting is required if no AP path was given.

Potential solution here: https://linuxhint.com/wi-fi_connecting_issues_raspberry_pi/

**New problem: cannot launch terminal or the python script**

Not checked, may be useful: https://www.makeuseof.com/tag/ubuntu-remote-desktop-builtin-vnc-compatible-dead-easy/

**Not tried recommended by mahjsa in Discord: (https://forums.raspberrypi.com/viewtopic.php?t=288769)**

## Create an alias

### Basic method to rename robot to remotely ssh

See [here](https://askubuntu.com/questions/487310/how-to-give-user-server-name-instead-of-ip-when-connecting-through-ssh)  

If you need access from only a single location - or a few - edit `/etc/hosts` in each of the computers that need to connect to the robot

```
(computer): $ sudo nano /etc/hosts
```

to add an entry for your robot

```
192.168.XX.XX mybot
```

Then connect with 

```
(computer): $ ssh username@mybot
```

Troubleshoot with `ssh -vvv mybot`

**Note: need to reboot?**

### A better method

According to [this](https://askubuntu.com/questions/257472/host-alias-for-ssh) it is better to have configuration files in the home directory to avoid having to be root to edit files, and also they can be configured in a way that other users have no access to this data. In addition this configuration will  be backed up with our home and also will "survive" an OS upgrade. To make a ssh configuration file:

```
(computer): $ nano ~/.ssh/config
```

 A simple entry may have the following content (remove the comments!):

```
Host mybot                # any name for the host
HostName 192.168.XX.XX    # IP, .local, or hostname if defined
User username             # your username
Port 22                   # port to listen
```

More details in the [ssh config man pages](http://manpages.ubuntu.com/manpages/jammy/en/man5/ssh_config.5.html) 

Then connect with:

```
(computer): $ ssh mybot
```

### Change hostname in the robot

See [here](https://itsfoss.com/change-hostname-ubuntu/)

```
(robot): $ sudo nano /etc/hostname
```

 You’ll only see one line that contains your system’s  hostname. Change it to the hostname you want and save the file.

You must also edit the /etc/hosts file in the similar fashion and replace all the occurrences of your old hostname with the new hostname.

```
(robot): $ sudo nano /etc/hosts
```

This is important otherwise you’ll start seeing “[sudo: unable to resolve host](https://linuxhandbook.com/sudo-unable-resolve-host/)” error.

## Calibrate

See [this Facebok post](https://www.facebook.com/groups/519009562699751/posts/634109341189772/?__cft__[0]=AZVORzic1LLnJS18v2zLCehiMn8FENKTnE4McS8zGoYvtBAiTg6Dekp6ScgxM09GRvWHQqcIS-z5SRtwkOs276ifPkWx-DqrC141TROgSCAOSzzUYcbezMBiDoSD8eFEUJsC2d42hSAyweE3e7ACwvq0a6XqETE3o1EGlvM6q_3J-8CS9rAce6AIGHjVfk3KgE8&__tn__=%2CO%2CP-R)

