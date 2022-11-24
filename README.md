# Julia Touch UI Documentation
Touch UI Octoprint >1.5 Plugin for Julia 2022 Extended based on Python3 and PyQt5 

# Development Documentation:
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

PyQt5: ```sudo apt-get install qt5-default pyqt5-dev pyqt5-dev-tools```

PyQT5: ```sudo apt install python3-pyqt5```

Git: ```sudo apt-get install git```

### Install 3.5inch touch screen driver

Reference: [link](https://www.waveshare.com/wiki/3.5inch_RPi_LCD_(A))

run: 
```
git clone https://github.com/waveshare/LCD-show.git
cd LCD-show
ls
chmod +x LCD35-show
./LCD35-show
```


### Install Xwindows

```sudo apt-get install xserver-xorg-video-fbdev```

```sudo apt-get install xserver-xorg```

```sudo apt-get install xinit```

```sudo apt-get install x11-xserver-utils```


```sudo mv /usr/share/X11/xorg.conf.d/99-fbturbo.conf ~```  see [link](https://learn.adafruit.com/adafruit-pitft-3-dot-5-touch-screen-for-raspberry-pi/faq) for more information on this step


To test out working, use the following to start a PyQt5 program with ```sudo startx```
Load ```SampleProgram``` from this repository into root ```home/pi``` by using ```sudo nano /etc/X11/xinit/xinitrc```

```#!/bin/sh

# /etc/X11/xinit/xinitrc
#
# global xinitrc file, used by all X sessions started by xinit (startx)
cd /home/pi/SampleProgram
sudo chmod +x main.py
sudo python3 main.py
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

to exit venv (to install other things under pi user):
```deactivate```

Update pip version inside octoprint:

```
cd OctoPrint
source venv/bin/activate
pip install --upgrade pip
```
### Update API key & Config.yaml


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

Adjust the paths to your octoprint binary  ```sudo nano /etc/systemd/system/octoprint.service```. If you set it up in a virtualenv as described above make sure your ```/etc/systemd/system/octoprint.service``` looks like this:

```ExecStart=/home/pi/OctoPrint/venv/bin/octoprint```

Then add the script to autostart using
 
```sudo systemctl enable octoprint.service```

This will also allow you to start/stop/restart the OctoPrint daemon via

```sudo service octoprint {start|stop|restart}```

### Removing boot messages

```sudo nano /boot/cmdline.txt```
change ```console=tty1``` to ```console=tty3``` 
Add ```loglevel=3``` to disable non-critical kernel log messages.
Add ```logo.nologo``` to the end of the line to remove the Raspberry PI logos from displaying
Press CTRL+X to exit and Y to save your changes.


### Changing the boot screen logo



Refeance : [link](https://www.hackster.io/kamaluddinkhan/changing-the-splash-screen-on-your-raspberry-pi-7aee31)
[link](https://raspberrypi.stackexchange.com/questions/100371/raspbian-buster-lite-splash-screen-instead-of-boot-messages-on-pi-3-model-b-a02)

The pi boots with a rainbow colour and many messages will be displayed during boot up.
to disable the boot message go to 
```sudo nano /boot/cmdline.txt```

in that file 
Add ```loglevel=3``` to disable non-critical kernel log messages.
Add ```logo.nologo``` to the end of the line to remove the Raspberry PI logos from displaying

now messages will not be shown 

### To add our logo 

download logo from this repository and transfer to root folder of pi

install fbi
```sudo apt-get install fbi```

Create the file ```sudo nano /etc/systemd/system/splashscreen.service``` with the following content:

```
[Unit] 
Description=Splash screen 
DefaultDependencies=no 
After=local-fs.target 

[Service] 
ExecStart=/usr/bin/fbi -d /dev/fb1 --noverbose -a /home/pi/splash.png >/dev/null 2>&1
StandardInput=tty 
StandardOutput=tty 
[Install] 
WantedBy=sysinit.target

```
Replace `````/pi/splash.png````` with the path to the splash screen image as appropriate. You might need to change fb1 to fb0 depending on the framebuffer the display is installed on

Enable the service to be run at boot by running as root:

```sudo systemctl enable splashscreen```

#### Webcam

Reference: https://community.octoprint.org/t/setting-up-octoprint-on-a-raspberry-pi-running-raspbian-or-raspberry-pi-os/2337

do a ```sudo chmod +x /home/pi/scripts/webcamDaemon``` of ths script from the link above

also enable the camera interface on raspi-config

#### Haproxy

The OctoPrint works on the ```:5000``` protocol which means ```http://192:168.0.24:5000``` will open the octoprint UI page. Now to access the OctoPrint on ```8080``` we have to install haproxy.

We will install Haproxy 1.8

```sudo apt install haproxy=1.8.\*```
After installing just verify the version of haproxy

```haproxy -v```
Now we have to configure the haproxy to open in ```:8080``` protocol.
Open configuration file type ```sudo nano /etc/haproxy/haproxy.cfg``` in terminal.
There will be some code in it replace it with our 
```
global
        maxconn 4096
        user haproxy
        group haproxy
        daemon
        log 127.0.0.1 local0 debug

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        retries 3
        option redispatch
        option http-server-close
        option forwardfor
        maxconn 2000
        timeout connect 5s
        timeout client  15min
        timeout server  15min

frontend public
        bind :::80 v4v6
        use_backend webcam if { path_beg /webcam/ }
        default_backend octoprint

backend octoprint
        reqrep ^([^\ :]*)\ /(.*)     \1\ /\2
        option forwardfor
        server octoprint1 127.0.0.1:5000

backend webcam
        reqrep ^([^\ :]*)\ /webcam/(.*)     \1\ /\2
        server webcam1  127.0.0.1:8080
```
If this dosent work then do copy from this link (https://gist.github.com/HarlemSquirrel/458dd9f8dfda5330d6cc622738f3da5e).
Now the Octoprint will work directly from ip address ```http://192.168.0.24``` just enter the Pi's ip address on the url section of webpage it will open Octoprint.

#### Touch UI
Touch UI can be installed in octoprint only. Open Settings and in that scroll to Plugin manager, after that click on Get More and in that page scroll down till you find ```...from URL```
Paste the (https://github.com/FracktalWorks/JuliaTouchUI/archive/refs/tags/0.0.1.zip)
Refference (https://github.com/FracktalWorks/JuliaTouchUI)

install dependencies under ```sudo pip3```

install qrcode:
```sudo pip3 install qrcode```

install websocket:
```sudo pip3 install websocket-client```

#### Configure Startx (Xwindow manager which helps to show GUI on Raspbian os lite)

```sudo nano /etc/rc.local```
Add “startx” just before exit 0
```sudo nano /etc/X11/xinit/xinitrc```
Here you have to add the location of touch ui where it is saved and file name
Example 
```

#!/bin/sh

# /etc/X11/xinit/xinitrc
#
# global xinitrc file, used by all X sessions started by xinit (startx)
cd /home/pi/OctoPrint/venv/lib/python3.7/site-packages/octoprint_JuliaTouchUI/
sudo chmod +x Main.py
sudo python3 Main.py
# invoke global X session script
. /etc/X11/Xsession


```
Now when you do startx the GUI will boot up

#### Now The pi boots up (properly)

The pi boot's up but it goes to the lock screen (That feature has to be disabled)
The change's had been made in the summit in the given link (https://github.com/FracktalWorks/JuliaTouchUI/commit/69d13ea66483670c42d59ddecc72bd41d18a289f) follow this and startx again the pi will boot to home screen this time.

#### Enable CORS

Octoprint>API>CORS


#### Other Settings

in ```sudo raspi-config```

- Do not wait for network on startup
- change defualt password
- Enable screen blanking




#### Install USB Automount
Referance: https://github.com/rbrito/usbmount/issues/25

```wget https://github.com/nicokaiser/usbmount/releases/download/0.0.24/usbmount_0.0.24_all.deb```
You can install it via ```sudo dpkg -i usbmount_0.0.24_all.deb``` after downloading. This updates the Debian/Raspbian version (0.0.22) to the current GitHub master (0.0.24).

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
start_x=1
gpu_mem=128
```


## Minimising image size

to reduce size of images read from win32diskimager

https://www.youtube.com/watch?v=77vOeofG0Kg

### Make sure Firmware updater is configured

https://github.com/OctoPrint/OctoPrint-FirmwareUpdater

### Upload anuthing added to allow PNG Upload to Octoprint

https://github.com/rlogiacco/UploadAnything

### Rotate Display:

https://howchoo.com/pi/raspberry-pi-display-rotation

see [link](https://github.com/FracktalWorks/JuliaAdvanced2022TouchUI/commit/399ea48d66bdc058930aa2c69b8ae00a3be3b2a7) for more information

### Touch Screen Calibration

Referance: [link](https://robu.in/how-to-calibrate-raspberry-pi-touch-screen/)
installed xinput_calibrator and other tools using:
```sudo apt install libts-bin```

created ```sudo nano /home/pi/setenv.sh``` with the following data:
change framebuffer ```fb0``` to what you are using

```
#! /bin/bash
export TSLIB_FBDEVICE=/dev/fb0
export TSLIB_TSDEVICE=/dev/input/event0
ts_calibrate
#exit
```
make it executable
```sudo chmod +x /home/pi/setenv.sh```


### Dissable  bluetooth to make Pi faster

https://di-marco.net/blog/it/2020-04-18-tips-disabling_bluetooth_on_raspberry_pi/


# Python3_PyQt5_dev

# Development Setup:

## Initial setup to run on Windows:
https://www.youtube.com/watch?v=Vde5SH8e1OQ

Text based tutuorial : https://www.techwithtim.net/tutorials/pyqt5-tutorial/basic-gui-application/
Setting up with Pycharm: https://pythonpyqt.com/how-to-install-pyqt5-in-pycharm/


## Differences between PyQt5 and PyQt4:
https://docs.huihoo.com/pyqt/PyQt5/pyqt4_differences.html#:~:text=PyQt5%20exposes%20only%20the%20signal,QObject%20(i.e.%20in%20mixins).

## Changes to the code being made:

### Changing clicable line edit:
https://stackoverflow.com/questions/35047349/pyqt-5-how-to-make-qlineedit-clickable

### Changing Signal and Slot mechanism:
https://stackoverflow.com/questions/53948455/pyqt5-how-to-connect-emit
https://doc.bccnsoft.com/docs/PyQt5/signals_slots.html

Proper way of using signals : https://www.programmersought.com/article/38055016039/
https://zetcode.com/gui/pyqt5/eventssignals/#:~:text=PyQt5%20emitting%20signals&text=We%20create%20a%20new%20signal,()%20slot%20of%20the%20QMainWindow%20.&text=A%20signal%20is%20created%20with,of%20the%20external%20Communicate%20class.

### Threading, Signals & Slots
https://wiki.python.org/moin/PyQt5/Threading%2C_Signals_and_Slots#:~:text=We%20connect%20the%20standard%20finished,a%20new%20star%20is%20drawn.

It is possible to pass any Python object as a signal argument by specifying PyQt_PyObject as the type of the argument in the signature. For example:

`finished = pyqtSignal('PyQt_PyObject')`
This would normally be used for passing objects where the actual Python type isn’t known. It can also be used to pass an integer, for example, so that the normal conversions from a Python object to a C++ integer and back again are not required.

The reference count of the object being passed is maintained automatically. There is no need for the emitter of a signal to keep a reference to the object after the call to finished.emit(), even if a connection is queued. 


### Websocket related changes

  Need to authenticate to keep recieving websocket data

  Client Code: https://artlist.io/song/57212/anthem
  Documentation: https://docs.octoprint.org/en/master/api/push.html


------------------------------------------------------------------------------------------------------

# TouchUI Architecture
==============================

## Functional Block Diagram

![alt-text](https://github.com/FracktalWorks/Julia2018ProDualABLTouchUI/blob/master/documentation/basic_blockdiagram.JPG?raw=true "Firmware Functional BLock Diagram")

* `MainWindow` is the object of `MainUiClass` class and this is the workhorse of the firmware where all the slots and event definitions are defined within this class. 
* `mainGUI` is inherited by the `MainUiClass` class.
*  `mainGUI` is the generated code from the `mainGUI.ui` file which was made in Qt Designer and then converted into python code using PyQt5 UI code generator.
* `ThreadSanityCheck` checks for the availability of the server which runs on the Raspberry Pi and when available, establishes a connection with the MKS. It's initialized as `SanityCheck` in the `MainUiClass`.
*  Octoprint provides the web interface and `OctoprintAPI`, along with `REST API` and `Websockets` establishes a communication between the user and the server through HTTP and TCP protocols respectively. 
* `QtWebsocket` is the QT Thread that takes care of setting up slots and signals and it is initialized as `QtSocket` in `MainUiClass`.
* `Optopiclient` is the object of OctoprintAPI and is initialised as global in the `ThreadSanityCheck` class.
*  `OctoprintAPI` class has functions that handle the connection to the printer through a serial interface.
* `ThreadFileUpload` is the thread that has functions to upload the Gcode file to the server and is initialised as `uploadThread` in `MainUi_Class`.
* `Image` class creates a respective QR code that enables to open the web-interface on a mobile phone.



# To compile .ui to .py:
 pyuic5 .\mainGUI.ui -o .\mainGUI.py
 
# To change the TouchUI of the Raspberry Pi display
 The following are the steps to enable the `TouchUI` in the `Raspberry Pi displays`:
 * Connect the `Raspberry Pi 4` and the `Display` and connecting it to the power supply using its respective USB cables.
 * Download the `Bitvise SSH Client` software, and open it. After opening, log into the IP address of the Raspberry Pi and the display that is being used, using the local server.  
 * After logging in, access the Remote files of the Raspberry Pi, and then follow the path ```/etc/X11/xinit```.
 * In the command prompt terminal of the Raspberry Pi, type in ```sudo nano /etc/X11/xinit/xinitrc```.
 * The list of files that are present will be displayed in the process. Replace the file named with the prefix `Octoprint_...` with name of the file that is to be replaced onto. 
 * After the above changes, in the command prompt terminal, type in ```sudo reboot``` to reflect the changes in the Raspberry Pi display.

# How to Shrink an image of Raspberry Pi using PiShrink
The following are the steps to shrink any image of `large size` into a `very small size` using the `PiShrink` feature:
* Download the `Ubuntu` OS from the browser or the `App Store` such as the `Microsoft Store`. 
* Open the `Command Prompt`. Type in: ```wsl --install```.
* In the next step, type ```ubuntu```. This is install all the files and installation packages in the local linux system server.
* In the next step, type in ```cd /```.
* Next step, type in ```ls```. This will get the list of all the files present in the Ubuntu OS directory.
* After the above steps, type in ```explorer.exe```. This will access the `Windows files` using the `Ubuntu OS`.
* Now, in order to install the PiShrink feature into the Ubuntu OS, enter the following commands in the `command prompt`
```
wget https://raw.githubusercontent.com/Drewsif/PiShrink/master/pishrink.sh
chmod +x pishrink.sh
sudo mv pishrink.sh /usr/local/bin
```
* After the above steps, type in ```cd /```.
* Then type ```ls```.
* Then type in ```cd mnt```.
* We have successfully installed the PiShrink feature into the OS. Now in the following steps below, we need to determine the location of the image file that is to be shrinked.
* Type in ```/mnt# cd (location path)```. Remember that, while mentioning the path of the file, the name of the `drive` should be in small letter. Eg., `c`. Remove the `colons(:)` from the path and use `/` instead of `\`.
* After the above step, type ```mnt/c/(location path) #ls```
* Then type in the following commands below:
```
wget https://raw.githubusercontent.com/Drewsif/PiShrink/master/pishrink.sh
chmod +x pishrink.sh
sudo mv pishrink.sh /usr/local/bin
```
* After the above process, type in ```sudo pishrink.sh img```.
* Finally, the large image file is replaced with the shrinked image, roughly the size of `3GB` from any large size as big as `60GB`.
