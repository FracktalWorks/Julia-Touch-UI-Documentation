# Julia Touch UI Documentation
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


To test out working, use the following to start a PyQt5 program with ```sudo startx```
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

### Installing Octoprint

Derived from  [this](https://community.octoprint.org/t/setting-up-octoprint-on-a-raspberry-pi-running-raspbian-or-raspberry-pi-os/2337) link for detailed instruction installing Octoprint
Fracktals Version of Octoprint: https://github.com/FracktalWorks/OctoPrint/

####Basic Installation
For the basic package you'll need Python 3.6 or newer (should be installed by default) and pip.
Make sure you are using the correct version - it is probably be installed as python3, not python. To check:

```python3 --version```
Installing OctoPrint should be done within a virtual environment, rather than an OS wide install, to help prevent dependency conflicts. To setup Python, dependencies and the virtual environment, run:
```
cd ~
sudo apt update
sudo apt install python3-pip python3-dev python3-setuptools python3-venv git libyaml-dev build-essential
mkdir OctoPrint && cd OctoPrint
python3 -m venv venv
source venv/bin/activate
```


#### Install from Git Repo:

```pip install wheel```

```pip install https://github.com/FracktalWorks/OctoPrint/archive/refs/tags/1.7.31.zip```


### Autologin
-This is primarily for the TouchUI App
https://community.octoprint.org/t/how-to-set-up-octoprint-to-autologin-a-single-user-when-connecting-from-the-internal-network/26235

See "The Hard Way"
```config.yaml``` config:
```accessControl:
  salt: 1jjovY7wxfFi7NrEuFv6eTAXgmyvECPc
  autologinLocal: true
  autologinAs: admin
  localNetworks:
  - 127.0.0.0/8
  - 192.168.0.0/24
  - 192.168.1.0/24
```

#### Automatic Octoprint Startup

Download the init script files from OctoPrint's repository, move them to their respective folders and make the init script executable:

```wget https://github.com/OctoPrint/OctoPrint/raw/master/scripts/octoprint.service && sudo mv octoprint.service /etc/systemd/system/octoprint.service```

Adjust the paths to your octoprint binary in `````/etc/systemd/system/octoprint.service`````. If you set it up in a virtualenv as described above make sure your `````/etc/systemd/system/octoprint.service````` looks like this:

```ExecStart=/home/pi/OctoPrint/venv/bin/octoprint```

Then add the script to autostart using
 
```sudo systemctl enable octoprint.service```

This will also allow you to start/stop/restart the OctoPrint daemon via

```sudo service octoprint {start|stop|restart}```


### Changing the boot screen logo
Refeance : [link](https://raspberrypi.stackexchange.com/questions/100371/raspbian-buster-lite-splash-screen-instead-of-boot-messages-on-pi-3-model-b-a02)


The pi boots with a rainbow colour and many messages will be displayed during boot up.
to disable the boot message go to 
```sudo nano /boot/cmdline.txt```

in that file 
Add ```loglevel=3``` to disable non-critical kernel log messages.
Add ```logo.nologo``` to the end of the line to remove the Raspberry PI logos from displaying

now messages will not be shown 

### To add our logo 
install fbi
```apt-get install fbi```

Create the file ```sudo nano /etc/systemd/system/splashscreen.service``` with the following content:

```
[Unit] 
Description=Splash screen 
DefaultDependencies=no 
After=local-fs.target 

[Service] 
ExecStart=/usr/bin/fbi -d /dev/fb0 --noverbose -a /opt/splash.png 
StandardInput=tty 
StandardOutput=tty 
[Install] 
WantedBy=sysinit.target
```
Replace `````/opt/splash.png````` with the path to the splash screen image as appropriate.

Enable the service to be run at boot by running as root:

```systemctl enable splashscreen```

#### Webcam

Reference: https://community.octoprint.org/t/setting-up-octoprint-on-a-raspberry-pi-running-raspbian-or-raspberry-pi-os/2337

#### Haproxy

The OctoPrint works on the ```:5000``` protocol which means ```http://192:168.0.24:5000``` will open the octoprint UI page. Now to access the OctoPrint on ```8080``` we have to install haproxy.

We will install Haproxy 1.8

```sudo apt install haproxy=1.8.\*```
After installing just verify the version of haproxy

```haproxy -v```
Now we have to configure the haproxy to open in ```:8080``` protocol.
Open configuration file ```sudo nano /etc/haproxy/haproxy.cfg```


#### Now The pi boots up (properly)
The pi boot's up but it goes to the lock screen (That feature has to be disabled)
The change's had been made in the summit in the given link (https://github.com/FracktalWorks/JuliaTouchUI/commit/69d13ea66483670c42d59ddecc72bd41d18a289f) follow this and startx again the pi will boot to home screen this time.
