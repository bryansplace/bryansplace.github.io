---
layout: page
title: Welcome to Bryan's Place
permalink: /
---

## Welcome

## Repurposing old smartphones

Welcome to my corner of the web. I like to breath new life into "obsolete" phones. Specifically turning them into functional, low-power homelabs. 

### PostmarketOS
After looking at loads of alternatives, I homed in on PostmarketOS. 
The big plus for me is its ability to run Docker.

It has an installation program, Pmbootstrap, that makes things real simple ( no rooting). 

I have two Xiaomi A1 devices that have been running for over three years with no problems.

The aim of this site is to supplement the excellent PostmarketOS (pmos) documentation which contains a tremendous amount of information by focusing on my real world experiences relating to one model.

The key steps are
- [Install Pmbootstrap on PC](#install-pmbootstrap)
- [Connect the phone](#connect-phone-to-pc)
- [PostmarketOS; initialise options](#initialisation)
- [PostmarketOS; install options](pmbootstrap_install.md)
- [Flash new system](pmbootstrap_flasher.md)
- [Setup your network WIFI](setup_wifi.md)

This gives you a functioning server.
I go on to install and use:
- [Reduce battery voltage](https://github.com/bryansplace/PostmarketOS-limit-battery-voltage/blob/main/README.md)
- [Docker](docker.md)
- [Firewall](firewall.md)
- [Home Assistant](hass.md)
- [Bluetooth](bluetooth.md)
- Jupyter
- Connect to NAS storage
- [Tailscale](tailscale.md)

### Install PMbootstrap

You will need a linux based system. 

The official installation instructions can be found [here](https://wiki.postmarketos.org/wiki/Installing_pmbootstrap). 

Unfortunately, there are are alternatives, which as a beginner, confused me more than it should. I personally found [using pip](https://wiki.postmarketos.org/wiki/Pmbootstrap/Installation#Using_pip) simplest.

To be honest, I found installing PMbootstrap to be the most difficult part of the whole process; but it is worth it as it really makes installing linux on you old phone easy.

Whatever, in the end...To test that pmbootstrap is installed correctly, run:

    pmbootstrap --version
    
    

### Connect phone to PC

To connect the phone, four things need to be done;

enable USB debugging on the phone,
use a good USB cable,
put the phone into flash mode, and
unlock the phone.

#### USB debugging.

In order to use a USB cable to communicate between the Android phone and the computer, something called 'USB debugging' needs to be enabled on the phone.

A Google search ( eg ' Xiaomi A1 enable USB debugging' ) will give lots of instructions and videos if needed.

The procedure is generally:
Go to the phone Settings -> About -> Tap build number repeatedly until developer menu is enabled. Then tap on “ developer options ” and tick USB debugging.

#### USB cable 

I used to think all USB cables were more or less the same. But experience taught me that they are finiky and a source of problems. A cable might work sometimes, but not other times. If things in the subsequent steps don't go right; try another cable and/or USB port.

#### Flash mode

Thr phone needs to be put into 'flash mode'.

Generally, the PMOS device page will tell you how to do this, eg
https://wiki.postmarketos.org/wiki/Xiaomi_Mi_A1_(xiaomi-tissot)#How_to_enter_flash_mode

 
Power on the device with both Power and Volume Down buttons down
 

OEM unlock %HEADING%oem-unlock%HEADING%

Plug in the USB cable between PC and phone, then on the PC run:

 
$ sudo fastboot oem unlock
 

When prompted on the the phone device, use Volume Up/Down buttons to highlight 'Yes' choice, then press the Power button to select it.

### Initialisation

The initialisation sets up PMbootstrap with you phone make and model together with any options, eg additional software.

Your device might have a pre-built image available. There is one for my Xiaomi Tissot, but I've never used it.

I want just a basic alpine server with the minimum of stuff installed. So we use the route using [Pmbootstrap init](https://wiki.postmarketos.org/wiki/Installation/Using_pmbootstrap#Initializing_pmbootstrap)   to tell pmbootstrap what device we have and what options we want.

On your PC, run 
```$pmbootstrap init```
 

This will give a series of options.
Accept the defaults... 
Except:
- Channel: Edge
- Vendor:  qcom 
- Device codename: msm8953
- User Interface: none 
 (As I just want a server, I don't want any user interface. Connection is through SSH.)
- Additional packages : nano,networkmanager,networkmanager-wifi,networkmanager-cli,networkmanager-tui,wpa_supplicant
(Surprisingly, the qcom package doesn't have standard network manager. So its best to add them in here.)

You'll be asked for a username . This will later be needed for login to the phone device (  thru SSH)

### Install

The command ```pmbootstrap install``` installs the chosen options into the image files that will be flashed to the phone.

It does not install anything onto the phone!

It will ask for a password that will be needed to log into the phone later.

### Flash to phone

The install will have created two files that need to be flashed to the phone; lk2nd ( a qualcom specific secondary bootloader) and rootfs ( the root file system).

The Xiaomi A1 tissot phone actually has what they call an A/B structure. I won’t explain it here, but I want to be sure it always boots into the new system, so I choose to flash it to both A and B slots

Ensure device is still in fastboot mode, and plugged in via USB. Then, on the PC, run:
```
    $ pmbootstrap flasher flash_lk2nd --partition boot_a
    $ pmbootstrap flasher flash_lk2nd --partition boot_b
    $ pmbootstrap flasher flash_rootfs --partition userdata
    $ fastboot reboot 
```    
Wait a while as the phone reboots into PostmarketOS.

As the display option was none, there is little indication that things have succeded.

### Login thru SSH
With the USB cable still connected, from your PC terminal ; with your user name run
```
    $ssh user@172.16.42.1
```     
You will be asked for your password.

Hopefully, you now have control of the phone from your computer.






