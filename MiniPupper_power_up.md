## Start raspberry pi

- Connect the Raspberry pi to the display with a microHDMI cable.
- Connect a USB keyboard and mouse to the Raspberry pi. 
- Press and hold the button on the bottom of the Mini Pupper to  start Raspberry pi for three seconds. 
- If you can see Ubuntu running on your screen, you have succeeded. You should also see the face on Mini Pupper display. If you don’t see  both screen, then you have followed the instructions incorrectly.

## Log in

- Press ubuntu on the purple screen to log in.
- Default username : `ubuntu`
- Default password : `mangdang`
- You should change your password.

## Enable wifi

- Press the menu button at the bottom left and type terminal to  start the terminal. In the terminal, open the configuration file. 

```bash
$ sudo nano /boot/firmware/syscfg.txt
```
- Type # to comment out the line disabling wifi on `syscfg.txt` 
```
  # dtoverlay=disable-wifi
```
- Reboot Ubuntu for changes to take effect.
- When you start up again, open settings and set up your wifi  settings. Choose your wifi access point SSID and enter your password. 
- Type `ip a`. See and make a note of IP address. This will be  necessary when you connect to your PC via ssh. The configuration within  Ubuntu is now complete.

## Wifi problems

RPi connects to Wifi and after a few seconds connection is interrupted with the message: `Connection failed.  Activation of network connection failed.`

It is recovered after rebooting the RPi. Happens with the main router and also with a mobile connection. All other devices work fine.

Possible solutions:

* From [the official documentation](https://www.raspberrypi.com/documentation/computers/configuration.html#configuring-networking):

> Note that on Raspberry Pi devices that support the 5GHz band (Pi3B+,  Pi4, CM4, Pi400), wireless networking is disabled for regulatory  reasons, until the country code has been set. 
>
> To set the country code,  open the `Raspberry Pi Configuration` application from the Preferences Menu, select **Localisation** and set the appropriate code.

Couldn't find this option, and setting Date&Time as per Solution 5 described [here](https://appuals.com/activation-of-network-connection-failed-in-linux/)  didn't work:

Settings -> Date & Time -> Toggle Automatic Date & Time and set Time Zone

* Reinstalling `network-manager` as suggested [here](https://askubuntu.com/questions/1062390/activation-of-network-connection-failed-for-ethernet-connection)  didn't work either:

```bash
$ sudo apt-get install --reinstall network-manager ; sudo reboot now
```

* [turning off WLAN power saving](https://unix.stackexchange.com/questions/269661/how-to-turn-off-wireless-power-management-permanently/315400#315400) as  suggested [here](https://techenclave.com/threads/wifi-activation-of-network-connection-failed.199880/)

```bash
$ sudo nano /etc/NetworkManager/conf.d/default-wifi-powersave-on.conf
```

??

## Questions

Why fan does not work with battery? ->it does work, sometimes

Can I continue to work while plugged in?

Can I plug directly the RPi?

**NOTE: should not switch off robot by pusing the power button from battery, it could destroy thr SD!! Do shutdown instead from SSH or press & hold the remote triangle button.** [ThousanDIY](https://note.com/tomorrow56/n/nf83edb723a08)


## Open ports for ssh

- Attempt to make an ssh connection from your PC to Ubuntu. 
```
ssh ubuntu@192.168.x.x -p 22
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

## run headless
[Eric Orso](https://www.facebook.com/groups/716473723088464/posts/771385057597330/?__cft__[0]=AZXUWx7FSpUGjLoFxnyfXComw0C5jJWM_oozGYioTrHZZ_RP9yoMPcCuhF5PlcDu3KCsyuLNGBHXvzNMhxvE_FRlhmKzA01HA3vNGTzfw3PdbLNZoLkB9xZhxwt0hflHeTZIWIAuKd-gkKpZBdqXyBse2vtQLAp1Nlei573sNG-Ucjm-MNaV4h0mBD-EliwYpto&__tn__=%2CO%2CP-R) 
I want to do an headless calibration, by remoting into MiniPupper
I have no problem SSH through ethernet, I would have no problems setting up the WiFi.
I installed xRDP to use remote desktop, but it results in a black screen. It has something to do with the ubuntu user automatically logging in, and remote clients being junky. I'm trying to remove the automatic login/adding users, to no avail for now.
Was someone able to remote into MiniPupper desktop?
SOLUTION:

* edit config file using ssh 

```bash
ubuntu@ubuntu-desktop:~$ sudo nano /etc/gdm3/custom.conf
```

Then comment out with # the two autologin lines:

```
# AutomaticLoginEnable=true
# AutomaticLogin=ubuntu
```

```bash
ubuntu@ubuntu-desktop:~$ sudo reboot now
```

now remote desktop works using "ubuntu" "mangdang" user
to run calibration run inside a console on the remote desktop 
"xhost +"
"sudo python3 /home/ubuntu/Robotics/QuadrupedRobot/StanfordQuadruped/calibrate_tool.py"
The calibration application runs and I can move all twelve servos using sliders.



I found a way that works with Ubuntu 21.10. There is a configuration file that can be edited from ssh that disables the autologin. Reboot. Then  remote desktop is able to get into MiniPupper with the ubuntu user  credentials. But I don't have desktop icons.Next snag is that the calibration script cannot create a windows because tkinter gets confused.

first install xRDP

 then edit the configuration file /etc/gdm3/custom.confcomment 

with # the AutomaticLoginEnable and AutomaticLogin

this way ubuntu doesn't log in locally when RPI4 is powered, and I can log  in from remote desktop  with "ubuntu" user and not get a black screen."xhost +" command is required before running "sudo python3  /home/ubuntu/Robotics/QuadrupedRobot/StanfordQuadruped/calibrate_tool.py" otherwise tkinter gets confused because sudo is in another virtual  desktop, and xhost disables all securities of xwindow (as far as i  understand)

## Calibrate

See [this Facebok post](https://www.facebook.com/groups/519009562699751/posts/634109341189772/?__cft__[0]=AZVORzic1LLnJS18v2zLCehiMn8FENKTnE4McS8zGoYvtBAiTg6Dekp6ScgxM09GRvWHQqcIS-z5SRtwkOs276ifPkWx-DqrC141TROgSCAOSzzUYcbezMBiDoSD8eFEUJsC2d42hSAyweE3e7ACwvq0a6XqETE3o1EGlvM6q_3J-8CS9rAce6AIGHjVfk3KgE8&__tn__=%2CO%2CP-R)