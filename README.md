# Julia2022ExtendedTouchUI
Touch UI Octoprint >1.5 Plugin for Julia 2022 Extended based on Python3 and PyQt5 

#Development Documentation:
## Setting up Raspberry Pi:
### Writing Image: 
[Raspbian Buster Lite](https://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2020-02-14/)

[Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/files/latest/download)

[SD Card Formatter](https://www.sdcard.org/downloads/formatter/sd-memory-card-formatter-for-windows-download/)

Clean with disk with SD card formatter selecting Quick Format and write Image to SD Card

### Setting up Pi
Add Wireless Settings & Enable SSH:
[link](https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html)

```
country=IN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
scan_ssid=1
ssid="Fracktal 5"
psk="******"
}
```
WPA Supplicant known to work

### Installing dependencies
run ```sudo apt-get update --allow-releaseinfo-change``` in case of issues installing

pip3: ```sudo apt-get install python3-pip```
OPtional: sudo apt-get install qt5-default pyqt5-dev pyqt5-dev-tools

PyQT5: ```sudo apt install python3-pyqt5```

Git: ```sudo apt-get install git```

### Install 3.5inch touch screen driver

clone repo: ```git  clone https://github.com/Smrazatech/Raspbian-LCD35```

run: 
```git clone https://github.com/waveshare/LCD-show.git
cd LCD-show
ls
chmod +x LCD35-show
./LCD35-show
```

### To make LCD Faster (Issue with ili based screens)

Changed ```sudo nano /boot/config.txt``` bottom to:

```
# Enable audio (loads snd_bcm2835)
dtparam=audio=on
dtoverlay=waveshare35a,fps=12,speed=16000000
#dtoverlay=ads7846,cs=1,penirq=17,penirq_pull=2,speed=1000000,keep_vref_on=1,swapxy=1,pmax=255,xohm$
hdmi_force_hotplug=1
#max_usb_current=1
hdmi_group=2
#hdmi_mode=1
hdmi_mode=87
hdmi_cvt 480 320 12 6 0 0 0
hdmi_drive=2
```
### Install Xwindows
```sudo apt-get install xserver-xorg-video-fbdev```

```sudo apt-get install xserver-xorg```

```sudo apt-get install xinit```

```sudo apt-get install x11-xserver-utils```

```sudo mv /usr/share/X11/xorg.conf.d/99-fbturbo.conf ```



```#!/bin/sh

# /etc/X11/xinit/xinitrc
#
# global xinitrc file, used by all X sessions started by xinit (startx)
cd /home/pi/Julia2018Octoprint/venv/lib/python2.7/site-packages/octoprint_Julia2018ProDualABLTouchUI
sudo chmod +x Main.py
sudo python Main.py
# invoke global X session script
. /etc/X11/Xsession
```




